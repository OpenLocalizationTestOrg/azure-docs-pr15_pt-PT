<properties
    pageTitle="Azure Mobile Cativação iOS SDK atualizar procedimento | Microsoft Azure"
    description="Atualizações e procedimentos para iOS SDK para Azure Mobile Cativação mais recentes"
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
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="upgrade-procedures"></a>Procedimentos de actualização

Se já tiver o integrado uma versão anterior do Cativação para a aplicação, tem de ter em conta os seguintes pontos ao atualizar o SDK.

Para cada nova versão do SDK tem de substituir pela primeira vez (remover e voltar a importar no xcode) as pastas EngagementSDK e EngagementReach.

##<a name="from-300-to-400"></a>A partir do 3.0.0 para 4.0.0

### <a name="xcode-8"></a>XCode 8
8 de XCode é obrigatória, começando pela versão 4.0.0 do SDK.

> [AZURE.NOTE] Se dependem XCode 7 na verdade, em seguida, pode utilizar o [iOS Cativação SDK v3.2.4](https://aka.ms/r6oouh). Existe um erro conhecido no módulo de atingir desta versão anterior ao executar o em dispositivos iOS 10: não são actioned notificações do sistema. Para corrigir isto, terá de implementar a API preterida `application:didReceiveRemoteNotification:` na sua aplicação delegar da seguinte forma:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Uma vez que este iOS API é preterida **não recomendamos esta solução** como este comportamento, pode alterar qualquer actualização da versão futuras iOS (mesmo menor). Deve mudar para XCode 8 mais cedo possível.

### <a name="usernotifications-framework"></a>UserNotifications framework
Tem de adicionar o `UserNotifications` framework no seu fases de criar.

no Explorador do projeto, abra o painel do project e selecione o destino correto. Em seguida, abra o separador **"Criar fases"** e no menu de **"ligação binário com bibliotecas"** , adicione framework `UserNotifications.framework` -configurar a ligação como`Optional`

### <a name="application-push-capability"></a>Capacidade de push da aplicação
XCode 8 podem repor a sua aplicação capacidade de emissão, duplo verifique- `capability` separador do seu destino selecionado.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Adicionar o novo código de registo de notificação do iOS 10
O mais antigo fragmento de código para registar a aplicação para notificações ainda funciona mas está a utilizar o APIs preteridos ao executar o IOS 10.

Importar o `User Notification` framework:

        #import <UserNotifications/UserNotifications.h> 

No seu delegado de aplicação `application:didFinishLaunchingWithOptions` substituir método:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

por:

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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se já tiver a sua própria implementação UNUserNotificationCenterDelegate

O SDK também tem a sua própria implementação do protocolo UNUserNotificationCenterDelegate. É utilizado pelo SDK para monitorizar o ciclo de vida de notificações de Cativação em dispositivos a executar o IOS ou superior a 10. Se o SDK Deteta o delegado não utilizará a sua própria implementação como pode haver apenas um delegado UNUserNotificationCenter por aplicação. Isto significa que tem de adicionar a lógica de Cativação para o seu próprio delegado.

Existem duas formas para realizar esta.

Basta pelo seu delegado de reencaminhamento de chamadas chamadas para o SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Ou ao Herdar do `AEUserNotificationHandler` classe

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Pode determinar se uma notificação provém Cativação ou não passando o respetivo `userInfo` dicionário para o agente de `isEngagementPushPayload:` classe método.

##<a name="from-200-to-300"></a>A partir do 2.0.0 para 3.0.0
Largadas suporte para iOS 4. x. Iniciar a partir desta versão o destino da implementação da sua aplicação tem de ser, pelo menos, iOS 6.

Se estiver a utilizar atingir na sua aplicação, tem de adicionar `remote-notification` valor para o `UIBackgroundModes` matriz no seu ficheiro Info.plist para receberem notificações remotas.

O método `application:didReceiveRemoteNotification:` tem de ser substituídos por `application:didReceiveRemoteNotification:fetchCompletionHandler:` no seu delegado de aplicação.

"AEPushDelegate.h" é preterida interface e precisa remover todas as referências. Isto inclui a remoção de `[[EngagementAgent shared] setPushDelegate:self]` e os métodos de delegado a partir do seu delegado de aplicação:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>A partir do 1.16.0 para 2.0.0
A seguinte tabela descreve como migrar uma integração SDK de serviço Capptain oferecido pelo Capptain SA para uma aplicação tecnologia do Azure Mobile Cativação.
Se estiver a migrar de uma versão anterior, consulte o web site da Capptain migrar para o 1.16 pela primeira vez, em seguida, aplique o procedimento seguinte.

>[AZURE.IMPORTANT] Capptain e Mobile Cativação não são os mesmos serviços e o procedimento indicado abaixo realça apenas como migrar a aplicação de cliente. Migrar o SDK na aplicação irá não migrar os dados a partir os servidores de Capptain para os servidores de Cativação Mobile

### <a name="agent"></a>Agente de

O método `registerApp:` foi substituído pelo novo método `init:`. O delegado de aplicação têm de ser atualizadas em conformidade e utilize a cadeia de ligação:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Controlo de SmartAd foi removido do SDK, que só tem de remover todas as instâncias do `AETrackModule` classe

### <a name="class-name-changes"></a>Alterações de nome de classe

Como parte da aplicação da marca corporativa, existem algumas nomes de ficheiro de classe/que têm de ser alterados.

Todas as categorias com "CP" o prefixo são mudadas com prefixo "SÇ".

Exemplo:

-   `CPModule.h`é o nome alterado para `AEModule.h`.

Todas as categorias com "Capptain" o prefixo são mudadas com prefixo "Cativação".

Exemplos:

-   A classe `CapptainAgent` é o nome alterado para `EngagementAgent`.
-   A classe `CapptainTableViewController` é o nome alterado para `EngagementTableViewController`.
-   A classe `CapptainUtils` é o nome alterado para `EngagementUtils`.
-   A classe `CapptainViewController` é o nome alterado para `EngagementViewController`.
