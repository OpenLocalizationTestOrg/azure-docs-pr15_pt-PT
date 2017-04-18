<properties 
    pageTitle="Advanced codificação com o fluxo de trabalho do Media Encoder Premium | Microsoft Azure" 
    description="Saiba como codificar com o fluxo de trabalho do Media Encoder Premium. Exemplos de código são gravados no c# e utilizam o SDK de serviços de multimédia para .NET." 
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

#<a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Advanced codificação com o fluxo de trabalho do Media Encoder Premium

>[AZURE.NOTE] Processador de multimédia de fluxo de trabalho do Media Encoder Premium outros fabricantes referido neste tópico não está disponível na China.

Para as questões de codificador premium, e-mail mepd em Microsoft.com.

##<a name="overview"></a>Descrição geral

Serviços de multimédia do Microsoft Azure é apresentamos o processador de multimédia de **Fluxo de trabalho do Media Encoder Premium** . Este ofertas de processador avançar codificação capacidades para os seus fluxos de trabalho do premium a pedido. 

Os tópicos seguintes descrevem detalhes relacionados com **o fluxo de trabalho do Media Encoder Premium**: 

- [Formatos suportados pelo fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md) – Discusses os codecs e formatos de ficheiro suportados pelo **Fluxo de trabalho do Media Encoder Premium**.

- A secção [Comparar codificadores](media-services-encode-asset.md#compare_encoders) compara as funcionalidades de codificação do **Fluxo de trabalho do Media Encoder Premium** e **Descodificador de multimédia padrão**.

Este tópico demonstra como codificar com **o fluxo de trabalho do Media Encoder Premium** utilizando o .NET.

Codificação de tarefas do de **Fluxo de trabalho do Media Encoder Premium** necessitam de um ficheiro de configuração separadas, denominado um ficheiro de fluxo de trabalho. Estes ficheiros têm uma extensão de .workflow e são criados ao utilizar a ferramenta de [Estruturador de fluxo de trabalho](media-services-workflow-designer.md) .

##<a name="encode"></a>Codificar

Codificação de tarefas do de **Fluxo de trabalho do Media Encoder Premium** necessitam de um ficheiro de configuração separadas, denominado um ficheiro de fluxo de trabalho. Estes ficheiros têm uma extensão de .workflow e são criados ao utilizar a ferramenta de [Estruturador de fluxo de trabalho](media-services-workflow-designer.md) .


Também pode aceder a predefinição ficheiros de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição destes ficheiros.

Os ficheiros de fluxo de trabalho precisam de ser carregado para a sua conta dos serviços de multimédia, como um activo e este recurso deve ser passado para a tarefa de codificação.

O exemplo seguinte demonstra como codificar com **o fluxo de trabalho do Media Encoder Premium**. 

São efectuados os seguintes passos: 
 
1. Criar um ativo e carregar um ficheiro de fluxo de trabalho. 
2. Criar um ativo e carregar um ficheiro de multimédia de origem.
3. Obter o processador de multimédia "Media Encoder Premium fluxo de trabalho".
4. Crie uma tarefa e de uma tarefa. 

    Na maioria dos casos, a cadeia de configuração para a tarefa está vazia (como no exemplo seguinte). Existem alguns cenários de avançadas (que necessitam para definir propriedades de runtime dinamicamente) caso em que iria fornecer uma cadeia de XML para a tarefa de codificação. Exemplos destes cenários são: criar uma sobreposição, paralelas ou sequenciais multimédia lombada, legendagem.
5. Adicione elementos de entrada duas à tarefa.
    
    um. 1. º – os elementos de fluxo de trabalho.

    b. 2º – elemento vídeo.
    
    **Nota**: os elementos de fluxo de trabalho tem de ser adicionado à tarefa antes dos elementos de multimédia. A cadeia de configuração para esta tarefa deve estar vazia. 

6. Submeta a tarefa de codificação.

Segue-se um exemplo completo. Para obter informações sobre como configurar o desenvolvimento .NET de serviços de multimédia, consulte o artigo [desenvolvimento dos serviços de multimédia com o .NET](media-services-dotnet-how-to-use.md).


    using System; 
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace MediaEncoderPremiumWorkflowSample
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
    
            private static readonly string _supportFiles =
                Path.GetFullPath(@"../..\Media");
    
            private static readonly string _workflowFilePath =
                Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
            
            private static readonly string _singleMP4InputFilePath =
                Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
    
            }
    
            static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
            {
                var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
    
                var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                    AccessPermissions.Write | AccessPermissions.List);
    
                var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
    
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);
    
                locator.Delete();
                accessPolicy.Delete();
    
                return asset;
            }
    
            static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                    processor,
                    "",
                    TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(workflow);
                task.InputAssets.Add(video); // we add one asset
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new
                        EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish. 
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                progressJobTask.Wait();
    
                // If job state is Error the event handling 
                // method for job progress should log errors.  Here we check 
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    throw new Exception("\nExiting method due to job error.");
                }
    
                return job.OutputMediaAssets[0];
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
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
            static private void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
    
                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
    
                Console.Write(builder.ToString());
            }
    
    
            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
                return processor;
            }
        }
    }


Para as questões de codificador premium, e-mail mepd em Microsoft.com.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
