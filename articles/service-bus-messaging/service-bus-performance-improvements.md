<properties 
    pageTitle="Melhores práticas para melhorar o desempenho do utilizando o serviço Bus | Microsoft Azure"
    description="Descreve como utilizar o Azure Service Bus para otimizar o desempenho quando trocar juntamente mensagens."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="sethm" />

# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Melhores práticas para melhorias de desempenho através de mensagens do serviço Bus

Este tópico descreve como utilizar o serviço de mensagens do Azure Service Bus para otimizar o desempenho quando trocar juntamente mensagens. A primeira parte deste tópico descreve os diferentes mecanismos que lhe são oferecidos para o ajudar a melhorar o desempenho. A segunda parte fornece orientações sobre como utilizar o serviço Bus de uma forma que pode oferecer o melhor desempenho num determinado cenário.

Ao longo neste tópico, o termo "cliente" que se refere a qualquer entidade que acede Bus de serviço. Um cliente pode demorar a função de um remetente ou um destinatário. O termo "remetente" é utilizado para um cliente de fila ou tópico Bus de serviço que envia mensagens para uma fila de serviço Bus ou um tópico. O termo "recetor" refere-se para um cliente de fila de espera ou subscrição Bus de serviço que recebe as mensagens a partir de uma subscrição ou fila Bus de serviço.

Estas secções apresentam várias conceitos que utiliza o serviço Bus para o ajudar a intensidade desempenho.

## <a name="protocols"></a>Protocolos

Serviço Bus permite que os clientes enviar e receber mensagens através de três protocolos

1. Avançadas colocação de protocolo (AMQP)

2. Serviço Bus mensagens Protocol (SBMP)

3. HTTP

AMQP e SBMP estão mais eficientes, porque mantêm a ligação ao serviço Bus desde que existe a fábrica de mensagens. Também implementa lotes e prefetching. A menos que explicitamente mencionado, todo o conteúdo neste tópico assume a utilização de AMQP ou SBMP.

## <a name="reusing-factories-and-clients"></a>Reutilizar fábricas e os clientes

Objetos de cliente de serviço Bus, tais como [QueueClient][] ou [MessageSender][], são criados através de um objecto [MessagingFactory][] , que também fornece gestão interna de ligações. Não deve fechar fábricas mensagens ou fila de espera, tópico e clientes de subscrição depois de enviar uma mensagem e voltar a criá-los ao enviar a mensagem seguinte. Fechar uma fábrica mensagens elimina a ligação ao serviço do serviço Bus e uma nova ligação é estabelecida quando recriar a fábrica de. Estabelecer uma ligação é uma operação dispendiosa que pode evitar utilizando voltar a mesma fábrica e objetos do cliente para várias operações. Pode utilizar com segurança o objeto [QueueClient][] para enviar mensagens de operações assíncronas em simultâneo e vários threads. 

## <a name="concurrent-operations"></a>Operações em simultâneo

Executar uma operação (enviar, receber, eliminar, etc.) leva-o até algum tempo. Desta vez inclui o processamento da operação pelo serviço Bus de serviço para além da latência do pedido e a resposta. Para aumentar o número de operações por hora, operações tem de executar em simultâneo. Pode fazê-lo de várias formas diferentes:

-   **Operações assíncronas**: o cliente agenda operações através da execução de operações assíncronas. O pedido seguinte é iniciado antes do pedido anterior é concluído. Segue-se um exemplo de uma operação de enviar assíncrona:

    ```
    BrokeredMessage m1 = new BrokeredMessage(body);
    BrokeredMessage m2 = new BrokeredMessage(body);
    
    Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #1");
      });
    Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #2");
      });
    Task.WaitAll(send1, send2);
    Console.WriteLine("All messages sent");
    ```

    Este é um exemplo de uma recepção assíncrona a operação:
    
    ```
    Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    
    Task.WaitAll(receive1, receive2);
    Console.WriteLine("All messages received");
    
    async void ProcessReceivedMessage(Task<BrokeredMessage> t)
    {
      BrokeredMessage m = t.Result;
      Console.WriteLine("{0} received", m.Label);
      await m.CompleteAsync();
      Console.WriteLine("{0} complete", m.Label);
    }
    ```

