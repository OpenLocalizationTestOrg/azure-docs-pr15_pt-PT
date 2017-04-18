<properties
    pageTitle="Utilizar as miniaturas de vídeo de multimédia do Microsoft Azure para criar um vídeo resumo | Microsoft Azure"
    description="Resumo de vídeo pode ajudar a criar resumos dos vídeos mais longos selecionando automaticamente fragmentos interessantes o vídeo de origem. Isto é útil quando quiser fornecer uma descrição geral do que esperar num vídeo por extenso."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>

#<a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Utilizar as miniaturas de vídeo de multimédia do Microsoft Azure para criar um resumo de vídeo
##<a name="overview"></a>Descrição geral

Processador de multimédia de **Miniaturas de vídeo de multimédia do Azure** (programa mínimo) permite-lhe criar um resumo de um vídeo que é útil para clientes que pretende apenas pré-visualizar um resumo de um vídeo por extenso. Por exemplo, clientes poderão querer ver uma breve "Resumo vídeo" quando estes Paire o cursor sobre uma miniatura. Por optimização os parâmetros de **miniaturas de Azure Media Video** através de uma predefinição de configuração, pode utilizar poderosa captura concatenação tecnologia de detecção e o MP para algorithmically gerar um subclip descritivo.  

A **Miniatura de vídeo de multimédia do Azure** MP atualmente está na pré-visualização.

Este tópico fornece-detalhes sobre a **Miniatura de vídeo de multimédia do Azure** e mostra como utilizá-lo com SDK de serviços de multimédia para .NET

##<a name="video-summary-example"></a>Exemplo de resumo de vídeo 

Eis alguns exemplos de como o pode fazer o processador de multimédia de miniaturas de vídeo de multimédia do Azure:

###<a name="original-video"></a>Vídeo original

[Vídeo original](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###<a name="video-thumbnail-result"></a>Resultado de miniatura de vídeo

[Resultado de miniatura de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="task-configuration-preset"></a>Configuração da tarefa (predefinido)

Ao criar uma tarefa de miniatura de vídeo com **Miniaturas de vídeo de multimédia do Azure**, tem de especificar uma predefinição de configuração. O exemplo acima miniatura foi criado com a configuração de JSON básica seguinte:

    {"version":"1.0"}

Atualmente, pode alterar os parâmetros seguintes:

Param|Descrição
---|---
outputAudio|Especifica se ou não o vídeo resultante contém qualquer áudio. <br/>Permitido valores são: VERDADEIRO ou FALSO. A predefinição é verdadeiro.
fadeInFadeOut|Especifica se é ou não o efeito de desvanecimento transições são utilizadas entre as miniaturas de movimento separada.  <br/>Permitido valores são: VERDADEIRO ou FALSO.  A predefinição é verdadeiro.
maxMotionThumbnailDurationInSecs|Número inteiro que indica quanto tempo será o vídeo resultante inteiro.  Predefinição depende da duração vídeo original.


A tabela seguinte descreve a duração predefinida, quando **maxMotionThumbnailInSecs** não é utilizada.

||||
---|---|---|---|---
Duração do vídeo|d < 3 min|3 min < d < 15 mínimo|mínimo 15 < d < 30 min.| mínimo 30 < d
Duração miniatura|15 sec (plano 2-3)| 30 sec (cenas 3-5)|60 sec (5-10 cenas)|90 sec (cenas de 10 a 15)


A seguinte JSON define parâmetros disponíveis.
    
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>Código de exemplo

A seguinte programa mostra como:

1. Criar um ativo e carregar um ficheiro de multimédia para elemento.
1. Cria uma tarefa com uma tarefa de miniatura vídeo com base num ficheiro de configuração que contém a predefinição de json seguinte. 
        
        {               
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }

1. Transferências os ficheiros de saída. 

###<a name="net-code"></a>Código .NET
     
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;
    
    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
    
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    

                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");
    
                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }
    
            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);
    
                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");
    
                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";
    
                var processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);
    
                // Specify the input asset.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    
                progressJobTask.Wait();
    
                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }
    
                return job.OutputMediaAssets[0];
            }
    
            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);
    
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);
    
                return asset;
            }
    
            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }
    
            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));
    
                return processor;
            }
    
            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
    
                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
        }
    }

###<a name="video-thumbnail-output"></a>Saída miniatura de vídeo

[Saída miniatura de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Ligações relacionadas

[Descrição geral de análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Azure demos de análise de multimédia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)