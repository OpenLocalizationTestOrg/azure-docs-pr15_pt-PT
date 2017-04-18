<properties 
    pageTitle="Escrever as aplicações que utilizam filas serviço Bus | Microsoft Azure"
    description="Como escrever uma aplicação baseada em fila simple que utiliza Bus de serviço."
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Criar aplicações que utilizam filas Bus de serviço

Este tópico descreve filas Bus de serviço e mostra como escrever uma aplicação baseada em fila simple que utiliza Bus de serviço.

Considere a hipótese de um cenário no mundo de retalho na qual os dados de vendas dos individuais terminais de Point-of-Sale (posição) devem ser encaminhados a um sistema de gestão de inventário que utiliza os dados para determinar quando cotações tem de ser reaprovisionamento. Esta solução utiliza Bus de serviço de mensagens para a comunicação entre os terminais e o sistema de gestão de inventário, conforme ilustrado na figura seguinte:

![Imagem de filas serviço Bus 1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal posição relatórios os respetivos dados de vendas ao enviar mensagens para o **DataCollectionQueue**. Estas mensagens permanecem este fila de espera até serem obtidos pelo sistema de gestão de inventário. Este padrão é frequentemente designado por *mensagens assíncrona*, porque o terminal posição não tem de aguardar uma resposta a partir do sistema de gestão de inventário para processamento de continuar.

## <a name="why-queuing"></a>Por que razão colocação?

Antes de olharmos para o código que é necessário para configurar esta aplicação, considere as vantagens de utilizar uma fila neste cenário em vez de ser terminais posição falar diretamente com (modo síncrono) ao sistema de gestão de inventário.

### <a name="temporal-decoupling"></a>Desacoplamento temporal

Com o comportamento assíncrono padrão de mensagens, produtor e consumidores não tem de estar online ao mesmo tempo. A infraestrutura de mensagens sujeito armazena as mensagens até à parte consome está pronta a recebê-las. Isto significa que os componentes da aplicação distribuída podem ser desligados, quer imediatamente; Por exemplo, para a manutenção ou devido a uma falha de sistema do componente, sem afetar a totalidade do sistema. Além disso, a aplicação consome apenas poderá ter de estar online durante determinadas horas do dia. Por exemplo, este cenário de venda a retalho, o sistema de gestão de inventário apenas poderá ter de ficar online após o fim do dia empresas.

### <a name="load-leveling"></a>Carregar o nivelamento

Em muitas aplicações carga do sistema varia ao longo do tempo, Considerando que o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. Produtor de mensagem intermediating e consumidores de com uma fila significa que a aplicação consome (o trabalhador) só tem de ser aprovisionada para uma média carga em vez de uma carga de pico de serviço. Profundidade da fila irá aumentar e como a carga recebida varia de contrato. Este procedimento guarda diretamente dinheiro exata relativamente à quantidade de infraestrutura necessária para a carga de aplicação de serviço.

![Imagem de filas serviço Bus 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Balanceamento de carga

À medida que aumenta a carregar, podem ser adicionados mais processos de trabalho lerem na fila de trabalho. Cada mensagem é processada por apenas um dos processos de trabalho. Além disso, este balanceamento de carga com base em solicitação permite para uma utilização ideal dos computadores trabalhador mesmo se os computadores trabalhador diferem exata relativamente à potência de processamento, tal como estes puxa mensagens a sua própria velocidade máxima. Este padrão é frequentemente designado pelo padrão de consumidor concorrentes.

![Imagem de filas serviço Bus 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Lato

Utilizar colocação intermédio entre produtor de mensagem e consumidores fornece um lato intrínseco, entre os componentes. Porque produtor e consumidores não estão cientes uns dos outros, pode ser actualizado um consumidor sem ter qualquer efeito no produtor. Além disso, pode evoluir a topologia de mensagens sem afetar os pontos finais existentes. Vamos abordar isto mais quando falarmos sobre publicar/subscrever.

## <a name="show-me-the-code"></a>Mostrar-me o código

A secção seguinte mostra como utilizar o serviço Bus para criar esta aplicação.

### <a name="sign-up-for-an-azure-account"></a>Inscrever-se para uma conta do Azure

Terá de uma conta Azure para poder começar a trabalhar com Bus de serviço. Se não já possui um, pode inscrever-se para uma conta gratuita [aqui](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Criar um espaço de nomes

Assim que tiver uma subscrição, pode [criar um novo espaço de nomes](service-bus-create-namespace-portal.md). Cada espaço de nomes funciona como um âmbito contentor para um conjunto de entidades Bus de serviço. Dê um nome exclusivo ao seu novo espaço de nomes em todas as contas de serviço Bus. 

### <a name="install-the-nuget-package"></a>Instale o pacote de NuGet

Para utilizar o espaço de nomes de serviço Bus, uma aplicação tem de referenciar a assemblagem de serviço Bus, especificamente Microsoft.ServiceBus.dll. Pode encontrar esta assemblagem como parte do Microsoft Azure SDK e a transferência estiver disponível na [página de transferência do Azure SDK](https://azure.microsoft.com/downloads/). No entanto, o [pacote de serviço Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a forma mais fácil para obter a API Bus do serviço e para configurar a aplicação de todas as dependências de Bus de serviço.

### <a name="create-the-queue"></a>Criar fila de espera

Operações de gestão para Bus de serviço de mensagens entidades (tópicos filas e publicar/subscrever) são executadas através da classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Serviço Bus utiliza um modelo de segurança baseada em [Assinatura partilhadas do Access (SA)](service-bus-sas-overview.md) . A classe de [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um fornecedor de tokens de segurança com métodos de fábrica do mesmo incorporados devolver alguns fornecedores de tokens conhecidos. Vamos utilizar um método de [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para colocar em espera as credenciais de SA. A instância [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , em seguida, fórmula foi criada com o endereço base do espaço de nomes de serviço Bus e o fornecedor de tokens.

A classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornece métodos para criar, enumerar e eliminar entidades de mensagens. O código que é apresentado aqui mostra como a instância [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é criada e utilizada para criar **DataCollectionQueue** fila de espera.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Tenha em atenção que não existem overloads do método [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) que permitem propriedades de fila de espera para ser optimizado. Por exemplo, pode definir o valor de (TTL) de time to live do predefinido para mensagens enviadas para a fila.

### <a name="send-messages-to-the-queue"></a>Enviar mensagens para fila de espera

Para tempo de execução de operações em entidades de serviço Bus; Por exemplo, enviar e receber mensagens, uma aplicação tem primeiro de criar um objeto de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Semelhante à classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , a instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) é criada a partir do endereço base do espaço de nomes de serviço e o fornecedor de tokens.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

As mensagens enviadas para e recebeu do serviço Bus filas são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Esta classe é composta por um conjunto de propriedades padrão (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é utilizado para colocar em espera propriedades da aplicação e um corpo dos dados da aplicação arbitrário. Uma aplicação, pode definir o corpo por passagem em qualquer objeto serializável (o exemplo seguinte transmite num objeto **SalesData** que representa os dados das vendas do terminal posição), que irá utilizar o [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) serializar o objeto. Em alternativa, pode ser fornecido um objeto de [sequência](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) .

A forma mais fácil para enviar mensagens para uma determinada fila, no nosso caso **DataCollectionQueue**, é utilizar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para criar um objeto de [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) diretamente a partir da instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Receber mensagens de fila de espera

Para receber mensagens de fila de espera, pode utilizar um objeto de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que cria diretamente a partir de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) utilizando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Destinatários da mensagem podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**. O [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) é definido quando é criado no auscultador de mensagem, como um parâmetro para a chamada [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) .


Ao utilizar o modo de **ReceiveAndDelete** , a receber está a ser uma operação de captura único; Isto é, quando serviço Bus recebe o pedido, este marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que a aplicação pode tolerar processamento de uma mensagem não se fosse uma falha a ocorrer. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Uma vez que o serviço Bus marcado a mensagem como consumida, quando a aplicação reinicia e inícios consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , a receber torna-se uma operação de duas fases, que torna possível a suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe o pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o suporte [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o serviço Bus vê [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) de chamadas, marca a mensagem como consumida.

Duas outras consequências são possíveis. Em primeiro lugar, se a aplicação estiver não é possível processar a mensagem por algum motivo,-pode ligar [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) a mensagem recebida (em vez de [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isto faz com que o serviço Bus desbloquear a mensagem e torná-lo disponível para ser recebidos novamente, pelo mesmo consumidor ou por outro consumidor a concluir. Segundo lugar, existe um limite de tempo associado com o bloqueio e se a aplicação não é possível processar a mensagem antes do bloqueio de tempo limite expirar (por exemplo, se a aplicação falha), em seguida, Bus serviço serão desbloquear a mensagem e torná-lo disponível para ser recebidos novamente (essencialmente executar uma operação [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) por predefinição).

Tenha em atenção que se a aplicação falha após processa a mensagem, mas antes de [Concluir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) o pedido foi emitido, a mensagem vai ser reenviada para a aplicação quando é reiniciado. Isto com frequência é designado por processamento de *Pelo menos uma vez* . Isto significa que cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, é necessário lógica adicional na aplicação para detetar duplicados. Isto pode ser realizado com base na propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem. O valor desta propriedade permanece constante ao longo de tentativas de entrega. Isto é designado por processamento *Exatamente uma vez* .

O código que é mostrado aqui recebe e processa uma mensagem com o modo de **PeekLock** , que é a predefinição se não [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) fornecido um valor é explicitamente.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### <a name="use-the-queue-client"></a>Utilizar o cliente de fila de espera

Os exemplos anteriormente nesta secção criado objectos [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) e [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) diretamente a partir do [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) para enviar e receber mensagens de fila de espera, respetivamente. Uma abordagem alternativa consiste em utilizar a classe de [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , que suporta envie e receba operações para além de funcionalidades mais avançadas, como sessões.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para filas, consulte o artigo [criar aplicações que utilizam o serviço Bus tópicos e subscrições](service-bus-create-topics-subscriptions.md) para continuar a este debate utilizando as capacidades de publicar/subscrever de tópicos de serviço Bus e subscrições.