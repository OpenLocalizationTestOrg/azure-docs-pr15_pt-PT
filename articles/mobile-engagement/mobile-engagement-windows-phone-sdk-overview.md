<properties 
    pageTitle="Descrição geral do Silverlight SDK do Windows Phone" 
    description="Descrição geral do Windows Phone Silverlight SDK para Azure Cativação móvel"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Descrição geral do Silverlight SDK para Azure Cativação móvel do Windows Phone

Comece aqui para obter detalhes sobre como integrar o Azure Mobile Cativação numa aplicação do Windows Phone Silverlight. Se gostaria experimente pela primeira vez, certifique-se de que concluir o nosso [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Clique para ver o [Conteúdo SDK](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Procedimentos de integração

1. Comece aqui: [como integrar o Mobile Cativação na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Para notificações: [como integrar o alcance (notificações) na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Marcar a execução de plano: [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Notas de lançamento

###<a name="330-04192016"></a>3.3.0 (19/04/2016)
Parte da nuget *MicrosoftAzure.MobileEngagement* compactar **v3.4.0**

-   Registos de consola do adicionados "TestLogLevel" API para ativar/desativar/filtro emitidos pelo SDK.

Para a versão anterior, consulte as [notas de lançamento concluída](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimentos de actualização

Se já tiver o integrado uma versão anterior do nosso SDK para a aplicação, tem de ter em conta os seguintes pontos ao atualizar o SDK.

Poderá ter de seguir vários procedimentos se perdeu várias versões do SDK. Consulte os [Procedimentos de actualização](mobile-engagement-windows-phone-upgrade-procedure.md)de concluída. Por exemplo, se migrar a partir do 0.10.1 para 0.11.0 que tem de pela primeira vez, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

###<a name="from-200-to-330"></a>A partir do 2.0.0 para 3.3.0

####<a name="test-logs"></a>Registos de teste

Registos de consola produzidos pelo SDK agora podem ser ativado/desactivado/filtrados. Para personalizar isto, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um do valor disponível a partir de `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Atualizar a partir de versões mais antigas

Consulte o artigo [Atualizar procedimentos](mobile-engagement-windows-phone-upgrade-procedure.md)
 
