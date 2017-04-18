<properties 
    pageTitle="Codecs e formatos de multimédia codificador padrão" 
    description="Este tópico fornece uma descrição geral de codecs e formatos de multimédia codificador padrão." 
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
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Codecs e formatos de padrão de descodificador de multimédia


Este documento contém uma lista de importar mais comuns e formatos de ficheiro de exportação que pode utilizar com o padrão de descodificador de multimédia.


##<a name="input-containerfile-formats"></a>Formatos de ficheiro/contentor de entrada

Formatos de ficheiro (extensões de ficheiro)|Suportada
---|---|---|---
FLV (com codecs h. 264 e AAC) (. flv)          |Sim 
MXF (.mxf)                  |Sim 
GXF (.gxf)                  |Sim 
MPEG2-PS, MPEG2-TS, 3GP (.ts,. PS, .3gp, .3gpp,. mpg)   |Sim 
Vídeo do Windows Media (. WMV) / ASF (. wmv,. asf) |Sim 
AVI (não comprimidas de 8 bits/10 bits) (. avi)|Sim 
MP4 (. mp4, .m4a, .m4v) / ISMV (.isma, .ismv)|Sim 
[Microsoft Digital Recording(DVR-MS) de vídeo](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr-ms) |Sim 
Matroska/WebM (.mkv)        |Sim 
Com ONDA/WAV (. wav) |Sim 
QuickTime (. mov) |Sim

>[AZURE.NOTE] Acima é uma lista das extensões de ficheiro mais geralmente detetadas. Media Encoder padrão suporta muitos outros (por exemplo: .m2ts, .mpeg2video,. qt). Se tentar codificar um ficheiro e recebe uma mensagem de erro sobre o formato não suportada, fornecer feedback [aqui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Formatos de áudio em contentores de entrada 

Media Encoder padrão suporta executar os seguintes formatos de áudio em contentores de entrada:

- MXF, GXF e QuickTime ficheiros que tenham faixas de áudio com estéreo intercalado ou 5.1 amostras

ou

- MXF, GXF e QuickTime ficheiros onde o áudio é executado como faixas PCM separadas, mas o mapeamento de canal (para estéreo ou 5.1) pode ser obtido os metadados de ficheiro

Tenha em atenção que suportem para mapeamento de canais explícitas/fornecidos pelo utilizador será fornecido no futuro próximo.


##<a name="input-video-codecs"></a>Codecs de vídeo de introdução

Codecs de vídeo de introdução|Suportada
---|---|---|---
AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra   |4 de 8 bits: 2:0 e 4:2:2 
DNxHD anotou (no MXF)                                 |Sim 
DVCPro/DVCProHD (no MXF)                            |Sim 
Vídeo digital (DV) (em ficheiros AVI)                   |Sim
JPEG 2000                                           |Sim 
MPEG-2 (por excesso para o perfil 422 e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Perfil até 422 
MPEG-1                                              |Sim 
VC-1/WMV9                                           |Sim 
Canopus Centro/HQX                                      |N 
MPEG-4 parte 2                                       |Sim 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sim 
YUV420 não comprimido ou mezzanine                   |Sim
Apple ProRes 422                                    |Sim
Apple ProRes 422 LT |Sim
Apple ProRes 422 Centro |Sim
Apple ProRes Proxy|Sim
Apple ProRes 4444 |Sim
Apple ProRes 4444 XQ |Sim



##<a name="input-audio-codecs"></a>Codecs de áudio de entrada

Codecs de áudio de entrada|Suportada
---|---|---|---
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim 
Camada MPEG 2|Sim 
MP3 (MPEG-1 a camada áudio 3)|Sim 
Áudio do Windows Media|Sim 
WAV/PCM|Sim 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim 
[Fornecidas](http://go.microsoft.com/fwlink/?LinkId=822667) |Sim 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim 
AMR (taxa; com várias ajustável)|Sim
AES (SMPTE 331 M e 302 M, AES3-2003)        |N 
Dolby® "e"                                    |N 
Dolby® Digital (AC3)                        |N 
Dolby® Digital Plus (AC3 "e")                 |N 


##<a name="output-formats-and-codecs"></a>Codecs e formatos de exportação

A tabela seguinte lista os formatos de ficheiro e de codecs que são suportados para exportar.


Formato de ficheiro|Codec de vídeo|Codec de áudio
---|---|---
MP4 <br/><br/>(incluindo multi velocidade MP4 contentores) |H. 264 (alto, principal e perfis do plano base)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2 TS |H. 264 (alto, principal e perfis do plano base)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Codificação de conteúdo a pedido com os serviços de multimédia do Microsoft Azure](media-services-encode-asset.md)

[Como codificar com o padrão de descodificador de multimédia](media-services-dotnet-encode-with-media-encoder-standard.md)
