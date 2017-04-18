<properties 
    pageTitle="Transferir elementos de multimédia" 
    description="Saiba prestes a transferir ativos com o seu computador. Exemplos de código são gravados no c# e utilizam o SDK de serviços de multimédia para .NET." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>

#<a name="how-to-deliver-an-asset-by-download"></a>Como: entregar um elementos por transferência

Este tópico descreve as opções para fornecer elementos de multimédia carregados para dos serviços de multimédia. Começar a apresentar o conteúdo dos serviços de multimédia em vários cenários de aplicação. Pode transferir elementos de multimédia ou aceder aos mesmos, utilizando um localizador. Pode enviar conteúdo multimédia para outra aplicação ou para outro fornecedor de conteúdo. Para melhor desempenho e escalabilidade, também pode entregar conteúdo utilizando uma rede de entrega de conteúdos (CDN).

Este exemplo mostra como transferir elementos de multimédia a partir dos serviços de multimédia para o computador local. O código de consultas as tarefas associadas com a conta dos serviços de multimédia por ID da tarefa e acede a sua coleção de **OutputMediaAssets** (que é o conjunto de uma ou mais elementos de multimédia de saída que resulta da execução de uma tarefa). Este exemplo mostra como transferir elementos de multimédia de saída de uma tarefa, mas pode aplicar a mesma abordagem para transferir outros elementos.

    
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##<a name="see-also"></a>Consulte também 

[Entregar transmissão de conteúdos](media-services-deliver-streaming-content.md)

