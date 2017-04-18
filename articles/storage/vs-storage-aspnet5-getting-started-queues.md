<properties
    pageTitle="Introdução ao armazenamento de fila de espera e Visual Studio ligado serviços (ASP.NET 5) | Microsoft Azure"
    description="Como começar a utilizar o armazenamento de fila Azure num projeto de 5 de ASP.NET no Visual Studio"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>Introdução ao armazenamento de fila de espera e Visual Studio ligado serviços (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

##<a name="overview"></a>Descrição geral

Este artigo descreve como começar a utilizar o armazenamento de fila Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento Azure num projeto de 5 de ASP.NET utilizando a caixa de diálogo do Visual Studio **Adicionar serviços ligados** . A operação de **Adicionar serviços ligados** instala os pacotes de NuGet adequados para aceder ao armazenamento Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração de projeto.

Armazenamento de filas Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas a partir de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 kilobytes (KB) e uma fila pode conter milhões de mensagens, por excesso para o limite de capacidade total de uma conta de armazenamento.

Para começar, tem primeiro criar uma fila Azure na sua conta de armazenamento. Vamos mostrar-lhe como criar uma fila no código. Vamos também mostrar-lhe como efetuar operações em fila básicas, tais como adicionar, modificar, de leitura e remover mensagens de fila de espera. Os exemplos são gravados no C\# código e utilizar a biblioteca de cliente de armazenamento do Azure para .NET. Para mais informações sobre o ASP.NET, consulte o artigo [ASP.NET](http://www.asp.net).

**Nota:** Algumas das APIs que efetuar chamadas para o armazenamento Azure 5, ASP.NET são assíncronas. Para obter mais informações, consulte a [assíncrono de programação com assíncrona e Await](http://msdn.microsoft.com/library/hh191443.aspx) . O código abaixo assume métodos de programação assíncrona estão a ser utilizados.

- Consulte o artigo [Introdução ao armazenamento de fila Azure utilizando o .NET](storage-dotnet-how-to-use-queues.md) para obter mais informações sobre como através de programação manipular filas.
- Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Windows Azure.
- Consulte a [documentação de serviços em nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre serviços em nuvem Azure.
- Consulte o artigo [ASP.NET](http://www.asp.net) para obter mais informações sobre a programação de aplicações do ASP.NET.





##<a name="access-queues-in-code"></a>Filas de acesso no código

Para aceder a filas em projetos ASP.NET 5, é necessário incluir os seguintes itens para qualquer c# ficheiro de origem que acede armazenamento de filas Azure.

1. Certifique-se que as declarações de espaço de nomes na parte superior do ficheiro c# incluem estas instruções **utilizar** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obter um objeto de **CloudStorageAccount** que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obter um objeto de **CloudQueueClient** para os objetos de fila de espera na sua conta de armazenamento de referência.  

        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obter um objeto de **CloudQueue** para fazer referência a uma fila específica.

        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Nota:** Utilize todo o código acima à frente o código os exemplos seguintes.

###<a name="create-a-queue-in-code"></a>Criar uma fila no código

Para criar a fila Azure no código, basta adicione uma chamada para **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

##<a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Para inserir uma mensagem uma fila existente, criar um novo objeto **CloudQueueMessage** , em seguida, chamar o método de **AddMessageAsync** .

Um objeto de **CloudQueueMessage** pode ser criado a partir de uma cadeia (em formato UTF-8) ou uma matriz de bytes.

Eis um exemplo que insere a mensagem 'Olá, mundo'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

##<a name="read-a-message-in-a-queue"></a>Ler uma mensagem de uma fila

Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o método de **PeekMessageAsync** .

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


##<a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem de uma fila

Pode remover o seu código (descarregado) uma mensagem a partir de uma fila em dois passos.
1. Chamar **GetMessageAsync** para obter a mensagem seguinte numa fila. Uma mensagem devolvida por **GetMessageAsync** torna-se invisível para qualquer outro código ler mensagens a partir desta fila. Por predefinição, esta mensagem permanece invisível para 30 segundos.
2.  Para concluir a remoção da mensagem de fila de espera, contacte o **DeleteMessageAsync**.

Este processo de dois passos de remoção de uma mensagem assegura que se o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. O código seguinte chamadas **DeleteMessageAsync** direita depois da mensagem foi processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Tirar partido opções adicionais para a opção de retirar fila mensagens

Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um limite de tempo invisibility ou mais comprida, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. Exemplo de código seguinte utiliza o método de **GetMessages** para receber mensagens de 20 numa chamada. Em seguida, processa cada mensagem utilizando um ciclo **foreach** . Também define o tempo limite invisibility para 5 minutos para cada mensagem. Tenha em atenção que o início de 5 minutos para todas as mensagens ao mesmo tempo, passados após ter 5 minutos depois da chamada para **GetMessages**, todas as mensagens às quais não tenham sido eliminadas ficam visíveis novamente.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens de uma fila. O método de **FetchAttributes** pede-lhe o serviço de fila de espera para obter os atributos de fila de espera, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** devolve o último valor obtido pelo método **FetchAttributes** , sem chamar o serviço de fila de espera.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizar o padrão de aguardar assíncrona com fila comuns APIs

Este exemplo mostra como utilizar o padrão de aguardar assíncrona com fila comuns APIs. O exemplo chamadas a versão de assíncrona de cada um dos métodos determinados. Isto pode ser visto pela fix pós assíncrona de cada método. Quando for utilizado um método de assíncrona, o padrão de aguardar assíncrona suspende execução local até que a chamada seja concluída. Este comportamento permite tópico atual de fazer outros trabalhos que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão de Await assíncrona no .NET, consulte o artigo [assíncrona e Await (c# e do Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na mesma, chame o método de **Eliminar** no objeto fila de espera.

    // Delete the queue.
    messageQueue.Delete();


##<a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
