<properties
    pageTitle="Detetar nominal e emoções com a análise de multimédia do Microsoft Azure | Microsoft Azure"
    description="Este tópico demonstra como detetar faces e emoções com a análise de multimédia do Azure."
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
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detetar nominal e emoções com a análise de multimédia do Microsoft Azure

##<a name="overview"></a>Descrição geral

O processador de multimédia do **Azure multimédia nominal Detector** (programa mínimo) permite-lhe contar, controlar movimentos e até mesmo avaliar participação da audiência e resposta através de expressões faciais. Este serviço contém duas funcionalidades: 

- **Deteção de nominal**

    Nominal deteção de documentos localiza e controla faces humanos dentro de um vídeo. Várias faces podem ser detetados e subsequentemente ser registados à medida que deslocar-se, com os metadados de hora e a localização devolvido num ficheiro JSON. Durante o controlo, tentará para dar um ID consistente para a mesma face enquanto a pessoa é mover-se no ecrã, mesmo são obstruídas ou brevemente sair da moldura.

    >[AZURE.NOTE]Este serviços não efetua reconhecimento facial. Um indivíduo que deixa a moldura ou torna-se obstruída para muito tempo será dada um novo ID quando devolverem.

- **Deteção de emoções**
    
    Emoções deteção é um componente opcional de processador de multimédia de detecção nominal que devolve análise no vários afectivos atributos da faces detectadas, incluindo felicidade, sadness, receio, anger e muito mais. 

O **Azure multimédia nominal Detector** MP atualmente está na pré-visualização.

Este tópico fornece-detalhes sobre **Azure multimédia nominal Detector** e mostra como utilizá-lo com SDK de serviços de multimédia para .NET.

##<a name="face-detector-input-files"></a>Cara ficheiros Detector de entrada

Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

##<a name="face-detector-output-files"></a>Cara Detector ficheiros de saída

A API de deteção e controlo nominal fornece deteção de localização de nominal alta precisão e controlo pode detetar até 64 faces humanos num vídeo. Faces frontais fornecem os melhores resultados, enquanto faces laterais e pequenas faces (menor ou igual a 24 x 24 pixels) poderão não ser exatas como.

As faces detectadas e registadas são devolvidas com coordenadas (da esquerda, superior, largura e altura) que indica a localização da faces na imagem em pixéis, bem como um número de ID de nominal que indica o rastreio nesse individuais. Números de ID nominal são sujeitas a repor em circunstâncias quando a face frontal é perdida ou sobreposta numa moldura, que resulta em alguns indivíduos introdução atribuídos múltiplos IDs.

###<a id="output_elements"></a>Elementos o JSON do ficheiro de saída

Para a deteção de nominal e controlar a operação, o resultado de saída contém os metadados de faces dentro do ficheiro no formato JSON determinado.

A deteção de nominal e registo JSON inclui os seguintes atributos:

