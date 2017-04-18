<properties
    pageTitle="Notificação concentradores localizadas força de notícias Tutorial para iOS"
    description="Saiba como utilizar o Azure Service Bus notificação concentradores para enviar notificações de notícias força localizados (iOS)."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Utilizar concentradores de notificação para enviar notícias de última hora localizados para dispositivos iOS

> [AZURE.SELECTOR]
- [Da loja Windows c#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como utilizar a funcionalidade de [modelos](notification-hubs-templates-cross-platform-push-messages.md) do Azure notificação concentradores difundir notificações de notícias recentes que foram localizadas por idioma e ao dispositivo. Neste tutorial, irá iniciar com a aplicação do iOS criada no [Concentradores de notificação de utilização para enviar notícias de última hora]. Quando estiver concluído, que poderá registe-se para as categorias que lhe interessam, especifique um idioma no qual pretende receber as notificações e receber apenas as notificações push para as categorias selecionadas nesse idioma.


Existem duas partes para este cenário:

- aplicação do iOS permite aos dispositivos cliente para especificar um idioma e para subscrever força diferentes categorias de notícias;

- back-end emite as notificações, utilizando o **modelo** e **etiquetas** feautres do Azure notificação concentradores.



##<a name="prerequisites"></a>Pré-requisitos

Tem já tiver concluído o tutorial [Concentradores de notificação de utilização para enviar notícias de última hora] e ter o código disponível, uma vez que este tutorial baseia-se diretamente nesse código.

O Visual Studio 2012 ou posterior é opcional.



##<a name="template-concepts"></a>Conceitos de modelo

Na [Notificação de utilização concentradores para enviar notícias de última hora] criadas uma aplicação que utilizado **etiquetas** para subscrever notificações para categorias de notícias diferente.
Muitas aplicações, no entanto, vários mercados de destino e necessitam de localização. Isto significa que o conteúdo das notificações deles próprios tem de ser localizadas e entregue para o conjunto de dispositivos correto.
Este tópico iremos mostrar como utilizar a funcionalidade de **modelo** de notificação concentradores para entregar facilmente as notificações de notícias força localizados.

Nota: uma forma de enviar notificações localizadas é criar várias versões de cada etiqueta. Por exemplo, para suportar o inglês, francês e Mandarim, teria precisamos três etiquetas diferentes para obter notícias do mundo: "world_en", "world_fr," e "world_ch". Em seguida, podemos teria que enviar uma versão localizada de notícias a nível mundial para cada um dos seguintes tags. Este tópico utilizamos modelos para evitar a multiplicação de etiquetas e o requisito de várias mensagens a enviar.

De alto nível, os modelos são uma forma de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de carga útil exata ao referir propriedades que fazem parte da mensagem que foi enviada por sua aplicação back-end. No nosso caso, podemos irá enviar uma mensagem de região desconhecido que contém todos os idiomas suportados:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Em seguida, vamos assegurar que dispositivos registe-se com um modelo que refere-se para a propriedade correta. Por exemplo, uma aplicação para iOS que queira registe-se para obter notícias francês registar o seguinte procedimento:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Os modelos são uma funcionalidade muito poderosa que pode saber mais sobre o nosso artigo de [modelos](notification-hubs-templates-cross-platform-push-messages.md) .

##<a name="the-app-user-interface"></a>Interface de utilizador da aplicação

Vamos agora irá modificar a aplicação de força de notícias que criou no tópico [Concentradores de notificação de utilização para enviar notícias de última hora] para enviar notícias força localizados utilizar modelos.


No seu MainStoryboard_iPhone.storyboard, adicionar um controlo segmentado com três idiomas irá suportamos: inglês, francês e Mandarim.

![][13]

Em seguida, certifique-se adicionar um IBOutlet no seu ViewController.h conforme apresentado abaixo:

![][14]

##<a name="building-the-ios-app"></a>Criar a aplicação do iOS


1. No seu Notification.h Adicionar método *retrieveLocale* e modificar o arquivo e subscrever métodos, conforme apresentado abaixo:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    No seu Notification.m, modificar o método de *storeCategoriesAndSubscribe* , adicionando o parâmetro de região e armazená-los as predefinições de utilizador:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Em seguida, modifique o método *subscrever* para incluir a região:

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Tenha em atenção como podemos estão agora a utilizar o método *registerTemplateWithDeviceToken*, em vez de *registerNativeWithDeviceToken*. Quando o podemos registe-se para um modelo temos que fornecer o modelo de json e também um nome para o modelo (tal como a nossa aplicação poderá pretender registar diferentes modelos). Certifique-se registar as categorias como etiquetas, tal como queremos para se certificar-se de que recebe notifciations para obter as notícias.

    Adicione um método para obtê a região das predefinições de utilizador:

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Agora que recomendamos modificado nossa classe de notificações, temos para se certificar de que os nossos ViewController faz com que utiliza o UISegmentControl novo. Adicione a linha seguinte no método *viewDidLoad* para se certificar-se mostrar a região atualmente selecionado:

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Em seguida, no seu método de *subscrever* , altere sua chamada para o *storeCategoriesAndSubscribe* para o seguinte:

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Por fim, tem de atualizar o método de *didRegisterForRemoteNotificationsWithDeviceToken* no seu AppDelegate.m, para que corretamente que pode atualizar o seu registo quando inicia a sua aplicação. Altere a sua chamada para o método *subscrever* de notificações com o seguinte:

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcional) Envie notificações de localizados modelo a partir da aplicação de consola do .NET.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(opcional) Enviar notificações de localizados modelo a partir do dispositivo

Se não tiver acesso a Visual Studio ou pretende apenas testar enviar as notificações de modelo localizados diretamente a partir da aplicação no dispositivo.  Pode simples adicionar os parâmetros de modelo localizados para os `SendNotificationRESTAPI` método definido no tutorial anterior.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como utilizar modelos, consulte:

- [Notificar os utilizadores com notificação concentradores: ASP.NET]
- [Notificar os utilizadores com notificação concentradores: serviços Mobile]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizar concentradores de notificação para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notificar os utilizadores com notificação concentradores: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notificar os utilizadores com notificação concentradores: serviços Mobile]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
