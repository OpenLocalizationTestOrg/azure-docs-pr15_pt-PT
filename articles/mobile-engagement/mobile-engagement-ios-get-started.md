<properties
    pageTitle="Introdução ao Azure Mobile Cativação para iOS no objectivo C | Microsoft Azure"
    description="Saiba como utilizar o Azure Mobile Cativação com as notificações push e de análise para as aplicações do iOS."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introdução ao Azure Mobile Cativação para aplicações do iOS no objectivo C

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e enviar as notificações push para os utilizadores segmentados para uma aplicação do iOS.
Neste tutorial, criar uma aplicação para iOS em branco que recolhe dados básico e recebe as notificações push utilizando o sistema de notificações Push (APNS) da Apple.

Neste tutorial requer o seguinte:

+ 8 de XCode, que pode instalar a partir do seu MAC App Store
+ o [Mobile Cativação iOS SDK]

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Mobile Cativação para as aplicações do iOS.

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Configuração móvel Cativação para a sua aplicação iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a sua aplicação ao Mobile Cativação back-end

Neste tutorial apresenta uma "básica integração", que é o conjunto mínimo necessário para recolher dados e enviar uma notificação de emissão. A documentação de integração completa pode ser encontrada na [integração com o SDK Cativação móveis iOS](mobile-engagement-ios-sdk-overview.md)

Vamos irá criar uma aplicação básica com XCode para demonstrar a integração.

###<a name="create-a-new-ios-project"></a>Criar um novo projeto do iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Ligar a sua aplicação ao Mobile Cativação back-end

1. Transferir o [Mobile Cativação iOS SDK].
2. Extrair a. tar.gz ficheiro para uma pasta no seu computador.
3. Com o botão direito do projeto e, em seguida, selecione **Adicionar ficheiros ao**.

    ![][1]

4. Navegue para a pasta onde extraída que o SDK, selecione o `EngagementSDK` e, em seguida, prima **OK**.

    ![][2]

5. Abra o separador **Criar fases** e, no menu **Binário com bibliotecas de ligação** , adicione os quadros conforme apresentado abaixo:

    ![][3]

6. Regresse ao portal do Azure na página de **Informações de ligação** da sua aplicação e copie a cadeia de ligação.

    ![][4]

7. Adicione a linha seguinte de código no seu ficheiro **AppDelegate.m** .

        #import "EngagementAgent.h"

8. Colar a cadeia de ligação no agora a `didFinishLaunchingWithOptions` delegado.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`é uma declaração opcional que permite aos registos SDK para identificar problemas. 

##<a id="monitor"></a>Ativar a monitorização em tempo real

Para poder começar a enviar dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (actividade) para o back-end Cativação Mobile.

1. Abra o ficheiro **ViewController.h** e importar **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Substituir agora super classe da interface do **ViewController** por `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações push e mensagens de na aplicação

Cativação Mobile permite-lhe interagir com os seus utilizadores e ALCANÇAR com as notificações push e mensagens no contexto das campanhas de na aplicação. Este módulo chama alcance no portal do Cativação Mobile.
As secções seguintes configurar a sua aplicação para recebê-las.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Ativar a sua aplicação receber notificações de emissão silenciosa

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca de aceder ao seu projeto

1. Com o botão direito do projeto.
2. Selecione o **ficheiro de adicionar a**.
3. Navegue para a pasta onde o SDK que extraída.
4. Selecione o `EngagementReach` pasta.
5. Clique em **Adicionar**.

### <a name="modify-your-application-delegate"></a>Modificar o seu delegado de aplicação

1. Novamente no ficheiro **AppDeletegate.m** , importe o módulo atingir compromisso.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. Dentro de `application:didFinishLaunchingWithOptions` método, crie um módulo de atingir e passam-lo à sua linha de inicialização Cativação existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Ativar a sua aplicação receber notificações de emissão de APNS

1. Adicionar a linha seguinte para o `application:didFinishLaunchingWithOptions` método:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Adicionar o `application:didRegisterForRemoteNotificationsWithDeviceToken` método da seguinte forma:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Adicionar o `didFailToRegisterForRemoteNotificationsWithError` método da seguinte forma:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Adicionar o `didReceiveRemoteNotification:fetchCompletionHandler` método da seguinte forma:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[IOS Mobile Cativação SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

