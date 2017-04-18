<properties
    pageTitle="Detetar movimentos com a análise de multimédia do Microsoft Azure | Microsoft Azure"
    description="O processador de multimédia do Azure multimédia movimento Detector (programa mínimo) permite-lhe identificar eficientemente secções de interesse dentro de um vídeo caso contrário longo e decorrer."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Detetar movimentos com a análise de multimédia do Microsoft Azure

##<a name="overview"></a>Descrição geral

O processador de multimédia do **Azure multimédia movimento Detector** (programa mínimo) permite-lhe identificar eficientemente secções de interesse dentro de um vídeo caso contrário longo e decorrer. Deteção de movimento pode ser utilizada no imagens estáticas câmara para identificar secções do vídeo onde movimento ocorre. Gera um ficheiro JSON que contenha um metadados com selos de tempo e o delimitadora região onde o evento ocorreu.

Esta tecnologia orientadas para feeds de vídeo de segurança, é possível categorizar movimento em eventos relevantes e falsos como sombreados e alterações de iluminação. Esta opção permite-lhe gerar alertas de segurança da câmara feeds sem a ser spammed com infinito eventos irrelevantes, ser capaz de extrair minutos de interesse de vídeos de controlo demasiado longo.

O **Detector de movimento de multimédia do Azure** MP atualmente está na pré-visualização.

Este tópico fornece-detalhes sobre **Detector de movimento de multimédia do Azure** e mostra como utilizá-lo com SDK de serviços de multimédia para .NET


##<a name="motion-detector-input-files"></a>Ficheiros de entrada de Detector de movimento

Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

##<a name="task-configuration-preset"></a>Configuração da tarefa (predefinido)

Ao criar uma tarefa com **Detector de movimento de multimédia do Azure**, tem de especificar uma predefinição de configuração. 

###<a name="parameters"></a>Parâmetros

Pode utilizar os seguintes parâmetros:

Nome|Opções|Descrição|Predefinido
---|---|---|---
sensitivityLevel|Cadeia: 'baixo', 'Médio', 'Alto'|Define o nível de confidencialidade no quais movimentos é comunicado. Ajuste esta opção para ajustar a quantidade de falsos.|'Médio'
frameSamplingValue|Número inteiro positivo|Conjuntos de frequência no qual o algoritmo é executado. 1 é igual a cada moldura, 2 significa cada moldura 2º e assim sucessivamente.|1
detectLightChange|Booleana: 'true', 'false'|Define se simplificada alterações são reportadas nos resultados de|'False'
mergeTimeThreshold|Tempo de x: Ss<br/>Exemplo: 00:00:03|Especifica a janela de tempo entre eventos de movimento onde 2 eventos serão combinados e comunicados como 1.|00:00:00
detectionZones|Uma matriz de zonas de detecção:<br/>-Zona de deteção é uma matriz de 3 ou mais pontos<br/>-Ponto é um x e y coordenar de 0 a 1.|Descreve a lista de detecção poligonal zonas para ser utilizado.<br/>Os resultados serão comunicados com as zonas como um ID, com a primeira imagem ser 'id': 0|Única zona que abrange todo o pacote.

###<a name="json-example"></a>Exemplo JSON

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>Ficheiros de saída de Detector de movimento

Uma tarefa de deteção de movimento irá devolver um ficheiro JSON no elemento de saída que descreve os alertas de movimento e os respetivos categorias, dentro do vídeo. O ficheiro irá conter informações sobre a hora e a duração de movimento detetado no vídeo.

A API do Detector movimento fornece indicadores quando existem objetos em movimento um fundo fixo vídeo (por exemplo, um controlo vídeo). O Detector movimento está preparado para reduzir alarmes FALSO, tais como iluminação e alterações de sombra. Atuais limitações dos algoritmos incluam vídeos de visão da noite, objetos semitransparente e objetos pequenas.

###<a id="output_elements"></a>Elementos o JSON do ficheiro de saída

>[AZURE.NOTE]Na versão mais recente, o formato de saída JSON tiver sido alterada e pode representar uma alteração de última hora para alguns clientes.

A tabela seguinte descreve os elementos o JSON do ficheiro de saída.

Elemento|Descrição
---|---
Versão|Refere-se para a versão da API do vídeo. A versão atual é 2.
Escala temporal|"Marcas de escala" por segundo do vídeo.
Desvio de|O desvio de tempo para selos de tempo no "marcas de escala". Na versão 1.0 do vídeo APIs, isto irá estar sempre 0. No futuro cenários que suportamos, pode alterar este valor.
Velocidade de fotogramas|Molduras por segundo do vídeo.
Largura, altura|Refere-se para a largura e altura do vídeo em pixéis.
Iniciar|O carimbo de hora de início no "marcas de escala".
Duração|O comprimento do evento, de "marcas de escala".
Intervalo|Intervalo de cada entrada no evento, no "marcas de escala".
Eventos|Cada fragmento de evento contém movimento detetado dentro dessa duração de tempo.
Tipo|Na versão atual, este é sempre 2 para movimento genérico. Este vídeo APIs a flexibilidade para categorizar da oferece-lhe de etiqueta de movimento no futuro versões.
RegionID|Como é explicado acima, este será sempre 0 nesta versão. Esta etiqueta dá vídeo API a flexibilidade de poder localizar movimento no diferentes regiões em versões futuras.
Regiões|Refere-se para a área no vídeo onde que lhe interessam movimento. <br/><br/>-"id" representa a área de região – nesta versão existe apenas uma, ID de 0. <br/>-"tipo" representa a forma de região que lhe interessam para movimento. Atualmente, "rectângulo" e "polígono" são suportadas.<br/> Se tiver especificado "retângulo", o região tem dimensões no X, Y, largura e altura. As coordenadas X e Y representam as coordenadas XY do canto superior esquerdo da região numa escala normalizada de 0,0 a 1,0. A largura e altura representam o tamanho da região numa escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, largura e altura são sempre fixado 0, 0 e 1, 1. <br/>Se tiver especificado "polígono", a região tem dimensões em pontos. <br/>
Fragmentos|Os metadados são partidos para cima em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalo e evento (s). Um fragmento com sem eventos significa que não movimento foi detectado durante a hora de início e a duração.
Parênteses Retos]|Cada parêntese representa um intervalo no evento. Foi detetado parênteses vazias nesse intervalo significa que não movimento.
localizações|Esta entrada nova em eventos de lista a localização onde ocorreu o movimento. Este é mais específico do que as zonas de detecção.

Segue-se um exemplo de saída JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Limitações

- Os formatos de vídeos de teclado suportados incluem MP4, MOV e WMV.
- Deteção de movimento está optimizada para vídeos fixas fundo. O algoritmo foca-se em redução alarmes FALSO, tais como alterações iluminação e sombras.
- Algumas movimento poderá não ser detectado devido desafios técnicos; Por exemplo, vídeos de visão da noite, objetos semitransparente e objetos pequenas.


## <a name="sample-code"></a>Código de exemplo

A seguinte programa mostra como:

1. Criar um ativo e carregar um ficheiro de multimédia para elemento.
1. Cria uma tarefa com uma tarefa de deteção de movimento vídeo com base num ficheiro de configuração que contém a predefinição de json seguinte. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Transfere os ficheiros JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Blogue do Azure Detector de movimento de serviços de multimédia](https://azure.microsoft.com/blog/motion-detector-update/)

[Descrição geral de análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Azure demos de análise de multimédia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
