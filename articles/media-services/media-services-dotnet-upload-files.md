<properties 
    pageTitle="Carregar ficheiros para uma conta dos serviços de multimédia utilizando o .NET | Microsoft Azure" 
    description="Saiba como obter o conteúdo de multimédia dos serviços de multimédia através da criação e carregar recursos." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Carregar ficheiros para uma conta dos serviços de multimédia utilizando o .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTO](media-services-rest-upload-files.md)
 - [Portal](media-services-portal-upload-files.md)

Serviços de multimédia, pode carrega (ou ingerir esta última) seus ficheiros digitais para um ativo. A entidade de **elementos** pode conter vídeo, áudio, imagens, coleções de miniaturas, texto tracks e legenda fechada ficheiros (e os metadados sobre estes ficheiros.)  Assim que os ficheiros carregados, o conteúdo está armazenado segura na nuvem para subsequente transformação e transmissão.

Os ficheiros no activo são denominados **Ficheiros ativo**. A instância **AssetFile** e no ficheiro de multimédia real estão dois objetos distintos. A instância AssetFile contém metadados sobre o ficheiro de multimédia, enquanto o ficheiro de multimédia contém os conteúdos de multimédia real.

>[AZURE.NOTE]As seguintes considerações aplicam quando escolher um nome de ficheiro de elementos:
>
>- Dos serviços de multimédia utiliza o valor da propriedade IAssetFile.Name durante a criação de URLs para o conteúdo da transmissão (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, codificação percentual não é permitida. O valor da propriedade **nome** não pode ter qualquer um dos seguintes [carateres percentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, só pode haver um '.' para a extensão de nome de ficheiro.
>
>- O comprimento do nome não deve ser maior do que 260 caracteres.

Quando cria elementos, pode especificar as seguintes opções de encriptação. 

- **Nenhum** – sem encriptação é utilizada. Este é o valor predefinido. Tenha em atenção que ao utilizar esta opção seu conteúdo não está protegido quando estão em trânsito ou no resto no armazenamento.
Se planear entregar uma MP4 utilizando transferência gradual, utilize esta opção. 
- **CommonEncryption** - Utilize esta opção se o carregamento de conteúdo que já foram encriptado e protegido com encriptação comuns ou DRM PlayReady (por exemplo, suaves transmissão protegido com PlayReady DRM).
- **EnvelopeEncrypted** – Utilize esta opção se são carregar HLS encriptados com AES. Note que os ficheiros tem ter sido codificados e encriptados por transformar gestor.
- **StorageEncrypted** - encripta o conteúdo de limpar localmente utilizar encriptação AES-256 bit e, em seguida, carregamentos-lo ao armazenamento do Azure onde foi armazenado encriptado no resto. Elementos protegidos com encriptação de armazenamento são automaticamente não encriptados colocados num sistema de ficheiros encriptados antes de codificação e, opcionalmente, encriptar novamente antes de carregar novamente como um novo activo de saída. No caso de utilização principal para encriptação de armazenamento é quando que pretende proteger os seus ficheiros de suporte de entrada de alta qualidade com encriptação forte em repouso no disco.

    Dos serviços de multimédia fornece encriptação de armazenamento no disco para os seus activos, não através de-de-fio como Gestor de direitos digitais (DRM).

    Se a elementos armazenamento encriptado, tem de configurar políticas de entrega de elementos. Para obter mais informações consulte o artigo [política de entrega de elementos de configurar](media-services-dotnet-configure-asset-delivery-policy.md).

Se especificar para sua activo a ser encriptados com uma opção de **CommonEncrypted** ou uma opção de **EnvelopeEncypted** , terá de associar o seu activo um **ContentKey**. Para mais informações, consulte o artigo [como criar um ContentKey](media-services-dotnet-create-contentkey.md). 

Se especificar para sua activo a ser encriptados com uma opção de **StorageEncrypted** , o SDK de serviços de multimédia para .NET irá criar um **StorateEncrypted** **ContentKey** para sua ativo.


Este tópico mostra como utilizar os serviços de multimédia .NET SDK, bem como as extensões de serviços de multimédia .NET SDK para carregar ficheiros para um activo dos serviços de multimédia.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Carregar um único ficheiro com serviços de multimédia .NET SDK 

O código de exemplo abaixo utiliza .NET SDK para efetuar as seguintes tarefas: 

- Cria um ativo vazio.
- Cria uma instância de AssetFile queremos para associar elemento.
- Cria uma instância de AccessPolicy que define as permissões e a duração do access ao activo.
- Cria uma instância de Locator que proporciona acesso à elemento.
- Os carregamentos pendentes um ficheiro de multimédia único dos serviços de multimédia. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Carregar vários ficheiros com serviços de multimédia .NET SDK 

O código seguinte mostra como criar um ativo e carregar vários ficheiros.

O código faz o seguinte:
    
-   Cria um ativo vazio utilizando o método de CreateEmptyAsset definido no passo anterior.
    
-   Cria uma instância de **AccessPolicy** que define as permissões e a duração do access ao activo.
    
-   Cria uma instância de **Locator** que proporciona acesso à elemento.
    
-   Cria uma instância de **BlobTransferClient** . Este tipo de representa um cliente que opera de blobs do Azure. Neste exemplo estamos a utilizar o cliente para monitorizar o progresso de carregamento. 
    
-   Enumera através de ficheiros no directório especificado e cria uma instância de **AssetFile** para cada ficheiro.
    
-   Os carregamentos pendentes os ficheiros para utilizando o método de **UploadAsync** dos serviços de multimédia. 
    
>[AZURE.NOTE] Utilize o método de UploadAsync para garantir que as chamadas não estão a bloquear e os ficheiros carregados em paralelo.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Ao carregar um grande número de elementos, considere o seguinte.

- Crie um novo objeto **CloudMediaContext** por tópico. A classe de **CloudMediaContext** não é tópico seguro.
 
- Aumente NumberOfConcurrentTransfers a partir do valor predefinido de 2 para um valor superior como 5. Definir esta propriedade afeta todas as instâncias do **CloudMediaContext**. 
 
- Manter ParallelTransferThreadCount no valor predefinido de 10.
 
##<a id="ingest_in_bulk"></a>Elementos ingesting em massa com serviços de multimédia .NET SDK 

Carregar ficheiros grandes elementos pode ser um congestionamento durante a criação de elementos. Ingesting ativos em volume ou "Em volume Ingesting", envolve desacoplamento criação de elementos do processo de carregamento. Para utilizar uma em volume ingesting abordagem, crie um manifestos (IngestManifest) que descreve o elementos e os respetivos ficheiros associados. Em seguida, utilize o método de carregamento da sua preferência para carregar os ficheiros associados ao contentor de Blobs do manifesto. O contentor de BLOBs associado com o manifestos está a observar dos serviços de multimédia do Microsoft Azure. Assim que um ficheiro é carregado para o contentor blob, dos serviços de multimédia do Microsoft Azure conclui a criação de elementos com base na configuração do elemento no manifesto (IngestManifestAsset).


Para criar uma nova chamada IngestManifest o método de criar exposto pela coleção de IngestManifests na CloudMediaContext. Este método irá criar um novo IngestManifest com o nome manifesto fornecidos.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Crie os ativos serão associados com IngestManifest em volume. Configure as opções de encriptação pretendido no activo para ingesting em volume.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Um IngestManifestAsset associa um ativo em volume IngestManifest para ingesting em volume. Além disso, associa a AssetFiles que compõem cada ativo. Para criar um IngestManifestAsset, utilize o método de criar no contexto de servidor.

O exemplo seguinte demonstra manifesto ingerir esta última da adicionando duas IngestManifestAssets novas que associar os duas ativos criados anteriormente para em volume. Além disso, cada IngestManifestAsset associa um conjunto de ficheiros que serão carregadas para cada imobilizado durante ingesting em volume.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
Pode utilizar qualquer aplicação de cliente de alta velocidade capaz de carregar os ficheiros de elementos para o contentor de armazenamento de BLOBs URI fornecido pela propriedade **IIngestManifest.BlobStorageUriForUpload** a IngestManifest. Um serviço de carregamento de alta velocidade dos é [Aspera no pedido de aplicação do Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Também pode escrever código para carregar os ficheiros de activos conforme mostrado no seguinte exemplo de código.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

O código para carregar os ficheiros de elementos de exemplo utilizados neste tópico é apresentado no seguinte exemplo código.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

Pode determinar o progresso da ingesting de em massa para todos os recursos associados a uma **IngestManifest** a propriedade estatísticas da **IngestManifest**de consulta. Para atualizar a informação de progresso, tem de utilizar uma nova **CloudMediaContext** cada vez que as inquérito a propriedade estatísticas.

O exemplo seguinte demonstra uma IngestManifest pelo seu **Id**de consulta.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Carregar ficheiros através do .NET SDK extensões 

O exemplo abaixo mostra como carregar um único ficheiro utilizando .NET SDK extensões. Neste caso o método de **CreateFromFile** é utilizado, mas a versão assíncrona também está disponível (**CreateFromFileAsync**). O método de **CreateFromFile** permite-lhe especificar o nome do ficheiro, opção de encriptação e uma chamada de retorno para comunicar o progresso de carregamento do ficheiro.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

O exemplo seguinte liga para a função de UploadFile e especifica a encriptação de armazenamento como a opção de criação de elementos.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Passo seguinte

Agora que carregou um ativo para dos serviços de multimédia, ir para o tópico de [como obter um processador de multimédia][] .

[Como obter um processador de multimédia]: media-services-get-media-processor.md
 
