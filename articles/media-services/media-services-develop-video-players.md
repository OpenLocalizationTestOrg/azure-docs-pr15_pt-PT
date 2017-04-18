<properties 
    pageTitle="Desenvolver aplicações de leitor de vídeo" 
    description="O tópico fornece ligações para o leitor de quadros e plug-ins que pode utilizar para desenvolver o seus próprio aplicações de cliente podem consumir transmissão de multimédia de serviços de multimédia." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="develop-video-player-applications"></a>Desenvolver aplicações de leitor de vídeo

##<a name="overview"></a>Descrição geral

Azure dos serviços de multimédia fornece as ferramentas necessárias para criar aplicações de leitor de cliente dinâmicos, complexos para a maioria dos plataformas, incluindo: iOS, dispositivos Android, Windows, Windows Phone, Xbox, dispositivos e Set-top caixas. Este tópico também fornece hiperligações para SDK e estruturas de leitor de trabalho que pode utilizar para desenvolver o seus próprio aplicações de cliente podem consumir transmissão de multimédia de serviços de multimédia do Azure.

##<a name="azure-media-player"></a>Reprodução de multimédia do Microsoft Azure

[Azure Media Player](http://aka.ms/ampinfo) é um leitor de vídeo da web construído para reproduzir conteúdo multimédia a partir dos serviços de multimédia do Microsoft Azure numa grande variedade de dispositivos e browsers. Azure Media Player utiliza normas da indústria, como em HTML5, extensões de origem de multimédia (MSE) e extensões de multimédia encriptados (EME) para fornecer uma experiência de transmissão ajustável enriquecida. Quando essas normas não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza Flash e Silverlight como tecnologia de contingência. Independentemente da tecnologia de reprodução utilizada, os programadores terá uma interface JavaScript unificada para aceder a APIs. Esta opção permite-para conteúdo fornecido dos serviços de multimédia do Azure para ser reproduzido ao longo de uma vasta gama de dispositivos e browsers sem qualquer esforço adicional.

Dos serviços de multimédia do Microsoft Azure permite conteúdo a ser fornecido para cima com travessão, transmissão suaves e HLS transmissão formatos para reproduzir conteúdo. Azure Media Player leva em consideração estes vários formatos e reproduz automaticamente a ligação melhor com base nas capacidades de plataforma/browser. Também permite dos serviços de multimédia do Microsoft Azure para encriptação dinâmica de activos com encriptação PlayReady ou AES de 128-bit encriptação de envelope. Azure Media Player permite desencriptar de PlayReady e AES de 128-bit conteúdo encriptado quando configuradas corretamente. 

Para obter mais informações:

- [Reprodução de multimédia do Microsoft Azure](http://aka.ms/ampinfo)
- [Documentação do leitor de multimédia do Microsoft Azure](http://aka.ms/ampdocs) 
- [Azure Media Player introdução iniciado no blogue](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Inscrever-se para se manter atualizado com as mais recentes do Azure Media Player](http://aka.ms/ampsignup)
- [Adicionar novos pedidos de funcionalidade, ideias e comentários](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Outras ferramentas para criação de aplicações do leitor

Também pode utilizar qualquer um dos seguintes SDK:

- [Lisa transmissão SDK do cliente](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Suaves aplicação da loja Windows em sequência](media-services-build-smooth-streaming-apps.md)
- [Plataforma de suporte da Microsoft: Quadro de leitor](http://playerframework.codeplex.com/) 
- [HTML5 Documentação do Framework Player](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft suave transmissão Plug-in para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licenciamento Microsoft® suaves transmissão cliente migrar Kit](http://aka.ms/sspk) 
- [XBOX desenvolvimento de aplicação de vídeo](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Publicidade

Azure dos serviços de multimédia fornece suporte para a inserção de ad através da plataforma do Windows Media: Player quadros. Quadros de leitor com suporte de ad estão disponíveis para dispositivos Windows 8, Windows Phone 8, Silverlight e iOS. Cada quadro de leitor contém o código de exemplo que mostra como implementar uma aplicação do leitor. Existem três tipos diferentes de anúncios que pode inserir os ficheiros de multimédia:

Linear – anúncios de moldura completa que interromper o vídeo principal

Não lineares – anúncios de sobreposição que são apresentados como estiver a reproduzir o vídeo principal, normalmente, um logótipo ou outra imagem estática colocado dentro do leitor

Acompanha – anúncios que são apresentadas fora do leitor

Anúncios podem ser colocados em qualquer ponto na linha de tempo o vídeo principal. Tem de indicar ao Media player quando reproduzir o ad e que anúncios para reproduzir. Isto é feito utilizando um conjunto de ficheiros baseado em XML padrão: vídeo Ad serviço modelo (VAST), Digital vídeo vários Ad lista pessoal (VMAP), modelo de Sequencing Abstratos multimédia (LATA) e Digital vídeo Player Ad Interface definição (VPAID). Ficheiros grandes especificam que anúncios para apresentar. Ficheiros VMAP especificam quando reproduzir vários anúncios e contêm XML grande. Ficheiros de LATA são outra forma de anúncios de sequência que também pode conter XML grande. Ficheiros VPAID definem uma interface entre o leitor de vídeo e o ad ou servidor dos ad. Para mais informações, consulte o artigo [Inserir anúncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obter informações sobre o suporte de anúncios e as legendas fechada no Live transmissão vídeos, consulte o artigo [suportadas legendas fechadas e padrões de inserção de Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Incorporar um TRAÇO MPEG ajustável transmissão de vídeo numa aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js repositório](https://github.com/Dash-Industry-Forum/dash.js)
 