-   **Várias fábricas**: todos os clientes (remetentes para além de destinatários) que são criados por mesma fábrica partilham uma ligação de TCP. O débito máximo da mensagem é limitado pelo número de operações que pode aceder a esta ligação TCP. O débito que pode ser obtido com uma única fábrica varia bastante com IDA TCP e o tamanho da mensagem. Para obter mais elevadas taxas de débito, deve utilizar vários fábricas mensagens.

## <a name="receive-mode"></a>Modo de recepção

Ao criar um cliente fila ou subscrição, pode especificar um modo de receção: *pré-visualização bloquear* ou *receber e eliminar*. A predefinição receber o modo é [PeekLock][]. Quando a funcionar neste modo, o cliente envia um pedido para receber uma mensagem a partir do serviço Bus. Depois do cliente tem recebeu a mensagem, envia um pedido para concluir a mensagem.

Quando definir o modo de receção para [ReceiveAndDelete][], ambos os passos são combinados num único pedido. Reduz o número geral de operações e pode melhorar a produtividade das mensagens geral. Este ganho desempenho é fornecido por conta perder mensagens.

Serviço Bus não suporta transações para operações de receber-e-delete. Além disso, a semântica de bloqueio de pré-visualização é necessária para qualquer cenários em que o cliente pretende diferir ou [entregues](service-bus-dead-letter-queues.md) uma mensagem.

## <a name="client-side-batching"></a>Lotes do lado do cliente

Lado do cliente de lotes permite que um cliente fila ou tópico atrasar o envio de uma mensagem para um determinado período de tempo. Se o cliente envia mensagens adicionais durante este período de tempo, transmite as mensagens de um único lote. Também lado do cliente de lotes faz com que um cliente de fila de espera/subscrição para batch vários pedidos de **concluída** para um único pedido. Lotes só está disponível para operações de **Enviar** e **concluída** assíncronas. Operações síncronas imediatamente são enviadas para o serviço do serviço Bus. Lotes não ocorrer pré-visualização de ou as operações de recepção, nem lotes ocorrerem ao longo de clientes.

Se o lote excede o tamanho máximo da mensagem, a última mensagem é removida o lote e o cliente envia o lote de imediato. A última mensagem torna-se a primeira mensagem da secção seguinte. Por predefinição, um cliente utiliza um intervalo de lote de 20ms. Pode alterar o intervalo de lote definindo a propriedade [BatchFlushInterval][] antes de criar a fábrica de mensagens. Esta definição afeta todos os clientes de que são criados por esta fábrica.

Para desativar lotes, defina a propriedade [BatchFlushInterval][] para **TimeSpan**. Por exemplo:

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Lotes não afeta o número de operações de mensagens cobrar e só estão disponível para o protocolo de cliente Bus de serviço. O protocolo HTTP não suporta lotes.

## <a name="batching-store-access"></a>Arquivo de acesso de lotes

Para aumentar o débito de uma fila/tópico/subscrição, o serviço Bus secções várias mensagens quando escreve à sua interno store. Se ativado numa fila ou tópico, escrever mensagens para o arquivo vai ser enviadas em batches. Se ativado numa fila ou subscrição, eliminar mensagens a partir da loja vai ser enviadas em batches. Se a loja por lotes access é ativada para uma entidade, serviço Bus atrasa uma operação de escrita de arquivo relativamente a essa entidade por excesso para 20ms. Operações de armazenamento adicionais que ocorrer durante este intervalo são adicionadas ao lote. Acesso por lotes loja afeta apenas operações **Enviar** e **concluída** ; receber operações não são afetadas. Loja por lotes access é uma propriedade numa entidade. Lotes ocorrem através de todas as entidades ativar o acesso por lotes loja.