Elemento|Descrição
---|---
Versão|Refere-se para a versão da API do vídeo.
Escala temporal|"Marcas de escala" por segundo do vídeo.
Desvio de|Este é o desvio de tempo para selos de tempo. Na versão 1.0 do vídeo APIs, isto irá estar sempre 0. No futuro cenários que suportamos, pode alterar este valor.
Velocidade de fotogramas|Molduras por segundo do vídeo.
Fragmentos|Os metadados são partidos para cima em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalo e evento (s).
Iniciar|A hora de início do primeiro evento de 'marcas de escala'.
Duração|O comprimento do fragmento, no "marcas de escala".
Intervalo|Intervalo de cada entrada de evento dentro o fragmento, no "marcas de escala".
Eventos|Cada evento contém as faces detetado e registadas dentro dessa duração de tempo. É uma matriz de matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é composta por 0 ou mais eventos que ocorreram essa na hora. [Um parêntese Reto vazia significa que foram detetadas sem faces.
ID| O ID da face que está a ser controlado. Este número inadvertidamente poderá alterar se uma cara ficar não detectada. Um indivíduo determinado deverá ter o mesmo ID em todo o vídeo geral, mas este não é possível garantir devido às limitações no algoritmo de deteção (oclusão, etc.)
X, Y|A parte superior esquerda X e Y coordenadas da face caixa numa escala normalizada de 0,0 a 1,0 delimitadora. <br/>-X e Y coordenadas são relativas para a horizontal sempre, para que se tiver uma vertical de vídeo (ou de cabeça para baixo, no caso de iOS), terá de transpor as coordenadas em conformidade.
Largura, altura|A largura e altura da face caixa numa escala normalizada de 0,0 a 1,0 delimitadora.
facesDetected|Este encontra-se no final dos resultados JSON e resume o número de faces que o algoritmo detectado durante o vídeo. Uma vez que os IDs de podem repor inadvertidamente se uma cara ficar não detectada (por exemplo, nominal ficar fora do ecrã, como ausente o seguinte aspeto), este número não poderá igual sempre o número VERDADEIRO de faces no vídeo.

Nominal Detector utiliza técnicas de fragmentação (onde os metadados podem ser divididos blocos baseados no tempo e pode transferir apenas o que precisa de) e segmentação (onde os eventos são divididos caso obtêm demasiado grandes). Alguns cálculos simples podem ajudá-lo transformar os dados. Por exemplo, se um evento iniciado em 6300 (marcas de escala), com uma escala temporal de 2997 (marcas de escala/seg.) e velocidade de fotogramas de 29.97 (molduras/seg.), em seguida:

- Início/escala temporal = 2.1 segundos
- Segundos x (velocidade de fotogramas/escala temporal) = 63 molduras

Segue-se um exemplo simples de extrair JSON para um por formato de moldura para deteção nominal e controlo:
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Cara deteção entrada e saída de exemplo

###<a name="input-video"></a>Vídeo de introdução

[Vídeo de introdução](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuração da tarefa (predefinido)

Ao criar uma tarefa com **Detector de nominal de multimédia do Azure**, tem de especificar uma predefinição de configuração. A predefinição de configuração seguinte é apenas para deteção nominal.

    {"version":"1.0"}

###<a name="json-output"></a>JSON saída

O exemplo seguinte de saída JSON foi truncado.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Deteção de emoções de entrada e saída exemplo


###<a name="input-video"></a>Vídeo de introdução

[Vídeo de introdução](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuração da tarefa (predefinido)

Ao criar uma tarefa com **Detector de nominal de multimédia do Azure**, tem de especificar uma predefinição de configuração. Especifica a predefinição de configuração seguintes para criar JSON com base em detecção emoções.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Descrições de atributo

Nome do atributo|Descrição
---|---
Modo|Faces: Apenas cara deteção <br/>AggregateEmotion: Valores de retorno emoções média para todos os faces numa moldura.
AggregateEmotionWindowMs|Utilize se AggregateEmotion modo selecionado. Especifica o comprimento de vídeo utilizado para produzir cada resultado de agregação, em milissegundos.
AggregateEmotionIntervalMs|Utilize se AggregateEmotion modo selecionado. Especifica com que frequência para produzir resultados agregação.

####<a name="aggregate-defaults"></a>Predefinições de agregação

Abaixo, são recomendadas valores para que as definições de janela e intervalo de agregação. AggregateEmotionWindowMs deve ser superior a AggregateEmotionIntervalMs.

   |Predefinições (s)|Max(s)|Min(s)
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>JSON saída

JSON de saída para agregação emoções (truncadas):
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Limitações

- Os formatos de vídeos de teclado suportados incluem MP4, MOV e WMV.
- O intervalo de tamanho nominal detectáveis é 24 x 24 para 2048 x 2048 pixels. Não irão ser detectadas faces fora deste intervalo.
- Para cada vídeo, o número máximo de faces devolvido é 64.
- Algumas faces poderão não ser detectados devido desafios técnicos; Por exemplo, muito grandes ângulos nominal (cabeça representa) e oclusão grande. Faces frontais e perto frontal tem os melhores resultados.


## <a name="sample-code"></a>Código de exemplo

A seguinte programa mostra como:

1. Criar um ativo e carregar um ficheiro de multimédia para elemento.
1. Cria uma tarefa com uma tarefa de deteção de nominal com base num ficheiro de configuração que contém a predefinição de json seguinte. 
                    
        {
            "version": "1.0"
        }

1. Transfere os ficheiros JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Ligações relacionadas

[Descrição geral de análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Azure demos de análise de multimédia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)