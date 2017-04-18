<properties 
    pageTitle="Criar aplicações que utilizam tópicos Bus de serviço e subscrições | Microsoft Azure"
    description="Introdução ao publicar-subscrever capacidades fornecidas pelo tópicos Bus de serviço e subscrições."
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

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Criar aplicações que utilizam tópicos Bus de serviço e subscrições

Azure Service Bus suporta um conjunto de tecnologias de software intermédio baseado na nuvem, orientado por mensagem incluindo colocação fiável e resistentes publicar/subscrever mensagens. Este artigo constrói na informação fornecida [Criar](service-bus-create-queues.md) aplicações que utilizam o serviço Bus filas e oferece uma introdução às capacidades de publicar/subscrever disponibilizadas pelos tópicos Bus de serviço.

## <a name="evolving-retail-scenario"></a>Cenário de revenda de evolução

Este artigo continua o cenário de revenda utilizado em [criar aplicações que utilizam o serviço Bus filas](service-bus-create-queues.md). Recuperar esses dados de vendas de individuais ponto de venda (posição) terminais tem de ser encaminhados para um sistema de gestão de inventário que utiliza os dados para determinar quando cotações tem de ser reaprovisionamento. Cada terminal posição reporta os respetivos dados de vendas ao enviar mensagens para a fila **DataCollectionQueue** , onde que permaneçam até que são recebidas pelo sistema de gestão de inventário, conforme mostrado aqui:

