<properties 
    pageTitle="Codecs e formatos de fluxo de trabalho do Media Encoder Premium | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral de codecs e formatos de formatos de fluxo de trabalho do Media Encoder Premium" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Codecs e formatos de fluxo de trabalho do Media Encoder Premium


>[AZURE.NOTE]Para as questões de codificador premium, e-mail mepd em Microsoft.com.
>
>Processador de multimédia de fluxo de trabalho do Media Encoder Premium outros fabricantes referido neste tópico não está disponível na China. 

Este documento contém uma lista de entrada e formatos de ficheiro de exportação e de codecs suportados pela versão do **Fluxo de trabalho do Media Encoder Premium** codificador público pré-visualização.

[Codecs e formatos de Worflow entrada do Media Encoder Premium](#input_formats)

[Codecs e formatos de exportação de Worflow de Premium de descodificador de multimédia](#output_formats)

**Fluxo de trabalho do Media Encoder Premium** suporta fechadas descritos [nesta](#closed_captioning) secção. 


##<a id="input_formats"></a>Fluxo de trabalho do Media Encoder Premium Codecs e formatos de entrada

A secção seguinte lista os formatos de ficheiro e de codecs compatível com este processador de multimédia como entrada.

###<a name="input-containerfile-formats"></a>Formatos de ficheiro/contentor de entrada

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Fluxos de transporte MPEG-2
- Fluxos de programa MPEG-2
- MPEG-4/MP4
- / ASF do Windows Media
- AVI (não comprimidas de 8 bits/10 bits)

###<a name="input-video-codecs"></a>Codecs de vídeo de introdução

- AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra
- DNxHD anotou (no MXF)
- DVCPro/DVCProHD (no MXF)
- JPEG2000
- MPEG-2 (por excesso para o perfil 422 e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Codecs de áudio de entrada

- AES (SMPTE 331 M e 302 M, AES3-2003)
- Dolby® "e"
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
- Camada MPEG 2
- MP3 (MPEG-1 a camada áudio 3)
- Áudio do Windows Media
- WAV/PCM
 
##<a id="output_format"></a>Codecs e formatos de exportação de fluxo de trabalho do Media Encoder Premium

A secção seguinte lista os formatos de ficheiro e de codecs que são suportados como saída a partir deste processador de multimédia.

###<a name="output-containerfile-formats"></a>Formatos de ficheiro/contentor de saída

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM e AS02)
- DPP (incluindo AS11)
- GXF
- MPEG-4/MP4
- / ASF do Windows Media
- AVI (não comprimidas de 8 bits/10 bits)
- Ficheiro formato de Smooth Streaming (PIFF 1.3)
- MPEG-TS 


###<a name="output-video-codecs"></a>Codecs de vídeo de saída

- AVC (h. 264; 8 bits; por excesso para o perfil de alto nível 5.2; 4 HD Ultra estanques aos K; Dentro de AVC)
- DNxHD anotou (no MXF)
- DVCPro/DVCProHD (no MXF)
- MPEG-2 (por excesso para o perfil 422 e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1
- Criação de miniaturas de JPEG

###<a name="output-audio-codecs"></a>Codecs de áudio de saída

- AES (SMPTE 331 M e 302 M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus ("e" AC3) até 7.1
- AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
- Camada MPEG 2
- MP3 (MPEG-1 a camada áudio 3)
- Áudio do Windows Media

##<a id="closed_captioning"></a>Suporte para as legendas fechadas

Em ingerir esta última, **O fluxo de trabalho do Media Encoder Premium** suporta:

1. Ficheiros de SCC
1. Ficheiros de SMPTE TT
1. CEA-608/CEA-708 – transportados como dados de utilizador (mensagens LEVADO de sequências ensino básico h. 264, ATSC/53, SCTE20) ou transportados como auxiliares dados em ficheiros MXF/GXF
1. Ficheiros de subtítulo STL

Em saída, estão disponíveis as seguintes opções:

1. CEA-608 para CEA 708 tradução
1. CEA 608/CEA 708 passar (incorporado em mensagens de LEVADO de sequências de ensino básico h. 264, ou realizados como auxiliares dados em ficheiros MXF)
1. SCC
1. SMPTE temporizado a texto (a partir da origem CEA 608 por SMPTE RP2052; incluindo DFXP a criação do ficheiro)
1. Ficheiro de ini esquerda para a direita
1. Fluxos de subtítulo DVB

Nota: nem todos os formatos de exportação acima são suportados para entrega de através de transmissão nos serviços de multimédia do Azure.

##<a name="known-issues"></a>Problemas conhecidos

Se o vídeo introdução não contiver legendas de áudio, o resultado elementos ainda conterá um ficheiro TTML vazio. 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
