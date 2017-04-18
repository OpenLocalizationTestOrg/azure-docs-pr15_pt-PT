<properties 
    pageTitle="Serviço Bus filas, tópicos e subscrições | Microsoft Azure"
    description="Descrição geral do serviço Bus entidades de mensagens."
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
    ms.date="10/14/2016"
    ms.author="sethm" />

# <a name="service-bus-queues-topics-and-subscriptions"></a>Serviço Bus filas, tópicos e subscrições

Microsoft Azure Service Bus suporta um conjunto de tecnologias baseado na nuvem, middleware mensagem orientados, incluindo a colocação fiável e resistentes publicar/subscrever mensagens. Estas capacidades de mensagens juntamente podem ser consideradas como desacoplados mensagens funcionalidades que suporte publicar-subscrever, temporal desacoplamento e balanceamento de carga cenários utilizando o Bus de serviço de mensagens ferro. Comunicação desacoplada tem muitas vantagens; Por exemplo, clientes e servidores podem ligar conforme necessário e executar as operações de uma forma assíncrona.

As entidades mensagens que formam core das capacidades de mensagens juntamente no serviço Bus são filas, tópicos/subscrições e regras/ações.

## <a name="queues"></a>Filas

Filas oferecem do primeiro entrega de mensagens do primeiro saída (FIFO) para um ou mais consumidores concorrentes. Ou seja, as mensagens são normalmente esperadas para ser recebido e processadas pelos destinatários pela ordem em que foram adicionadas à fila de espera e cada mensagem é recebida e processada pelos consumidores de apenas uma mensagem. É dos principais benefícios da utilização filas alcançar "desacoplamento temporal" dos componentes de aplicação. Por outras palavras, a produtor (remetentes) e consumidores (destinatários) não tem de ser enviar e receber mensagens ao mesmo tempo, uma vez que as mensagens são armazenadas durável na fila de espera. Além disso, o produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e enviar mensagens.

Das vantagens relacionada é "nivelamento de carga," que permite produtor e consumidores para enviar e receber mensagens com taxas diferentes. Em muitas aplicações, a carga de sistema varia ao longo do tempo; No entanto, o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. Produtor de mensagem intermediating e consumidores de com uma fila significa que a aplicação consome só tem de ser aprovisionada possam processar carga média em vez de carga de pico. Profundidade da fila cresce e contratos os variações de carga recebida. Este procedimento guarda diretamente dinheiro exata relativamente à quantidade de infraestrutura necessária para a carga de aplicação de serviço. À medida que aumenta a carregar, podem ser adicionados mais processos de trabalho lerem fila de espera. Cada mensagem é processada por apenas um dos processos de trabalho. Além disso, este com base em solicitação balanceamento de carga permite para uma utilização ideal dos computadores de trabalho mesmo se os computadores trabalhador diferem exata relativamente à potência de processamento, tal como estes puxa mensagens a sua própria velocidade máxima. Este padrão é frequentemente designado pelo padrão de "competir consumidor".

Utilizar filas para intermédio entre produtor de mensagem e consumidores fornece um lato inerente, entre os componentes. Porque produtor e consumidores não estão cientes uns dos outros, pode ser actualizado um consumidor sem ter qualquer efeito no produtor.

