<properties
    pageTitle="Cara redaction com a análise de multimédia do Microsoft Azure | Microsoft Azure"
    description="Este tópico demonstra como reter faces com a análise de multimédia do Microsoft Azure."
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
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>Nominal redaction com a análise de multimédia do Microsoft Azure

##<a name="overview"></a>Descrição geral

**Redactor de multimédia do Azure** é um processador de multimédia de [Análise de multimédia do Azure](media-services-analytics-overview.md) (programa mínimo) que oferece redaction nominal dimensionáveis na nuvem. Nominal redaction permite-lhe modificar o seu vídeo para poder esbater faces dos indivíduos selecionados. Pretende utilizar o serviço de redaction nominal de cenários de segurança e de multimédia de notícias públicos. Alguns minutos de imagens que contenha várias faces podem demorar horas a reter manualmente, mas com este serviço o processo de redaction nominal irão necessitar apenas alguns passos simples. Para mais informações, consulte o artigo [neste](https://azure.microsoft.com/blog/azure-media-redactor/) blogue.

Este tópico fornece-detalhes sobre **Redactor de multimédia do Azure** e mostra como utilizá-lo com SDK de serviços de multimédia para .NET.

**Redactor de multimédia do Azure** MP atualmente está na pré-visualização.

## <a name="face-redaction-modes"></a>Modos de redaction nominal

Facial redaction funciona ao detetar faces em cada fotograma de vídeo e controlar o objeto nominal ambas as frente e para trás no tempo, para que a mesma pessoa pode ser desfocada ângulos outros também. O processo automatizado redaction é muito complexo e não sempre produtos agrícolas 100% de saída pretendida, por este motivo multimédia Analytics fornece-lhe algumas formas para modificar o resultado final.

Para além de um modo completamente automático, existe um fluxo de trabalho de dois passos que permite a seleção/de eliminação de-selection das faces encontrados através de uma lista de IDs de. Além disso fazer arbitrário por ajustes de moldura MP utiliza um ficheiro no formato JSON de metadados. Este fluxo de trabalho é dividido em modos de **Analisar** e **Redact** . Pode combinar dois modos de num único passo que é executada ambas as tarefas numa tarefa; Neste modo chama o **combinado**.

###<a name="combined-mode"></a>Modo combinado

Isto vai produzir uma mp4 redacted automaticamente sem qualquer manual de entrada.

Fase|Nome do ficheiro|Notas
---|---|---
Elementos de entrada|foo.bar|Imagem de vídeo no formato WMV, MOV ou MP4
Configuração de entrada|Configuração de trabalho predefinida|{«versão»:'1.0 ', 'Opções': {'modo': 'combinados'}}
Elementos de saída|foo_redacted.mp4|Vídeo com blurring aplicados

####<a name="input-example"></a>Exemplo de entrada:

[Assista a este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>Exemplo do resultado:

[Assista a este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>Analisar modo

A fase do fluxo de trabalho dois passos de **Analisar** assume uma entrada de vídeo e produz um ficheiro JSON das localizações nominal e imagens em jpg de cada nominal detectado.

Fase|Nome do ficheiro|Notas
---|---|----
Elementos de entrada|foo.bar|Imagem de vídeo no formato WMV, MPV ou MP4
Configuração de entrada|Configuração de trabalho predefinida|{«versão»:'1.0 ', 'Opções': {'modo': 'Analisar'}}
Elementos de saída|foo_annotations.JSON|Dados de anotação das localizações nominal no formato JSON. Isto pode ser editado por utilizador para modificar blurring delimitadora caixas. Consulte o artigo exemplo abaixo.
Elementos de saída|foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg]|Um jpg recortado de cada detetado nominal, onde o número de indica labelId da face

####<a name="output-example"></a>Exemplo do resultado:

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… truncados


###<a name="redact-mode"></a>Reter modo

O segundo passo do fluxo de trabalho leva um grande número de entradas que devem ser combinados um único activo.

Isto inclui uma lista de IDs de esbater, o vídeo original e as anotações JSON. Neste modo utiliza as anotações para aplicar blurring no vídeo de entrada.

O resultado da fase de analisar não inclui o vídeo original. O vídeo tem de ser carregado para o elemento entrado para a tarefa de modo Redact e selecionado como o ficheiro principal.

Fase|Nome do ficheiro|Notas
---|---|---
Elementos de entrada|foo.bar|Vídeo num formato MP4, MPV ou WMV. O mesmo vídeo tal como no passo 1.
Elementos de entrada|foo_annotations.JSON|ficheiro de metadados de anotações a partir da primeira fase, com modificações opcionais.
Elementos de entrada|foo_IDList.txt (opcional)|Lista de nominal IDs para reter separados por opcional nova linha. Se deixado em branco, isto Desfoca todas as faces.
Configuração de entrada|Configuração de trabalho predefinida|{«versão»:'1.0 ', 'Opções': {'modo': 'Reter'}}
Elementos de saída|foo_redacted.mp4|Vídeo com blurring aplicado com base em anotações

####<a name="example-output"></a>Exemplo de saída

Este é o resultado de uma IDList com um ID seleccionado.

[Assista a este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>Descrições de atributo

Redaction MP fornece uma precisão de alta nominal localização detecção e controlo que pode detetar até 64 faces humanos num fotograma de vídeo. Faces frontais fornecem os melhores resultados, enquanto faces laterais e pequenas faces (menor ou igual a 24 x 24 pixels) um desafio.

As faces detectadas e registadas são devolvidas com coordenadas indicando a localização da faces, bem como uma cara número de ID que indica o rastreio nesse individuais. Números de ID nominal são sujeitas a repor em circunstâncias quando a face frontal é perdida ou sobreposta numa moldura, que resulta em alguns indivíduos introdução atribuídos múltiplos IDs.

Para obter explicações detalhadas dos atributos, consulte o tópico de [detetar nominal e emoções com a análise de multimédia do Azure](media-services-face-and-emotion-detection.md) .

## <a name="sample-code"></a>Código de exemplo

A seguinte programa mostra como:

1. Criar um ativo e carregar um ficheiro de multimédia para elemento.
1. Crie uma tarefa com uma tarefa de redaction nominal baseada num ficheiro de configuração que contém a predefinição de json seguinte. 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. Transferir os ficheiros JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
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
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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


##<a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Ligações relacionadas

[Descrição geral de análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Azure demos de análise de multimédia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
