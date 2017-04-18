<properties 
    pageTitle="Advanced codificação com Media Encoder padrão" 
    description="Este tópico mostra como efetuar a codificação avançadas através da personalização Media Encoder padrão predefinições de tarefa. O tópico mostra como utilizar serviços de multimédia .NET SDK para criar uma tarefa de codificação e de projeto. Também mostra como fornecer predefinições personalizadas para a tarefa de codificação." 
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
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>Advanced codificação com Media Encoder padrão

##<a name="overview"></a>Descrição geral

Este tópico mostra como efetuar tarefas de codificação avançadas com Media Encoder padrão. O tópico mostra [como utilizar o .NET para criar uma tarefa de codificação e uma tarefa que executa esta tarefa](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). Também mostra como fornecer predefinições personalizadas à codificação tarefa. Para descrição dos elementos que são utilizadas pelo predefinições, consulte [Este documento](https://msdn.microsoft.com/library/mt269962.aspx). 

Predefinições personalizadas que efetuar as seguintes tarefas de codificação são demonstradas:

- [Gerar miniaturas](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Cortar um vídeo (recorte)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Criar uma sobreposição](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Inserir uma faixa de áudio silenciosa quando entrada não têm áudio](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Desativar retirar entrelaçamento automática](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Predefinições só de áudio](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Concatenar duas ou mais ficheiros de vídeo](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Vídeos de recorte com Media Encoder padrão](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Inserir uma pista de vídeo quando entrada não dispõe de vídeo](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [Rodar um vídeo](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>Codificação com .NET SDK dos serviços de multimédia

Exemplo de código seguinte utiliza .NET SDK serviços de multimédia para efetuar as seguintes tarefas:

- Crie uma tarefa de codificação.
- Obter uma referência para o codificador Media Encoder padrão.
- Carregar o XML personalizado ou JSON predefinir. Pode guardar o XML ou JSON (por exemplo, [XML](media-services-custom-mes-presets-with-dotnet.md#xml) ou [JSON](media-services-custom-mes-presets-with-dotnet.md#json) num ficheiro e utilize o seguinte código para carregar o ficheiro.

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- Adicione uma tarefa codificação à tarefa. 
- Especifique o elemento entrado para ser codificado.
- Crie um ativo de saída que contém o activo codificado.
- Adicione um processador de eventos para verificar o progresso da tarefa.
- Submeta a tarefa.
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
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
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
                
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
                
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
                
                    return job.OutputMediaAssets[0];
                }
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                            break;
                        default:
                            break;
                    }
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
            }
        }


##<a name="support-for-relative-sizes"></a>Suporte para tamanhos de relativos

Quando a gerar miniaturas do mesmo, não tem de especificar sempre saída largura e altura em pixéis. Pode especificá-las no percentagens, no intervalo [% de 1,..., 100%].

###<a name="json-preset"></a>JSON predefinido 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>XML predefinido
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>Gerar miniaturas

Esta secção mostra como personalizar uma configuração predefinida que gera miniaturas. Predefinido definido abaixo contém informações sobre como pretende codificar o seu ficheiro, bem como a informação necessária para gerar miniaturas. Pode efetuar qualquer uma do EEM predefinições documentado [aqui](https://msdn.microsoft.com/library/mt269960.aspx) e adicionar código que gera miniaturas.  

>[AZURE.NOTE]A definição de **SceneChangeDetection** no seguinte predefinida pode apenas ser definida como VERDADEIRO, se estiver a codificação para uma única velocidade vídeo. Se está a codificar a um vídeo de velocidade multi e definir **SceneChangeDetection** como VERDADEIRO, o codificador devolve um erro.  


Para obter informações acerca do esquema, consulte o artigo [neste](https://msdn.microsoft.com/library/mt269962.aspx) tópico.

Certifique-se para a secção [Considerações](media-services-custom-mes-presets-with-dotnet.md#considerations) de rever.

###<a id="json"></a>JSON predefinido


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>XML predefinido


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>Considerações sobre

As seguintes considerações aplicam-se:

- A utilização de explícitas selos de tempo para iniciar/passo/intervalo assume que a origem de entrada é, pelo menos, um minuto por extenso.
- Elementos de Png/jpg/BmpImage ter iniciar, passo e o intervalo atributos de cadeia – estas podem ser interpretadas como:

    - Número da frame se forem números inteiros que não sejam negativos, por exemplo "Iniciar": "120"
    - Relativa a duração de origem, se expresso como %-com o sufixo, por exemplo "Iniciar": "15%", ou
    - Carimbo se expresso como ss... Formatar, por exemplo "Iniciar": "00: 01:00"

    Pode misturar e corresponder científicas à medida que introduza.
    
    Para além disso, iniciar também suporta a uma Macro especial: {utilizações}, que tenta determinar a primeira moldura "interessante" da nota conteúda: (passo e intervalo são ignorados quando iniciar está definida como {melhor})
    
    - Predefinições: Iniciar: {melhor}
- Formato de exportação tem de ser fornecido explicitamente para cada formato da imagem: Png/Jpg/BmpFormat. Quando presentes, EEM corresponde ao JpgVideo para JpgFormat e assim sucessivamente. OutputFormat apresenta uma nova Macro específico de imagem codec: {Index}, que precisa de estar apresenta (uma vez e apenas uma vez) para formatos de exportação de imagem.

##<a id="trim_video"></a>Cortar um vídeo (recorte)

Esta secção fala sobre como modificar predefinições codificador para recortar ou cortar o vídeo introdução onde a entrada é um ficheiro de mezzanine chamados ou a pedido. O codificador também pode ser utilizado para recortar ou cortar um ativo, que é capturado ou arquivado a partir de uma sequência direto – os detalhes para este estão disponíveis [neste blogue](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Cortar os seus vídeos, pode efetuar qualquer uma do EEM predefinições documentado [aqui](https://msdn.microsoft.com/library/mt269960.aspx) e modificar o elemento **origens** (como mostrado abaixo). O valor de hora de início tem de corresponder as marcas de hora absolutas do vídeo entrada. Por exemplo, se o primeiro fotograma do vídeo entrado tem um carimbo de data/hora de 12:00:10.000, em seguida, hora de início deve ser, pelo menos, 12:00:10.000 e superiores. No exemplo abaixo, podemos partem do princípio de que o vídeo introdução tem um carimbo de data/hora inicial de zero. **Origens** devem ser colocadas no início da predefinição. 
 
###<a id="json"></a>JSON predefinido
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>XML predefinido
    
Cortar os seus vídeos, pode efetuar qualquer uma do EEM predefinições documentado [aqui](https://msdn.microsoft.com/library/mt269960.aspx) e modificar o elemento **origens** (como mostrado abaixo).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>Criar uma sobreposição

O padrão de descodificador de multimédia permite-lhe sobreposição de uma imagem para um vídeo existente. Atualmente, são suportados os seguintes formatos: png, jpg, gif e bmp. Predefinido definido como se segue é um exemplo básico de uma sobreposição de vídeo.

Para além de definir um ficheiro predefinidas, também tem que permitir que os serviços de multimédia saber qual o ficheiro no activo é a imagem de sobreposição e qual o ficheiro que é o vídeo de origem para o qual pretende sobreposição a imagem. O ficheiro de vídeo tem de ser o ficheiro **principal** . 

O exemplo .NET acima define duas funções: **UploadMediaFilesFromFolder** e **EncodeWithOverlay**. A função UploadMediaFilesFromFolder carregamentos de ficheiros a partir de uma pasta (por exemplo, BigBuckBunny.mp4 e Image001.png) e define o ficheiro de mp4 a ser o ficheiro no elemento principal. A função **EncodeWithOverlay** utiliza o ficheiro predefinidas personalizado que foi lhe transmitido (por exemplo, a configuração predefinida que se segue) para criar a tarefa de codificação. 

>[AZURE.NOTE]Limitações atuais:
>
>A definição de opacidade sobreposição não é suportada.
>
>O ficheiro de vídeo de origem e o ficheiro de imagem de sobreposição tem de estar no mesmo activo e o ficheiro de vídeo tem de ser definidas como ficheiro primário neste recurso.

###<a name="json-preset"></a>JSON predefinido
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>XML predefinido
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>Inserir uma faixa de áudio silenciosa quando entrada não têm áudio

Por predefinição, se enviar uma entrada ao codificador que contém apenas vídeo e, sem áudio, em seguida, os elementos de saída contém ficheiros que contêm os dados de apenas vídeo. Alguns leitores poderão não conseguir processar as sequências de saída. Pode utilizar esta definição para forçar o codificador para adicionar uma faixa de áudio silenciosa ao resultado nesse mesmo cenário.

Para forçar o codificador para produzir um ativo que contém uma faixa de áudio silenciosa quando entrada não têm áudio, especifique o valor de "InsertSilenceIfNoAudio".

Pode efetuar qualquer uma do EEM predefinições documentado [aqui](https://msdn.microsoft.com/library/mt269960.aspx)e efetuar a alteração seguinte:

###<a name="json-preset"></a>JSON predefinido

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>XML predefinido

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Desativar retirar entrelaçamento automática

Os clientes não precisa de fazer nada se que pretenderem o conteúdo da entrelaçado fazendo para ser automaticamente retirar entrelaçado. Quando o automática retirar entrelaçamento está ativada (predefinição) o EEM é que a deteção automática de molduras entrelaçadas e apenas anular interlaces molduras marcadas como entrelaçado.

Pode desativar o automática retirar entrelaçamento. Esta opção não é recomendada.

###<a name="json-preset"></a>JSON predefinido
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>XML predefinido
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>Predefinições só de áudio

Esta secção demonstra duas predefinições de EEM só de áudio: AAC áudio e AAC boa qualidade de áudio.

###<a name="aac-audio"></a>AAC áudio 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>AAC boa qualidade áudio

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>Concatenar duas ou mais ficheiros de vídeo

O exemplo seguinte demonstra como pode gerar uma configuração predefinida para concatenar duas ou mais ficheiros de vídeo. O cenário mais comum é ao qual pretende adicionar um cabeçalho ou central para o vídeo principal. A sua utilização prevista é quando os ficheiros de vídeo a ser editados em conjunto partilham propriedades (resolução de vídeo, taxa de moldura, contagem de pista de áudio, etc.). Deverá tomar cuidado para não misturar vídeos de taxas de moldura diferente ou com um número diferente de faixas de áudio.

###<a name="requirements-and-considerations"></a>Requisitos e considerações

- Vídeos de entrada apenas devem ter uma faixa de áudio.
- Vídeos de entrada devem ter a mesma taxa de moldura.
- Tem de carregar vídeos para activos separados e definir os vídeos como ficheiro primário no cada ativo.
- Precisa de saber a duração de vídeos.
- Os exemplos predefinidos abaixo assume que todos os vídeos entrados começam com um carimbo de data/hora de zero. Tem de modificar os valores de hora de início se os vídeos tem carimbo inicial diferente, tal como está normalmente as maiúsculas/minúsculas com arquivos direto.
- A configuração JSON predefinida torna referências explícitas para os valores de iddoactivo dos activos entrados.
- O código de exemplo assume que o predefinido JSON ter sido guardado para um ficheiro local, tal como "C:\supportFiles\preset.json". Também partem do princípio que duas recursos tem sido criados por ao carregar ficheiros de vídeos duas e que conhece os valores de iddoactivo resultante.
- O fragmento de código e JSON predefinido mostra um exemplo de concatenação de dois ficheiros de vídeo. Pode expandi-lo para vídeos mais de duas por:

    1. Tarefa estiver a ligar. InputAssets.Add() repetidamente para adicionar mais vídeos, ordem.
    2. Efetuar correspondente edita o elemento "Origens" JSON, adicionando mais entradas, na mesma ordem. 


###<a name="net-code"></a>Código .NET

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>JSON predefinido

Atualize a sua página personalizada pré-configurar com ids de activos que pretende para concatenar e com o segmento de tempo apropriado para cada vídeo.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="crop"></a>Vídeos de recorte com Media Encoder padrão

Consulte o tópico de [vídeos de recorte com Media Encoder padrão](media-services-crop-video.md) .

##<a id="no_video"></a>Inserir uma pista de vídeo quando entrada não dispõe de vídeo

Por predefinição, se enviar uma entrada ao codificador que contenha apenas áudio e vídeo sem, em seguida, elemento saída contém ficheiros que contêm dados só de áudio. Alguns leitores, incluindo Azure Media Player (consulte [Este](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) poderá não conseguir processar essas sequências. Pode utilizar esta definição para forçar o codificador para adicionar uma pista de vídeo monocromática ao resultado nesse mesmo cenário. 

>[AZURE.NOTE]Forçar o codificador para inserir uma pista de vídeo de saída aumenta o tamanho da saída activo, e assim os custos suportados com o para a tarefa de codificação. Deve executar testes para verificar que este aumento resultante tem apenas um impacto conhecimentos básicos no seu encargos mensais.

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Inserir vídeo a apenas a velocidade mais baixa

Suponha que está a utilizar uma vários velocidade codificação de predefinir como ["H264 velocidade vários 720p"](https://msdn.microsoft.com/library/mt269960.aspx) codificar a sua entrado catálogo completo para streaming, que contém uma mistura de ficheiros de vídeo e ficheiros só de áudio. Neste cenário, quando a entrada de dados não dispõe de vídeo, que poderá pretende forçar o codificador para inserir uma pista de vídeo monocromática a apenas a velocidade mais baixa, por oposição a inserir vídeo a cada velocidade de saída. Para alcançar isto, é necessário especificar o sinalizador de "InsertBlackIfNoVideoBottomLayerOnly".

Pode efetuar qualquer uma do EEM predefinições documentado [aqui](https://msdn.microsoft.com/library/mt269960.aspx)e efetuar a alteração seguinte:

#### <a name="json-preset"></a>JSON predefinido

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML predefinido

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>Inserir o vídeo em todos os bitrates de saída

Suponha que está a utilizar uma vários velocidade codificação de predefinir como ["H264 velocidade vários 720p](https://msdn.microsoft.com/library/mt269960.aspx) codificar a sua entrado catálogo completo para streaming, que contém uma mistura de ficheiros de vídeo e ficheiros só de áudio. Neste cenário, quando a entrada de dados não dispõe de vídeo, que poderá pretende forçar o codificador para inserir uma pista de vídeo monocromática de todo o bitrates de saída. Isto garante que a saída activos são todas as homogéneos relativamente às número de faixas de vídeos e faixas de áudio. Para alcançar isto, é necessário especificar o sinalizador de "InsertBlackIfNoVideo".

Pode efetuar qualquer uma do EEM predefinições documentado [aqui](https://msdn.microsoft.com/library/mt269960.aspx)e efetuar a alteração seguinte:

#### <a name="json-preset"></a>JSON predefinido

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML predefinido
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>Rodar um vídeo

O [Padrão de descodificador de multimédia](media-services-dotnet-encode-with-media-encoder-standard.md) suporta rotação pelos ângulos de 0/90/180/270. O comportamento predefinido é "Automático", onde tenta para detetar os metadados de rotação no ficheiro de vídeo de entrada e o Adquirente na íntegra para a mesma. Inclua o seguinte elemento de **origens** para um das predefinições definido [aqui](http://msdn.microsoft.com/library/azure/mt269960.aspx):

### <a name="json-preset"></a>JSON predefinido

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>XML predefinido

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Além disso, consulte o artigo [neste](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation) tópico para obter mais informações sobre como o codificador interpreta as definições de largura e altura na predefinição, quando é acionada compensação de rotação.

Pode utilizar o valor "0" para indicar ao codificador ignorar metadados de rotação, se existir, no vídeo de entrada. 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também 

[Descrição geral de codificação de serviços de multimédia](media-services-encode-asset.md)
