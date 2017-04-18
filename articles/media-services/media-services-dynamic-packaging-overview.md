<properties
    pageTitle="Descrição geral de embalagem dinâmico | Microsoft Azure"
    description="O tópico dá e a descrição geral da embalagem dinâmica."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Embalagem dinâmica

##<a name="overview"></a>Descrição geral

Formatos de proteção de conteúdo a uma variedade de tecnologias de cliente e dos serviços de multimédia do Microsoft Azure pode ser utilizados para fornecer muitos origem formatos de multimédia, formatos de multimédia (por exemplo, iOS, XBOX, Silverlight, Windows 8). Estes clientes compreender protocolos diferentes, por exemplo iOS requer um formato de HTTP Live transmissão (HLS) V4 e Silverlight e Xbox necessitam de transmissão suaves. Se tiver um conjunto de velocidade ajustável (multi-velocidade) MP4 ficheiros (ISO Base Media 14496 12) ou um conjunto de velocidade ajustável transmissão suaves ficheiros que pretende servir para clientes que compreender MPEG travessão, HLS ou transmissão suaves, deverá tira partido de embalagem dinâmico dos serviços de multimédia.

Com o embalagem dinâmica tudo o que necessita é criar um ativo que contém um conjunto de ficheiros de velocidade ajustável MP4 ou transmissão suaves ajustável velocidade. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, a transmissão da pedido no servidor irá garantir que recebe a sequência de no protocolo que escolheu. Como resultado, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e serviço dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente.

O diagrama seguinte mostra a codificação tradicional e o fluxo de trabalho da embalagem estático.

![Codificação estático](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama seguinte mostra o fluxo de trabalho de embalagem dinâmicos.

![Codificação dinâmicos](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Para tirar partido da embalagem dinâmico, terá de obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [como escala dos serviços de multimédia](media-services-portal-manage-streaming-endpoints.md).

##<a name="common-scenario"></a>Cenário comum

1. Carregar um ficheiro de entrada (denominado um ficheiro de mezzanine). Por exemplo, h. 264, MP4 ou WMV (para a lista de formatos suportados consulte o artigo [Formatos suportados pelo padrão de descodificador de multimédia](media-services-media-encoder-standard-formats.md).

1. Codificar o ficheiro mezzanine para conjuntos de velocidade ajustável h. 264 MP4.

1. Publica o elemento que contém a velocidade de bits ajustável MP4 definir criando o localizador de sucessivamente a pedido.

1. Crie os URLs transmissão para aceder e transmitir em fluxo seu conteúdo.


##<a name="preparing-assets-for-dynamic-streaming"></a>Preparar a elementos de transmissão dinâmicos

Para preparar o activo para transmissão dinâmico tem duas opções:

1. [Carregar um ficheiro de modelo global](media-services-dotnet-upload-files.md).
2. [Utilizar o codificador Media Encoder padrão para produzir conjuntos de velocidade ajustável h. 264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Sequência de seu conteúdo](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formatos que não são suportados pelo embalagem dinâmica

Os seguintes formatos de ficheiro de origem não são suportados pelo embalagem dinâmica.

- Ficheiros do Dolby digital mp4.
- Dolby digitais suaves os ficheiros.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
