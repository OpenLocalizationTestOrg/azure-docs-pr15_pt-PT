<properties
    pageTitle="Introdução ao armazenamento de Blobs do Azure (armazenamento de objeto), utilizando o .NET | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com armazenamento de Blobs do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Introdução ao armazenamento de Blobs do Azure utilizando o .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. Armazenamento de BLOBs pode armazenar qualquer tipo de texto ou dados binários, como um documento, ficheiro de multimédia ou instalador de aplicações. Armazenamento de BLOBs é também conhecido como o armazenamento do objeto.

### <a name="about-this-tutorial"></a>Informações sobre este tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns a utilizar o armazenamento de Blobs do Azure. Cenários abrangidos incluem a carregar, listar, transferir e eliminar blobs.

**Estimativa tempo a concluir:** 45 minutos

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de armazenamento Azure do cliente para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestor de configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Uma [conta de armazenamento Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais amostras

Para obter exemplos adicionais a utilizar o armazenamento de BLOBs, consulte o artigo [Introdução ao armazenamento de Blobs do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Pode transferir a aplicação de exemplo e executá-la ou procure o código no GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicione o seguinte `using` declarações na parte superior da `program.cs` ficheiro:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Criar o cliente do serviço Blob

A classe de **CloudBlobClient** permite-lhe obter contentores e blobs armazenados no armazenamento Blob. Eis uma forma de criar o cliente do serviço:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Agora, está pronto para escrever código que lê dados a partir de e escreve dados ao armazenamento Blob.

## <a name="create-a-container"></a>Criar um contentor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contentor se não existir já:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Por predefinição, o novo contentor for privado, que significa que tem de especificar a chave de acesso de armazenamento para transferir blobs neste contentor. Se pretender disponibilizar os ficheiros dentro do contentor para todos, pode definir o contentor para ser público utilizando o seguinte código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Qualquer pessoa na Internet pode ver blobs num contentor público, mas pode modificar ou elimine-as apenas se tiver a tecla de acesso adequado conta ou de uma assinatura de acesso partilhado.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Armazenamento de Blobs do Azure suporta bloco blobs e blobs de página.  Na maioria dos casos, BLOBs bloco são o tipo recomendado para utilizar.

Para carregar um ficheiro para um blob bloco, obtenha uma referência de contentor e utilizá-la para obter uma referência de Blobs do bloco. Assim que tiver uma referência de BLOBs, pode carregar qualquer sequência de dados para o mesmo ao contactar o método de **UploadFromStream** . Esta operação irá criar o blob se anteriormente não existir, ou substituí-lo, se existir.

O exemplo seguinte mostra como carregar um blob para um contentor e assume que o contentor já foi criado.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista

Para listar blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método de **ListBlobs** o contentor para obter o blobs e/ou directórios dentro da mesma. Para aceder o conjunto avançado de propriedades e métodos para um devolvida **IListBlobItem**, tem de convertê-la para um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** .  Se o tipo é desconhecido, pode utilizar uma verificação de tipo para determinar qual convertê-la para.  O código seguinte demonstra como obter e de saída URI de cada item na `photos` contentor:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Como é mostrado acima, pode dar nome blobs com informações do caminho nos seus nomes. Esta ação cria uma estrutura de directório virtual que pode organizar e percorrer tal como faria com um sistema de ficheiros tradicional. Tenha em atenção que a estrutura do directório virtual só está - são os únicos recursos disponíveis no armazenamento de BLOBs contentores e blobs. No entanto, a biblioteca do cliente armazenamento oferece um objeto de **CloudBlobDirectory** consultar um diretório virtual e simplificar o processo de trabalhar com blobs que estão organizadas desta forma.

Por exemplo, considere o seguinte conjunto de blobs do bloco num contentor denominada `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando ligar **ListBlobs** no contentor 'fotografias' (tal como no exemplo acima), é devolvida uma lista hierárquica. Contém objetos **CloudBlobDirectory** e **CloudBlockBlob** , que representa a directórios e blobs no contentor, respetivamente. O resultado tem a seguinte apresentação:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** **Verdadeiro**. Neste caso, cada blob no contentor é devolvido como um objeto de **CloudBlockBlob** . A chamada para **ListBlobs** para devolver uma simples listagem tem o seguinte aspeto:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e os resultados o seguinte aspeto:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Transferir blobs

Para transferir blobs, primeiro de obter uma referência de BLOBs e, em seguida, chamar o método de **DownloadToStream** . O exemplo seguinte utiliza o método de **DownloadToStream** para transferir o conteúdo BLOBs para um objeto de sequência, em seguida, pode manter os para um ficheiro local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Também pode utilizar o método de **DownloadToStream** para transferir o conteúdo de um blob como uma cadeia de texto.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar blobs

Para eliminar um blob, pela primeira vez obtenha uma referência de BLOBs e, em seguida, chamar o método de **Eliminar** no mesmo.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs nas páginas de forma assíncrona

Se são listar um grande número de blobs ou pretender para controlar o número de resultados a que devolver numa operação de listagem, pode listar blobs nas páginas de resultados. Este exemplo mostra como devolver resultados nas páginas de forma assíncrona, para que não está bloqueada execução enquanto a aguardar para devolver um grande conjunto de resultados.

Este exemplo mostra um blob simples listar, mas também pode executar uma lista hierárquica, ao definir a `useFlatBlobListing` parâmetro do método **ListBlobsSegmentedAsync** para `false`.

Uma vez que o método de exemplo chama um método assíncrono, tem de ser precedido com o `async` palavra-chave e tem de devolver um objeto de **tarefa** . A palavra-chave de await especificada para o método de **ListBlobsSegmentedAsync** suspende execução do método de exemplo, até conclui a tarefa de listagem.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>Escrever uma BLOBs de acréscimo

Um blob acrescentar é um novo tipo de BLOBs, introduzidos com a versão 5. x da biblioteca de cliente de armazenamento Azure para .NET. Um blob acrescentar está optimizado para operações de acréscimo, tal como registo. Como um blob bloco, um blob acrescentar é composto por blocos, mas quando adiciona um novo bloco para um blob de acréscimo, são sempre anexado para o fim do blob. Não é possível atualizar ou eliminar um bloco existente num BLOBs acrescentar. O bloco de IDs de para um BLOBs de acréscimo não são expostos como são para um BLOBs bloco.

Cada bloco de um blob acrescentar pode ser um tamanho diferente, até um máximo de 4 MB, e um blob acrescentar pode incluir um máximo de 50.000 blocos. O tamanho máximo de um blob de acréscimo, por isso, é ligeiramente mais do que 195 GB (4 MB X 50000 bloqueia).

O exemplo abaixo cria um novo blob de acréscimo e acrescenta alguns dados à mesma, simulação de uma operação de registo simples.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Consulte o artigo [Noções sobre bloco Blobs, página Blobs e acrescentar Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) para obter mais informações sobre as diferenças entre os três tipos de blobs.

## <a name="managing-security-for-blobs"></a>Gerir a segurança para blobs

Por predefinição, armazenamento do Windows Azure mantém os dados protegidos por limitar o acesso ao proprietário da conta que possua das teclas de acesso de conta. Quando precisar de partilhar blob dados na sua conta de armazenamento, é importante para fazê-lo sem comprometer a segurança dos seus teclas de acesso de conta. Para além disso, pode encriptar BLOBs dados para se certificar de que é seguro aceder sobre o fio e no armazenamento do Windows Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controlar o acesso a dados blob

Por predefinição, os dados na sua conta de armazenamento blob estão acessíveis apenas ao proprietário da conta de armazenamento. Autenticação de pedidos de armazenamento de BLOBs requer a tecla de acesso de conta por predefinição. No entanto, poderá pretender disponibilizar determinados dados BLOBs a outros utilizadores. Tem duas opções:

- **Acesso anónimo:** Pode disponibilizar um contentor ou o respetivos blobs publicamente para acesso anónimo. Para mais informações, consulte [Gerir anónimo acesso de leitura contentores e blobs](storage-manage-access-to-resources.md) .
- **Partilhado acesso assinaturas:** Pode fornecer clientes com uma assinatura de acesso partilhado (SA), que fornece um delegado acesso a um recurso na sua conta de armazenamento, com as permissões que especificar e através de um intervalo que especificar. Para mais informações, consulte [Utilizar partilhado acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>Encriptar blob dados

Armazenamento Azure suporta a encriptação blob dados no cliente e no servidor:

- **Encriptação do lado do cliente:** A biblioteca de cliente de armazenamento para .NET suporta a encriptação de dados em aplicações cliente do antes de a carregar para o armazenamento do Windows Azure e desencriptar dados durante a transferência para o cliente. A biblioteca também suporta a integração com o Azure chave cofre para gestão de chave de conta de armazenamento. Para mais informações, consulte [Encriptação do lado do cliente com o .NET para armazenamento do Windows Azure](storage-client-side-encryption.md) . Consulte também [Tutorial: encriptar e desencriptar blobs no armazenamento do Windows Azure utilizando Azure chave cofre](storage-encrypt-decrypt-blobs-key-vault.md).
- **Encriptação do lado do servidor**: armazenamento do Windows Azure agora suporta a encriptação do lado do servidor. Consulte o artigo [encriptação do serviço de armazenamento Azure para os dados em repouso (pré-visualização)](storage-service-encryption.md).

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de BLOBs, siga estas ligações para mais informações.

### <a name="microsoft-azure-storage-explorer"></a>Explorador de armazenamento do Microsoft Azure
- [Microsoft Azure armazenamento Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma gratuita, da Microsoft que lhe permite trabalhar visualmente com dados de armazenamento do Windows Azure no Windows, OS X e Linux.

### <a name="blob-storage-samples"></a>Exemplos de armazenamento de BLOBs

- [Introdução ao armazenamento de Blobs do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referência de armazenamento de BLOBs

- [Biblioteca de armazenamento do cliente para referência .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Referência de REST API](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guias conceptuais

- [Transferir dados com o utilitário da linha de comandos AzCopy](storage-use-azcopy.md)
- [Introdução ao armazenamento de ficheiros para .NET](storage-dotnet-how-to-use-files.md)
- [Como utilizar o armazenamento de Blobs do Azure com o SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