![Serviço Bus 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Evoluir neste cenário, um requisito novo foi adicionado ao sistema: o proprietário do arquivo de pretende que possam monitorizar como está a executar o arquivo em tempo real.

Para resolver este requisito, o sistema tem "toque em" desativar o fluxo de dados de vendas. Queremos cada mensagem que foi enviada por terminais posição sejam enviadas para o sistema de gestão de inventário como antes de continuar, mas queremos outra cópia de cada mensagem que pode utilizamos para apresentar a vista de dashboard ao proprietário do arquivo.

Em qualquer situação como estas, na qual exigir a cada mensagem para ser consumida por várias partes, pode utilizar o serviço Bus *Tópicos*. Tópicos fornecem um padrão de subscrever/publicar em que cada mensagem publicada é feita disponível para subscrições de um ou mais registadas com o tópico. Em contrapartida, com filas cada mensagem for recebida por um único consumidor.

Mensagens são enviadas para um tópico da mesma forma como são enviados para uma fila. No entanto, não forem recebidas mensagens do tópico de diretamente; Estes são recebidos de subscrições. Poderá pensar uma subscrição de um tópico como uma fila de espera virtual que recebe cópias das mensagens que são enviadas para esse tópico. As mensagens são recebidas de uma subscrição da mesma forma à medida que são recebidos a partir de uma fila.

Ir novamente o cenário de venda a retalho, fila de espera é substituída por um tópico e uma subscrição é adicionada, o que pode utilizar o componente de sistema de gestão de inventário. O sistema aparece agora da seguinte forma:

![Serviço Bus 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

A configuração aqui idêntico executa na estrutura baseados em filas anterior. Isto é, as mensagens enviadas para o tópico são encaminhadas para a subscrição de **inventário** , a partir do qual o **Sistema de gestão de inventário** consome-los.

Para suportar o dashboard de gestão, criamos uma segunda subscrição no tópico, conforme mostrado aqui:

![Serviço Bus 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Com esta configuração, cada mensagem a partir do terminais posição é disponibilizada para o **Dashboard** e o **inventário** subscrições.

## <a name="show-me-the-code"></a>Mostrar-me o código

O artigo [criar aplicações que utilizam o serviço Bus filas](service-bus-create-queues.md) descreve como inscrever-se para uma conta do Azure e criar um espaço de nomes de serviço. Para utilizar um espaço de nomes de serviço Bus, uma aplicação tem de referenciar a assemblagem de serviço Bus, especificamente Microsoft.ServiceBus.dll. A forma mais fácil para fazer referência a dependências de serviço Bus é instalar o serviço Bus [Nuget pacote](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Também pode encontrar a assemblagem como parte do Azure SDK. A transferência está disponível na [página de transferência do Azure SDK](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Criar o tópico e subscrições

Operações de gestão para Bus de serviço de mensagens entidades (tópicos filas e publicar/subscrever) são executadas através da classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Para criar uma instância de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para um determinado espaço de nomes, são necessárias credenciais adequadas. Serviço Bus utiliza um modelo de segurança baseada em [Assinatura partilhadas do Access (SA)](service-bus-sas-overview.md) . A classe de [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um fornecedor de tokens de segurança com métodos de fábrica do mesmo incorporados devolver alguns fornecedores de tokens conhecidos. Vamos utilizar um método de [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para colocar em espera as credenciais de SA. A instância [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , em seguida, fórmula foi criada com o endereço base do espaço de nomes de serviço Bus e o fornecedor de tokens.

A classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornece métodos para criar, enumerar e eliminar entidades de mensagens. O código que é apresentado aqui mostra como a instância [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é criada e utilizada para criar o tópico **DataCollectionTopic** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Tenha em atenção que não existem overloads do método [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) que permitem-lhe definir propriedades do tópico. Por exemplo, pode definir a time to live (TTL) predefinição para mensagens enviadas para o tópico. Em seguida, adicione as subscrições de **inventário** e **dashboards** .

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Enviar mensagens para o tópico

Para tempo de execução de operações em entidades de serviço Bus; Por exemplo, enviar e receber mensagens, uma aplicação tem primeiro de criar um objeto de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Semelhante à classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , a instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) é criada a partir do endereço base do espaço de nomes de serviço e o fornecedor de tokens.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

As mensagens enviadas para e recebido a partir de tópicos Bus de serviço, são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Esta classe é composta por um conjunto de propriedades padrão (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é utilizado para colocar em espera propriedades da aplicação e um corpo dos dados da aplicação arbitrário. Uma aplicação, pode definir o corpo por passagem em qualquer objeto serializável (o exemplo seguinte transmite num objeto **SalesData** que representa os dados das vendas do terminal posição), que irá utilizar o [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) serializar o objeto. Em alternativa, pode ser fornecido um objeto de [sequência](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) .

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

A forma mais fácil para enviar mensagens para o tópico é utilizar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para criar um objeto de [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) diretamente a partir da instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

Semelhante à utilização de filas, para receber mensagens de uma subscrição que pode utilizar um objeto de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que cria diretamente a partir de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) utilizando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Pode utilizar uma das duas diferentes receber modos (**ReceiveAndDelete** e **PeekLock**), tal como é abordado no [criar aplicações que utilizam o serviço Bus filas](service-bus-create-queues.md).

Tenha em atenção que, quando cria uma [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para subscrições, o parâmetro de *entityPath* é do formulário `topicPath/subscriptions/subscriptionName`. Por conseguinte, para criar um [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para a subscrição do **inventário** do tópico **DataCollectionTopic** , *entityPath* tem de estar definida `DataCollectionTopic/subscriptions/Inventory`. O código é apresentada da seguinte forma:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
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

## <a name="subscription-filters"></a>Filtros de subscrição

Até ao momento, este cenário todas as mensagens enviadas para o tópico ficam disponíveis a todas as subscrições registadas. A expressão chave aqui é "disponibilizada." Enquanto subscrições do serviço Bus ver todas as mensagens enviadas para o tópico, pode copiar apenas um subconjunto dessas mensagens para fila de espera virtual da subscrição. Esta é executada utilizando subscrição *filtros*. Quando cria uma subscrição, pode fornecer uma expressão de filtro sob a forma de um predicado de estilo de SQL92 opera sobre as propriedades da mensagem, as propriedades do sistema (por exemplo, de [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) e as propriedades da aplicação, tal como **StoreName** no exemplo anterior.

Um arquivo de segundo a evoluir o cenário para ilustrar isto, é a serem adicionados ao nosso cenário de venda a retalho. Dados de vendas de todos os terminais de posição de ambas as armazena continua a ter de ser encaminhado para o sistema de gestão de inventário centralizada, mas só está interessado no desempenho da loja um Gestor de arquivo utilizando a ferramenta de dashboard. Pode utilizar filtros para alcançar isto de subscrição. Tenha em atenção que quando os terminais posição publicar mensagens, eles definido a propriedade da aplicação **StoreName** na mensagem. Dois arquivos, por exemplo, **Redmond** e **Seattle**, tendo em conta os terminais de posição de Redmond armazenam carimbo suas mensagens de dados de vendas com um **StoreName** igual a **Redmond**, Considerando que os terminais de posição de arquivo de Seattle utilizam um **StoreName** igual a **Seattle**. O Gestor da loja do arquivo de Redmond apenas quer ver dados a partir do seus terminais de posição. O sistema será apresentada da seguinte forma:

![Serviço Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Para configurar esta encaminhamento, crie a subscrição de **Dashboard** da seguinte forma:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Com este filtro de subscrição, apenas as mensagens a propriedade de **StoreName** definida como **Redmond** serão copiadas para a fila de espera virtual da subscrição do **Dashboard** . Há muito mais para filtragem de subscrição, no entanto. Aplicações podem ter múltiplas regras de filtro por subscrição para além da capacidade de modificar as propriedades de uma mensagem à medida que passa para fila de espera virtual uma subscrição.

## <a name="summary"></a>Resumo

Todos os motivos para utilizar a colocação descrito em [criar aplicações que utilizam o serviço Bus filas](service-bus-create-queues.md) também se aplicam a tópicos, especificamente:

- Desacoplamento temporal – produtor de mensagem e consumidores não tem de estar online ao mesmo tempo.

- Carregar o nivelamento – picos de carregamento são suavizados por tópico permitindo que aplicações consome para aprovisionado de média carregamento em vez de carga de pico.

- Balanceamento de carga – semelhante a uma fila, pode ter vários consumidores concorrentes escutar uma subscrição única, com cada mensagem editora apenas para um dos consumidores, portanto balanceamento de carga.

- Lato – pode evoluir a rede de mensagens sem afetar os pontos finais existentes; Por exemplo, adicionar subscrições ou alterar filtros a um tópico para permitir que para novos consumidores.

## <a name="next-steps"></a>Próximos passos

Consulte o artigo [criar aplicações que utilizam o serviço Bus filas](service-bus-create-queues.md) para obter informações sobre como utilizar filas no cenário de revenda posição.