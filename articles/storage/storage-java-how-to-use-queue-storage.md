<properties
    pageTitle="Como utilizar o armazenamento de fila de Java | Microsoft Azure"
    description="Saiba como utilizar o serviço do Azure fila de espera para criar e eliminar filas e inserir, obtenha e eliminar mensagens. Amostras escritas em Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Como utilizar o armazenamento de fila de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Este guia mostrar-lhe como efetuar cenários comuns utilizando o serviço de armazenamento do Azure fila de espera. Os exemplos são gravados no Java e utilizam o [Azure armazenamento SDK para Java][]. Os cenários abrangidos incluem **Inserir**, **observação**, **Introdução**e **Eliminar** mensagens de fila de espera, bem como **Criar** e **Eliminar** filas. Para mais informações sobre filas, consulte a secção [os passos seguintes](#Next-Steps) .

Nota: Uma SDK está disponível para programadores que estão a utilizar o armazenamento do Windows Azure em dispositivos Android. Para mais informações, consulte o [SDK de armazenamento do Azure para Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação de Java

Neste guia, irá utilizar funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação Java localmente ou no dentro de uma função de web ou trabalhador no Azure a execução de código.

Para fazê-lo, terá de instalar o Java Development Kit (JDK) e criar uma conta de armazenamento Azure na sua subscrição do Azure. Assim que tiver feito, terá de confirmar se o seu sistema de desenvolvimento cumpre os requisitos mínimos e dependências que sejam encontram listadas do repositório de [Azure armazenamento SDK para Java][] no GitHub. Se o seu sistema de cumprir essas exigências, pode seguir as instruções para transferir e instalar as bibliotecas de armazenamento do Azure para Java no sistema da partir desse repositório. Assim que tiver concluído essas tarefas, pode poderão criar uma aplicação de Java que utiliza os exemplos neste artigo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar a aplicação para aceder ao armazenamento de fila de espera

Adicione as seguintes instruções de importar para a parte superior do ficheiro Java em que pretende utilizar o armazenamento Azure APIs para aceder a filas:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>Uma cadeia de ligação do Azure armazenamento de configuração

Um cliente de armazenamento Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e as credenciais para aceder a serviços de gestão de dados. Quando a ser executado numa aplicação cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, utilizando o nome da sua conta de armazenamento e a chave primária de acesso para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Este exemplo mostra como podem declarar um campo estático para colocar em espera a cadeia de ligação:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Numa aplicação em execução no interior de uma função no Microsoft Azure, esta cadeia pode ser armazenada no ficheiro de configuração de serviço, *ServiceConfiguration.cscfg*e pode ser acedida através de uma chamada para o método de **RoleEnvironment.getConfigurationSettings** . Eis um exemplo de obter a cadeia de ligação a partir de um elemento de **definição** chamado *StorageConnectionString* no ficheiro de configuração de serviço:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Exemplos que se seguem partem do pressuposto de que utilizou um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="how-to-create-a-queue"></a>Como: criar uma fila

Um objeto de **CloudQueueClient** permite-lhe obter objetos de referência para as filas. O código seguinte cria um objeto de **CloudQueueClient** . (Nota: existem formas adicionais de criar objetos **CloudStorageAccount** ; para obter mais informações, consulte o artigo **CloudStorageAccount** na [Referência do Azure armazenamento Client SDK].)

Utilize o objeto **CloudQueueClient** para obter uma referência para a fila que pretende utilizar. Pode criar fila de espera, se não existe.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Como: adicionar uma mensagem a uma fila

Para inserir uma mensagem uma fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, ligue para o método de **addMessage** . Um **CloudQueueMessage** podem ser criados a partir de uma cadeia (em formato UTF-8) ou uma matriz de bytes. Eis o código que cria uma fila (se não existir) e insere a mensagem "Olá, mundo".

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Como: observar a mensagem seguinte

Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o suporte **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: alterar o conteúdo de uma mensagem em fila de espera

Pode alterar os conteúdos de um mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte atualiza a mensagem de fila com novo conteúdo e define o tempo limite visibilidade para alargar a outra 60 segundos. Guarda o estado do trabalho associado à mensagem e dá o cliente minuto outra para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar passos de vários fluxos de trabalho em mensagens de fila de espera, sem ter de recomeçar desde o início, se um passo de processamento falhar devido a falha do software ou hardware. Normalmente, teria de manter uma contagem de repetir, assim e, se a mensagem é repetida mais do que *n* vezes, pretende eliminá-lo. Este procedimento protege contra a uma mensagem que accionadores um erro de aplicação sempre que esta é processada.

O seguinte código exemplo procura através de fila de espera de mensagens, localiza a primeira mensagem corresponde a "Olá, mundo" para o conteúdo, em seguida, modifica o conteúdo da mensagem e sai.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Em alternativa, o código seguinte de exemplo atualiza apenas a primeira mensagem visível na fila de espera.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Como: obter o comprimento da fila

Pode obter uma estimativa do número de mensagens de uma fila. O método de **downloadAttributes** pede-lhe o serviço de fila de espera para vários valores atuais, incluindo uma contagem das quantas mensagens são numa fila. A contagem de apenas é aproximada uma vez que podem ser adicionadas ou removidas depois do serviço de fila responde ao seu pedido de mensagens. O método **getApproximateMessageCount** devolve o último valor obtido pela chamada para **downloadAttributes**, sem chamar o serviço de fila de espera.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Como: descarregado a mensagem seguinte

O código descarrega as uma mensagem a partir de uma fila em dois passos. Quando ligar para o **retrieveMessage**, receberá a mensagem seguinte numa fila. Uma mensagem devolvida por **retrieveMessage** torna-se invisível para qualquer outro código ler mensagens a partir desta fila. Por predefinição, esta mensagem permanece invisível para 30 segundos. Para concluir a remoção da mensagem de fila de espera, também tem de chamar **deleteMessage**. Este processo de dois passos de remoção de uma mensagem assegura que se o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. O código chama **deleteMessage** direita depois da mensagem foi processada.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para a opção de retirar fila mensagens

Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um limite de tempo invisibility ou mais comprida, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem.

Exemplo de código seguinte utiliza o método de **retrieveMessages** para receber mensagens de 20 numa chamada. Em seguida, processa cada mensagem utilizando um ciclo **para** . Também define o tempo limite invisibility para cinco minutos (300 segundos) para cada mensagem. Tenha em atenção que inicia de cinco minutos para todas as mensagens ao mesmo tempo, por isso, quando cinco minutos tiverem passado desde a chamada para **retrieveMessages**, todas as mensagens que não tenham sido eliminadas irão tornar-se visíveis novamente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Como: as filas de lista

Para obter uma lista das filas atuais, contacte o método de **CloudQueueClient.listQueues()** , que irá devolver uma coleção de objetos **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Como: eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na mesma, chame o método de **deleteIfExists** no objeto **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de fila de espera, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

- [Armazenamento Azure SDK para Java][]
- [Referência SDK do cliente de armazenamento Azure][]
- [Serviços de armazenamento Azure REST API][]
- [Blogue da equipa do armazenamento Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Armazenamento Azure SDK para Java]: https://github.com/azure/azure-storage-java
[Armazenamento Azure SDK para Android]: https://github.com/azure/azure-storage-android
[Referência SDK do cliente de armazenamento Azure]: http://dl.windowsazure.com/storage/javadoc/
[Serviços de armazenamento Azure REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
