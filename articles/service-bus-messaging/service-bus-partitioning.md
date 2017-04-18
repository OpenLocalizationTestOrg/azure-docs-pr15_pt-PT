<properties 
    pageTitle="Divididos filas e tópicos | Microsoft Azure"
    description="Descreve como partição filas Bus de serviço e tópicos utilizando vários corretor de mensagem."
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
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Filas com partições e tópicos

Azure Service Bus utiliza vários corretor de mensagem para processar mensagens e vários arquivos de mensagens para armazenar mensagens. Uma fila convencional ou tópico é gerido pelo corretor única mensagem e armazenado no arquivo de mensagens de um. Serviço Bus também lhe permite filas ou tópicos para ser divididos em múltiplas corretor de mensagem e armazena mensagens. Isto significa que o débito geral de uma partição fila ou tópico já não é limitado pelo desempenho de um corretor única mensagem ou um arquivo de mensagens. Além disso, uma falha de temporária de um arquivo de mensagens não compor uma fila com partições ou tópico indisponível. Filas com partições e tópicos podem conter todas as serviço Bus funcionalidades avançadas, tais como suporte para transações e sessões.

Para obter mais detalhes sobre o serviço Bus internos, consulte o tópico de [arquitectura de Bus de serviço][] .

## <a name="how-it-works"></a>Como funciona

Cada fila com partições ou tópico é composta por vários fragmentos. Fragmento de cada é armazenado num arquivo de mensagens diferentes e gerido pelo corretor mensagem diferente. Quando uma mensagem é enviada para uma fila com partições ou tópico, o serviço Bus atribui a mensagem para um dos fragmentos. A seleção é feita aleatoriamente ao serviço Bus ou ao utilizar uma chave de partição pode especificar o remetente.

Quando um cliente pretende receber uma mensagem a partir de uma partição fila ou de uma subscrição de um tópico com partições, consultas de serviço Bus todos os segmentos de mensagens, em seguida, devolve a primeira mensagem que é obtida a partir de qualquer um dos arquivos de mensagens para o destinatário. Caches serviço Bus o outro as mensagens e devolve-los quando recebe adicionais recebem pedidos. Um cliente receção não estiver em mente a criação de partições; o comportamento de cliente destinado de uma partição fila ou o tópico (por exemplo, ler, concluir, diferir mensagens não entregues, prefetching) é idêntico ao comportamento de uma entidade normal.

Não há custo adicional quando enviar uma mensagem para ou receber uma mensagem de um com partições fila ou um tópico.

## <a name="enable-partitioning"></a>Ativar a partições

Para utilizar com partições filas e tópicos com Azure Service Bus, utilizar o SDK do Azure versão 2.2 ou posterior, ou especificar `api-version=2013-10` no seu HTTP pede.

Pode criar filas Bus de serviço e tópicos em tamanhos de 1, 2, 3, 4 ou 5 GB (a predefinição é 1 GB). Com a partições ativado, o serviço Bus cria 16 partições para cada GB que especificar. Como tal, se criar uma fila que é de 5 GB de tamanho, com a 16 partições o tamanho máximo da fila torna-se (5 \* 16) = 80 GB. Pode ver o tamanho máximo da fila com partições ou tópico verificando sua entrada no [portal do Azure][].

Existem várias formas de criar uma fila com partições ou um tópico. Quando cria a fila ou tópico a partir da sua aplicação, pode ativar a partições para a fila ou tópico, respetivamente definindo a propriedade [QueueDescription.EnablePartitioning][] ou [TopicDescription.EnablePartitioning][] **Verdadeiro**. Tópico é criado ou estas propriedades tem de estar definidas no momento fila de espera. Não é possível alterar estas propriedades num tópico ou fila existente. Por exemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Em alternativa, pode criar uma fila com partições ou tópico no Visual Studio ou no [portal do Azure][]. Quando cria uma nova fila ou tópico no portal do, defina a opção **Ativar a partições** na pá de **Definições gerais** da fila ou tópico janela **definições do site** para **true**. No Visual Studio, clique na caixa de verificação **Ativar a partições** na caixa de diálogo **Nova fila** ou **Novo tópico** .

## <a name="use-of-partition-keys"></a>Utilização de chaves de partição

Quando uma mensagem é colocado em fila para uma fila com partições ou tópico, serviço Bus verifica a presença de uma chave de partição. Se encontrar um, seleciona o fragmento de com base nessa tecla. Se não encontrar uma chave de partição, seleciona o fragmento de com base num algoritmo interno.

### <a name="using-a-partition-key"></a>Utilizar uma chave de partição

Alguns cenários, tais como sessões ou transações, requerem mensagens a ser armazenado no fragmento de específico. Todos estes cenários necessitam da utilização de uma chave de partição. Todas as mensagens que utilizam a mesma chave partição são atribuídas para o mesmo fragmento. Se o fragmento de está temporariamente indisponível, o serviço Bus devolve um erro.

