<properties 
    pageTitle="Como utilizar o Azure Service Bus com o SDK WebJobs" 
    description="Saiba como utilizar filas Bus de serviço do Azure e tópicos com o SDK WebJobs." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Como utilizar o Azure Service Bus com o SDK WebJobs

## <a name="overview"></a>Descrição geral

Este guia fornece c# exemplos de código que mostram como acionar um processo quando é recebida uma mensagem de Azure Service Bus. Os exemplos de código utilizam [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versão 1. x.

O guia assume que saber [como criar um projeto WebJob no Visual Studio com cadeias de ligação que apontam para a sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md).

Fragmentos de código mostram apenas as funções, não o código que cria a `JobHost` objeto tal como no exemplo:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

É um [exemplo de código do serviço Bus completo](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) no repositório de azure-webjobs sdk amostras de GitHub.com.

## <a id="prerequisites"></a>Pré-requisitos

Para trabalhar com o serviço Bus tem de instalar o pacote de NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) para além de outros volumes de WebJobs SDK. 

Também tem de configurar a cadeia de ligação AzureWebJobsServiceBus para além de cadeias de ligação de armazenamento.  Pode fazê-lo `connectionStrings` secção do ficheiro App, conforme mostrado no exemplo seguinte:

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Para um projeto de exemplo que inclui a definição de cadeia de ligação de serviço Bus no ficheiro de App, consulte o artigo [Bus de serviço de exemplo](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Também podem ser definidas as cadeias da ligação no ambiente do Azure runtime, em seguida, substitui as definições de App quando o WebJob é executado no Azure; Para mais informações, consulte o artigo [Começar a trabalhar com o SDK WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a>Como acionar uma função quando for recebida uma mensagem de fila Bus de serviço

Para escrever uma função que o SDK WebJobs chamadas quando for recebida uma mensagem de fila de espera, utilize o `ServiceBusTrigger` atributo. O construtor de atributo leva um parâmetro que especifica o nome da fila de espera para as de inquérito.

### <a name="how-servicebustrigger-works"></a>Como funciona a ServiceBusTrigger

O SDK recebe uma mensagem na `PeekLock` chamadas e o modo `Complete` na mensagem se a função é concluída com êxito ou chamadas `Abandon` se a função falhar. Se a função for executado durante mais da `PeekLock` limite de tempo, o bloqueio é renovada automaticamente.

Serviço Bus é que a sua própria processamento de fila corrompida foi que não pode ser controlado ou configurado pelo SDK WebJobs. 

### <a name="string-queue-message"></a>Mensagem de fila de cadeia

O código seguinte de exemplo lê uma mensagem de fila de espera que contém uma cadeia e escreve a cadeia ao dashboard WebJobs SDK.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Nota:** Se estiver a criar as mensagens de fila numa aplicação que não utiliza o SDK WebJobs, certifique-se definir [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) para "texto/simples".

### <a name="poco-queue-message"></a>Mensagem de fila POCO

O SDK serão automaticamente serialização de uma mensagem de fila de espera que contenha JSON para um POCO [(simples objecto CLR antigo](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) tipo. O código seguinte de exemplo lê uma mensagem de fila de espera que contenha uma `BlobInformation` objeto que tem um `BlobName` propriedade:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Para obter exemplos de código que mostra como utilizar as propriedades da POCO para trabalhar com blobs e tabelas na mesma função, consulte o artigo a [versão de filas de armazenamento deste artigo](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Se o seu código que cria a mensagem de fila de espera não utilizar o SDK WebJobs, utilize código semelhante ao exemplo seguinte:

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Tipos de ServiceBusTrigger funciona com

Além disso `string` e tipos de POCO, pode utilizar o `ServiceBusTrigger` atributo com uma matriz de bytes ou um `BrokeredMessage` objeto.

## <a id="create"></a>Como criar mensagens em fila Bus de serviço

Para escrever uma função que cria uma nova mensagem utilização da fila a `ServiceBus` atributo e transmitir no nome da fila de espera para o construtor de atributo. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Criar uma mensagem de fila única numa função não assíncrona

O código de exemplo seguinte utiliza um parâmetro de saída para criar uma nova mensagem na fila de espera com o nome "outputqueue" com o mesmo conteúdo como a mensagem recebida na fila de espera com o nome "inputqueue".

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

O parâmetro de saída para criar uma mensagem de fila única pode ser qualquer um dos seguintes tipos de:

* `string`
* `byte[]`
* `BrokeredMessage`
* Um tipo POCO serializável definidos por si. Automaticamente serializados como JSON.

Para os parâmetros de tipo POCO, fila é criada uma mensagem sempre quando termina a função; Se o parâmetro é nulo, o SDK cria uma mensagem de fila de espera que devolvem nula quando a mensagem é recebida e anular a serialização. Os outros tipos, se o parâmetro é nulo nenhuma mensagem de fila é criada.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Criar múltiplas mensagens em fila ou nas funções assíncrona

Para criar várias mensagens, utilize o `ServiceBus` atributo com `ICollector<T>` ou `IAsyncCollector<T>`, conforme mostrado no seguinte exemplo de código:

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Cada mensagem fila é criada imediatamente quando o `Add` método é chamado.

## <a id="topics"></a>Como trabalhar com tópicos Bus de serviço

Para escrever uma função que o SDK chamadas quando for recebida uma mensagem sobre um tópico de serviço Bus, utilize o `ServiceBusTrigger` atributo com o construtor do feed de tópico e nome da subscrição, conforme mostrado no seguinte exemplo de código:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Para criar uma mensagem sobre um tópico, utilize o `ServiceBus` atributo com um nome de tópico da mesma forma utilizá-la com um nome de fila de espera.

## <a name="features-added-in-release-11"></a>Funcionalidades de adicionadas na versão 1.1

As funcionalidades seguintes foram adicionadas na versão 1.1:

* Permitir personalização abrangente de processamento através de mensagens `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`suporta a personalização do serviço Bus `MessagingFactory` e `NamespaceManager`.
* A `MessageProcessor` padrão estratégia permite-lhe especificar um processador por fila/tópico.
* Simultaneidade de processamento de mensagem é suportada por predefinição. 
* Personalização fácil de `OnMessageOptions` através do `ServiceBusConfiguration.MessageOptions`.
* Permitir [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) deve ser especificado no `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (para obter cenários onde poderá não ter gerem direitos). 

## <a id="queues"></a>Tópicos relacionados abrangidos pelo artigo sobre como utilizar de filas de armazenamento

Para obter informações sobre cenários WebJobs SDK não específicos Bus de serviço, consulte o artigo [como utilizar o armazenamento de fila Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Os tópicos abrangidos no artigo incluem o seguinte:

* Funções de assíncrona
* Várias instâncias
* Desligar sem problemas
* Utilize os atributos de WebJobs SDK no corpo de uma função
* Definir as cadeias de ligação SDK no código
* Valores definidos para WebJobs SDK parâmetros construtor no código
* Acionar uma função manualmente
* Escrever os registos

## <a id="nextsteps"></a>Próximos passos

Este guia forneceu exemplos de código que mostram como lidar cenários comuns de trabalhar com Azure Service Bus. Para obter mais informações sobre como utilizar Azure WebJobs e o SDK WebJobs, consulte o artigo [Azure WebJobs recomendado recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
