<properties
    pageTitle="Configuração para Cativação de aplicações Universal do Windows SDK avançada"
    description="Opções avançadas de configuração para Azure Mobile Cativação com aplicações Universal do Windows"                    
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configuração para Cativação de aplicações Universal do Windows SDK avançada

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Este procedimento descreve como configurar várias opções de configuração para as aplicações do Azure Mobile Cativação Android.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Configuração avançada

### <a name="disable-automatic-crash-reporting"></a>Desactivar o relatório de falha de sistema automática

Pode desativar a falha automática funcionalidade de Cativação de elaboração de relatórios. Em seguida, quando ocorre uma exceção não processada, Cativação não acontece nada.

> [AZURE.WARNING] Se desativar esta funcionalidade, em seguida, quando ocorre uma falha de sistema não processada na sua aplicação Cativação não enviar que a falhar **e** não Fechar sessão e tarefas de implementação.

Para desativar falha automática relatórios, personalize a sua configuração dependendo da forma que declarado como:

#### <a name="from-engagementconfigurationxml-file"></a>A partir do `EngagementConfiguration.xml` ficheiro

Configurar falha de sistema do relatório para `false` entre `<reportCrash>` e `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>A partir do `EngagementConfiguration` objeto em tempo de execução

Defina falha de sistema do relatório para FALSO utilizando o objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Desactivar o relatório de tempo real

Por predefinição, os relatórios de serviço de Cativação inicia em tempo real. Se a sua aplicação relatórios registos frequentemente, é melhor para os registos de memória intermédia e reportá-las todas ao mesmo tempo numa base de tempo regular. Esta opção é denominada "rajada modo".

Para fazê-lo, contacte o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. Sempre que pretender reativar o registo em tempo real, ligue para o método de sem qualquer parâmetro ou com o valor 0.

O modo de rajada ligeiramente aumenta a vida de baterias, mas tem um impacto no Monitor Cativação: todos os duração sessões e tarefas de implementação são arredondados para o limiar de rajada (portanto, sessões e tarefas mais pequena do que o limiar de rajada poderão não estar visível). Recomendamos que utilize um limite de rajada já não que 30000 (30s). Registos guardados estão limitados a 300 itens. Se enviar for demasiado tempo, pode perder alguns registos.

> [AZURE.WARNING] Não pode ser configurado o limiar de rajada a um período de menor que um segundo. Se fazê-lo, o SDK mostra um rastreio com o erro e automaticamente repõe o valor predefinido, igual a zero segundos. Isto accionadores SDK para comunicar os registos em tempo real.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