Consoante o cenário, propriedades de mensagem diferente são utilizadas como uma chave de partição:

**ID de sessão**: se uma mensagem com a propriedade [BrokeredMessage.SessionId][] definida, em seguida, serviço Bus utiliza esta propriedade como chave partição. Desta forma, todas as mensagens a que pertence à mesma sessão são processadas pelo mesmo corretor de mensagem. Permite que o serviço Bus garantir a mensagem corrigida assim como a consistência de Estados de sessão.

**PartitionKey**: se tem uma mensagem a propriedade [BrokeredMessage.PartitionKey][] mas não a propriedade [BrokeredMessage.SessionId][] definida, em seguida, serviço Bus utiliza a propriedade de [PartitionKey][] como chave partição. Se a mensagem tiver o [ID de sessão][] e o conjunto de propriedades [PartitionKey][] , devem ser idênticas ambas as propriedades. Se a propriedade [PartitionKey][] estiver definida para um valor diferente que a propriedade [identificador de sessão][] , o serviço Bus devolve uma exceção **InvalidOperationException** . A propriedade [PartitionKey][] deve ser utilizada se um remetente envia mensagens de transaccionais deverá ter em consideração que não sejam sessão. A tecla partição assegura que todas as mensagens que são enviadas dentro de uma transação são processadas pelo mesmo corretor de mensagens.

**MessageId**: se a fila ou tópico tem a propriedade de [QueueDescription.RequiresDuplicateDetection][] definida como **Verdadeiro** e não estão definidas as propriedades [BrokeredMessage.SessionId][] ou [BrokeredMessage.PartitionKey][] , em seguida, a propriedade de [BrokeredMessage.MessageId][] funciona como chave partição. (Tenha em atenção que as bibliotecas do Microsoft .NET e AMQP atribua automaticamente um ID de mensagem se não tiver a aplicação de envio.) Neste caso, todas as cópias da mesma mensagem são processadas pelo mesmo corretor de mensagem. Esta opção permite-Bus de serviço para detetar e eliminar mensagens de duplicados. Se a propriedade [QueueDescription.RequiresDuplicateDetection][] não estiver definida como **Verdadeiro**, Bus de serviço não considerar que a propriedade [MessageId][] como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não utilizar uma chave de partição

Em caso de ausência de uma chave de partição, serviço Bus distribui mensagens num método de round-robin para todos os segmentos da fila com partições ou tópico. Se o fragmento de que selecionou não estiver disponível, o serviço Bus atribui a mensagem para um fragmento diferente. Desta forma, a operação de enviar é concluída com êxito apesar da indisponibilidade temporária de um arquivo de mensagens.

Para dar ao serviço Bus tempo suficiente para colocar a mensagem para um fragmento diferente, o valor de [MessagingFactorySettings.OperationTimeout][] especificado pelo cliente que envia a mensagem tem de ser superior a 15 segundos. É recomendado que o utilizador defina a propriedade [OperationTimeout][] para o valor predefinido de 60 segundos.

Tenha em atenção que uma chave de partição "PIN" uma mensagem para um fragmento específico. Se o arquivo de mensagens que detém este fragmento estiver disponível, o serviço Bus devolve um erro. Em caso de ausência de uma chave de partição, serviço Bus pode escolher um fragmento diferente e a operação é concluída com êxito. Por isso, é recomendável que não fornecer uma chave de partição a menos que seja necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos de avançadas: utilizar as transações com entidades com partições

Mensagens que são enviadas como parte de uma transação tem de especificar uma chave de partição. Isto pode ser uma das seguintes propriedades: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]ou [BrokeredMessage.MessageId][]. Todas as mensagens que são enviadas como parte da transação mesmo tem de especificar a mesma chave partição. Se tentar para enviar uma mensagem sem uma chave de partição dentro de uma transação, o serviço Bus devolve uma exceção **InvalidOperationException** . Se tentar enviar várias mensagens dentro da mesma transação tem teclas partição diferente, o serviço Bus devolve uma exceção **InvalidOperationException** . Por exemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se qualquer uma das propriedades que servir de uma chave de partição estiver definida, o serviço Bus PIN a mensagem para um fragmento específico. Este comportamento ocorre se ou não uma transação é utilizada. É recomendável que não especificar uma chave de partição se não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Utilizar sessões com entidades com partições

Para enviar uma mensagem transaccional para um tópico deverá ter em consideração sessão ou fila de espera, a mensagem tem de ter a propriedade [BrokeredMessage.SessionId][] definida. Se a propriedade [BrokeredMessage.PartitionKey][] for especificada, assim, tem de ser idêntico à propriedade [identificador de sessão][] . Se forem diferentes, o serviço Bus devolve uma exceção **InvalidOperationException** .

