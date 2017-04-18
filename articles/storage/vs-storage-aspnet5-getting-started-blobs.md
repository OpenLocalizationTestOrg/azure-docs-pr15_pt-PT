<properties
    pageTitle="Introdução ao blob armazenamento e Visual Studio ligado serviços (ASP.NET 5) | Microsoft Azure"
    description="Como começar a utilizar o armazenamento de Blobs do Azure num projeto do Visual Studio ASP.NET 5 depois de ter criado uma conta de armazenamento utilizando o Visual Studio ligado serviços"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Introdução ao Azure Blob armazenamento e Visual Studio ligado serviços (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Descrição geral

Este artigo descreve como começar a utilizar o armazenamento de Blobs do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento Azure num projeto de 5 de ASP.NET utilizando a caixa de diálogo do Visual Studio adicionar serviços ligados.

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Um único blob pode ser de qualquer tamanho. BLOBs podem ser coisas como imagens, ficheiros de áudio e vídeos, dados não processados e ficheiros de documentos. Este artigo descreve como começar com o armazenamento de BLOBs depois de criar uma conta de armazenamento Azure utilizando a caixa de diálogo do Visual Studio **Adicionar serviços ligados** num projeto ASP.NET 5.

Tal como ficheiros live em pastas, blobs armazenamento encontram-se na contentores. Depois de ter criado um armazenamento, crie um ou mais contentores no armazenamento. Por exemplo, num armazenamento denominado "Álbum de recortes", pode criar contentores no armazenamento denominado "images" para armazenar imagens e outra chamada "áudio" para armazenar ficheiros de áudio. Depois de criar os contentores, pode carregar ficheiros individuais blob às mesmas. Consulte o artigo [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md) para obter mais informações sobre como através de programação manipular blobs.

##<a name="access-blob-containers-in-code"></a>Contentores de Blobs do Access no código

Para aceder a blobs ASP.NET 5 projetos através de programação, é necessário adicionar os itens seguintes, se já estiverem não apresentar.

1. Adicione as seguintes declarações de espaço de nomes de código para a parte superior de qualquer ficheiro c# na qual pretende aceder programaticamente armazenamento Azure.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. Obter um objeto de **CloudStorageAccount** que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Nota:** Utilize todo o código acima à frente o código nas secções seguintes.


3. Utilize um objecto **CloudBlobClient** para obter uma referência de **CloudBlobContainer** a um contentor existente na sua conta de armazenamento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Criar um contentor no código

Também pode utilizar o **CloudBlobClient** para criar um contentor na sua conta de armazenamento. Tudo o que precisa de fazer consiste em Adicionar uma chamada para **CreateIfNotExistsAsync** tal como o seguinte código:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Nota:** As APIs que efectuem chamadas para o armazenamento Azure 5, ASP.NET são assíncronas. Para obter mais informações, consulte a [assíncrono de programação com assíncrona e Await](http://msdn.microsoft.com/library/hh191443.aspx) . O código abaixo assume métodos de programação assíncrona estão a ser utilizados.

Para disponibilizar para todos os ficheiros dentro do contentor, pode definir o contentor para ser público utilizando o código seguinte.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Para carregar um ficheiro blob para um contentor, obtenha uma referência de contentor e utilizá-la para obter uma referência de Blobs. Depois de ter uma referência de BLOBs, pode carregar qualquer sequência de dados para o mesmo ao contactar o método de **UploadFromStreamAsync** . Esta operação cria o blob se ainda não estiver lá, ou substitui-lo, se existir. O exemplo seguinte mostra como carregar um blob para um contentor e assume que o contentor já foi criado.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista
Para listar blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode ligar o método de **ListBlobsSegmentedAsync** o contentor para obter o blobs e/ou directórios dentro da mesma. Para aceder o conjunto avançado de propriedades e métodos para um devolvida **IListBlobItem**, tem de convertê-la para um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Se não souber qual o tipo de BLOBs, pode utilizar uma verificação de tipo para determinar qual convertê-la para. O código seguinte demonstra como obter e o URI de cada item num contentor de saída.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Existem outras formas para listar o conteúdo de um contentor de Blobs. Para obter mais informações, consulte a [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) .

##<a name="download-a-blob"></a>Transferir um blob
Para transferir um blob, pela primeira vez obter uma referência para o blob e, em seguida, chamar o método de **DownloadToStreamAsync** . O exemplo seguinte utiliza o método de **DownloadToStreamAsync** para transferir o conteúdo de BLOBs para um objeto de sequência, em seguida, pode guardar como um ficheiro local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Existem outras formas de guardar blobs como ficheiros. Para obter mais informações, consulte a [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) .

##<a name="delete-a-blob"></a>Eliminar um blob
Para eliminar um blob, pela primeira vez obter uma referência para o blob e, em seguida, chamar o método de **DeleteAsync** no mesmo.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
