<properties
    pageTitle="Como utilizar o armazenamento de fila de espera (C++) | Microsoft Azure"
    description="Saiba como utilizar o serviço de armazenamento de fila de espera no Azure. Amostras estão escritas em C++."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-queue-storage-from-c"></a>Como utilizar o armazenamento de fila de C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostrar-lhe como efetuar cenários comuns utilizando o serviço de armazenamento do Azure fila de espera. Os exemplos são gravados no C++ e utilizam a [Biblioteca de cliente do Azure armazenamento para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **Inserir**, **observação**, **Introdução**e **Eliminar** mensagens de fila de espera, bem como **criar e eliminar filas**.

>[AZURE.NOTE] Este guia destina-se a biblioteca de cliente de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é biblioteca de armazenamento do cliente 2.2.0, que se encontra disponível através de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação de C++  
Neste guia, irá utilizar funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação C++.

Para fazê-lo, terá de instalar a biblioteca de cliente de armazenamento do Azure para C++ e criar uma conta de armazenamento Azure na sua subscrição do Azure.

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, pode utilizar os seguintes métodos:

-   **Linux:** Siga as instruções indicadas na página de [Biblioteca de cliente do Azure armazenamento para C++ ficheiro Leia-me](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows:** No Visual Studio, clique em **Ferramentas > Gestor de pacotes NuGet > consola do Gestor de pacote**. Escreva o seguinte comando na [consola do Gestor de pacote NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar a aplicação para aceder ao armazenamento de fila de espera
Adicione que instruções para a parte superior do ficheiro C++ em que pretende utilizar o armazenamento Azure APIs para aceder a filas de incluir o seguinte:  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Azure armazenamento

Um cliente de armazenamento Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e as credenciais para aceder a serviços de gestão de dados. Quando a ser executado numa aplicação cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, utilizando o nome da sua conta de armazenamento e a tecla de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Para obter informações sobre contas de armazenamento e as teclas de acesso, consulte o artigo [Sobre contas de armazenamento do Azure](storage-create-storage-account.md). Este exemplo mostra como podem declarar um campo estático para colocar em espera a cadeia de ligação:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar a sua aplicação no seu computador local do Windows, pode utilizar o Microsoft Azure [emulador de armazenamento](storage-use-emulator.md) que é instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços de BLOBs, fila de espera e tabela disponíveis no Azure no seu computador de desenvolvimento local. O exemplo seguinte mostra como podem declarar um campo estático para colocar em espera a cadeia de ligação ao seu emulador armazenamento local:  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador armazenamento Azure, selecione o botão **Iniciar** ou prima a tecla do **Windows** . Comece a escrever **Emulador de armazenamento do Azure**e selecione **Emulador de armazenamento do Microsoft Azure** a partir da lista de aplicações.

Exemplos que se seguem partem do pressuposto de que utilizou um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="retrieve-your-connection-string"></a>Obter a cadeia de ligação
Pode utilizar a classe de **cloud_storage_account** para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método de **Analisar** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Como: criar uma fila
Um objeto de **cloud_queue_client** permite-lhe obter objetos de referência para as filas. O código seguinte cria um objeto de **cloud_queue_client** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Utilize o objeto **cloud_queue_client** para obter uma referência para a fila que pretende utilizar. Pode criar fila de espera, se não existe.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem uma fila
Para inserir uma mensagem uma fila existente, primeiro crie um novo **cloud_queue_message**. Em seguida, ligue para o método de **add_message** . Um **cloud_queue_message** podem ser criados a partir de uma cadeia ou uma matriz de **bytes** . Eis o código que cria uma fila (se não existir) e insere a mensagem 'Olá, mundo':

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Como: observar a mensagem seguinte
Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o método de **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: alterar o conteúdo de uma mensagem em fila de espera
Pode alterar os conteúdos de um mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte atualiza a mensagem de fila com novo conteúdo e define o tempo limite visibilidade para alargar a outra 60 segundos. Guarda o estado do trabalho associado à mensagem e dá o cliente minuto outra para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar passos de vários fluxos de trabalho em mensagens de fila de espera, sem ter de recomeçar desde o início, se um passo de processamento falhar devido a falha do software ou hardware. Normalmente, teria de manter uma contagem de repetir, assim e, se a mensagem é repetida mais do que n vezes, pretende eliminá-lo. Este procedimento protege contra a uma mensagem que accionadores um erro de aplicação sempre que esta é processada.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Como: fila anular a mensagem seguinte
Anular o seu código filas uma mensagem a partir de uma fila em dois passos. Quando ligar para o **get_message**, receberá a mensagem seguinte numa fila. Uma mensagem devolvida por **get_message** torna-se invisível para qualquer outro código ler mensagens a partir desta fila. Para concluir a remoção da mensagem de fila de espera, também tem de chamar **delete_message**. Este processo de dois passos de remoção de uma mensagem assegura que se o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. O código chama **delete_message** direita depois da mensagem foi processada.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Como: tirar partido opções adicionais para anular a colocação de mensagens
Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um limite de tempo invisibility ou mais comprida, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. Exemplo de código seguinte utiliza o método de **get_messages** para receber mensagens de 20 numa chamada. Em seguida, processa cada mensagem utilizando um ciclo **para** . Também define o tempo limite invisibility para cinco minutos para cada mensagem. Tenha em atenção que inicia o 5 minutos para todas as mensagens ao mesmo tempo, após ter 5 minutos passado desde a chamada para **get_messages**, todas as mensagens que não tenham sido eliminadas irão tornar-se visíveis novamente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Como: obter o comprimento da fila
Pode obter uma estimativa do número de mensagens de uma fila. O método de **download_attributes** pede-lhe o serviço de fila de espera para obter os atributos de fila de espera, incluindo a contagem de mensagens. O método **approximate_message_count** obtém o número aproximado de mensagens na fila de espera.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Como: eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas na mesma, chame o método de **delete_queue_if_exists** no objeto fila de espera.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de fila de espera, siga estas ligações para mais informações sobre o armazenamento do Windows Azure.

-   [Como utilizar o armazenamento de Blobs do C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Como utilizar o armazenamento de tabela a partir do C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Recursos da lista de armazenamento Azure no C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de armazenamento do cliente para referência C++](http://azure.github.io/azure-storage-cpp)
-   [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)

