<properties 
    pageTitle="Serviço Bus assíncrona mensagens | Microsoft Azure"
    description="Descrição do serviço Bus assíncrona mensagens."
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Assíncronas padrões de mensagens e elevada disponibilidade

Mensagens assíncrona podem ser implementada numa variedade de formas diferentes. Com a filas, tópicos e subscrições, Azure Service Bus suporta asynchrony através de um arquivo e mecanismo reencaminhar. Numa operação de (síncrona) normal, enviar mensagens para filas e tópicos e receber mensagens de filas e subscrições. Aplicações que escreve dependem destas entidades sempre que está a ser disponíveis. Quando altera o estado de funcionamento de entidade, devido a uma variedade de circunstâncias, tem uma maneira de fornecer uma entidade de funcionalidade reduzida pode satisfazem a maior parte das suas necessidades.

Aplicações utilizam normalmente assíncronas padrões de mensagens para ativar um número de cenários de comunicação. Pode criar aplicações em que os clientes podem enviar mensagens para serviços, mesmo quando o serviço não está em execução. Para aplicações essa experiência pode ajudar a rajada das comunicações, uma fila nível a carregar, fornecendo um local para comunicações de memória intermédia. Por fim, pode obter um balanceador de carga simples, mas eficaz para distribuir mensagens através de múltiplos computadores.

Para manter a disponibilidade de qualquer uma destas entidades, considere um número de formas diferentes, na qual estas entidades podem aparecer indisponíveis para um sistema de mensagens resistente. Regra geral, vemos entidade ficar indisponíveis para aplicações que vamos escrever das seguintes formas diferentes:

- Não é possível enviar mensagens.

- Não é possível receber mensagens.

- Não é possível gerir entidades (criar, obter, atualizar ou eliminar entidades).

- Não é possível contactar o serviço.

Para cada um dos seguintes falhas, modos de falha diferentes existem que permitem uma aplicação continuar a trabalhar com algumas nível de funcionalidade reduzida. Por exemplo, um sistema que pode enviar mensagens, mas não recebê-las pode continuar a receber encomendas de clientes, mas não é possível processar as encomendas. Este tópico aborda potenciais problemas que podem ocorrer e como são atenuados esses problemas. Serviço Bus tenha introduzido um número de atenuações que tem optem para e este tópico aborda também as regras que regem a utilização desses atenuações optar ativamente por participar no.

## <a name="reliability-in-service-bus"></a>Fiabilidade no serviço Bus

Existem várias formas para lidar com problemas com a mensagem e entidade e existem orientações que regem a utilização apropriada desses atenuações. Para compreender as diretrizes, tem primeiro de compreender o que pode falhar em Bus de serviço. Devido a estrutura de sistemas Azure, todas estas questões tendem para ser curto. As causas diferentes da indisponibilidade de alto nível, aparecem da seguinte forma:

-   Limitação a partir de um sistema externo que depende Bus de serviço. Limitação ocorre a partir do interações com os recursos de armazenamento e cluster.

-   Problema para um sistema que depende Bus de serviço. Por exemplo, uma determinada peça de armazenamento pode deparar-se com problemas.

-   Falha do serviço Bus subsystem único. Esta situação, um nó cluster pode na estado inconsistente e tem de reiniciar o próprio, a causar todas as entidades que serve para carregar o saldo para outros nós. Isto por sua vez, pode provocar um período curto de processamento de mensagens lenta.

-   Falha do serviço Bus dentro de um centro de dados do Azure. Esta é uma "falha grave" durante o qual o sistema está inacessível para o número de minutos ou algumas horas.

> [AZURE.NOTE] O termo **armazenamento** pode significam que tanto o armazenamento do Windows Azure SQL Azure.

Serviço Bus contém um número de atenuações para estes problemas. As secções seguintes abordam cada problema e as respetivas atenuações.

### <a name="throttling"></a>Limitação

