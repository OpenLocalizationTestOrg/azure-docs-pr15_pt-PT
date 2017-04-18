<properties
    pageTitle="Integração do Azure Cativação móvel Android SDK"
    description="Atualizações e procedimentos para Android SDK para Azure Mobile Cativação mais recentes"
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
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Como integrar GCM Cativação móvel

> [AZURE.IMPORTANT] Tem de seguir o integração com o procedimento descrito na secção de como integrar Cativação no Android documento antes de seguir este guia.
>
> Este documento é útil apenas se já integrado o módulo alcance e plano para transmitir Google Play dispositivos. Para integrar campanhas de atingir na sua aplicação, leia primeiro como integrar Cativação contactar no Android.

##<a name="introduction"></a>Introdução

Integrar o GCM permite que a aplicação ser seguia.

Contenham payloads GCM seguia para o SDK sempre o `azme` chave no objecto de dados. Assim, se utilizar GCM para outro efeito na sua aplicação, pode filtrar puxa com base nessa tecla.

> [AZURE.IMPORTANT] Apenas os dispositivos que executem o Android 2.2 ou acima, tendo Google Play instalado e ter Google ligação fundo activada pode ser seguia utilizando GCM; No entanto, pode integrar o este código com segurança em dispositivos não suportados (apenas utiliza tipos).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Criar um projeto de mensagens do Google Cloud com chave de API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Integração de SDK

### <a name="managing-device-registrations"></a>Gestão de registos de dispositivo

Cada dispositivo tem de enviar um comando de registo para os servidores do Google, caso contrário não é possível alcançar.

Um dispositivo também pode anular o registo a partir de notificações de GCM (o dispositivo é automaticamente anulado se a aplicação é desinstalada).

Se não utiliza o [Google reproduzir SDK] ou não já enviar a intenção de registo si mesmo, pode tornar Cativação registar automaticamente o dispositivo para si.

Para permitir que este, adicione o seguinte para sua `AndroidManifest.xml` de ficheiros, interior a `<application/>` etiqueta:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar o id de registo para o serviço de notificações Push Cativação e receber notificações

Para comunicar o id de registo do dispositivo para o serviço de notificações Push Cativação e receber as suas notificações, adicione o seguinte para sua `AndroidManifest.xml` de ficheiros, interior a `<application/>` marcar (mesmo se pode gere os registos de dispositivo próprios):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Certifique-se de que tem as seguintes permissões seu `AndroidManifest.xml` (depois do `</application>` etiqueta).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Conceder acesso móvel Cativação a sua chave de API GCM

Siga [Este guia](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) para conceder acesso de Cativação Mobile a sua chave de API GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
