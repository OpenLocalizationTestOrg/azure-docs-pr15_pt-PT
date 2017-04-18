<properties
    pageTitle="Como recortar vídeo | Microsoft Azure"
    description="Este artigo mostra como pode recortar vídeos com Media Encoder padrão."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Vídeos de recorte com Media Encoder padrão

Pode utilizar Media Encoder padrão (EEM) para recortar os dados introduzidos vídeo. Recorte é o processo de selecionar uma janela retangular dentro no fotograma de vídeo e codificação apenas os pixels dentro essa janela. O diagrama seguinte ajuda a ilustrar o processo.

![Recortar um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que tem como entrada um vídeo que tem uma resolução de 1920x1080 pixels (proporção de 16:9), mas tem barras pretas (montantes caixas) à esquerda e à direita, para que apenas uma janela de 4:3 ou 1440 x 1080 pixels contém o vídeo ativo. Pode utilizar EEM para recortar ou editar saída as barras de pretas e descodificar a região 1440 x 1080.

Recorte no EEM é uma fase de pré-processamento, para que os parâmetros de recortes a predefinição de codificação aplicam-se para o vídeo introdução original. Codificação é uma fase subsequente e aplicam as definições de altura/largura para a *pré-processadas* vídeo e não para o vídeo original. Ao estruturar o seu predefinido que tem de fazer o seguinte: (um) selecione os parâmetros de recortar, com base no vídeo entrado original e (b) selecione seu codificar definições com base no vídeo recortado. Se não corresponder a codificar definições para o vídeo recortada, o resultado não será como esperado.

O tópico [seguinte](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) mostra como criar uma tarefa de codificação com EEM e como especificar um personalizado predefinido para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criar uma configuração personalizada

No exemplo mostrado no diagrama:

1. Entrada original é 1920x1080
1. Precisa de ser recortada para um resultado de 1440 x 1080, que está centrado na moldura de entrada
1. Isto significa que um desvio X (1920 – 1440) / 2 = 240 e um Y desvio de zero
1. A largura e altura do retângulo de recorte estão 1440 e 1080, respetivamente
1. No palco da codificar, faça é produzir três camadas, são resoluções 1440 x 1080, 960 x 720 e 480 x 360, respetivamente

###<a name="json-preset"></a>JSON predefinido


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


##<a name="restrictions-on-cropping"></a>Restrições de recorte

A funcionalidade de recorte se destinar a ser manual. Seria necessário carregar o vídeo introdução para uma ferramenta de edição adequada que lhe permite selecionar molduras de interesse, posicione o cursor para determinar desvios para o retângulo de recorte, para determinar a predefinição de codificação que está optimizada para esse etc de vídeo, específico. Esta funcionalidade não se destinar a ativar coisas como: deteção automática e a remoção de limites de preto letterbox/pillarbox no seu vídeo de entrada.

Restrições seguintes aplicam-se para a funcionalidade de recorte. Se estes não forem cumpridas, o codificar tarefa pode falhar ou produzir um resultado inesperado.

1. A cocriação ordenadas e o tamanho do retângulo de recorte, tem de se encaixam o vídeo introdução
1. Tal como mencionado acima, a largura e altura nas definições de codificar têm de corresponder à recortadas de vídeo
1. Recorte aplica-se ao vídeos capturados no modo horizontal (ou seja, não se aplica às vídeos gravados com um smartphone mantidas na vertical ou no modo de vertical)
1. Melhor se adequa ao vídeo gradual capturado com pixels ao quadrado

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Passo seguinte
 
Consulte o artigo Azure dos serviços de multimédia caminhos de aprendizagem para o ajudar a obter informações sobre funcionalidades excelentes disponibilizadas pelos AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