Com o serviço Bus, limitação permite a gestão de taxa de mensagem cooperativa. Cada nó serviço Bus individual aloja aos muitas entidades. Cada uma dessas entidades torna exigências no sistema em termos de CPU, memória, armazenamento e outros facetas. Quando qualquer um dos seguintes facetas Deteta a utilização que exceda limiares definidos, o serviço Bus pode recusar um pedido de determinado. O autor da chamada recebe um [ServerBusyException][] e repetições após 10 segundos.

Como uma mitigação, tem do código de ler o erro e parar qualquer número de tentativas da mensagem, pelo menos, 10 segundos. Uma vez que o erro pode ocorrer ao longo de peças da aplicação de cliente, é esperado que cada porção independentemente executa a lógica de repetição. O código pode reduzir a probabilidade de a ser limitada ao ativar a partições num tópico ou fila de espera.

### <a name="issue-for-an-azure-dependency"></a>Problema uma dependência Azure

Outros componentes do Azure podem, ocasionalmente, ter problemas com o serviço. Por exemplo, quando um sistema que utiliza o serviço Bus está a ser actualizado, esse sistema pode temporariamente uma experiência de capacidades reduzidas. Para resolver estes tipos de problemas, o serviço Bus regularmente investiga e implementa atenuações. Efeitos de lado destas atenuações aparecem. Por exemplo, para processar problemas temporários com o armazenamento, serviço Bus implementa um sistema que permite operações de envio da mensagem trabalhar de forma consistente. Devido à natureza a mitigação, uma mensagem enviada pode demorar até 15 minutos apareça na fila de espera afetada ou subscrição e estar pronto para ser uma operação de receção. Regra geral, a maior parte das entidades não ocorrerá este problema. No entanto, tendo em conta o número de entidades no serviço Bus dentro Azure, este mitigação, por vezes, é necessário para um pequeno subconjunto de clientes Bus de serviço.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha do serviço Bus numa única subsystem

Com qualquer aplicação circunstâncias podem causar um componente interno da Bus de serviço para se tornar inconsistente. Quando o serviço Bus Deteta isto, recolhe dados a partir da aplicação para ajudar a diagnosticar o que aconteceu. Assim que os dados são recolhidos, a aplicação é reiniciada uma tentativa para regressar num estado consistente. Este processo acontece bastante rapidamente e os resultados numa entidade sejam apresentados aos não estar disponível para aprender alguns minutos, apesar de típicas para baixo vezes são muito mais curtos.

Nestes casos, a aplicação de cliente gera uma exceção [System.TimeoutException][] ou [MessagingException][] . Serviço Bus contém uma mitigação para este problema em forma de lógica de repetição automática de cliente. Quando se esgotar o período de repetir e não é possível entregar a mensagem, pode explorar utilizar outras funcionalidades, como [espaços de nomes pares][]. Espaços de nomes emparelhados tem outros avisos que são descritos no artigo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Falha do serviço Bus dentro de um centro de dados do Azure

O motivo para uma falha de um centro de dados do Azure mais provável é uma implementação falha de atualização do serviço Bus ou num sistema dependente. Tal como a plataforma tem maturação, a probabilidade deste tipo de falha tem diminuído. Também pode ocorrer uma falha de centro de dados por razões que incluem o seguinte:

-   Falha do eléctrica (de alimentação e gerar power desaparecer).
-   Conectividade (quebra de internet entre os seus clientes e Azure).

Em ambos os casos, uma falhas singular ou ou causaram o problema. Para resolver este problema e certifique-se de que ainda podem enviar mensagens, pode utilizar [pares espaços de nomes][] para ativar as mensagens sejam enviadas para uma segunda localização enquanto a principal localização é tornada Saudável novamente. Para mais informações, consulte o artigo [melhores práticas para aplicações isolamento contra Bus de serviço de corrente e catástrofes][].

## <a name="paired-namespaces"></a>Espaços de nomes de pares

