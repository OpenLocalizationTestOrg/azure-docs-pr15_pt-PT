<properties
    pageTitle="Integração do Azure Cativação móvel Android SDK"
    description="Atualizações e procedimentos para Android SDK para Azure Mobile Cativação mais recentes"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-android"></a>Como integrar o Cativação no Android

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimento descreve a forma mais simples para ativar do Cativação e de análise monitorização de funções na sua aplicação Android.

> [AZURE.IMPORTANT] O nível de Android SDK API mínimo tem de ser 10 ou posterior (Android 2.3.3 ou superior).

Os passos seguintes são suficiente para activa o relatório de registos necessários para calcular todas as estatísticas sobre utilizadores, sessões, atividades, falhas e Technicals. O relatório de registos necessários para calcular outras estatísticas como tarefas, erros e eventos têm de ser efetuado manualmente utilizando a API Cativação (consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API no seu Android](mobile-engagement-android-use-engagement-api.md) uma vez que estas estatísticas são dependentes das aplicações.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Incorporar a Cativação SDK e o serviço no seu projeto Android

Transferir o SDK Android a partir de [aqui](https://aka.ms/vq9mfn) obter `mobile-engagement-VERSION.jar` e colocá-los para o `libs` pasta do seu projeto Android (criar a pasta de efectuar se não existir ainda).

> [AZURE.IMPORTANT]
> Se criar o pacote de aplicações com ProGuard, tem de manter algumas classes. Pode utilizar o fragmento de configuração seguintes:
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Especifique a cadeia de ligação de Cativação ao contactar o método que se segue na atividade de iniciador de aplicações:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de ligação para a aplicação é apresentada no Portal do Azure.

-   Se em falta, adicione as seguintes permissões Android (antes do `<application>` etiqueta):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Adicione a seguinte secção (entre o `<application>` e `</application>` etiquetas):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Alterar `<Your application name>` pelo nome da sua aplicação.

> [AZURE.TIP] O `android:label` atributo permite-lhe escolher o nome do serviço de Cativação que aparece para os utilizadores finais no ecrã "O services" do seu telefone. É recomendado para definir este atributo para `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especificar o `android:process` atributo assegura que o serviço de Cativação será executado no seu próprio processo (em execução Cativação no mesmo processo como a aplicação irá tornar o tópico principal/IU potencialmente menor capacidade de resposta).

> [AZURE.NOTE] Qualquer código que coloca na `Application.onCreate()` e outras chamadas de retorno aplicação serão executadas para processos de todos os seus aplicação, incluindo o serviço de Cativação. Pode ter efeitos lado indesejados (como atribuições de memória desnecessárias e threads no processo de Cativação, destinatários difusão duplicados ou serviços).

Se é substituir `Application.onCreate()`, é recomendado para adicionar o fragmento de código seguinte no início do seu `Application.onCreate()` função:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Pode fazer a mesma coisa por `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Também pode expandir `EngagementApplication` em vez de expandir `Application`: a chamada de retorno `Application.onCreate()` é que a verificação do processo e chamadas de `Application.onApplicationProcessCreate()` apenas se o processo de atual não seja aquele que aloja o serviço de Cativação, as mesmas regras de aplicação para outras chamadas de retorno.

##<a name="basic-reporting"></a>Relatório básico

### <a name="recommended-method-overload-your-activity-classes"></a>Recomendado método: Sobrecarga seu `Activity` classes

Para ativar o relatório de todos os registos necessários por Cativação para calcular os utilizadores, sessões, atividades, falhas e estatísticas técnicas, que só tem de efetuar todas as seu `*Activity` sub-classes herdam a correspondente `Engagement*Activity` aulas (por exemplo, se expande a sua atividade legada `ListActivity`, tornar-expande a `EngagementListActivity`).

**Sem Cativação:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Com Cativação:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Quando utilizar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se qualquer uma chamada para `requestWindowFeature(...);` é tornado antes das chamadas para `super.onCreate(...);`, caso contrário, irá ocorrer uma falha de sistema.

Pode encontrar estas classes na `src` pasta e pode copiá-los para o seu projeto. As classes também estão a ser o **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: chamar `startActivity()` e `endActivity()` manualmente

Se não conseguir ou não pretende que seja sobrecarga seu `Activity` classes, em vez disso, pode iniciar e terminar as suas atividades ao contactar o suporte `EngagementAgent`do métodos diretamente.

> [AZURE.IMPORTANT] O SDK Android nunca chamadas a `endActivity()` método, mesmo quando a aplicação é fechada (no Android, aplicações estão realmente nunca fechadas). Assim, é *vivamente* recomendado para ligar para o `startActivity()` método na `onResume` chamada de retorno de *todas as* suas atividades e o `endActivity()` método na `onPause()` chamada de retorno de *todas as* suas atividades. Este é a única forma para se certificar de que não serão possível fuga sessões. Se uma sessão é fuga, o serviço de Cativação nunca desligar a partir de Cativação back-end (uma vez que o serviço permanece ligado desde que uma sessão está pendente).

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

Este exemplo muito semelhante à `EngagementActivity` escolares e respectivas variantes, cujo código fonte é fornecido no `src` pasta.

##<a name="test"></a>Teste

Agora verifique se a integração do executando a aplicação móvel num emulador ou dispositivo e verificar a registos de uma sessão no separador Monitor.

As secções seguintes são opcionais.

##<a name="location-reporting"></a>Relatórios de localização

Se pretender localizações de modo a ser comunicado, é necessário adicionar algumas linhas de configuração (entre o `<application>` e `</application>` etiquetas).

### <a name="lazy-area-location-reporting"></a>Área preguiça relatórios de localização

Relatórios de localização de área preguiça permite para comunicar a país, região e Localidade associadas a dispositivos. Este tipo de elaboração de relatórios de localização utiliza apenas localizações de rede (com base na célula ID ou conta). A área de dispositivo é comunicada pelo mais uma vez por sessão. Nunca é utilizado o GPS e, portanto, este tipo de relatório de localização com poucas (não para dizer n) impacto na baterias.

Áreas denunciadas são utilizadas para calcular estatísticas geográficas sobre utilizadores, sessões, eventos e erros. Também pode ser utilizados como critério em campanhas de atingir.

Para ativar a área preguiça localização relatórios, pode fazê-lo utilizando a configuração anteriormente mencionada neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de se em falta, adicione as seguintes permissões:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar a utilizar ``ACCESS_FINE_LOCATION`` se já a utilizar na sua aplicação.

### <a name="real-time-location-reporting"></a>Relatórios de localização em tempo real

Relatórios de localização em tempo real permite comunicar latitude e longitude associadas a dispositivos. Por predefinição, este tipo de elaboração de relatórios de localização utiliza apenas localizações de rede (com base na célula ID ou conta), e o reporte só está ativo quando a aplicação é executado em primeiro plano (ou seja, durante uma sessão).

Em tempo real localizações *não* utilizado para calcular as estatísticas estão. O seu objectivo apenas é permitir a utilização de tempo real geo-vedações \<alcance-audiência-geofencing\> critério em campanhas de atingir.

Para ativar a localização em tempo real elaboração de relatórios, pode fazê-lo utilizando a configuração anteriormente mencionada neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de se em falta, adicione as seguintes permissões:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar a utilizar ``ACCESS_FINE_LOCATION`` se já a utilizar na sua aplicação.

#### <a name="gps-based-reporting"></a>GPS com base elaboração de relatórios

Por predefinição, relatórios de localização em tempo real só utiliza localizações de rede com base. Para ativar a utilização de GPS baseados localizações (que são precisas muito mais), utilize o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de se em falta, adicione as seguintes permissões:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatórios de fundo

Por predefinição, relatórios de localização em tempo real só está ativa quando a aplicação é executado em primeiro plano (ou seja, durante uma sessão). Para ativar a denúncia também em segundo plano, utilize o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando a aplicação é executada em segundo plano, só de rede com base localizações são reportados, mesmo se activou o GPS.

O relatório de localização de fundo será interrompido se o utilizador reinicia o seu dispositivo, pode adicionar esta opção para torná-lo reiniciar automaticamente durante o arranque:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Também terá de se em falta, adicione as seguintes permissões:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Permissões de M Android

Começando Android M, algumas permissões são geridos durante a execução e precisa de aprovação de utilizador.

As permissões de runtime serão desligadas por predefinição para novas instalações aplicação se destinar nível Android API 23. Caso contrário-irão ser ativada por predefinição.

O utilizador pode ativar/desativar essas permissões a partir do menu de definições de dispositivo. Desativar permissões a partir do menu sistema elimina se processos em segundo plano da aplicação, este é um comportamento do sistema e não tem nenhum impacto na capacidade de receber push em segundo plano.

No contexto de Cativação Mobile, as permissões que necessitam de aprovação no runtime são:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(apenas quando a filtragem de nível de Android API 23 para este)

O armazenamento externo é utilizado apenas para funcionalidade conceito de atingir. Se encontrar lhes pedir esta permissão para ser desorganização, pode removê-se tiver utilizado apenas para Mobile Cativação mas pelo valor desativar a funcionalidade de imagem grande.

Para as funcionalidades de localização, deverá pedir permissões para utilizar uma caixa de diálogo sistema padrão de utilizador. Se o utilizador aprova, tem de indicar ao ``EngagementAgent`` tirar essa alteração para a conta em tempo real (caso contrário, a alteração será processada da próxima vez que o utilizador inicia a aplicação).

Eis um exemplo de código para utilizar numa atividade da sua aplicação para solicitar permissões e reencaminhar o resultado se for positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Avançadas de elaboração de relatórios

Opcionalmente, se pretender comunicar eventos específicos da aplicação, erros e tarefas, terá de utilizar a API Cativação através dos métodos da `EngagementAgent` escolares. Um objecto desta classe pode ser ao contactar o `EngagementAgent.getInstance()` método estático.

A API Cativação permite-lhe para utilizar todas as funcionalidades avançadas de Cativação e detalhadas mais como utilizar a API Cativação no Android (bem como na documentação técnica da `EngagementAgent` escolares).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Configuração avançada (no AndroidManifest.xml)

### <a name="wake-locks"></a>Activar bloqueios

Se pretender Certifique-se de que as estatísticas são enviadas em tempo real quando utilizar a conta ou quando o ecrã está desativada, adicione a permissão opcional seguinte:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Relatório de falha de sistema

Se pretende desativar relatórios de falha de sistema, adicionar esta (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Limiar de rajada

Por predefinição, os relatórios de serviço de Cativação inicia em tempo real. Se a sua aplicação relatórios registos muito frequentemente, é melhor para os registos de memória intermédia e reportá-las todas ao mesmo tempo um tempo regular base (esta opção é denominada "modo de rajada"). Para fazê-lo, adicione este (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo de rajada ligeiramente aumentar a vida de baterias, mas tem o impacto no Monitor Cativação: todos os duração sessões e tarefas de implementação será arredondada para o limiar de rajada (portanto, sessões e tarefas mais pequena do que o limiar de rajada poderão não estar visível). Recomenda-se para utilizar um limite de rajada já não que 30000 (30s).

### <a name="session-timeout"></a>Tempo limite de sessão

Por predefinição, uma sessão é 10s terminada após o fim da sua última actividade (que normalmente acontece ao premir a tecla Home ou anterior, definindo o telefone idle ou por saltar para outra aplicação). Este é para evitar uma divisão sessão cada um tempo de saída de utilizador e regressar à aplicação muito rapidamente (que pode acontecer quando ele Pegue uma imagem, selecione uma notificação, etc.). Pretende modificar este parâmetro. Para fazê-lo, adicione este (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Desactivar o relatório de registo

### <a name="using-a-method-call"></a>Utilizar uma chamada de método

Se pretender Cativação para parar o envio de registos, pode ligar:

            EngagementAgent.getInstance(context).setEnabled(false);

Esta chamada é persistente: utiliza um ficheiro de preferências partilhada.

Se Cativação está ativa ao ligar para esta função, poderá demorar um minuto para parar o serviço. No entanto,-não iniciar o serviço de todo da próxima vez que iniciar a aplicação.

Pode ativar o registo de elaboração de relatórios novamente ao contactar o suporte a mesma função com `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração no seu próprio`PreferenceActivity`

Em vez de chamar esta função, também pode integrar o esta definição diretamente no seu existentes `PreferenceActivity`.

Pode configurar Cativação para utilizar o seu ficheiro de preferências (com o modo de pretendido) na `AndroidManifest.xml` ficheiro com `application meta-data`:

-   O `engagement:agent:settings:name` chave é utilizada para definir o nome do ficheiro partilhado preferências.
-   O `engagement:agent:settings:mode` chave é utilizada para definir o modo do ficheiro de preferências partilhada, deve utilizar o mesmo modo como no seu `PreferenceActivity`. Tem de ser transmitido o modo como um número: Se estiver a utilizar uma combinação de sinalizadores constantes no seu código, selecione o valor total.

Utilizar sempre Cativação a `engagement:key` chave booleana dentro o ficheiro de preferências para gerir esta definição.

O exemplo seguinte de `AndroidManifest.xml` mostra os valores predefinidos:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Em seguida, pode adicionar um `CheckBoxPreference` no seu esquema preferência tal como o seguinte:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
