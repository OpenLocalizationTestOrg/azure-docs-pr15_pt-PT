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


#<a name="how-to-integrate-adm-with-engagement"></a>Como integrar ADM Cativação

> [AZURE.IMPORTANT] Tem de seguir o integração com o procedimento descrito na secção de como integrar Cativação no Android documento antes de seguir este guia.
>
> Este documento é útil apenas se já integrado o módulo alcance e plano para transmitir dispositivos Amazon. Para integrar campanhas de atingir na sua aplicação, leia primeiro como integrar Cativação contactar no Android.

##<a name="introduction"></a>Introdução

Integrar o ADM permite que a aplicação ser seguia quando o destino dispositivos Amazon Android.

Contenham payloads ADM seguia para o SDK sempre o `azme` chave no objecto de dados. Assim, se utilizar ADM para outro efeito na sua aplicação, pode filtrar puxa com base nessa tecla.

> [AZURE.IMPORTANT] Apenas Amazon Kindle dispositivos que executem o Android 4.0.3 ou acima são suportadas pelos Amazon dispositivo mensagens; No entanto, pode integrar o este código com segurança nos outros dispositivos.

##<a name="sign-up-to-adm"></a>Inscrever-se para ADM

Se ainda não tiver concluído, tem de ativar ADM na sua conta do Amazon.

O procedimento é detalhado na: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Após concluir o processo, obtém:

-   OAuth as credenciais (um ID de cliente e uma palavra-passe cliente) para Cativação possam emissão seus dispositivos.
-   Uma chave de API que devem ser integradas na sua aplicação.

##<a name="sdk-integration"></a>Integração de SDK

### <a name="managing-device-registrations"></a>Gestão de registos de dispositivo

Cada dispositivo tem de enviar um comando de registo para os servidores ADM, caso contrário não é possível alcançar.

Se já a utilizar a [biblioteca do cliente ADM]e já tiver [ADM integrada] pode aceder diretamente ao receber de android sdk adm.

Se não tiver integrado ADM ainda, Cativação tem uma forma mais simples para ativá-la na sua aplicação:

Editar o `AndroidManifest.xml` ficheiro:

-   Adicionar espaço de nomes Amazon, o ficheiro deverá começar da seguinte forma:

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Dentro de `<application/>` marcar, adicionar nesta secção:

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   Depois de adicionar a etiqueta amazon, poderá ter um erro de compilação se projeto de destino construir for inferior a Android 2.1. Tem de utilizar um alvo de compilação **Android 2.1 +** (não se preocupe, ainda pode ter uma `minSdkVersion` definido como 4).
-   Integre a chave de API ADM como um activo pelo que se segue [Este procedimento].

Em seguida, siga as instruções das secções seguintes.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar o id de registo para o serviço de notificações Push Cativação e receber notificações

Para comunicar o id de registo do dispositivo para o serviço de notificações Push Cativação e receber as suas notificações, adicione o seguinte para sua `AndroidManifest.xml` de ficheiros, interior a `<application/>` marcar (mesmo se utilizar ADM sem Cativação):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Certifique-se de que tem as seguintes permissões seu `AndroidManifest.xml` (antes do `</application>` etiqueta).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>Conceder Cativação OAuth credenciais

Submeta as suas credenciais de OAuth (ID de cliente e segredo cliente) no Portal de Cativação.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[Biblioteca de ADM do cliente]:https://developer.amazon.com/sdk/adm/setup.html
[ADM integrada]:https://developer.amazon.com/sdk/adm/integrating-app.html
[Este procedimento]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
