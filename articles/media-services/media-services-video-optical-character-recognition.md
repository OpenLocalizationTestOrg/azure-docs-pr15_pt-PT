<properties
    pageTitle="Utilizar a análise de multimédia do Azure para converter o conteúdo de texto em ficheiros de vídeo em texto digital | Microsoft Azure"
    description="Azure multimédia Analytics OCR (ótico de carateres) permite-lhe converter texto digital editável e pesquisável conteúdo de texto em ficheiros de vídeo.  Esta opção permite-lhe automatizar a extração de metadados significativo a partir do sinal de vídeo dos seus ficheiros de multimédia."
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
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Utilizar a análise de multimédia do Azure para converter o conteúdo de texto em ficheiros de vídeo em texto digital 

##<a name="overview"></a>Descrição geral

Se precisar de extrair o conteúdo de texto dos ficheiros de vídeo e gerar um texto digital editável, pesquisável, deve utilizar OCR de análise de multimédia do Azure (ótico de carateres). Este processador de multimédia do Azure Deteta conteúdo de texto em que os ficheiros de vídeo e gera ficheiros de texto para a sua utilização. OCR permite-lhe automatizar a extração de metadados significativo a partir do sinal de vídeo dos seus ficheiros de multimédia.

Quando utilizado juntamente com um motor de busca, pode facilmente indexar os ficheiros de multimédia por texto e melhorar a descoberta do seu conteúdo. Isto é muito útil altamente textual vídeo, como uma gravação de vídeo ou de uma captura de ecrã de uma apresentação de apresentação de diapositivos. O processador de multimédia do Azure OCR está optimizado para texto digital.

O processador de multimédia do **Azure multimédia OCR** atualmente está na pré-visualização.

Este tópico fornece-detalhes sobre **OCR de multimédia do Azure** e mostra como utilizá-lo com SDK de serviços de multimédia para .NET. Para obter informações adicionais e exemplos, consulte o artigo [neste blogue](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##<a name="ocr-input-files"></a>Ficheiros de entrada de OCR

Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

##<a name="task-configuration"></a>Configuração da tarefa 

Configuração da tarefa (predefinido). Ao criar uma tarefa através de **OCR de multimédia do Azure**, tem de especificar uma configuração pré-configurar utilizando JSON ou XML. 

###<a name="attribute-descriptions"></a>Descrições de atributo

Nome do atributo|Descrição
---|---
Idioma|(opcional) descreve o idioma do texto para o qual pretende procurar. Um dos seguintes: deteção automática de (predefinição), árabe, ChineseSimplified, ChineseTraditional, checo dinamarquês, neerlandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polaco, português, romeno, russo, SerbianCyrillic, SerbianLatin, eslovaco, espanhol, sueco, turco.
TextOrientation|(opcional) descreve a orientação do texto para o qual pretende procurar.  "Esquerda" significa que a parte superior da todas as letras são indicada para a esquerda.  Texto predefinido (por exemplo, que podem ser encontradas num livro) pode ser chamado "Seguimento" orientado.  Um dos seguintes: deteção automática de (predefinição), para cima, para a direita, para baixo, para a esquerda.
TimeInterval|(opcional) descreve a taxa de amostragem.  A predefinição é cada segundo de 1/2.<br/>Formato de JSON – ss. SSS (predefinição 00:00:00.500)<br/>Formato XML – W3C XSD duração primitiva (predefinição PT0.5)
DetectRegions|(opcional) Uma matriz de objetos DetectRegion especificando regiões dentro no fotograma de vídeo nas quais se detetar texto.<br/>Um objeto de DetectRegion é constituído os seguintes valores de número inteiro de quatro:<br/>Esquerda – pixéis a partir da margem esquerda<br/>Início – pixéis a partir da margem de início<br/>Largura – largura da região em pixéis<br/>Altura – altura da região em pixéis

####<a name="json-preset-example"></a>Exemplo de predefinidas JSON
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>Exemplo XML predefinido

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>Ficheiros de saída de OCR

O resultado do processador de multimédia OCR é um ficheiro JSON.

###<a name="elements-of-the-output-json-file"></a>Elementos o JSON do ficheiro de saída

O resultado de OCR vídeo fornece dados segmentado tempo os carateres que se encontram no seu vídeo.  Pode utilizar atributos como idioma ou orientação que telefónica no exatamente as palavras que se estiver interessado em analisar. 

O resultado contém os seguintes atributos:

Elemento|Descrição
---|---
Escala temporal|"marcas de escala" por segundo do vídeo
Desvio de|tempo de desfasamento para selos de tempo. Na versão 1.0 do vídeo APIs, isto irá estar sempre 0.
Velocidade de fotogramas|Molduras por segundo do vídeo
largura|largura do vídeo em pixéis
altura|altura do vídeo em pixéis
Fragmentos|matriz de blocos com base em vez de vídeo no qual os metadados são blocos
Iniciar|hora de início de uma fragmento no "marcas de escala"
duração|comprimento de um fragmento no "marcas de escala"
intervalo|intervalo de cada evento dentro o fragmento de determinado
eventos|matriz de regiões
região|objeto que representa detetado palavras ou expressões
idioma|idioma do texto detetado dentro de uma região
orientação|orientação do texto detetado dentro de uma região
linhas|matriz de linhas de texto detetado dentro de uma região
texto|o texto real

###<a name="json-output-example"></a>Exemplo de saída JSON

O exemplo seguinte de saída contém as informações gerais de vídeos e várias segmentos de vídeos. Em cada fragmento de vídeo, contém cada região que é detetado pelo OCR MP com o idioma e a orientação do texto. Região de também contém todas as linhas de word nesta região com texto a linha, posição da linha e todas as informações do word (conteúdo do word, posição e confiança) nesta linha. Segue-se um exemplo e colocar o inline algumas comentários.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Código de exemplo

A seguinte programa mostra como:

1. Criar um ativo e carregar um ficheiro de multimédia para elemento.
1. Cria uma tarefa com um ficheiro de configuração/predefinido OCR.
1. Transfere os ficheiros JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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
