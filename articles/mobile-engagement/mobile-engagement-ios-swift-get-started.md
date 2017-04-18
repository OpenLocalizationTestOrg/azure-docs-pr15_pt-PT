<properties
    pageTitle="Introdução ao Azure Mobile Cativação para iOS no Swift | Microsoft Azure"
    description="Saiba como utilizar o Azure Mobile Cativação com a análise e as notificações de emissão para iOS aplicações."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introdução ao Azure Mobile Cativação para iOS aplicações na Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e enviar as notificações push para os utilizadores segmentados para uma aplicação do iOS.
Neste tutorial, criar uma aplicação para iOS em branco que recolhe dados básico e recebe as notificações push utilizando o sistema de notificações Push (APNS) da Apple.

Neste tutorial requer o seguinte:

+ 8 de XCode, que pode instalar a partir do seu MAC App Store
+ o [Mobile Cativação iOS SDK]
+ Certificado de notificações push (. p12) que podem obter no seu Dev Center do Apple

> [AZURE.NOTE] Neste tutorial, utiliza Swift versão 3.0. 

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Mobile Cativação para as aplicações do iOS.

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Configuração móvel Cativação para a sua aplicação iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a sua aplicação ao Mobile Cativação back-end

Neste tutorial apresenta uma "básica integração", que é o conjunto mínimo necessário para recolher dados e enviar uma notificação de emissão. A documentação de integração completa pode ser encontrada na [integração com o SDK Cativação móveis iOS](mobile-engagement-ios-sdk-overview.md)

Vamos irá criar uma aplicação básica com XCode para demonstrar a integração:

###<a name="create-a-new-ios-project"></a>Criar um novo projeto do iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a sua aplicação ao back-end de Cativação Mobile

1. Transferir o [Mobile Cativação iOS SDK]
2. Extrair a. tar.gz ficheiro para uma pasta no seu computador
3. Clique com o botão direito do rato em projeto e selecione "Adicionar ficheiros para..."

    ![][1]

4. Navegue para a pasta onde extraída SDK e selecione o `EngagementSDK` pasta, em seguida, prima OK.

    ![][2]

5. Abrir o `Build Phases` separador e, na `Link Binary With Libraries` menu Adicionar os quadros, conforme apresentado abaixo:

    ![][3]

8. Criar um cabeçalho Bridging possam-se de que utiliza o SDK objectivo C APIs ao selecionar ficheiro > novo > ficheiro > iOS > origem > ficheiro de cabeçalho.

    ![][4]

9. Edite o ficheiro de cabeçalho correspondência para expor o código de Mobile Cativação objectivo-C para o seu código rápido, adicione as seguintes importações:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. Em definições de criar, certifique-se a compilação de objectivo-C pontes cabeçalho definição em Swift compilador - geração código tem um caminho para este cabeçalho. Eis um exemplo de caminho: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (consoante o caminho)**

    ![][6]

11. Regresse ao portal do Azure na página de *Informações de ligação* da sua aplicação e copie a cadeia de ligação

    ![][5]

12. Colar a cadeia de ligação no agora a `didFinishLaunchingWithOptions` delegar

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Activar a monitorização em tempo real

Para poder começar a enviar dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (actividade) para o back-end Cativação Mobile.

1. Abra o ficheiro **ViewController.swift** e substitua a classe de **ViewController** para ser **EngagementViewController**base:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações de emissão e mensagens de na aplicação

Cativação Mobile permite-lhe interagir e contactar com os seus utilizadores com as notificações de emissão e mensagens de na aplicação no contexto de campanhas. Este módulo chama alcance no portal do Cativação Mobile.
As secções seguintes irão programa de configuração a aplicação recebê-las.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Ativar a sua aplicação receber notificações de emissão silenciosa

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca de aceder ao seu projeto

1. Clique com o botão direito do rato em seu projeto
2. Selecione`Add file to ...`
3. Navegue para a pasta onde o SDK que extraída
4. Selecione o `EngagementReach` pasta
5. Clique em Adicionar
6. Edite o ficheiro de cabeçalho correspondência para expor Mobile Cativação objectivo-C atinja cabeçalhos e adicione as seguintes importações:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modificar o seu delegado de aplicação

1. Dentro de `didFinishLaunchingWithOptions` - crie um módulo de atingir e passá-lo à sua linha de inicialização Cativação existente:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Ativar a sua aplicação receber notificações de emissão de APNS
1. Adicionar a linha seguinte para o `didFinishLaunchingWithOptions` método:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Adicionar o `didRegisterForRemoteNotificationsWithDeviceToken` método da seguinte forma:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Adicionar o `didReceiveRemoteNotification:fetchCompletionHandler:` método da seguinte forma:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[IOS Mobile Cativação SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
