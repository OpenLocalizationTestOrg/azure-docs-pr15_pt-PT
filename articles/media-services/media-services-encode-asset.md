<properties 
    pageTitle="Descrição geral e comparação do Azure no codificadores de multimédia pedido | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral e comparação do Azure no codificadores de multimédia do pedido." 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Descrição geral e comparação do Azure no codificadores de multimédia de procura

##<a name="encoding-overview"></a>Descrição geral de codificação

Azure dos serviços de multimédia fornece várias opções para a codificação de multimédia na nuvem.

Quando começando dos serviços de multimédia, é importante compreender a diferença entre os formatos de ficheiro e de codecs.
Codecs estão o software que implementa dos algoritmos compressão/descompressão Considerando que formatos de ficheiro são contentores que mantenha o vídeo comprimido.

Dos serviços de multimédia fornece embalagem dinâmica que permite-lhe distribuir o conteúdo de velocidade ajustável MP4 ou transmissão suaves codificado no transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) sem ter de voltar Compactar para estes formatos de transmissão.

Para tirar partido da [embalagem dinâmica](media-services-dynamic-packaging-overview.md), terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de velocidade ajustável MP4 ficheiros ou de velocidade ajustável transmissão suaves (os passos de codificação são demonstrados mais tarde neste tutorial).
- Obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [como a pedido transmissão reservadas unidades da escala](media-services-portal-manage-streaming-endpoints.md).

Dos serviços de multimédia suporta o seguinte procedimento no codificadores pedido que são descritos neste artigo:

