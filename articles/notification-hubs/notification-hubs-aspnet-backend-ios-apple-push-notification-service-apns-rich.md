<properties
    pageTitle="Notificação Azure concentradores Rich Push"
    description="Saiba como enviar notificações push formatado a uma aplicação do iOS a partir do Azure. Exemplos de código escritos objectivo-C e c#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Notificação Azure concentradores Rich Push


##<a name="overview"></a>Descrição geral

Para poder participar utilizadores com conteúdo rich instantâneo, uma aplicação poderá pretender notificações push para além de texto simples. Estas notificações promover interações de utilizador e apresentar conteúdo tal como urls, sons, imagens/cupões e muito mais. Neste tutorial constrói no tópico [Notificar utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e mostra como enviar as notificações push incorporam payloads (por exemplo, imagem).


Neste tutorial é compatível com iOS 7 e 8.

  ![][IOS1]

De alto nível:

1. O back-end da aplicação:
    - Armazena a carga de útil avançada (neste caso, imagens) no armazenamento/local da base de dados back-end
    - Envia o ID desta notificação formatada para o dispositivo
2. Aplicação no dispositivo:
    - O back-end pedir a carga de útil formatada com o ID de que recebe de contactos
    - Envia notificações de utilizadores no dispositivo quando obtenção de dados estiver concluída e mostra a carga de útil imediatamente quando os utilizadores, toque em para obter mais informações


## <a name="webapi-project"></a>WebAPI Project

1. No Visual Studio, abra o projeto de **AppBackend** que criou no tutorial [Notificar utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obter uma imagem que pretender para notificar os utilizadores com e colocá-la numa pasta do **img** no diretório do seu projeto.
3. Clique em **Mostrar todos os ficheiros** no Explorador de solução e o botão direito do rato na pasta para **Incluir no Project**.
4. Com a imagem selecionada, altere a sua ação criar na janela Propriedades para **Recurso incorporado**.

    ![][IOS2]

5. No **Notifications.cs**, adicione o seguinte utilizando instrução:

        using System.Reflection;

6. Atualize a classe de **notificações** inteira com o código seguinte. Certifique-se de que substitua os marcadores de posição com o seu credenciais do concentrador de notificação e o nome de ficheiro de imagem.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (opcional) Consulte [como incorporar aceder e recursos utilizando o Visual c#](http://support.microsoft.com/kb/319292) para obter mais informações sobre como adicionar e obter recursos de projeto.

7. No **NotificationsController.cs**, redefina **NotificationsController** com os seguintes fragmentos. Isto envia um id de notificação de rich silenciosa inicial para o dispositivo e permite a obtenção do lado do cliente da imagem:

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. Agora já podemos irá implementar novamente esta aplicação para um site do Azure encaminhá-la acessível a partir de todos os dispositivos. Com o botão direito no projeto **AppBackend** e selecione **Publicar**.

9. Selecione o Web site Azure como publicar de destino. Inicie sessão com a sua conta Azure e selecione um Web site novo ou existente e tome nota da propriedade do **URL de destino** no separador **ligação** . Vamos irá referem-se para este URL como o *ponto final de back-end* posteriormente neste tutorial. Clique em **Publicar**.

## <a name="modify-the-ios-project"></a>Modificar o projecto iOS

Agora que tenha modificado seu back-end de aplicação para enviar apenas o *id* de uma notificação, irá alterar a sua aplicação iOS para processar nesse id e obter a mensagem avançada a partir do seu back-end.

1. Abra o projeto do iOS e ativar notificações remotas ao aceder ao seu destino aplicação principal na secção **alvos** .

2. Clique no **capacidades**, ative **Os modos de fundo**e selecione a caixa de verificação **Notificações remoto** .

    ![][IOS3]

3. Aceda a **Main.storyboard**e certifique-se de que tem um controlador de vista (referidas como controlador de vista de base neste tutorial) a partir do tutorial [Notificar utilizador](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .

4. Adicionar um **Controlador de navegação** para o seu storyboard e controlo enquanto arrasta para casa controlador de vista para torná-lo a **vista de raiz** de navegação. Certifique-se de **É controlador de vista inicial** o inspector de atributos está selecionada para apenas o controlador de navegação.

5. Adicione um **Controlador de vista** para guião gráfico e adicionar uma **Vista da imagem**. Esta é a página os utilizadores irão ver assim que escolherem saber mais, clicando na notifiication. O storyboard deverá ter o aspeto da seguinte forma:

    ![][IOS4]

6. Clique no **Controlador de Vista Home** storyboard e certifique-se tem **homeViewController** como de **Classe personalizada** e **Storyboard ID** no inspector de identidade.

7. Faça o mesmo para controlador de vista de imagem como **imageViewController**.

8. Em seguida, crie uma nova classe de controlador de vista intitulado **imageViewController** para processar IU que acabou de criar.

9. No **imageViewController.h**, adicione o seguinte para declarações de interface o controlador. Certifique-se para o controlo enquanto arrasta a partir da vista de imagem storyboard a estas propriedades para ligar as duas:

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. No **imageViewController.m**, adicione o seguinte no final da **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. No **AppDelegate.m**, importe o controlador de imagem que criou:

        #import "imageViewController.h"

12. Adicione uma secção de interface com a declaração seguinte:

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. No **AppDelegate**, certifique-se a aplicação regista para notificações silenciosos no **aplicação: didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. Substitua na implementação que se segue para a **aplicação: didRegisterForRemoteNotificationsWithDeviceToken** para tirar o storyboard IU muda para a conta:

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Em seguida, adicione os seguintes métodos para **AppDelegate.m** para obter a imagem a partir do seu ponto final e enviar uma notificação local quando obtenção estiver concluída. Certifique-se substituir o marcador de posição `{backend endpoint}` com o seu ponto final de back-end:

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Alça de notificação de local acima abrindo o o controlador de vista de imagem no **AppDelegate.m** com os seguintes métodos:

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Executar a aplicação

1. No XCode, execute a aplicação num dispositivo iOS física (push notificações não irão funcionar no simulator).

2. Na aplicação iOS IU, introduza um nome de utilizador e palavra-passe do mesmo valor para a autenticação e clique em **Log In**.

3. Clique em **Enviar push** e deverá ver um alerta de na aplicação. Se clicar em **mais**, que irá trouxe para a imagem tiver optado por incluir na sua aplicação do back-end.

4. Pode também clicar em **Enviar push** e prima imediatamente no botão Home page do seu dispositivo. Alguns minutos, receberá uma notificação de emissão. Se toque no mesmo ou clique em mais, ser colocado para a sua aplicação e o conteúdo de imagem avançada.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