Criar uma fila é um processo com várias passos. Executar operações de gestão para Bus de serviço de mensagens entidades (filas e tópicos) através da classe de [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , fórmula foi criada, fornecendo o endereço base do espaço de nomes de serviço Bus e as credenciais de utilizador. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornece métodos para criar, enumerar e eliminar entidades de mensagens. Depois de criar um objeto de [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) a partir do nome de SA e chave e um objeto de gestão do espaço de nomes de serviço, pode utilizar o método de [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) para criar fila de espera. Por exemplo:

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Em seguida, pode criar um objeto de fila de espera e uma fábrica de mensagens com o URI do serviço de Bus como um argumento. Por exemplo:

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Em seguida, pode enviar mensagens para a fila. Por exemplo, se tiver uma lista de mensagens juntamente denominadas `MessageList`, o código aparece semelhante ao seguinte:

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Pode, em seguida, receber mensagens de fila de espera, da seguinte forma:

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

No modo [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , a operação de receção é o único-captura; Isto é, quando serviço Bus recebe o pedido, este marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) é o modelo mais simples e funciona melhor para cenários em que a aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Uma vez que o serviço Bus marca a mensagem como consumida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha de sistema.

No modo de [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , a operação de receção fica duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe o pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que os outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o suporte [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o serviço Bus vê [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) de chamadas, marca a mensagem como consumida.

Se a aplicação não é possível processar a mensagem por algum motivo,-pode chamar o método [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) a mensagem recebida (em vez de [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Permite que Bus de serviço para desbloquear a mensagem e torná-lo disponível para ser recebidos novamente, pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, existe um limite de tempo associado com o bloqueio e se a aplicação falhar processar a mensagem antes do bloqueio de tempo limite expira (por exemplo, se a aplicação falha), em seguida, serviço Bus desbloqueia a mensagem e torna disponível na receber novamente (essencialmente executar uma operação [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) por predefinição).

Tenha em atenção que se que a aplicação falha após a transformação a mensagem, mas antes do pedido de [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) é emitido, a mensagem é reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada *Pelo menos uma vez *processamento; Isto é, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações poderá reenviada a mesma mensagem. Se o cenário de não poder permitir processamento duplicados, em seguida, é necessário lógica adicional na aplicação para detetar duplicados que podem ser obtidos baseadas a propriedade **MessageId** da mensagem permanece constante ao longo de tentativas de entrega. Isto é conhecido como *Exatamente uma vez* processamento.

Para obter mais informações e um exemplo de como criar e enviar mensagens para e de filas, consulte [serviço Bus controlada mensagens .NET Tutorial](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Subscrições e tópicos

Contrariamente ao filas, em que cada mensagem é processada por um único consumidor, *Tópicos* e *subscrições* fornecem um formulário um-para-muitos de comunicação, num padrão *Publicar/subscrever* . Útil para dimensionamento para muito grandes quantidades de destinatários, cada mensagem publicada é disponibilizada para cada subscrição registada com o tópico. As mensagens são enviadas para um tópico e entregue a uma ou mais subscrições associadas, dependendo das regras de filtro que podem ser definidas numa base por subscrição. As subscrições podem utilizar filtros adicionais para restringir as mensagens que pretendem receber. As mensagens são enviadas para um tópico da mesma forma que sejam enviadas para uma fila, mas não forem recebidas mensagens do tópico de diretamente. Em vez disso, são recebidas de subscrições. Uma subscrição de tópico é semelhante a uma fila de espera virtual que recebe cópias das mensagens que são enviadas para o tópico. Mensagens são recebidas de uma subscrição idêntico ao modo como que são recebidas a partir de uma fila.

Em comparação, a funcionalidade Enviar mensagem de uma fila mapas diretamente a um tópico e respectiva mensagem recepção funcionalidade mapas a uma subscrição. Entre outras coisas, isto significa que subscrições suportam os mesmo padrões descritos anteriormente nesta secção exata relativamente à filas: consumidor concorrente, desacoplamento temporal, carga de nivelamento e balanceamento de carga.

Criar um tópico é semelhante à criação de uma fila, conforme mostrado no exemplo na secção anterior. Criar o serviço de URI e, em seguida, utilize a classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para criar o cliente de espaço de nomes. Em seguida, pode criar um tópico utilizando o método de [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) . Por exemplo:

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Em seguida, adicione subscrições como pretender:

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Em seguida, pode criar um cliente de tópico. Por exemplo:

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Utilizar o remetente da mensagem, pode enviar e receber mensagens e para o tópico, conforme mostrado na secção anterior. Por exemplo:

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Semelhante ao filas, as mensagens são recebidas de uma subscrição utilizando um objeto de [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) em vez de um objeto de [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Crie o cliente de subscrição, prisma o nome do tópico, o nome da subscrição e (opcionalmente) o modo de receção como parâmetros. Por exemplo, com a subscrição de **inventário** :

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Regras e ações

Em muitos cenários, todas as mensagens com características específicas têm de ser processadas formas diferentes. Para permitir que este, pode configurar as subscrições para encontrar mensagens que tenham pretendido propriedades e, em seguida, executar determinadas modificações as propriedades. Enquanto subscrições do serviço Bus ver todas as mensagens enviadas para o tópico, pode copiar apenas um subconjunto dessas mensagens para fila de espera virtual da subscrição. Isto é feito através dos filtros de subscrição. Essas modificações chamam *Acções do filtro*. Quando é criada uma subscrição, pode fornecer uma expressão de filtro que opera nas propriedades da mensagem, as propriedades do sistema (por exemplo, de **etiqueta**) e de propriedades da aplicação personalizado (por exemplo, a **StoreName**.) A expressão de filtro SQL é opcional neste caso; sem uma expressão de filtro SQL, qualquer ação de filtro foi definida numa subscrição será executada em todas as mensagens para esse subscrição.

Utilizar o exemplo anterior, para filtrar mensagens provenientes apenas do **Store1**, pretende criar a subscrição de Dashboard da seguinte forma:

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Com este filtro de subscrição no local, apenas as mensagens que tenham o `StoreName` propriedade definida como `Store1` são copiados para a fila de espera virtual para o `Dashboard` subscrição.

Para mais informações sobre os valores possíveis de filtro, consulte a documentação para as classes [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) e [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx) . Além disso, consulte o [controlada mensagens: filtros avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) e exemplos de [Filtros de tópico](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) .

## <a name="next-steps"></a>Próximos passos

Consulte os seguintes tópicos para obter mais informações e exemplos de utilização de serviço Bus controlada mensagens entidades de avançados.

- [Descrição geral do serviço Bus messaging](service-bus-messaging-overview.md)
- [Serviço Bus controlada mensagens .NET tutorial](service-bus-brokered-tutorial-dotnet.md)
- [Serviço Bus controlada mensagens restantes tutorial](service-bus-brokered-tutorial-rest.md)
- [Exemplo de filtros de tópico](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
- [Mensagens juntamente: Avançadas amostra de filtros](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

