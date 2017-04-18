<properties
    pageTitle="Como utilizar o armazenamento de BLOBs (armazenamento de objeto) a partir do C++ | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com armazenamento de Blobs do Azure (armazenamento de objeto)."
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

# <a name="how-to-use-blob-storage-from-c"></a>Como utilizar o armazenamento de Blobs do C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. Armazenamento de BLOBs pode armazenar qualquer tipo de texto ou dados binários, como um documento, ficheiro de multimédia ou instalador de aplicações. Armazenamento de BLOBs é também conhecido como o armazenamento do objeto.

Este guia vai demonstrar como efetuar cenários comuns utilizando o serviço de armazenamento de Blobs do Azure. Os exemplos são gravados no C++ e utilizam a [Biblioteca de cliente do Azure armazenamento para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **a carregar**, **Listar**, **Transferir**e **Eliminar** blobs.  

>[AZURE.NOTE] Este guia destina-se a biblioteca de cliente de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é biblioteca de armazenamento do cliente 2.2.0, que se encontra disponível através de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação de C++
Neste guia, irá utilizar funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação C++.  

Para fazê-lo, terá de instalar a biblioteca de cliente de armazenamento do Azure para C++ e criar uma conta de armazenamento Azure na sua subscrição do Azure.   

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, pode utilizar os seguintes métodos:

-   **Linux:** Siga as instruções indicadas na página de [Biblioteca de cliente do Azure armazenamento para C++ ficheiro Leia-me](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** No Visual Studio, clique em **Ferramentas > Gestor de pacotes NuGet > consola do Gestor de pacote**. Escreva o seguinte comando na [consola do Gestor de pacote NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar a aplicação para aceder ao armazenamento Blob do  
Adicione que instruções para a parte superior do ficheiro em que pretende utilizar o Azure APIs de armazenamento para aceder a blobs C++ de incluir o seguinte:  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>Uma cadeia de ligação do Azure armazenamento de configuração
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

Em seguida, obter uma referência a uma classe **cloud_blob_client** como permite-lhe obter os objetos que representam contentores e blobs armazenados o serviço de armazenamento de Blobs. O código seguinte cria um objeto de **cloud_blob_client** utilizando o objeto de conta de armazenamento que podemos obtidos acima:  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>Como: criar um contentor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contentor se não existir já:  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

Por predefinição, o novo contentor for privado e tem de especificar a chave de acesso de armazenamento para transferir blobs neste contentor. Se pretender disponibilizar os ficheiros (blobs) dentro do contentor para todos, pode definir o contentor para ser público utilizando o seguinte código:  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

Qualquer pessoa na Internet pode ver blobs num contentor público, mas pode modificar ou elimine-as apenas se tiver a tecla de acesso adequado.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Como: carregar um blob para um contentor
Armazenamento de Blobs do Azure suporta bloco blobs e blobs de página. Na maioria dos casos, blob bloco é o tipo recomendado para utilizar.  

Para carregar um ficheiro para um blob bloco, obtenha uma referência de contentor e utilizá-la para obter uma referência de Blobs do bloco. Assim que tiver uma referência de BLOBs, pode carregar qualquer sequência de dados para o mesmo ao contactar o método de **upload_from_stream** . Esta operação irá criar o blob se anteriormente não existir, ou substituí-lo, se existir. O exemplo seguinte mostra como carregar um blob para um contentor e assume que o contentor já foi criado.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

Em alternativa, pode utilizar o método de **upload_from_file** para carregar um ficheiro para um blob bloco.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: blobs num contentor de lista
Para listar blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método de **list_blobs** o contentor para obter o blobs e/ou directórios dentro da mesma. Para aceder o conjunto avançado de propriedades e métodos para um devolvida **list_blob_item**, tem de chamar o método de **list_blob_item.as_blob** para obter um objeto de **cloud_blob** , ou o método de **list_blob.as_directory** para obter um objeto de cloud_blob_directory. O código seguinte demonstra como obter e de saída URI de cada item no contentor de **contentor meus exemplo** :

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

Para obter mais detalhes sobre como listar operações, consulte o artigo [Lista Azure recursos de armazenamento no C++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: Transferir blobs
Para transferir blobs, primeiro de obter uma referência de BLOBs e, em seguida, chamar o método de **download_to_stream** . O exemplo seguinte utiliza o método de **download_to_stream** para transferir o conteúdo de BLOBs para um objeto de sequência, em seguida, pode manter os para um ficheiro local.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

Em alternativa, pode utilizar o método de **download_to_file** para transferir o conteúdo de um blob para um ficheiro.
Além disso, também pode utilizar o método de **download_text** para transferir o conteúdo de um blob como uma cadeia de texto.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>Como: eliminar blobs
Para eliminar um blob, pela primeira vez obtenha uma referência de BLOBs e, em seguida, chamar o método de **delete_blob** no mesmo.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>Próximos passos
Agora que aprendeu os princípios básicos de armazenamento de BLOBs, siga estas ligações para mais informações sobre o armazenamento do Windows Azure.  

-   [Como utilizar o armazenamento de fila de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Como utilizar o armazenamento de tabela a partir do C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Recursos da lista de armazenamento Azure no C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de armazenamento do cliente para referência C++](http://azure.github.io/azure-storage-cpp)
-   [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Transferir dados com o utilitário da linha de comandos AzCopy](storage-use-azcopy.md)
