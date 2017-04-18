<properties 
    pageTitle="Reproduzir o conteúdo | Microsoft Azure" 
    description="Este tópico indica leitores existentes que pode utilizar para reproduzir o conteúdo." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>Reproduzir o seu conteúdo com jogadores existentes

Azure dos serviços de multimédia suporta vários formatos de transmissão populares, como transmissão suaves, HTTP Live transmissão e MPEG-traço. Este tópico encaminha-o para leitores de existentes que pode utilizar para testar a sua sequências.

>[AZURE.NOTE]Para reproduzir dinamicamente empacotados ou conteúdo dinamicamente encriptado, certifique-se obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual pretende distribuir o conteúdo. Para obter informações sobre dimensionamento transmissão de unidades, consulte o artigo: [como dimensionar transmissão unidades](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>O Azure portal dos serviços de multimédia conteúdo Media player

Portal do **Azure** fornece um leitor de conteúdo que pode utilizar para testar o seu vídeo.

Clique no vídeo pretendido (Certifique-se de que foi [publicada](media-services-portal-publish.md)) e clique no botão **Reproduzir** na parte inferior do portal.

Algumas considerações aplicam-se:

- O **LEITOR de conteúdo de serviços de multimédia** reproduz da predefinição de transmissão de ponto final. Se pretende reproduzir a partir de um ponto final a transmissão de não predefinida, utilize outro jogador. Por exemplo, [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Reprodução de multimédia do Microsoft Azure

Utilizar o [Leitor de multimédia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproduzir o conteúdo (limpar ou protegido) em qualquer um dos seguintes formatos:

- Transmissão suaves
- MPEG TRAVESSÃO
- HLS
- MP4 gradual


###<a name="flash-player"></a>O Flash Player

####<a name="aes-encrypted-with-token"></a>Encriptação AES com Token

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Leitores do Silverlight

####<a name="monitoring"></a>Monitorização

[http://SMF.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady com Token

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Leitores de TRAÇO

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Outros

Para testar URLs HLS também pode utilizar:

- **Safari** num dispositivo iOS ou
- **3ivx HLS leitor** no Windows.

##<a name="developing-video-players"></a>Desenvolver leitores de vídeo

Para obter informações sobre como desenvolver o seus próprio leitores, consulte o artigo [leitores de vídeo do desenvolvimento](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