- [Padrão de descodificador de multimédia](media-services-encode-asset.md#media-encoder-standard)
- [Fluxo de trabalho do Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Este artigo fornece uma breve descrição geral da pedido codificadores de multimédia e fornece ligações para artigos que fornecem informações mais detalhadas. O tópico fornece também comparação entre os codificadores.

Tenha em atenção que por predefinição cada conta dos serviços de multimédia pode ter uma tarefa de codificação ativa cada vez. É possível reservar unidades de codificação que permitem que tenha várias tarefas codificação em execução em simultâneo, um para cada unidade reservada codificação de compras. Para obter informações, consulte o artigo [dimensionamento codificação de unidades](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Padrão de descodificador de multimédia

###<a name="how-to-use"></a>Como utilizar

[Como codificar com o padrão de descodificador de multimédia](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Formatos

[Codecs e formatos](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Predefinições

Media Encoder padrão está configurado utilizando um do codificador predefinições descritas [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadados de entrada e saído

Os metadados de entrada codificadores são descrito [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Os metadados de saída codificadores são descrito [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Gerar miniaturas

Para obter informações, consulte o artigo [como gerar miniaturas utilizando Media Encoder padrão](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Cortar vídeos (recorte)

Para obter informações, consulte o artigo [como cortar vídeos utilizando o padrão de descodificador de multimédia](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Criar sobreposições

Para obter informações, consulte o artigo [como criar sobreposições utilizando Media Encoder padrão](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Consulte também

[O blogue dos serviços de multimédia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Fluxo de trabalho do Media Encoder Premium

###<a name="overview"></a>Descrição geral

[Introdução ao Premium codificação de multimédia do Microsoft Azure serviços](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Como utilizar

Fluxo de trabalho do Media Encoder Premium está configurado para utilizar fluxos de trabalho complexos. Ficheiros de fluxo de trabalho podem ser criados e actualizados utilizando a ferramenta de [Estruturador de fluxo de trabalho](media-services-workflow-designer.md) .

[Como utilizar Premium codificação de multimédia do Microsoft Azure serviços](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Problemas conhecidos

Se o vídeo introdução não contiver legendas de áudio, o resultado elementos ainda conterá um ficheiro TTML vazio. 


##<a id="compare_encoders"></a>Comparar codificadores

###<a id="billing"></a>Indicador de faturação utilizado por cada encoder

Nome de processador de multimédia|Preços aplicável|Notas
---|---|---
**Padrão de descodificador de multimédia** |DESCODIFICADOR|Codificação tarefas será cobrado de acordo com o tamanho da saída activo, no GBytes, à velocidade especificada [aqui][1], sob a coluna CODIFICADOR.
**Fluxo de trabalho do Media Encoder Premium** |DESCODIFICADOR PREMIUM|Codificação tarefas será cobrado de acordo com o tamanho da saída activo, no GBytes, à velocidade especificada [aqui][1], sob a coluna CODIFICADOR PREMIUM.


Esta secção compara as funcionalidades de codificação do **Fluxo de trabalho do Media Encoder Premium**e **Media Encoder padrão** .


###<a name="input-containerfile-formats"></a>Formatos de ficheiro/contentor de entrada

Formatos de ficheiro/contentor de entrada|Padrão de descodificador de multimédia|Fluxo de trabalho do Media Encoder Premium
---|---|---
Adobe® Flash® F4V           |Sim|Sim
MXF/SMPTE 377M              |Sim|Sim
GXF                         |Sim|Sim
Fluxos de transporte MPEG-2    |Sim|Sim
Fluxos de programa MPEG-2      |Sim|Sim
MPEG-4/MP4                  |Sim|Sim
/ ASF do Windows Media           |Sim|Sim
AVI (não comprimidas de 8 bits/10 bits)|Sim|Sim
3GPP/3GPP2                  |Sim|N
Ficheiro formato de Smooth Streaming (PIFF 1.3)|Sim|N
[Microsoft Digital Recording(DVR-MS) de vídeo](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sim|N
Matroska/WebM               |Sim|N
QuickTime (. mov) |Sim|N

###<a name="input-video-codecs"></a>Codecs de vídeo de introdução

Codecs de vídeo de introdução|Padrão de descodificador de multimédia|Fluxo de trabalho do Media Encoder Premium
---|---|---
AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra   |4 de 8 bits: 2:0 e 4:2:2|Sim
DNxHD anotou (no MXF)                                 |Sim|Sim
DVCPro/DVCProHD (no MXF)                            |Sim|Sim
JPEG2000                                            |Sim|Sim
MPEG-2 (por excesso para o perfil 422 e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Perfil até 422|Sim
MPEG-1                                              |Sim|Sim
Windows Media Video/VC-1                            |Sim|Sim
Canopus Centro/HQX                                      |N|N
MPEG-4 parte 2                                       |Sim|N
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sim|N
Apple ProRes 422    |Sim|N
Apple ProRes 422 LT |Sim|N
Apple ProRes 422 Centro |Sim|N
Apple ProRes Proxy|Sim|N
Apple ProRes 4444 |Sim|N
Apple ProRes 4444 XQ |Sim|N

###<a name="input-audio-codecs"></a>Codecs de áudio de entrada

Codecs de áudio de entrada|Padrão de descodificador de multimédia|Fluxo de trabalho do Media Encoder Premium
---|---|---
AES (SMPTE 331 M e 302 M, AES3-2003)        |N|Sim
Dolby® "e"                                    |N|Sim
Dolby® Digital (AC3)                        |N|Sim
Dolby® Digital Plus (AC3 "e")                 |N|Sim
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim
Camada MPEG 2|Sim|Sim
MP3 (MPEG-1 a camada áudio 3)|Sim|Sim
Áudio do Windows Media|Sim|Sim
WAV/PCM|Sim|Sim
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim|N
[Fornecidas](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Sim|N
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim|N


###<a name="output-containerfile-formats"></a>Formatos de ficheiro/contentor de saída

Formatos de ficheiro/contentor de saída|Padrão de descodificador de multimédia|Fluxo de trabalho do Media Encoder Premium
---|---|---
Adobe® Flash® F4V|N|Sim
MXF (OP1a, XDCAM e AS02)|N|Sim
DPP (incluindo AS11)|N|Sim
GXF|N|Sim
MPEG-4/MP4|Sim|Sim
MPEG-TS|Sim|Sim
/ ASF do Windows Media|N|Sim
AVI (não comprimidas de 8 bits/10 bits)|N|Sim
Ficheiro formato de Smooth Streaming (PIFF 1.3)|N|Sim

###<a name="output-video-codecs"></a>Codecs de vídeo de saída

Codecs de vídeo de saída|Padrão de descodificador de multimédia|Fluxo de trabalho do Media Encoder Premium
---|---|---
AVC (h. 264; 8 bits; por excesso para o perfil de alto nível 5.2; 4 HD Ultra estanques aos K; Dentro de AVC)|8 apenas bit 4:2:0|Sim
DNxHD anotou (no MXF)|N|Sim
MPEG-2 (por excesso para o perfil 422 e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|N|Sim
MPEG-1|N|Sim
Windows Media Video/VC-1|N|Sim
Criação de miniaturas de JPEG|N|Sim

###<a name="output-audio-codecs"></a>Codecs de áudio de saída

Codecs de áudio de saída|Padrão de descodificador de multimédia|Fluxo de trabalho do Media Encoder Premium
---|---|---
AES (SMPTE 331 M e 302 M, AES3-2003)|N|Sim
Dolby® Digital (AC3)|N|Sim
Dolby® Digital Plus ("e" AC3) até 7.1|N|Sim
AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)|Sim|Sim
Camada MPEG 2|N|Sim
MP3 (MPEG-1 a camada áudio 3)|N|Sim
Áudio do Windows Media|N|Sim


##<a name="error-codes"></a>Códigos de erro  

A tabela seguinte lista os códigos de erro que podem ser devolvidos caso foi detectado um erro durante a execução codificação de tarefa.  Para obter detalhes do erro no seu código .NET, utilize a classe de [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Para obter detalhes do erro no seu código de descanso, utilize o [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

ErrorDetail.Code|Causas possíveis para o erro
-----|-----------------------
Desconhecido| Erro desconhecido durante a execução da tarefa
ErrorDownloadingInputAssetMalformedContent|Categoria de erros que abrange os erros de transferência entrado elementos como os nomes de ficheiro danificado, igual a zero ficheiros de comprimento, incorretos formatos e assim sucessivamente.
ErrorDownloadingInputAssetServiceFailure|Categoria de erros que abrange os problemas de num lado do serviço - existência de erros de armazenamento ou rede de exemplo durante a transferência.
ErrorParsingConfiguration|Categoria de erros de tarefas onde <see cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo a configuração não é predefinida de um sistema válido ou se contém XML inválido.
ErrorExecutingTaskMalformedContent|Categoria de erros durante a execução da tarefa onde problemas dentro de ficheiros de multimédia entrada causam a falha.
ErrorExecutingTaskUnsupportedFormat|Categoria de erros de onde o processador de multimédia não é possível processar ficheiros fornecidos - formato media não suportadas ou não corresponder a configuração. Por exemplo, está a tentar produzir um resultado só de áudio a partir de um ativo que tem apenas vídeo
ErrorProcessingTask|Categoria de outros erros que o processador de multimédia encontra durante o processamento da tarefa que estão relacionado com o conteúdo.
ErrorUploadingOutputAsset|Categoria de erros ao carregar os elementos de saída
ErrorCancelingTask|Categoria de erros para cobrir falhas ao tentar cancelar a tarefa
TransientError|Categoria de erros para cobrir breves problemas (por ex. problemas de rede temporários com o armazenamento do Windows Azure)


Para obter ajuda da equipa **Dos serviços de multimédia** , abra uma [bilhetes de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Artigos relacionados

- [Executar tarefas de codificação avançadas através da personalização predefinições padrão de descodificador de multimédia](media-services-custom-mes-presets-with-dotnet.md)
- [Quotas e limitações](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
