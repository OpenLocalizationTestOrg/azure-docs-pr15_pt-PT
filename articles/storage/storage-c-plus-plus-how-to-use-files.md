<properties
    pageTitle="Como utilizar o armazenamento de ficheiros a partir do C++ | Microsoft Azure"
    description="Armazene os dados do ficheiro na nuvem com armazenamento de ficheiros do Azure."
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>Como utilizar o armazenamento de ficheiros a partir do C++

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>Informações sobre este tutorial

Neste tutorial, irá Saiba como efetuar operações básicas sobre o serviço de armazenamento do ficheiro do Microsoft Azure. Através de exemplos de escrita em C++, vai aprender a criar partilhas e diretórios, carregar, lista e eliminar ficheiros. Se estiver familiarizado com o serviço de armazenamento de ficheiros do Microsoft Azure, será muito útil para ajudar a compreender as amostras percorrer os conceitos nas secções que se seguem.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação de C++

Para criar as amostras, terá de instalar a biblioteca de cliente de armazenamento do Azure 2.4.0 para C++. Devem também ter criado uma conta de armazenamento Azure.

Para instalar o cliente de armazenamento do Azure 2.4.0 para C++, pode utilizar um dos seguintes métodos:

-   **Linux:** Siga as instruções indicadas na página de [Biblioteca de cliente do Azure armazenamento para C++ ficheiro Leia-me](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .

-   **Windows:** No Visual Studio, clique em **ferramentas &gt; Gestor de pacotes NuGet &gt; consola do Gestor de pacote**. Escreva o seguinte comando na [consola do Gestor de pacote NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.

    Wastorage do pacote de instalação

## <a name="set-up-your-application-to-use-file-storage"></a>Configurar a aplicação para utilizar o armazenamento de ficheiros

Adicione que instruções para a parte superior do ficheiro C++ em que pretende utilizar o Azure APIs de armazenamento para aceder a ficheiros de incluir o seguinte procedimento:

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Azure armazenamento

Para utilizar o armazenamento de ficheiros, é necessário ligar à sua conta de armazenamento Azure. Seria o primeiro passo configurar uma cadeia de ligação que vamos utilizar para ligar à sua conta de armazenamento. Vamos definir uma variável estática para o fazer.

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>Ligar a uma conta de armazenamento Azure

Pode utilizar a classe de **cloud_storage_account** para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método de **Analisar** .

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>Como: criar uma partilha

Todos os ficheiros e directórios de armazenamento de ficheiros residem num contentor denominado uma **partilha**. A sua conta de armazenamento pode possui partilhas tantas como permite a capacidade de conta. Para obter acesso a uma partilha e os seus conteúdos, é necessário utilizar um cliente de armazenamento do ficheiro.

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

Utilizar o cliente de armazenamento do ficheiro, em seguida, pode obter uma referência a uma partilha.

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

Para criar a partilha, utilize o método de **create_if_not_exists** do objeto **cloud_file_share** .

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

Neste momento, **partilhar** contém uma referência a uma partilha de **partilhar meus exemplo**com o nome.

## <a name="how-to-upload-a-file"></a>Como: carregar um ficheiro

No mínimo, uma partilha de armazenamento de ficheiros do Azure contém um diretório de raiz onde residem os ficheiros. Nesta secção, vai aprender carregar um ficheiro a partir do armazenamento local para o diretório de raiz de uma partilha.

É o primeiro passo ao carregar um ficheiro obter uma referência ao diretório onde deve residem. Pode fazê-lo ao contactar o método de **get_root_directory_reference** do objeto partilhar.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

Agora que tem uma referência para o diretório de raiz da partilha, pode carregar um ficheiro para o mesmo. Este exemplo os carregamentos pendentes a partir de um ficheiro, a partir do texto e partir de uma sequência.

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>Como: criar um diretório

Para organizar armazenamento colocação de ficheiros no interior seus subdiretórios em vez de ser todos eles no diretório de raiz. O serviço de armazenamento de ficheiros Azure permite-lhe criar quanto directórios como permitirá que a sua conta. O código abaixo irá criar num diretório chamado **meu exemplo directório** sob o diretório de raiz, bem como um subdiretório denominado **subdirectório meus exemplo**.
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como: lista de ficheiros e directórios de uma partilha

Obter uma lista de ficheiros e directórios dentro de uma partilha facilmente é feito ao contactar o suporte **list_files_and_directories** uma referência de **cloud_file_directory** . Para aceder o conjunto avançado de propriedades e métodos para um devolvida **list_file_and_directory_item**, tem de chamar o método de **list_file_and_directory_item.as_file** para obter um objeto de **cloud_file** , ou o método de **list_file_and_directory_item.as_directory** para obter um objeto de **cloud_file_directory** .

O código seguinte demonstra como obter e o URI de cada item no diretório de raiz da partilha de saída.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>Como: transferir um ficheiro

Para transferir ficheiros, primeiro de obter uma referência de ficheiro e, em seguida, chamar o método de **download_to_stream** para transferir o conteúdo do ficheiro para um objeto da cadeia que, em seguida, pode manter os para um ficheiro local. Em alternativa, pode utilizar o método de **download_to_file** para transferir o conteúdo de um ficheiro para um ficheiro local. Pode utilizar o método de **download_text** para transferir o conteúdo de um ficheiro como uma cadeia de texto.

O exemplo seguinte utiliza os métodos de **download_to_stream** e **download_text** para demonstrar transferir os ficheiros que foram criados numa secções anteriores.

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>Como: eliminar um ficheiro

Outro ficheiro armazenamento operação comum é eliminação dos ficheiros. O código seguinte elimina num ficheiro denominado meus-exemplo-ficheiro-3 armazenados no diretório de raiz.

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>Como: eliminar um diretório

Eliminar um diretório é uma tarefa simple, apesar de ser de notar que não é possível eliminar um diretório que ainda contém ficheiros ou outros directórios.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>Como: eliminar uma partilha

Eliminar uma partilha é feito ao contactar o método de **delete_if_exists** num objeto cloud_file_share. Eis o código de exemplo que é que.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>Definir o tamanho máximo de uma partilha de ficheiros

Pode definir a quota (ou o tamanho máximo) para uma partilha de ficheiros, gigabytes. Também pode verificar para ver a quantidade de dados está atualmente armazenado na partilha.

Ao definir a quota de uma partilha, pode limitar o tamanho total dos ficheiros armazenados na partilha. Se o tamanho total dos ficheiros na partilha exceder a quota de definir na partilha, em seguida, clientes será possível aumentar o tamanho de ficheiros existentes ou criar novos ficheiros, a menos que os ficheiros estão vazios.

O exemplo abaixo mostra como verificar a utilização de atual de uma partilha e como configurar a quota de partilhar.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso partilhado para um ficheiro ou partilha de ficheiros

Pode gerar uma assinatura de acesso partilhado (SA) para uma partilha de ficheiros ou para um ficheiro individual. Também pode criar uma política de acesso partilhado numa partilha de ficheiros para gerir o acesso partilhado assinaturas. Criar uma política de acesso partilhado é recomendado, à medida que fornece um meio de revogar as associações de segurança, se deve ser comprometida-lo.

O exemplo seguinte cria uma política de acesso partilhado numa partilha e, em seguida, utiliza essa política para fornecer as restrições de uma SA num ficheiro na partilha.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

Para mais informações sobre como criar e utilizar assinaturas de acesso partilhado, consulte o artigo [Utilizar partilhada acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o armazenamento do Windows Azure, explore estes recursos:

-   [Biblioteca de armazenamento do cliente para C++](https://github.com/Azure/azure-storage-cpp)

-   [Explorador de armazenamento Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
