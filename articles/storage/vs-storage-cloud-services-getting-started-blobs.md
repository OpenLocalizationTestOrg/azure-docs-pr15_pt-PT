<properties
    pageTitle="Introdução ao blob armazenamento e Visual Studio ligado serviços (serviços na nuvem) | Microsoft Azure"
    description="Como começar a utilizar o armazenamento de Blobs do Azure num projeto de serviço de nuvem no Visual Studio após ligar a uma conta de armazenamento utilizando o Visual Studio ligado serviços"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de Blobs do Azure e o Visual Studio ligado serviços (cloud services projetos)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Este artigo descreve como começar com o armazenamento de Blobs do Azure depois de criado ou referenciada uma conta de armazenamento do Windows Azure utilizando a caixa de diálogo do Visual Studio **Adicionar serviços ligados** num projeto do Visual Studio cloud services. Vamos mostrar-lhe como aceder e criar blob contentores e como executar tarefas comuns, como carregar, lista e transferir blobs. Os exemplos estão escritos em C\# e utilizar a [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Um único blob pode ser de qualquer tamanho. BLOBs podem ser coisas como imagens, ficheiros de áudio e vídeos, dados não processados e ficheiros de documentos.

Tal como ficheiros live em pastas, blobs armazenamento encontram-se na contentores. Depois de ter criado um armazenamento, crie um ou mais contentores no armazenamento. Por exemplo, num armazenamento denominado "Álbum de recortes", pode criar contentores no armazenamento denominado "images" para armazenar imagens e outra chamada "áudio" para armazenar ficheiros de áudio. Depois de criar os contentores, pode carregar ficheiros individuais blob às mesmas.

- Para mais informações sobre como através de programação manipular blobs, consulte o artigo [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md).
- Para obter informações gerais sobre o armazenamento do Windows Azure, consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/).
- Para obter informações gerais sobre Azure serviços em nuvem, consulte a [documentação de serviços em nuvem](https://azure.microsoft.com/documentation/services/cloud-services/).
- Para mais informações sobre a programação de aplicações do ASP.NET, consulte o artigo [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Contentores de Blobs do Access no código

Para aceder a através de programação blobs em projetos de serviço de nuvem, tem de adicionar os itens seguintes, se já estiverem não apresentar.

1. Adicione as seguintes declarações de espaço de nomes de código para a parte superior de qualquer ficheiro c# na qual pretende aceder programaticamente armazenamento do Windows Azure.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obter um objeto de **CloudStorageAccount** que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obter um objeto de **CloudBlobClient** para fazer referência a um contentor existente na sua conta de armazenamento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Obter um objeto de **CloudBlobContainer** para fazer referência a um contentor blob específico.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Utilize a totalidade do código mostrado no procedimento anterior à frente do código mostrado nas secções seguintes.

## <a name="create-a-container-in-code"></a>Criar um contentor no código

> [AZURE.NOTE] Algumas APIs que efectuem chamadas de saída ao armazenamento do Azure no ASP.NET são assíncronas. Para obter mais informações, consulte a [assíncrono de programação com assíncrona e Await](http://msdn.microsoft.com/library/hh191443.aspx) . O código no seguinte exemplo assume que está a utilizar assíncrona métodos de programação.

Para criar um contentor na sua conta de armazenamento, tudo o que precisa de fazer é adicionar uma chamada para **CreateIfNotExistsAsync** tal como o seguinte código:

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Para disponibilizar para todos os ficheiros dentro do contentor, pode definir o contentor para ser público utilizando o código seguinte.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Qualquer pessoa na Internet pode ver blobs num contentor público, mas pode modificar ou elimine-as apenas se tiver a tecla de acesso adequado.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Armazenamento Azure suporta bloco blobs e blobs de página. Na maioria dos casos, blob bloco é o tipo recomendado para utilizar.

Para carregar um ficheiro para um blob bloco, obtenha uma referência de contentor e utilizá-la para obter uma referência de Blobs do bloco. Assim que tiver uma referência de BLOBs, pode carregar qualquer sequência de dados para o mesmo ao contactar o método de **UploadFromStream** . Esta operação cria o blob se este não existia anteriormente ou substitui-lo, se existir. O exemplo seguinte mostra como carregar um blob para um contentor e assume que o contentor já foi criado.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista

Para listar blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método de **ListBlobs** o contentor para obter o blobs e/ou directórios dentro da mesma. Para aceder o conjunto avançado de propriedades e métodos para um devolvida **IListBlobItem**, tem de convertê-la para um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Se o tipo é desconhecido, pode utilizar uma verificação de tipo para determinar qual convertê-la para. O código seguinte demonstra como obter e o URI de cada item no contentor de **fotografias** de saída:

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

Como é mostrado no exemplo anterior código, o serviço de BLOBs tem o conceito de directórios dentro de contentores, também. Isto é, de modo que pode organizar os seus blobs numa estrutura mais pasta semelhante. Por exemplo, considere o seguinte conjunto de blobs do bloco num contentor de **fotografias**com o nome:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Ao ligar para **ListBlobs** no contentor (tal como no exemplo anterior), a coleção de devolvido contém objetos de **CloudBlobDirectory** e **CloudBlockBlob** que representa a directórios e blobs contidos no nível superior. Eis o resultado:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** **Verdadeiro**. Isto resulta em cada blob a ser devolvido como um **CloudBlockBlob**, independentemente do diretório. Eis a chamada para **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e aqui estão os resultados:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para mais informações, consulte o artigo [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Transferir blobs

Para transferir blobs, primeiro de obter uma referência de BLOBs e, em seguida, chamar o método de **DownloadToStream** . O exemplo seguinte utiliza o método de **DownloadToStream** para transferir o conteúdo de BLOBs para um objeto de sequência, em seguida, pode manter os para um ficheiro local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Também pode utilizar o método de **DownloadToStream** para transferir o conteúdo de um blob como uma cadeia de texto.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar blobs

Para eliminar um blob, obtenha primeiro uma referência de BLOBs e, em seguida, chamar o método de **Eliminar** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs nas páginas de forma assíncrona

Se são listar um grande número de blobs ou pretender para controlar o número de resultados a que devolver numa operação de listagem, pode listar blobs nas páginas de resultados. Este exemplo mostra como devolver resultados nas páginas de forma assíncrona, para que não está bloqueada execução enquanto a aguardar para devolver um grande conjunto de resultados.

Este exemplo mostra um blob simples listar, mas também pode executar uma lista hierárquica, ao definir o parâmetro **useFlatBlobListing** do método **ListBlobsSegmentedAsync** **Falso**.

Uma vez que o método de exemplo chama um método assíncrono, tem de ser precedido com a palavra-chave **assíncrona** e tem de devolver um objeto de **tarefa** . A palavra-chave de await especificada para o método de **ListBlobsSegmentedAsync** suspende execução do método de exemplo, até conclui a tarefa de listagem.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