Ao criar uma nova fila, tópico ou de subscrição, acesso por lotes arquivo está ativado por predefinição. Para desativar o acesso de arquivo por lotes, defina a propriedade de [EnableBatchedOperations][] **Falso** antes de criar a entidade. Por exemplo:

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Loja por lotes access não afeta o número de operações de mensagens cobrar e é uma propriedade de um fila de espera, tópico ou subscrição. É independente de modo a receber e o protocolo que é utilizado entre um cliente e o serviço do serviço Bus.

## <a name="prefetching"></a>Prefetching

Prefetching permite que o cliente de fila de espera ou subscrição carregar mensagens adicionais de serviço quando executar uma operação de receção. O cliente armazena estas mensagens numa local cache. Tamanho da cache é determinado pelas propriedades [QueueClient.PrefetchCount][] ou [SubscriptionClient.PrefetchCount][] . Cada cliente que permite prefetching mantém as suas próprias cache. Uma cache não está a ser partilhada através de clientes. Se o cliente inicia uma operação de recepção e respectiva cache estiver em branco, o serviço transmite um lote de mensagens. O tamanho do lote de for igual ao tamanho da cache ou 256KB, consoante o que é mais pequeno. Se o cliente inicia uma operação de recepção e a cache contém uma mensagem, a mensagem é disponibilizada da cache de.

Quando uma mensagem é obtidos previamente, o serviço bloqueia a mensagem prefetched. Ao fazê-lo, a mensagem prefetched não é possível seriam recebida por um destinatário diferente. Se o destinatário não conseguir concluir a mensagem antes do bloqueio expira, a mensagem fica disponível para outros destinatários. A cópia prefetched da mensagem permanece na cache. No auscultador que consome a cópia em cache expirada irão receber uma exceção ao tentar concluir essa mensagem. Por predefinição, o bloqueio de mensagem expira após 60 segundos. Pode ser prolongado este valor para 5 minutos. Para impedir que o consumo de mensagens expiradas, o tamanho da cache deve estar sempre mais pequeno do que o número de mensagens que pode ser consumida por um cliente dentro do intervalo de tempo limite de bloqueio.

Ao utilizar a expiração de bloqueio predefinida de 60 segundos, um bom valor para [SubscriptionClient.PrefetchCount][] é 20 vezes processamento máximo taxas de todos os destinatários da fábrica. Por exemplo, uma fábrica cria 3 destinatários e cada destinatário pode processar até 10 mensagens por segundo. A contagem de obtenção prévia não deve exceder 20\*3\*10 = 600. Por predefinição, [QueueClient.PrefetchCount][] está definido como 0, o que significa que não mensagens adicionais são obtidas a partir do serviço.

Prefetching mensagens aumenta o débito geral para uma fila ou subscrição porque reduz o número geral de operações de mensagem, ou round viagens. Obter a primeira mensagem, no entanto, irá demorar mais tempo (devido o tamanho da mensagem maior). Receber mensagens de obtidos previamente vai ser mais rápido porque já foram transferidas estas mensagens pelo cliente.

A propriedade para time to live (TTL) de uma mensagem é verificada pelo servidor ao tempo que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL a mensagem quando a mensagem é recebida. Em vez disso, pode ser recebeu a mensagem, mesmo se o TTL a mensagem passou enquanto a mensagem foi colocada em cache pelo cliente.

Prefetching não afeta o número de cobrar operações de mensagens e só está disponível para o protocolo de cliente Bus de serviço. O protocolo HTTP não suporta prefetching. Prefetching está disponível para síncrono e assíncronas recebem operações.

## <a name="express-queues-and-topics"></a>Express filas e tópicos

