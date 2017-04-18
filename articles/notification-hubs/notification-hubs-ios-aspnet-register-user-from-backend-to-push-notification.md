<properties
    pageTitle="Registe-se o utilizador atual para notificações push utilizando Web API | Microsoft Azure"
    description="Saiba como pedir o registo de notificação de push numa aplicação iOS com Azure notificação concentradores quando registeration é executado por ASP.NET Web API."
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
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registe-se o utilizador atual para notificações push utilizando ASP.NET

> [AZURE.SELECTOR]
- [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como pedir o registo de notificações push com Azure notificação concentradores quando o registo é executado por ASP.NET Web API. Este tópico expande o tutorial [Notificar utilizadores com notificação concentradores]. Tem já tiver concluído os passos necessários desse tutorial, para criar o serviço móvel autenticado. Para mais informações sobre o cenário de utilizadores notificar, consulte o artigo [Notificar utilizadores com notificação concentradores].

##<a name="update-your-app"></a>Atualizar a sua aplicação  

1. No seu MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca do objeto:

    + **Etiqueta**: "Notificações Push para o utilizador com concentradores de notificação"
    + **Etiqueta**: "InstallationId"
    + **Etiqueta**: "Utilizador"
    + **Campo de texto**: "Utilizador"
    + **Etiqueta**: "Palavra-passe"
    + **Campo de texto**: "Palavra-passe"
    + **Botão**: "Início de sessão"

    Neste momento, o storyboard aspeto o seguinte procedimento:

    ![][0]

2. No editor de assistente, criar mercados para todos os controlos ligados e ligá-las, ligar-se os campos de texto com o controlador de vista (delegado) e criar uma **ação** do botão de **início de sessão** .

    ![][1]

    O ficheiro BreakingNewsViewController.h deve agora conter o seguinte código:

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. Criar uma classe denominada **DeviceInfo**e copie o código seguinte para a secção da interface do ficheiro DeviceInfo.h:

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. Copie o código seguinte na secção de implementação do ficheiro DeviceInfo.m:

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. No PushToUserAppDelegate.h, adicione o singleton propriedade seguintes:

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. Método de **didFinishLaunchingWithOptions** no PushToUserAppDelegate.m, adicione o seguinte código:

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    A primeira linha inicia a singleton **DeviceInfo** . A segunda linha é iniciado o registo de já tiver concluído o tutorial de [Introdução ao concentradores de notificação] que é o push notificações, o que já está a apresentam.

9. Na PushToUserAppDelegate.m, implementar o método **didRegisterForRemoteNotificationsWithDeviceToken** no seu AppDelegate e adicione o seguinte código:

        self.deviceInfo.deviceToken = deviceToken;

    Este procedimento define o token de dispositivo para o pedido.

    > [AZURE.NOTE] Neste momento, deverá não existir qualquer outro código deste método. Se já tiver uma chamada para o método de **registerNativeWithDeviceToken** que foi adicionado quando tiver concluído o tutorial de [Introdução ao concentradores de notificação](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , tem saída comentário ou remover essa chamada.

10. No ficheiro de PushToUserAppDelegate.m, adicione o seguinte método de processador:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     Este método apresenta um alerta na interface de utilizador quando a sua aplicação recebe as notificações enquanto estiver em execução.

9. Abra o ficheiro PushToUserViewController.m e devolver o teclado na implementação que se segue:

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. Método de **viewDidLoad** no ficheiro PushToUserViewController.m, iniciar a etiqueta installationId da seguinte forma:

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. Adicione as seguintes propriedades na interface em PushToUserViewController.m:

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. Em seguida, adicione implementação que se segue:

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. Copie o código seguinte para o método de processador de **início de sessão** criado por XCode:

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    Este método obtém um ID de instalação e o canal para as notificações push e envia-lo, juntamente com o tipo de dispositivo, para o método Web API autenticado que cria um registo na notificação concentradores. Esta API Web foi definido no [Notificar utilizadores com notificação concentradores].

Agora que a aplicação de cliente foi atualizada, regresse ao [Notificar utilizadores com notificação concentradores] e atualizar o serviço móvel para enviar notificações ao utilizar concentradores de notificação.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificar os utilizadores com notificação concentradores]: /manage/services/notification-hubs/notify-users-aspnet

[Introdução ao concentradores de notificação]: /manage/services/notification-hubs/get-started-notification-hubs-ios