A funcionalidade de [espaços de nomes emparelhados][] suporta cenários em que uma entidade de serviço Bus ou uma implementação dentro de um centro de dados fica disponível. Enquanto ocorre com pouca frequência este evento, ainda existem sistemas distribuídos que tem de estar preparados para processar pior cenários de maiúsculas/minúsculas. Normalmente, este evento acontece porque algumas elemento que depende Bus serviço está a ter um problema curto prazo. Para manter a disponibilidade de aplicação durante uma falha, os utilizadores do serviço Bus podem utilizar dois espaços de nomes em separado, preferência em centros de dados em separado, para alojar o seus entidades de mensagens. O resto nesta secção utiliza a terminologia seguinte:

-   Espaço de nomes principal: O espaço de nomes com os quais a sua aplicação interage, para enviar e receber operações.

-   Espaço de nomes secundário: O espaço de nomes que age como uma cópia de segurança para o espaço de nomes principal. Lógica da aplicação não interagir com este espaço de nomes.

-   Intervalo de activação pós-falha: A quantidade de tempo para aceitar falhas normais antes da aplicação muda do espaço de nomes principal para o espaço de nomes secundário.

Emparelhadas espaços de nomes suporte *Enviar disponibilidade*. Envie disponibilidade preserva a capacidade para enviar mensagens. Para utilizar enviar disponibilidade, a aplicação tem de cumprir os seguintes requisitos:

1.  As mensagens são recebidas apenas do espaço de nomes principal.

2.  Mensagens enviadas para uma determinado fila ou tópico poderá chegar fora de ordem.

3.  Se a sua aplicação utiliza sessões, poderão chegam fora de ordem mensagens dentro de uma sessão. Esta é uma quebra de funcionalidade normal de sessões. Isto significa que a aplicação utiliza sessões para logicamente mensagens de grupo. Estado da sessão só é mantido no espaço de nomes principal.

4.  Poderão chegam fora de ordem mensagens dentro de uma sessão. Esta é uma quebra de funcionalidade normal de sessões. Isto significa que a aplicação utiliza sessões para logicamente mensagens de grupo.

5.  Estado da sessão só é mantido no espaço de nomes principal.

6.  Fila de espera primária pode ficar online e comece a aceitação antes da fila secundária fornece todas as mensagens na fila principal de mensagens.

As secções seguintes abordam as APIs, como as APIs são implementadas e mostra o código de exemplo que utilizam a funcionalidade. Tenha em atenção que não existem implicações de faturaçãohttps associadas com esta funcionalidade.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>A API MessagingFactory.PairNamespaceAsync

