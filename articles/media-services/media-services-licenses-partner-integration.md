<properties 
    pageTitle="Utilizar parceiros a Widevine licenças para dos serviços de multimédia do Azure | Microsoft Azure" 
    description="Este artigo descreve como pode utilizar serviços de multimédia do Azure (MGA) para a prestação uma sequência de que está encriptada dinamicamente pela AMS com PlayReady e Widevine DRMs. A licença PlayReady provêm de servidor de licenças PlayReady de serviços de multimédia e Widevine licença é entregue ao servidor de licenças castLabs." 
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
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilizar parceiros a Widevine licenças para dos serviços de multimédia do Azure

##<a name="overview"></a>Descrição geral

Serviços de multimédia do Microsoft Azure permite-lhe entregar MPEG-TRAÇO protegido com Widevine DRM, que está encriptado pela especificação de encriptação comum (CENC).

Começar com o suporte de dados serviços .NET SDK versão 3.5.2, dos serviços de multimédia permite-lhe configurar Widevine licença modelo e obter Widevine licenças. Também pode utilizar os parceiros de AMS seguintes para ajudá-lo a entregar Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

Pode utilizar [castLabs](http://castlabs.com/company/partners/azure/) para a prestação Widevine licenças. Para obter mais informações, consulte o artigo [utilizar castLabs para a prestação licenças DRM dos serviços de multimédia do Azure](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

Pode utilizar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para a prestação Widevine licenças. Para obter mais informações, consulte o artigo [Utilizar Axinom para a prestação licenças DRM dos serviços de multimédia do Azure](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Utilizar a encriptação comuns dinâmica PlayReady e/ou Widevine](media-services-protect-with-drm.md)

[Blogue do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

