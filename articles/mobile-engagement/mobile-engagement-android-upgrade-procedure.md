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


#<a name="upgrade-procedures"></a>Procedimentos de actualização

Se já tiver o integrado uma versão anterior do nosso SDK para a aplicação, tem de ter em conta os seguintes pontos ao atualizar o SDK.

Poderá ter de seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se migrar a partir do 1.4.0 para 1.6.0 que tem de pela primeira vez, siga o procedimento "de 1.4.0 para 1.5.0", em seguida, o procedimento "de 1.5.0 para 1.6.0".

Independentemente da sua versão de atualizar a partir de, tem de substituir o `mobile-engagement-VERSION.jar` pelo novo.

##<a name="from-420-to-421"></a>A partir do 4.2.0 para 4.2.1

Este passo realmente pode ser efetuado num qualquer versão do SDK, é uma melhoria de segurança quando integrar alcance atividades.

Agora deve adicionar `exported="false"` para todas as atividades de atingir.

Atividades de atingir deverão agora ter este aspeto seu `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

##<a name="from-400-to-410"></a>A partir do 4.0.0 para 4.1.0

O SDK agora alça novo permissão modelo a partir de Android M.

Se utilizar funcionalidades de localização ou notificações conceito leia [Esta secção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Para além do novo modelo de permissão, suportamos agora configurar funcionalidades de localização o tempo de execução.
Estamos a ainda compatíveis com os parâmetros manifesto para a localização mas -lo agora é preterida. Para utilizar a configuração de runtime, remova as secções seguintes a partir do seu ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

e leia [Este procedimento atualizado](mobile-engagement-android-integrate-engagement.md#location-reporting) para utilizar em vez disso, a configuração de runtime.

##<a name="from-300-to-400"></a>A partir do 3.0.0 para 4.0.0

### <a name="native-push"></a>Push nativo

Push nativo (GCM/ADM) agora também é utilizado no notificações da aplicação para que tem de configurar as credenciais de push nativo para qualquer tipo de campanha push.

Se ainda não siga [Este procedimento](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Integração de atingir terem sido modificada num ``AndroidManifest.xml``.

Substitua isto:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Por

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Existe possivelmente um ecrã de carregamento agora quando clica num anúncio (com texto/conteúdo da web) ou um inquérito.
Tem de adicioná-lo para esses campanhas trabalhar no 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Recursos

Incorporar o novo `res/layout/engagement_loading.xml` ficheiro para o seu projeto.

##<a name="from-240-to-300"></a>A partir do 2.4.0 para 3.0.0

A seguinte tabela descreve como migrar uma integração SDK de serviço Capptain oferecido pelo Capptain SA para uma aplicação tecnologia do Azure Mobile Cativação. Se estiver a migrar de uma versão anterior, consulte o web site da Capptain para migrar para o 2.4.0 pela primeira vez e, em seguida, aplique o procedimento seguinte.

>[AZURE.IMPORTANT] Capptain e Mobile Cativação não são os mesmos serviços e o procedimento indicado abaixo realça apenas como migrar a aplicação de cliente. Migrar o SDK na aplicação não será migrado os dados dos servidores Capptain para os servidores de Cativação Mobile.

### <a name="jar-file"></a>Ficheiro para caixa

Substituir `capptain.jar` por `mobile-engagement-VERSION.jar` no seu `libs` pasta.

### <a name="resource-files"></a>Ficheiros de recursos

Todos os ficheiros de recursos que fornecemos (o prefixo por `capptain_`) tem de ser substituídos por novos (com o prefixo `engagement_`).

Se tiver personalizado esses ficheiros, tem de voltar a aplicar a personalização no novos ficheiros, **todos os identificadores os ficheiros de recursos também nomes foram mudados**.

### <a name="application-id"></a>ID da aplicação

Agora Cativação utiliza uma cadeia de ligação para configurar os identificadores SDK tal como o identificador de aplicação.

Tem de utilizar `EngagementAgent.init` método na sua atividade iniciador da seguinte forma:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de ligação para a aplicação é apresentada no Portal do Azure.

Remova qualquer chamada para `CapptainAgent.configure` como `EngagementAgent.init` substitui esse método.

O `appId` já não podem ser configuradas utilizando `AndroidManifest.xml`.

Remova nesta secção a partir do seu `AndroidManifest.xml` se tivê-lo:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API

Todas as chamadas para qualquer classe Java do nosso SDK tem de ser mudado; Por exemplo, `CapptainAgent.getInstance(this)` deverá ser mudado `EngagementAgent.getInstance(this)`, `extends CapptainActivity` deverá ser mudado `extends EngagementActivity` etc...

Se foram integrado com ficheiros de preferências do agente de predefinidos, o nome de ficheiro predefinido é agora `engagement.agent` e a chave está `engagement:agent`.

Quando criar anúncios web, o arquivador Javascript está agora `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Muitas alterações aconteceu lá, o serviço já não é partilhado e muitas destinatários já não são exportáveis.

A declaração de serviço está agora mais simples; remover o filtro intenção e todos os dados meta dentro da mesma e adicionar `exportable=false`.

Para além disso tudo o que é o nome alterado para utilizar Cativação.

Assemelha-se agora:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Quando pretende ativar registos de teste, os dados meta agora foi movido para a etiqueta de aplicação e tem sido mudado:

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Todos os outros meta-dados apenas nomes foram mudados, eis a lista completa (de curso Mudar o nome apenas aqueles que utiliza):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play e SmartAd rastreio foi removido SDK, que só tem de remover isto sem substituição:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

As atividades de atingir são agora declaradas da seguinte forma:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Se tiver personalizadas alcance atividades, terá de apenas alterar as ações intenção para corresponder ao quer `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Os destinatários difusão nomes foram mudados, assim como o podemos agora adicione `exported=false`. Eis a lista completa dos destinatários com a especificação de novo, (do curso Mudar o nome apenas aqueles que utiliza):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Controlo recetor ter sido removida, pelo que tem de remover nesta secção:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Tenha em atenção que a declaração de implementação no auscultador difusão **EngagementMessageReceiver** foi alterado no `AndroidManifest.xml`. Isto acontece porque a API para enviar e remover arbitrários XMPP mensagens arbitrários entidades XMPP e a API para enviar e receber mensagens entre dispositivos foram removidas. Assim sendo, tem também de eliminar as chamadas de retorno seguintes a partir da sua implementação **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

e

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

em seguida, elimine quaisquer chamada no **EngagementAgent** para:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

e

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Configuração proguard pode ser afetada pelo aplicação da marca corporativa, as regras são agora está à procura, como:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