A funcionalidade de espaços de nomes emparelhado inclui o método de [PairNamespaceAsync][] a classe de [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Quando a tarefa estiver concluída, o espaço de nomes emparelhar também é concluída e pronto para tomar ações relativamente a para qualquer [MessageReceiver][], [QueueClient][], ou [TopicClient][] criadas com a instância de [MessagingFactory][] . [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] é a classe base para os vários tipos de emparelhar que estão disponíveis com um objeto de [MessagingFactory][] . Atualmente, a única classe derivada é uma com nome [SendAvailabilityPairedNamespaceOptions][], que implementa os requisitos de disponibilidade de enviar. [SendAvailabilityPairedNamespaceOptions][] tem um conjunto de construtores construir uns dos outros. Consultar o construtor com a maioria dos parâmetros, é possível compreender o comportamento dos outros construtores.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Estes parâmetros têm os seguintes significados:

-   *secondaryNamespaceManager*: uma instância de [NamespaceManager][] inicializada para o espaço de nomes secundário que pode utilizar o método de [PairNamespaceAsync][] para configurar o espaço de nomes secundário. O Gestor de espaço de nomes é utilizado para obter a lista de filas no espaço de nomes e para se certificar de que existem de filas de registo de tarefas pendentes necessários. Se não existir essas filas, são criadas. [NamespaceManager][] requer a capacidade de criar um token com a afirmação **Gerir** .

-   *messagingFactory*: A instância [MessagingFactory][] para o espaço de nomes secundário. O objeto [MessagingFactory][] é utilizado para enviar e, se a propriedade [EnableSyphon][] está definida como **Verdadeiro**, receber mensagens de filas de registo de tarefas pendentes.

-   *backlogQueueCount*: O número de registo de tarefas pendentes filas para criar. Este valor tem de ser, pelo menos, 1. Quando enviar mensagens para o registo de tarefas pendentes, uma destas filas aleatoriamente é escolhida. Se definir o valor para 1, em seguida, apenas uma fila pode nunca ser utilizada. Quando isto acontece e na fila de um registo de tarefas pendentes gera erros, o cliente é a não ser possível experimentar uma fila de registo de tarefas pendentes diferente e poderá falhar enviar a mensagem. Recomendamos que a definição deste valor para alguns maior valor e predefinido o valor a 10. Pode alterar para um valor superior ou inferior dependendo da quantidade de dados da aplicação envia por dia. Cada fila de registo de tarefas pendentes pode conter até 5 GB de mensagens.

-   *failoverInterval*: A quantidade de tempo durante o qual aceitará falhas no espaço de nomes primária antes de mudar de qualquer entidade única ao longo do espaço de nomes secundário. Activações pós-falha ocorre numa base de entidade por entidade. Entidades num único espaço de nomes frequentemente direto em nós diferentes dentro Bus de serviço. Uma falha de uma entidade não implicam uma falha na outra. Pode definir este valor para [System.TimeSpan.Zero][] para activação pós-falha para secundária imediatamente após a sua falha em primeiro lugar, não transitório. Falhas acionam o temporizador activação pós-falha são qualquer [MessagingException][] na qual a propriedade [IsTransient][] for FALSO, ou uma [System.TimeoutException][]. Outros exceções, tal como [UnauthorizedAccessException][] não causam falha na ligação, uma vez que indicam que o cliente está configurado incorretamente. Não faz com que uma [ServerBusyException][] activação pós-falha porque o padrão de correto está a aguardar 10 segundos, em seguida, envie a mensagem novamente.

-   *enableSyphon*: indica que este emparelhar determinado deve também syphon mensagens do espaço de nomes secundário novamente para o espaço de nomes principal. Em geral, aplicações que enviam mensagens devem este valor definido para **Falso**; as aplicações que recebem mensagens devem defina este valor para **true**. O motivo para isto é frequentemente, se há menos destinatários de mensagem que remetentes de mensagens. Consoante o número de destinatários, pode optar por ter uma instância da aplicação única processar os direitos de sifão. Utilizar vários destinatários tem implicações de faturaçãohttps para cada fila de registo de tarefas pendentes.

Para utilizar o código, crie uma instância de [MessagingFactory][] principal, uma instância de [MessagingFactory][] secundária, uma instância de [NamespaceManager][] secundária e uma instância de [SendAvailabilityPairedNamespaceOptions][] . A chamada pode ser tão simple como o seguinte procedimento:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Quando concluir a tarefa devolvida pelo método [PairNamespaceAsync][] , tudo está configurado e pronto a utilizar. Antes da tarefa é devolvida, poderá não concluiu todos os trabalhos de fundo necessários para o emparelhar para trabalhar para a direita. Como resultado, não deverá começar a enviar mensagens até a tarefa devolve. Se ocorrerem falhas, tais como as credenciais incorretas ou falha ao criar as filas de registo de tarefas pendentes, essas exceções serão devolvidas assim que a tarefa for concluída. Assim que a tarefa devolve, certifique-se de que as filas foram encontradas ou criadas por examinar a propriedade [BacklogQueueCount][] do seu instância [SendAvailabilityPairedNamespaceOptions][] . Para o código anterior, essa operação é apresentada da seguinte forma:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para mensagens assíncrona no serviço Bus, leia mais detalhes sobre como [espaços de nomes pares][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Práticas recomendadas para aplicações contra falhas de serviço Bus e catástrofes de isolamento]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [espaços de nomes de pares]: service-bus-paired-namespaces.md