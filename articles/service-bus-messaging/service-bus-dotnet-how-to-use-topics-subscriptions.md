<properties
    pageTitle="Utilizar tópicos serviço Bus com .NET | Microsoft Azure"
    description="Saiba como utilizar a subscrições e tópicos de serviço Bus com .NET no Azure. Exemplos de código são escritos para aplicações do .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como utilizar a subscrições e tópicos de Bus de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar a subscrições e tópicos de Bus de serviço. Os exemplos estão escritos em c# e utilizam as APIs .NET. Os cenários abrangidos incluem a criação de tópicos e subscrições, criar filtros de subscrição, enviar mensagens a um tópico, receber mensagens de uma subscrição e eliminar tópicos e subscrições. Para mais informações sobre tópicos e subscrições, consulte a secção [os passos seguintes](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Quando cria uma aplicação que utiliza Bus de serviço, tem de adicionar uma referência para a assemblagem de serviço Bus e incluir os espaços de nomes correspondentes. A forma mais fácil para fazer isto é transferir o pacote de [NuGet](https://www.nuget.org) adequado.

## <a name="get-the-service-bus-nuget-package"></a>Obter o pacote de serviço Bus NuGet

O [pacote de serviço Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a forma mais fácil para obter a API Bus do serviço e para configurar a aplicação com todas as dependências de serviço Bus necessárias. Para instalar o pacote de serviço Bus NuGet no seu projeto, faça o seguinte:

1.  No Explorador de solução, **referências**de contexto, em seguida, clique em **Gerir pacotes de NuGet**.
2.  Procurar "Serviço Bus" e selecione o item do **Microsoft Azure Service Bus** . Clique em **instalar** para concluir a instalação, em seguida, feche a caixa de diálogo seguintes:

    ![][7]

Agora está pronto para escrever código para Bus de serviço.

## <a name="create-a-service-bus-connection-string"></a>Criar uma cadeia de ligação Bus de serviço

Serviço Bus utiliza uma cadeia de ligação para armazenar os pontos finais e credenciais. Pode colocar a cadeia de ligação de um ficheiro de configuração, em vez de disco rígido codificação-lo:

- Ao utilizar os serviços Azure, é recomendável que armazene sua cadeia de ligação utilizando o sistema de configuração do Azure service (ficheiros .csdef e .cscfg).
- Quando utilizar o Azure Web sites ou máquinas virtuais do Azure, é recomendável que armazene sua cadeia de ligação utilizando o sistema de configuração de .NET (por exemplo, o ficheiro da Web. config).

Em ambos os casos, pode obter a sua cadeia de ligação através de `CloudConfigurationManager.GetSetting` método, conforme mostrado neste artigo.

### <a name="configure-your-connection-string"></a>Configurar a sua cadeia de ligação

O mecanismo de configuração do serviço permite-lhe dinamicamente alterar as definições de configuração a partir do [Azure portal][] sem Reimplementar a sua aplicação. Por exemplo, adicionar um `Setting` etiqueta ao seu ficheiro de definição (**.csdef**) do serviço, conforme mostrado no seguinte exemplo.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Em seguida, especifique valores no ficheiro de configuração (.cscfg) de serviço.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Utilize o nome da assinatura partilhadas do Access (SA) chave e chaves valores obtidos a partir do portal, tal como descrito anteriormente.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurar a sua cadeia de ligação ao utilizar sites públicos do Azure ou máquinas virtuais do Azure

Ao utilizar a Web sites ou máquinas virtuais, recomendamos que utilize o sistema de configuração de .NET (por exemplo, Web. config). Armazenar a cadeia de ligação utilizando o `<appSettings>` elemento.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Utilize o nome de SA e valores chave que obtidos a partir do [Azure portal][], conforme descrito anteriormente.

## <a name="create-a-topic"></a>Criar um tópico

Pode executar operações de gestão para tópicos Bus de serviço e subscrições utilizando a classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Esta classe fornece métodos para criar, enumerar e eliminar tópicos.

O exemplo seguinte constrói uma `NamespaceManager` objeto utilizando o Azure `CloudConfigurationManager` classe com uma cadeia de ligação que consiste o endereço base de um espaço de nomes de serviço Bus e as SA adequadas credenciais com permissões para geri-lo. É nesta cadeia de ligação da seguinte forma:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilize o exemplo seguinte, tendo em conta as definições de configuração na secção anterior.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Existem overloads do método [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) que permitem-lhe definir propriedades do tópico; Por exemplo, para predefinir o time to live (TTL) valor a ser aplicadas a mensagens enviadas para o tópico. Estas definições são aplicadas ao utilizar a classe de [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . O exemplo seguinte mostra como criar um tópico denominado **TestTopic** com um tamanho máximo de 5 GB e uma mensagem de predefinido TTL de 1 minuto.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] Pode utilizar o método de [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) no [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) objetos para verificar se já existe um tópico com um nome especificado dentro de um espaço de nomes.

## <a name="create-a-subscription"></a>Criar uma subscrição

Também pode criar subscrições tópico utilizando a classe de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Subscrições são com nome e podem ter um filtro opcional que restringe o conjunto de mensagens passado para fila de espera virtual da subscrição.

> [AZURE.IMPORTANT] Para mensagens a receber por uma subscrição, terá de criar esse subscrição antes de enviar todas as mensagens para o tópico. Se não existirem sem subscrições a um tópico, o tópico rejeita essas mensagens.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro predefinido (MatchAll)

Se não for especificado nenhum filtro quando é criada uma nova subscrição, o filtro de **MatchAll** é o filtro predefinido que é utilizado. Quando utiliza o filtro de **MatchAll** , todas as mensagens publicadas para o tópico são colocadas na fila de espera virtual da subscrição. O exemplo seguinte cria uma subscrição denominada "AllMessages" e utiliza o filtro de **MatchAll** predefinido.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com os filtros

Também pode configurar filtros que permitem-lhe especificar quais as mensagens enviadas para um tópico deverão aparecer dentro de uma subscrição de tópico específico.

O tipo de filtro suportado em subscrições mais flexível é a classe de [SqlFilter][] , que implementa um subconjunto de SQL92. Trabalhar com os filtros SQL nas propriedades das mensagens que são publicadas para o tópico. Para mais informações sobre as expressões que podem ser utilizados com um filtro SQL, consulte a sintaxe de [SqlFilter.SqlExpression][] .

O exemplo seguinte cria uma subscrição denominada **HighMessages** com um objeto de [SqlFilter][] apenas seleciona todas as mensagens com uma propriedade personalizada de **MessageNumber** maior do que 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Do mesmo modo, o exemplo seguinte cria uma subscrição denominada **LowMessages** com [SqlFilter][] que apenas seleciona todas as mensagens com uma **MessageNumber** propriedade menor ou igual a 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Agora quando uma mensagem é enviada para `TestTopic`, sempre seja entregue destinatários subscrito para a subscrição de tópico **AllMessages** e seletivamente entregues destinatários subscritos para as subscrições de tópico **HighMessages** e **LowMessages** (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem a um tópico de serviço Bus, a sua aplicação cria um objeto de [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) através da cadeia de ligação.

O código seguinte demonstra como criar um objecto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) para o tópico de **TestTopic** criado anteriormente utilizando o [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) chamada à API.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

As mensagens enviadas para tópicos do serviço Bus são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Objectos de [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) têm um conjunto de propriedades padrão (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é utilizado para colocar em espera propriedades personalizadas de específicos da aplicação bem como um corpo dos dados da aplicação arbitrário. Uma aplicação pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e o adequado **DataContractSerializer** , em seguida, é utilizado para serializar o objeto. Em alternativa, pode ser fornecido um **System.IO.Stream** .

O exemplo seguinte demonstra como enviar mensagens de teste cinco ao objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) **TestTopic** obtido no exemplo anterior código. Tenha em atenção que o valor da propriedade [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de cada mensagem varia consoante a iteração do ciclo (Isto determina quais as subscrições recebem-la).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Tópicos de serviço Bus suportam um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos num tópico mas há um remate no tamanho total das mensagens contidos por um tópico. Tamanho este tópico é definido no momento de criação, com um limite máximo de 5 GB. Se a partições estiver ativada, o limite superior é superior. Para mais informações, consulte o artigo [Partitioned entidades de mensagens](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma subscrição

O caminho recomendado para receber mensagens de uma subscrição é utilizar um objeto de [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) . Objetos de [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) podem trabalhar em dois modos diferentes: [ *ReceiveAndDelete* e *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando utilizar o modo **ReceiveAndDelete** , receber é uma operação de captura único; Isto é, quando serviço Bus recebe um pedido de leitura para uma mensagem de uma subscrição, este marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Uma vez que o serviço Bus tem marcado a mensagem como consumidas, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha de sistema.

No modo de **PeekLock** (que é o modo predefinido), o processo de receção torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o suporte [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o serviço Bus vê **concluída** de chamadas, marca a mensagem como sendo consumidas e remove-a da subscrição.

O exemplo seguinte demonstra como mensagens podem ser recebidas e processados utilizando o modo de **PeekLock** predefinido. Para especificar um valor diferente de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , pode utilizar outra sobrecarga para [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Este exemplo utiliza a chamada de retorno [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) para processar mensagens à medida que chegam a subscrição **HighMessages** .

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Este exemplo configura a chamada de retorno [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) utilizando um objeto de [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) . [Conclusão automática](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) está definido como **Falso** para activar o controlo manual de quando chamar [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) está definido para 1 minuto, o que faz com que o cliente de esperar até um minuto antes de terminar a funcionalidade de renovação automática e o cliente faz uma chamada do nova para verificar a existência de mensagens. Este valor da propriedade reduz o número de vezes que o cliente faz cargo chamadas que não obter as mensagens.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação de recepção for não é possível processar a mensagem por algum motivo,-pode ligar o método [abandonar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) a mensagem recebida (em vez do método [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). Isto faz com que o serviço Bus desbloquear a mensagem no prazo da subscrição e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro da subscrição e, se a aplicação falhar processar a mensagem antes do limite de tempo de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus desbloqueia automaticamente a mensagem e torna disponível na receber novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do pedido de [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) é emitido, a mensagem será reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada *pelo menos uma vez processamento*; Isto é, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando a propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem permanece constante ao longo de tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

O exemplo seguinte demonstra como eliminar o tópico **TestTopic** do espaço de nomes de serviço **HowToSample** .

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Eliminar um tópico também elimina quaisquer subscrições que estão registadas com o tópico. Subscrições também podem ser eliminadas forma independente. O código seguinte demonstra como eliminar uma subscrição denominada **HighMessages** do tópico de **TestTopic** .

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos sobre tópicos do serviço Bus e subscrições, siga estas ligações para mais informações.

-   [Filas, tópicos e subscrições][].
-   [Exemplo de filtros de tópico][]
-   Referência de API para [SqlFilter][].
-   Criar uma aplicação de trabalho que envia e recebe mensagens e para uma fila Bus de serviço: [serviço Bus controlada tutorial de .NET mensagens][].
-   Exemplos de serviço Bus: Transferir a partir do [Azure amostras][] ou ver um [Descrição geral](service-bus-samples.md).

  [Portal do Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
  [Exemplo de filtros de tópico]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Serviço Bus controlada mensagens .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
  [Amostras Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