Ao contrário filas normais (que não sejam divididos) ou tópicos, não é possível utilizar uma única transação para enviar mensagens de múltiplas para sessões de diferentes. Se tentou, serviço Bus devolve uma exceção **InvalidOperationException **. Por exemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reencaminhamento de mensagens automaticamente com entidades com partições

Azure Service Bus suporta o reencaminhamento de mensagens automáticas a partir de, para ou entre entidades com partições. Para permitir que as mensagens automaticamente reencaminhamento de chamadas, defina a propriedade de [QueueDescription.ForwardTo][] na fila de origem ou subscrição. Se a mensagem Especifica uma chave de partição ([ID de sessão][], [PartitionKey][]ou [MessageId][]), essa tecla partição é utilizada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações a ter e diretrizes

- **Funcionalidades de alta consistência**: se uma entidade utiliza funcionalidades como sessões, deteção de duplicados ou controlo explícito de criar a partições chave, em seguida, as mensagens operações sempre são encaminhadas para fragmentos específicos. Se qualquer um dos fragmentos uma experiência de tráfego alto ou o arquivo subjacente está danificado, essas operações falhar e disponibilidade é reduzida. Resumindo, a consistência é ainda muito superior a partições não entidades; apenas um subconjunto de tráfego está com problemas, por oposição a todo o tráfego.
- **Gestão**: devem ser realizadas operações como criar, atualizar e eliminar todos os segmentos da entidade. Se qualquer fragmento está danificado poderá resultar em falhas para estas operações. Para a operação de obter, informações, tais como mensagem de conta têm de ser agregadas a partir de todos os segmentos. Se qualquer fragmento está danificado, o estado de disponibilidade de entidade é comunicado como limitado.
- **Cenários de mensagem de volume baixa**: para destes cenários, especialmente quando utilizar o protocolo HTTP, poderá ter de efetuar várias operações de recepção para obter todas as mensagens. Para pedidos de receção, o front-end efetua uma receção com base em todos os fragmentos e coloca em cache todas as respostas recebidas. Um pedido de receber subsequentes na mesma ligação seria beneficiem das vantagens deste colocação em cache e receber latências será inferiores. No entanto, se tiver várias ligações ou utilizar HTTP, que estabelece uma ligação nova para cada pedido. Como tal, há garantias que-seria terra no mesmo nó. Se todas as mensagens existentes estiver bloqueadas e em cache no outro front-end, a operação de receção devolve **Nulo**. As mensagens são eventualmente expirarem e pode recebê-las novamente. É recomendado HTTP persistentes.
- **Procurar/pré-visualização mensagens**: PeekBatch sempre não devolver o número de mensagens especificada na [propriedade MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Existem duas razões mais comuns para esta situação. Um motivo é que o tamanho da coleção de mensagens agregado excede o tamanho máximo de 256KB. Qualquer outra razão é que se a fila ou tópico tiver [EnablePartitioning propriedade](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) definida como **Verdadeiro**, uma partição poderá não ter suficiente mensagens para concluir o número de mensagens pedido. Em geral, se pretender uma aplicação receber um número específico de mensagens,-deverá contactar o [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) repetidamente até que obtém a esse número de mensagens ou não existem mais mensagens para surgir. Para mais informações, incluindo exemplos de código, consulte o artigo [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) ou [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Funcionalidades mais recentes adicionadas

- Adicionar ou remover regra é agora suportada com entidades com partições. Diferente de entidades não divididos, estas operações não são suportadas em transações. 
- AMQP agora é suportada para enviar e receber mensagens e para a partir de uma entidade com partições.
- AMQP ser agora suportado para as seguintes operações: [Lote enviar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Lote receber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [receber pelo número de sequência](https://msdn.microsoft.com/library/azure/hh330765.aspx), [surgir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Bloquear renovar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Mensagem agenda](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Cancelar mensagem agendado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Adicionar regra de](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Remover regra](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Sessão renovar bloquear](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Definir o estado da sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Obter o estado da sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Surgir mensagens de sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)e [Enumerar sessões](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitações de entidades com partições

Atualmente serviço Bus impõe as seguintes limitações em filas com partições e tópicos:

-   Filas com partições e tópicos não suportam envio de mensagens que pertencem a diferentes sessões de uma única transação.
-   Serviço Bus atualmente permite até 100 filas com partições ou tópicos por espaço de nomes. Cada fila com partições ou tópico conta no sentido a quota de 10.000 entidades por espaço de nomes (não se aplica a camada Premium).

## <a name="next-steps"></a>Próximos passos

Consulte o debate sobre [AMQP 1.0 suporte para o serviço Bus divididos filas e tópicos][] para saber mais sobre como criar a partições entidades de mensagens. 

  [Arquitetura de Bus de serviço]: service-bus-architecture.md
  [Portal do Azure]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [ID de sessão]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Suporte de AMQP 1.0 para serviço Bus divididos filas e tópicos]: service-bus-partitioned-queues-and-topics-amqp-overview.md
