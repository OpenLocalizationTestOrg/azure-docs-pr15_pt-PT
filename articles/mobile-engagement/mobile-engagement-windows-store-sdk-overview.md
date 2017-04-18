<properties
    pageTitle="Integração com o Windows Universal SDK"
    description="Universal integração com o Windows para SDK para Azure Cativação móvel"                                     
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integração com o Windows Universal SDK para Azure Cativação móvel

Este documento descreve todas as integração e configuração opções disponíveis para o Azure Mobile Cativação Universal SDK do Windows.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, primeiro tem de concluir o nosso [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Funcionalidades avançadas

### <a name="reporting-features"></a>Funcionalidades de relatório
Pode adicionar estas funcionalidades:

1. [Opções de elaboração de relatórios avançadas](mobile-engagement-windows-store-advanced-reporting.md)

2. [Opções de configuração avançadas](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notificações

[Como integrar o alcance (notificações) na sua aplicação Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementação de plano de etiqueta:

[Como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Notas de lançamento

###<a name="340-04192016"></a>3.4.0 (19/04/2016)

-   Melhorias de sobreposição de atingir.
-   Registos de consola do adicionados "TestLogLevel" API para ativar/desativar/filtro emitidos pelo SDK.
-   Inicie o fixas na atividade notificações filtragem à primeira atividade não apresentada na aplicação.

Para versões anteriores, consulte as [notas de lançamento concluída](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimentos de actualização

Se já tiver o integrado uma versão anterior do Cativação para a aplicação, tem de ter em conta os seguintes pontos ao atualizar o SDK.

Se não atendidas várias versões do SDK, poderá ter de seguir várias procedimentos. Consulte os [Procedimentos de actualização](mobile-engagement-windows-store-upgrade-procedure.md)de concluída. Por exemplo, se migrar a partir do 0.10.1 para 0.11.0 que tem de pela primeira vez, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

###<a name="from-330-to-340"></a>A partir do 3.3.0 para 3.4.0

####<a name="test-logs"></a>Registos de teste

Registos de consola produzidos pelo SDK agora podem ser ativado/desactivado/filtrados. Para personalizar, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis a partir de `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Recursos

Sobreposição de atingir foi melhorada. É parte dos recursos de pacote SDK NuGet.

Ao atualizar para a nova versão do SDK, pode escolher se pretende manter os seus ficheiros existentes a partir da pasta de sobreposição dos seus recursos ou não:

* Se a sobreposição anterior está a trabalhar para si ou estar a integrar o `WebView` elementos manualmente, em seguida, pode optar por manter o seu sair ficheiros, continuará a funcionar.
* Para atualizar a sobreposição de nova, substituir a totalidade `overlay` pasta a partir do seu recursos pelo novo a partir do pacote SDK (UWP aplicações: após a atualização, pode obter a nova pasta de sobreposição do perfil de utilizador %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Utilizar a nova sobreposição substitui as personalizações efetuadas na versão anterior.

### <a name="upgrade-from-older-versions"></a>Atualizar a partir de versões mais antigas

Consulte o artigo [Atualizar procedimentos](mobile-engagement-windows-store-upgrade-procedure.md)
