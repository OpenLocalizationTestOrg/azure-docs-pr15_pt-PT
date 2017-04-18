<properties
    pageTitle="Opções avançadas de elaboração de relatórios para Azure Mobile Cativação Android SDK"
    description="Descreve como fazer relatórios avançados para capturar analytics para Azure Mobile Cativação Android SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>As avançadas de relatório com Cativação no Android

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tópico descreve cenários de elaboração de relatórios adicionais na sua aplicação Android. Pode aplicar estas opções para a aplicação que criou no tutorial [Introdução](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Tutorial se tiver concluído a foi deliberadamente direta e simples, mas não existem são opções avançadas de pode escolher.

## <a name="modifying-your-activity-classes"></a>Modificar o `Activity` classes

[Introdução ao tutorial](mobile-engagement-android-get-started.md), foi tudo o que tinha fazer tornar o `*Activity` subclasses herdam a correspondente `Engagement*Activity` aulas. Por exemplo, se a sua atividade legada expandido `ListActivity`, fariam expandir `EngagementListActivity`.

> [AZURE.IMPORTANT] Quando utilizar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se qualquer uma chamada para `requestWindowFeature(...);` é tornado antes das chamadas para `super.onCreate(...);`, caso contrário, ocorre uma falha de sistema.

Pode encontrar estas classes na `src` pasta e pode copiá-los para o seu projeto. As classes também estão a ser o **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: chamar `startActivity()` e `endActivity()` manualmente

Se não conseguir ou não pretende que seja sobrecarga seu `Activity` classes, em vez disso, pode iniciar e terminar as suas atividades ao contactar o `EngagementAgent`do métodos diretamente.

> [AZURE.IMPORTANT] O SDK Android nunca chamadas a `endActivity()` método, mesmo quando a aplicação é fechada (no Android, aplicações estão nunca fechadas). Assim, é *vivamente* recomendado para ligar para o `startActivity()` método na `onResume` chamada de retorno de *todas as* suas atividades e o `endActivity()` método na `onPause()` chamada de retorno de *todas as* suas atividades. Este é a única forma para se certificar de que as sessões não são fuga. Se uma sessão é fuga, o serviço de Cativação desliga nunca Cativação back-end (uma vez que o serviço permanece ligado desde que uma sessão está pendente).

Eis um exemplo:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Este exemplo é semelhante a `EngagementActivity` escolares e respectivas variantes, cujo código fonte é fornecido no `src` pasta.

## <a name="using-applicationoncreate"></a>Utilizar Application.onCreate()

Qualquer código que coloca na `Application.onCreate()` e na outra aplicação é executado chamadas de retorno para processos de todos os seus aplicação, incluindo o serviço de Cativação. Pode ter efeitos lado indesejados, como atribuições de memória desnecessárias e threads no processo de Cativação, ou os destinatários de difusão duplicados ou serviços.

Se é substituir `Application.onCreate()`, recomendamos que adicionar o fragmento de código seguinte no início do seu `Application.onCreate()` função:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Pode fazer a mesma coisa por `Application.onTerminate()`, `Application.onLowMemory()`, e `Application.onConfigurationChanged(...)`.

Também pode expandir `EngagementApplication` em vez de expandir `Application`: a chamada de retorno `Application.onCreate()` é que a verificação do processo e chamadas de `Application.onApplicationProcessCreate()` apenas se o processo de atual não seja aquele que aloja o serviço de Cativação, as mesmas regras de aplicação para outras chamadas de retorno.

## <a name="tags-in-the-androidmanifestxml-file"></a>Etiquetas no ficheiro AndroidManifest.xml

No código do serviço no ficheiro de AndroidManifest.xml, o `android:label` atributo permite-lhe escolher o nome do serviço de Cativação, tal como é apresentado aos utilizadores finais no ecrã "O services" do seu telefone. Recomendamos que a definição deste atributo `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especificar o `android:process` atributo assegura que o serviço de Cativação é executado no seu próprio processo (em execução Cativação no mesmo processo tal como a sua aplicação faz com que o tópico principal/IU potencialmente menor capacidade de resposta).

## <a name="building-with-proguard"></a>Edifício com ProGuard

Se criar o pacote de aplicações com ProGuard, tem de manter algumas classes. Pode utilizar o fragmento de configuração seguintes:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