Entidades Express ativar débito alto e reduzidas cenários de latência. Com entidades express, se uma mensagem é enviada para uma fila ou tópico, a mensagem não é armazenada imediatamente o arquivo de mensagens. Em vez disso,-é colocada em cache na memória. Se uma mensagem permanece na fila de espera para a mais do que de aguardar alguns segundos, é automaticamente escrito armazenamento, assim como proteger contra a perda de devido a uma falha de estável. Escrever a mensagem para uma cache de memória aumenta o débito e reduz a latência porque não existe acesso armazenamento estável ao tempo que a mensagem é enviada. As mensagens que são consumidas dentro de alguns segundos não são escritas para o arquivo de mensagens. O exemplo seguinte cria um tópico express.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Se uma mensagem que contenha informações importantes que não devem ser perdidas é enviada para uma entidade express, o remetente pode forçar Bus de serviço para persistirem imediatamente a mensagem para armazenamento estável definindo a propriedade [ForcePersistence][] **Verdadeiro**.

## <a name="use-of-partitioned-queues-or-topics"></a>Utilização das filas com partições ou tópicos

Interior, serviço Bus utiliza o nó do mesmo e mensagens armazenar para processar e armazenar todas as mensagens para uma entidade de mensagens (fila ou tópico). Uma fila com partições ou tópico, outro lado, é distribuído em múltiplas nós e armazena mensagens. Filas com partições e tópicos de lucro não apenas um débito mais elevado que filas normais e tópicos, também mostrarem disponibilidade superior. Para criar uma entidade com partições, defina a propriedade de [EnablePartitioning][] como **Verdadeiro**, conforme mostrado no seguinte exemplo. Para mais informações sobre as entidades com partições, consulte a [partições entidades de mensagens][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Utilização das várias filas

Se não é possível utilizar uma fila com partições ou um tópico ou a carga esperada não pode ser resolvida por uma única fila com partições ou tópico, tem de utilizar várias entidades de mensagens. Quando utilizar várias entidades, crie um cliente dedicado para cada entidade, em vez de utilizar o mesmo cliente para todas as entidades.

## <a name="development--testing-features"></a>Desenvolvimento e funcionalidades de testes

Serviço Bus tem uma funcionalidade que é utilizado especificamente para o desenvolvimento que **nunca deve ser utilizado em configurações de produção**.

[TopicDescription.EnableFilteringMessagesBeforePublishing](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing.aspx)
- Quando novas regras ou filtros são adicionados para o tópico, EnableFilteringMessagesBeforePublishing pode ser utilizado para confirmar que a expressão de filtro novo está a funcionar como esperado.

## <a name="scenarios"></a>Cenários

As secções seguintes descrevem cenários típicos que mensagens e as definições de serviço Bus preferenciais de destaque. Taxas de débito são classificadas como pequenas (menos de 1 mensagem/segundo), moderar (1 mensagem/segundo ou mensagens/segundo maior mas menos de 100) e máximo (100 mensagens/segunda ou maior). O número de clientes é classificado como pequeno (5 ou menos), moderado (mais de 5, mas menor ou igual a 20) e grande (mais de 20).

### <a name="high-throughput-queue"></a>Fila de alto débito

Objectivo: Maximize o débito de uma única fila. O número de remetentes e destinatários é pequeno.

-   Utilize uma fila com partições para e disponibilidade de desempenho melhorado.

-   Para aumentar a taxa de enviar global na fila de espera, utilize várias fábricas de mensagem para remetentes de criar. Para cada remetente, utilize as operações assíncronas ou vários threads.

-   Para aumentar a taxa de receção geral da fila de espera, utilize várias fábricas de mensagem para criar destinatários.

-   Utilize as operações assíncronas para tirar partido das lotes do lado do cliente.

-   Defina o intervalo de lotes para 50ms para reduzir o número de transmissões Bus de serviço do protocolo de cliente. Se forem utilizados vários remetentes, aumente o intervalo de lotes a 100ms.

-   Deixe o acesso de arquivo por lotes activado. Isto aumenta a taxa global no qual as mensagens podem ser escritas na fila de espera.

-   Defina a contagem de obtenção prévia 20 vezes as processamento máximo taxas de todos os destinatários de uma fábrica. Este procedimento reduz o número de transmissões Bus de serviço do protocolo de cliente.

### <a name="multiple-high-throughput-queues"></a>Várias filas de alto débito

Objectivo: Maximize o débito geral de múltiplas filas. O débito de uma fila individual é moderado ou alta.

Para obter débito máximo em múltiplas filas, utilize as definições descritas maximizar o rendimento de uma única fila. Além disso, utilize fábricas diferentes para criar os clientes que enviar ou recebem a partir de diferentes filas.

### <a name="low-latency-queue"></a>Fila de latência baixa

Objectivo: Minimize latência de fim para fim de uma fila ou um tópico. O número de remetentes e destinatários é pequeno. O débito da fila é pequenas ou moderado.

-   Utilize uma fila com partições para disponibilidade melhorada.

-   Desative lotes do lado do cliente. O cliente imediatamente envia uma mensagem.

-   Desative o acesso de arquivo por lotes. O serviço escreve imediatamente a mensagem para o arquivo.

-   Se utilizar um único cliente, defina a contagem de obtenção prévia para 20 vezes a processamento de taxa do destinatário. Se várias mensagens chegam na fila de espera ao mesmo tempo, o protocolo de cliente do serviço Bus transmite-las todas ao mesmo tempo. Quando o cliente recebe a mensagem seguinte, essa mensagem já está a cache local. A cache deve ser pequenas.

-   Se utilizar vários clientes, defina a contagem de obtenção prévia como 0. Ao fazê-lo, o segundo cliente pode receber a mensagem segunda enquanto o primeiro cliente ainda está a processar a primeira mensagem.

### <a name="queue-with-a-large-number-of-senders"></a>Fila de espera com um grande número de remetentes

Objectivo: Maximize o débito de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de destinatários é pequeno.

Serviço Bus permite até 1000 ligações em simultâneo para uma entidade de mensagens (ou 5000 utilizando AMQP). Este limite é aplicada ao nível do espaço de nomes e tópicos/filas/subscrições são limitadas pelo limite de ligações em simultâneo por espaço de nomes. Para filas, este número é partilhado entre remetentes e destinatários. Se todas as ligações de 1000 são necessárias para remetentes, deve substituir fila de espera com um tópico e uma única subscrição. Um tópico aceita até 1000 ligações em simultâneo de remetentes, Considerando que a subscrição aceita uma ligações em simultâneo de 1000 adicionais a partir de destinatários. Se forem necessárias mais de 1000 remetentes em simultâneo, os remetentes devem enviar mensagens para o protocolo de serviço Bus através de HTTP.

Maximizar o rendimento, faça o seguinte:

-   Utilize uma fila com partições para e disponibilidade de desempenho melhorado.

-   Se a cada remetente encontra-se num processo diferente, utilize apenas uma única fábrica por processo.

-   Utilize as operações assíncronas para tirar partido das lotes do lado do cliente.

-   Utilize a predefinição de lotes intervalo de 20ms para reduzir o número de transmissões Bus de serviço do protocolo de cliente.

-   Deixe o acesso de arquivo por lotes activado. Isto aumenta a taxa global no qual as mensagens podem ser escritas para a fila ou um tópico.

-   Defina a contagem de obtenção prévia 20 vezes as processamento máximo taxas de todos os destinatários de uma fábrica. Este procedimento reduz o número de transmissões Bus de serviço do protocolo de cliente.

### <a name="queue-with-a-large-number-of-receivers"></a>Fila de espera com um grande número de destinatários

Objectivo: Maximize a taxa de receção de uma subscrição com um grande número de destinatários ou fila de espera. Cada destinatário recebe mensagens de uma taxa de juro moderado. O número de remetentes é pequeno.

Serviço Bus permite até 1000 ligações em simultâneo para uma entidade. Se uma fila exigir mais de 1000 destinatários, deve substituir fila de espera com um tópico e múltiplas subscrições. Cada subscrição pode suportar até ligações em simultâneo 1000. Em alternativa, os destinatários podem aceder fila de espera através do protocolo HTTP.

Maximizar o rendimento, faça o seguinte:

-   Utilize uma fila com partições para e disponibilidade de desempenho melhorado.

-   Se cada destinatário reside num processo diferentes, utilize apenas uma única fábrica por processo.

-   Podem utilizar o comportamento assíncronas ou síncronas operações de destinatários. Dada a taxa de receção moderado de um destinatário individual, lado do cliente de lotes de um pedido completo não afeta débito recetor.

-   Deixe o acesso de arquivo por lotes activado. Isto reduz a carga geral da entidade. Também reduz a taxa global no qual as mensagens podem ser escritas para a fila ou um tópico.

-   Definir a contagem de obtenção prévia com um valor pequeno (por exemplo, PrefetchCount = 10). Isto impede que destinatários sejam idle enquanto outros destinatários têm grandes quantidades de mensagens em cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de subscrições

Objectivo: Maximize o caudal de um tópico com um pequeno número de subscrições. É recebida uma mensagem por muitos subscrições, que significa que a taxa de receção combinado sobre todas as subscrições é maior do que a taxa de enviar. O número de remetentes é pequeno. O número de destinatários por subscrição é pequeno.

Maximizar o rendimento, faça o seguinte:

-   Utilize um tópico com partições para e disponibilidade de desempenho melhorado.

-   Para aumentar a taxa de enviar global para o tópico, utilize várias fábricas de mensagem para remetentes de criar. Para cada remetente, utilize as operações assíncronas ou vários threads.

-   Para aumentar a taxa de receção geral de uma subscrição, utilize várias fábricas de mensagem para criar destinatários. Para cada destinatário, utilize as operações assíncronas ou vários threads.

-   Utilize as operações assíncronas para tirar partido das lotes do lado do cliente.

-   Utilize a predefinição de lotes intervalo de 20ms para reduzir o número de transmissões Bus de serviço do protocolo de cliente.

-   Deixe o acesso de arquivo por lotes activado. Isto aumenta a taxa global no qual as mensagens podem ser escritas para o tópico.

-   Defina a contagem de obtenção prévia 20 vezes as processamento máximo taxas de todos os destinatários de uma fábrica. Este procedimento reduz o número de transmissões Bus de serviço do protocolo de cliente.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de subscrições

Objectivo: Maximize o caudal de um tópico com um grande número de subscrições. É recebida uma mensagem por muitos subscrições, o que significa que a taxa de receção combinado sobre todas as subscrições é muito maior do que a taxa de enviar. O número de remetentes é pequeno. O número de destinatários por subscrição é pequeno.

Tópicos com um grande número de subscrições expõem normalmente um débito global baixo se todas as mensagens são encaminhadas para todas as subscrições. Isto é causado pelo facto de cada mensagem for recebida muitas vezes, e todas as mensagens que estão contidas num tópico e todas as suas subscrições são armazenadas no arquivo do mesmo. É assumido que o número de remetentes e o número de destinatários por subscrição são pequenas. Serviço Bus suporta até 2.000 subscrições por tópico.

Maximizar o rendimento, faça o seguinte:

-   Utilize um tópico com partições para e disponibilidade de desempenho melhorado.

-   Utilize as operações assíncronas para tirar partido das lotes do lado do cliente.

-   Utilize a predefinição de lotes intervalo de 20ms para reduzir o número de transmissões Bus de serviço do protocolo de cliente.

-   Deixe o acesso de arquivo por lotes activado. Isto aumenta a taxa global no qual as mensagens podem ser escritas para o tópico.

-   Defina a contagem de obtenção prévia para 20 vezes a receber esperado taxa em segundos. Este procedimento reduz o número de transmissões Bus de serviço do protocolo de cliente.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como otimizar o desempenho Bus de serviço, consulte o artigo [Partitioned entidades de mensagens][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Entidades de mensagens com partições]: service-bus-partitioning.md
  