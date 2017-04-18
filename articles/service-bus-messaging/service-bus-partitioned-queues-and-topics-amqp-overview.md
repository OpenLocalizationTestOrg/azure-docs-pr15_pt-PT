<properties 
    pageTitle="Suporte de AMQP 1.0 para serviço Bus divididos filas e tópicos | Microsoft Azure" 
    description="Saiba como utilizar avançadas mensagem colocação Protocol (AMQP) 1.0 com o serviço Bus divididos filas e tópicos." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Suporte de AMQP 1.0 para serviço Bus divididos filas e tópicos 

Azure Service Bus suporta agora o avançadas colocação protocolo de mensagens (**AMQP**) 1.0 para serviço Bus **divididos filas e tópicos.**

**AMQP** é um protocolo de colocação de mensagem abrir padrão que permite-lhe desenvolver aplicações em diferentes plataformas utilizar linguagens de programação diferentes. Para obter informações gerais sobre AMQP suporte Bus de serviço, consulte [AMQP 1.0 Bus de serviço de suporte](service-bus-amqp-overview.md).

**Partitioned filas e tópicos**, também conhecido como *entidades com partições*, fornecem mais elevados disponibilidade, a fiabilidade e débito que convencional filas sem partições e tópicos. Para mais informações sobre as entidades com partições, consulte o artigo [Com partições entidades de mensagens](service-bus-partitioning.md).

A adição de AMQP 1.0 como um protocolo para comunicar com filas com partições e tópicos permite-lhe construir interoperabilidade aplicações que podem tirar partido da disponibilidade superior, a fiabilidade e ao longo disponibilizadas pelos entidades de serviço Bus divididos.

Para um nível de fio AMQP 1.0 protocolo guia detalhado, que explica como serviço Bus implementa e cria na especificação de técnica de organização de normalização AMQP, consulte o artigo [AMQP 1.0 no Azure Service Bus e evento concentradores protocolo guia](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Utilizar AMQP com filas com partições

Filas são úteis para cenários que requerem desacoplamento temporal, nivelamento de carga, balanceamento de carga e lato. Com uma fila fabricantes enviam mensagens para a fila e consumidores de recebem mensagens de fila de espera, em situações onde recebida uma mensagem pode apenas ser uma vez. Um bom exemplo isto é um sistema de inventário na qual terminais de pontos de venda publicar dados para uma fila em vez de diretamente para o sistema de gestão de inventário. O sistema de gestão de inventário consome, em seguida, os dados em qualquer altura para gerir reaprovisionamento cotações. Isto tem várias vantagens, incluindo o sistema de gestão de inventário não ter de estar online sempre. Para obter mais detalhes sobre filas Bus de serviço, consulte o artigo [criar aplicações que utilizam o serviço Bus filas](service-bus-create-queues.md). 

Ainda mais uma fila com partições aumenta a disponibilidade, a fiabilidade e o débito para as aplicações, tal como estas filas são divididas em múltiplas corretor de mensagem e armazena mensagens.     

### <a name="create-partitioned-queues"></a>Criar filas com partições

Pode criar uma fila com partições utilizando o [portal clássica Azure][] e o SDK Bus de serviço. Para criar uma fila com partições, defina a propriedade de [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) **Verdadeiro** na instância [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) . O código seguinte mostra como criar uma fila com partições utilizando o SDK Bus de serviço. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Enviar e receber mensagens de correio AMQP

Pode enviar mensagens para e receber mensagens de uma partição fila utilizando AMQP como protocolo, definindo a propriedade de [um TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) para [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) , conforme mostrado no seguinte código.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Utilizar AMQP com tópicos com partições

Tópicos são conceptual semelhantes às filas, mas tópicos podem encaminhar uma cópia da mesma mensagem para múltiplas *subscrições*. Num tópico, fabricantes enviam mensagens para o tópico e consumidores de recebem mensagens de subscrições. O inventário sistema pontos de venda cenário, terminais publicar dados para o tópico. O sistema de gestão de inventário, em seguida, recebe mensagens de uma subscrição. Além disso, um sistema de controlo pode receber a mesma mensagem de uma subscrição diferente. Para obter mais detalhes sobre tópicos do serviço Bus e subscrições, consulte o artigo [criar aplicações que utilizam o serviço Bus tópicos e subscrições](service-bus-create-topics-subscriptions.md). 

Tal como acontece com filas, ainda mais tópicos com partições aumentar a disponibilidade, a fiabilidade e o débito para as aplicações, tal como estes tópicos e as suas subscrições são divididas em múltiplas corretor de mensagem e armazena mensagens. 

### <a name="create-partitioned-topics"></a>Criar tópicos com partições

Pode criar um tópico com partições utilizando o [portal clássica Azure][] e o SDK Bus de serviço. Para criar um tópico com partições, defina a propriedade de [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) **Verdadeiro** na instância [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . O código seguinte mostra como criar um tópico com partições utilizando o SDK Bus de serviço.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Enviar e receber mensagens de correio AMQP

Pode enviar mensagens para e receber mensagens de uma subscrição com partições tópico utilizar AMQP como um protocolo, definindo a propriedade de [um TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) para [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), conforme mostrado no seguinte código.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Próximos passos

Consulte as seguintes informações adicionais para saber mais sobre entidades de mensagens com partições, bem como AMQP.

*    [Entidades de mensagens com partições](service-bus-partitioning.md)
*    [Organização de normalização avançadas Message colocação Protocol (AMQP) versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Suporte de AMQP 1.0 no serviço Bus](service-bus-amqp-overview.md)
*    [AMQP 1.0 Guia de protocolo Bus de serviço do Azure e concentradores de evento](service-bus-amqp-protocol-guide.md)
*    [Como utilizar o Java mensagem serviço (JMS) API com Bus de serviço e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Como utilizar AMQP 1.0 com a API do serviço Bus .NET](service-bus-dotnet-advanced-message-queuing.md)

[Azure portal clássico]: http://manage.windowsazure.com
