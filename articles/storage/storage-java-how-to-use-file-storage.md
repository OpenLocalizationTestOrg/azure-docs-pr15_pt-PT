<properties
    pageTitle="Como utilizar o armazenamento de ficheiros a partir do Java | Microsoft Azure"
    description="Saiba como utilizar o serviço do Azure ficheiro para carregar, transferir, lista e eliminar ficheiros. Amostras escritas em Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Como utilizar o armazenamento de ficheiros a partir do Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Descrição geral

Este guia vai aprender efetuar operações básicas sobre o serviço de armazenamento do ficheiro do Microsoft Azure. Através de exemplos escritos em Java vai aprender a criar partilhas e diretórios, carregar, lista e eliminar ficheiros. Se estiver familiarizado com o serviço de armazenamento de ficheiros do Microsoft Azure, será muito útil para ajudar a compreender as amostras percorrer os conceitos nas secções que se seguem.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação de Java

Para criar as amostras, terá do Java Development Kit (JDK) e o [[Azure SDK de armazenamento para Java]]. Devem também ter criado uma conta de armazenamento Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Configurar a aplicação para utilizar o armazenamento de ficheiros

Para utilizar o armazenamento Azure APIs, adicione a seguinte instrução para a parte superior do ficheiro Java onde pretende aceder ao serviço de armazenamento a partir de.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Uma cadeia de ligação do Azure armazenamento de configuração

Para utilizar o armazenamento de ficheiros, é necessário ligar à sua conta de armazenamento Azure. Seria o primeiro passo configurar uma cadeia de ligação que vamos utilizar para ligar à sua conta de armazenamento. Vamos definir uma variável estática para o fazer.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Substitua os valores reais para a sua conta de armazenamento your_storage_account_name e your_storage_account_key.

## <a name="connecting-to-an-azure-storage-account"></a>Ligar a uma conta de armazenamento Azure

Para ligar à sua conta de armazenamento, tem de utilizar o objeto **CloudStorageAccount** , prisma uma cadeia de ligação para o seu método de **Analisar** .

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** inicia uma InvalidKeyException, pelo que terá de colocá-la dentro de um bloco de experimentar/capturas.

## <a name="how-to-create-a-share"></a>Como: criar uma partilha

Todos os ficheiros e directórios de armazenamento de ficheiros residem num contentor denominado uma **partilha**. A sua conta de armazenamento pode possui quanto partilhas como permite a capacidade de conta. Para obter acesso a uma partilha e os seus conteúdos, é necessário utilizar um cliente de armazenamento do ficheiro.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

Utilizar o cliente de armazenamento do ficheiro, em seguida, pode obter uma referência a uma partilha.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

Para criar realmente a partilha, utilize o método de **createIfNotExists** do objeto CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

Neste momento, **partilhar** contém uma referência a uma partilha com o nome **sampleshare**.

## <a name="how-to-upload-a-file"></a>Como: carregar um ficheiro

Uma partilha de armazenamento de ficheiros do Azure contém pelo menos, um diretório de raiz onde residem os ficheiros. Nesta secção, vai aprender carregar um ficheiro a partir do armazenamento local para o diretório de raiz de uma partilha.

É o primeiro passo ao carregar um ficheiro obter uma referência ao diretório onde deve residem. Pode fazê-lo ao contactar o método de **getRootDirectoryReference** do objeto partilhar.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Agora que tem uma referência para o diretório de raiz da partilha, pode carregar um ficheiro para a mesma com o código seguinte.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Como: criar um diretório

Para organizar armazenamento colocação de ficheiros no interior do sub-directórios em vez de ser todos eles no diretório de raiz. O serviço de armazenamento de ficheiros Azure permite-lhe criar quanto directórios como permitirá que a sua conta. O código abaixo irá criar um diretório subpastas com o nome **sampledir** no diretório de raiz.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como: lista de ficheiros e directórios de uma partilha

Obter uma lista de ficheiros e directórios dentro de uma partilha facilmente é feito ao contactar o suporte **listFilesAndDirectories** uma referência de CloudFileDirectory. O método devolve uma lista de objetos de ListFileItem que pode iteramos sobre. Por exemplo, o código seguinte irá listar ficheiros e directórios dentro o diretório de raiz.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Como: transferir um ficheiro

Uma das operações mais frequentes que fará contra armazenamento de ficheiros é transferir ficheiros. No exemplo seguinte, o código de transferências SampleFile.txt e apresenta os seus conteúdos.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Como: eliminar um ficheiro

Outro ficheiro armazenamento operação comum é eliminação dos ficheiros. O código seguinte elimina num ficheiro denominado SampleFile.txt armazenados no interior de um diretório chamado **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Como: eliminar um diretório

Eliminar um diretório é uma tarefa bastante simple, apesar de ser de notar que não é possível eliminar um diretório que ainda contém ficheiros ou outros directórios.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Como: eliminar uma partilha

Eliminar uma partilha é efetuada ao chamar o método de **deleteIfExists** num objeto CloudFileShare. Eis o código de exemplo que é que.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Próximos passos

Se quiser saber mais sobre outras armazenamento Azure APIs, siga estas ligações.

- [Centro de programadores do Java](http://azure.microsoft.com/develop/java/)
- [Armazenamento Azure SDK para Java](https://github.com/azure/azure-storage-java)
- [Armazenamento Azure SDK para Android](https://github.com/azure/azure-storage-android)
- [Referência SDK do cliente de armazenamento Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Serviços de armazenamento Azure REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)
