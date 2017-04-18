<properties
    pageTitle="Introdução ao armazenamento de fila Azure utilizando o .NET | Microsoft Azure"
    description="Azure filas fornecem fiável, assíncrona mensagens entre componentes da aplicação. Nuvem mensagens permite que os componentes de aplicação para dimensionar de forma independente."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao armazenamento de fila Azure utilizando o .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de fila Azure fornece nuvem mensagens entre componentes da aplicação. Estruturar os pedidos de escala, componentes da aplicação com frequência são desacoplados, para que estes podem dimensionar de forma independente. Armazenamento de filas fornece mensagens assíncrona para comunicação entre os componentes de aplicação, se estiverem em execução na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. Armazenamento de filas também suporta a gerir tarefas assíncronas e de construção de fluxos de trabalho do processo.

### <a name="about-this-tutorial"></a>Informações sobre este tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns a utilizar o armazenamento do Azure fila de espera. Cenários abrangidos incluem criar e eliminar filas e adicionar, de leitura e eliminar mensagens de fila de espera.

**Estimativa tempo a concluir:** 45 minutos

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de armazenamento Azure do cliente para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestor de configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Uma [conta de armazenamento Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicione o seguinte `using` declarações na parte superior da `program.cs` ficheiro:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Criar o cliente do serviço fila de espera

A classe de **CloudQueueClient** permite-lhe obter filas armazenadas na fila de espera. Eis uma forma de criar o cliente do serviço:

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Agora, está pronto para escrever código que lê dados a partir de e escreve dados ao armazenamento de fila de espera.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila de espera, se ainda não existir:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Inserir uma mensagem uma fila

Para inserir uma mensagem uma fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, ligue para o método de **AddMessage** . Um **CloudQueueMessage** podem ser criados a partir de uma cadeia (em formato UTF-8) ou uma matriz de **bytes** . Eis o código que cria uma fila (se não existir) e insere a mensagem 'Olá, mundo':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Observar a mensagem seguinte

Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o método de **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem em fila de espera

Pode alterar os conteúdos de um mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte atualiza a mensagem de fila com novo conteúdo e define o tempo limite visibilidade para alargar a outra 60 segundos. Guarda o estado do trabalho associado à mensagem e dá o cliente minuto outra para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar passos de vários fluxos de trabalho em mensagens de fila de espera, sem ter de recomeçar desde o início, se um passo de processamento falhar devido a falha do software ou hardware. Normalmente, teria de manter uma contagem de repetir, assim e, se a mensagem é repetida mais do que *n* vezes, pretende eliminá-lo. Este procedimento protege contra a uma mensagem que accionadores um erro de aplicação sempre que esta é processada.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>Retirar fila a mensagem seguinte

Anular o seu código filas uma mensagem a partir de uma fila em dois passos. Quando ligar para o **GetMessage**, receberá a mensagem seguinte numa fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para qualquer outro código ler mensagens a partir desta fila. Por predefinição, esta mensagem permanece invisível para 30 segundos. Para concluir a remoção da mensagem de fila de espera, também tem de chamar **DeleteMessage**. Este processo de dois passos de remoção de uma mensagem assegura que se o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. O código chama **DeleteMessage** direita depois da mensagem foi processada.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utilizar aguardar assíncrona padrão com armazenamento fila comum APIs

Este exemplo mostra como utilizar o padrão de aguardar assíncrona com armazenamento de fila APIs comum. O exemplo chamadas a versão assíncrona de cada um dos métodos determinados, como indicado por sufixo *assíncrona* de cada método. Quando for utilizado um método de assíncrona, a assíncrona-aguardar padrão suspende execução local até que a chamada for concluída. Este comportamento permite que o tópico atual efetuar outros trabalhos, que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta geral da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão de Await assíncrona no .NET consulte [assíncrona e Await (c# e do Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Tirar partido opções adicionais para anular a colocação de mensagens

Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um limite de tempo invisibility ou mais comprida, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. Exemplo de código seguinte utiliza o método de **GetMessages** para receber mensagens de 20 numa chamada. Em seguida, processa cada mensagem utilizando um ciclo **foreach** . Também define o tempo limite invisibility para cinco minutos para cada mensagem. Tenha em atenção que inicia o 5 minutos para todas as mensagens ao mesmo tempo, após ter 5 minutos passado desde a chamada para **GetMessages**, todas as mensagens que não tenham sido eliminadas irão tornar-se visíveis novamente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens de uma fila. O método de **FetchAttributes** pede-lhe o serviço de fila de espera para obter os atributos de fila de espera, incluindo a contagem de mensagens. A propriedade **ApproximateMessageCount** devolve o último valor obtido pelo método **FetchAttributes** , sem chamar o serviço de fila de espera.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na mesma, chame o método de **Eliminar** no objeto fila de espera.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de fila de espera, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

- Ver a documentação de referência do serviço de fila de espera para obter detalhes completos sobre APIs disponíveis:
    - [Biblioteca de armazenamento do cliente para referência .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referência de REST API](http://msdn.microsoft.com/library/azure/dd179355)
- Saiba como simplificar o código que escrever para trabalhar com o armazenamento do Windows Azure utilizando o [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Ver mais guias de funcionalidade para saber mais sobre as opções adicionais para armazenar dados no Azure.
    - [Introdução ao armazenamento de tabela do Azure utilizando o .NET](storage-dotnet-how-to-use-tables.md) para armazenar dados estruturados.
    - [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
    - [Ligar à base de dados SQL utilizando o .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
