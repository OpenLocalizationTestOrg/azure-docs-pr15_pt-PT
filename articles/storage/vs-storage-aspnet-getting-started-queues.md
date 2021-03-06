<properties
    pageTitle="Introdução ao armazenamento de fila de espera e Visual Studio ligado serviços (ASP.NET) | Microsoft Azure"
    description="Como começar a utilizar o armazenamento de Azure filas num projeto de ASP.NET no Visual Studio após ligar a uma conta de armazenamento utilizando o Visual Studio ligado serviços"
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
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Introdução ao Azure fila armazenamento e Visual Studio ligado serviços

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Este artigo descreve como começar a utilizar o armazenamento de Azure filas no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento Azure num projeto ASP.NET utilizando a caixa de diálogo do Visual Studio **Adicionar serviços ligados** .

Vamos mostrar-lhe como criar e aceder a uma fila Azure na sua conta de armazenamento. Vamos também mostrar-lhe como efetuar operações em fila básicas, tais como adicionar, modificar, de leitura e remover mensagens em fila. Os exemplos são gravados no código c# e utilizam a [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para mais informações sobre o ASP.NET, consulte o artigo [ASP.NET](http://www.asp.net).

Armazenamento de fila Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas a partir de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, por excesso para o limite de capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Filas de acesso no código

Para aceder a filas ASP.NET projetos, necessita de incluir os seguintes itens para qualquer ficheiro de origem c# que aceder ao armazenamento de Azure filas.

1. Certifique-se que as declarações de espaço de nomes na parte superior do ficheiro c# incluem estas instruções **utilizar** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obter um objeto de **CloudStorageAccount** que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obter um objeto de **CloudQueueClient** para os objetos de fila de espera na sua conta de armazenamento de referência.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obter um objeto de **CloudQueue** para fazer referência a uma fila específica.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Nota** Utilize todo o código acima à frente o código os exemplos seguintes.

## <a name="create-a-queue-in-code"></a>Criar uma fila no código

Para criar uma fila Azure no código, basta adicione uma chamada para **CreateIfNotExists** o código acima.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Para inserir uma mensagem uma fila existente, criar um novo objeto **CloudQueueMessage** , em seguida, chamar o método de **AddMessage** .

Um objeto de **CloudQueueMessage** pode ser criado a partir de uma cadeia (em formato UTF-8) ou uma matriz de bytes.

Eis um exemplo que insere a mensagem 'Olá, mundo'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem de uma fila

Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o método de PeekMessage().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem de uma fila

Pode remover o seu código (retirar fila de espera) uma mensagem a partir de uma fila em dois passos.
1. Chamar GetMessage() para obter a mensagem seguinte numa fila. Uma mensagem devolvida por GetMessage() torna-se invisível para qualquer outro código ler mensagens a partir desta fila. Por predefinição, esta mensagem permanece invisível para 30 segundos.
2.  Para concluir a remoção da mensagem de fila de espera, contacte o **DeleteMessage**.

Este processo de dois passos de remoção de uma mensagem assegura que se o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. O código seguinte chamadas **DeleteMessage** direita depois da mensagem foi processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Utilize as opções adicionais para anular a colocação de mensagens

Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um limite de tempo invisibility ou mais comprida, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. Exemplo de código seguinte utiliza o método de **GetMessages** para receber mensagens de 20 numa chamada. Em seguida, processa cada mensagem utilizando um ciclo **foreach** . Também define o tempo limite invisibility para cinco minutos para cada mensagem. Tenha em atenção que inicia o 5 minutos para todas as mensagens ao mesmo tempo, após ter 5 minutos passado desde a chamada para **GetMessages**, todas as mensagens que não tenham sido eliminadas irão tornar-se visíveis novamente.

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

Pode obter uma estimativa do número de mensagens de uma fila. O método de **FetchAttributes** pergunta queueservice para obter os atributos de fila de espera, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** devolve o último valor obtido pelo método **FetchAttributes** , sem chamar a queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Utilizar aguardar assíncrona padrão com queueAPIs comuns

Este exemplo mostra como utilizar o padrão de aguardar assíncrona com queueAPIs comuns. O exemplo liga para a versão de assíncrona de cada um dos métodos determinados, este pode ser visto pela fix pós assíncrona de cada método. Quando for utilizado um método de assíncrona a assíncrona-aguardar padrão suspende execução local até que a chamada for concluída. Este comportamento permite que o tópico atual efetuar outros trabalhos que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta geral da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão de Await assíncrona no .NET consulte [assíncrona e Await (c# e do Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na mesma, chame o método de **Eliminar** no objeto fila de espera.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]