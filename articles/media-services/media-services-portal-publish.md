<properties
    pageTitle="  Publicar conteúdo com o portal do Azure | Microsoft Azure"
    description="Neste tutorial explica os passos do seu conteúdo com o Azure portal de publicação."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="juliako"/>

# <a name="publish-content-with-the-azure-portal"></a>Publicar conteúdo com o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [RESTO](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Descrição geral

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Para fornecer o utilizador com um URL que pode ser utilizado para transmitir em fluxo ou transferir o seu conteúdo, tem primeiro de "publicação" a sua elementos criando um localizador. Locator fornece acesso aos ficheiros contidas num elemento. Dos serviços de multimédia suporta dois tipos de Locator: 

- Transmissão Locator (OnDemandOrigin), utilizado para a transmissão ajustável (por exemplo, a sequência de TRAÇO MPEG, HLS ou transmissão suave). Para criar um localizador de transmissão seu activo tem de conter um ficheiro de .ism. 
- Gradual Locator (SA), utilizado para a entrega de vídeo através de transferência gradual.


Um URL de transmissão tem o seguinte formato e utilizá-lo para reproduzir activos transmissão suaves.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar um HLS transmissão URL, acrescentar (formato = m3u8 aapl) para o URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar um TRAÇO MPEG transmissão URL, acrescentar (formato = mpd-tempo-LCR) para o URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Um URL de SA tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Para mais informações, consulte o artigo [Descrição geral do conteúdo entregar](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Se tiver utilizado o portal para criar Locator antes de Março de 2015, Locator com uma data de expiração de dois anos foram criado.  

Para atualizar uma data de validade um locator, utilize [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) APIs. Tenha em atenção que quando atualiza a data de expiração de um localizador de SA, o URL for alterado.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Utilizar o portal para publicar um ativo

Utilizar o portal para publicar um ativo, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
1. Selecione **Definições** > **elementos**.
1. Selecione os elementos que pretende publicar.
1. Clique no botão **Publicar** .
1. Selecione o tipo de localizador.
2. Prima a **Adicionar**.

    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

O URL será adicionado à lista de **URLs publicado**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo a partir do portal

Portal do Azure fornece um leitor de conteúdo que pode utilizar para testar o seu vídeo.

Clique no vídeo pretendido e, em seguida, clique no botão **Reproduzir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações aplicam-se:

- Certifique-se de que publicou o vídeo.
- Este **Media player** reproduz da predefinição de transmissão de ponto final. Se pretende reproduzir a partir de um ponto final a transmissão de não predefinida, clique em para copiar o URL e utilizar o leitor de outra. Por exemplo, [Leitor de serviços de multimédia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- Tem de executar o ponto final de transmissão a partir do qual está a transmissão.  
- Para transmitir em fluxo a partir de um ponto final de transmissão, deve adicionar pelo menos uma unidade de transmissão. Para mais informações, consulte o artigo [neste](media-services-portal-scale-streaming-endpoints.md) tópico.   

##<a name="next-steps"></a>Próximos passos

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


