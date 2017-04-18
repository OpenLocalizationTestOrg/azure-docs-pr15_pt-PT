<properties
    pageTitle="Notificação Azure concentradores seguros de emissão"
    description="Saiba como enviar notificações push seguro para uma aplicação do iOS a partir do Azure. Exemplos de código escritos objectivo-C e c#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Notificação Azure concentradores seguros de emissão

> [AZURE.SELECTOR]
- [Universal do Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Descrição geral

Suporte de notificações push no Microsoft Azure permite-lhe aceder a infraestrutura de push fáceis de utilizar múltiplas plataformas, dimensionada-out, o que simplifica significativamente a implementação de notificações push para aplicações do consumidor e enterprise para as plataformas de dispositivos móveis.

Devido ao regulamentação ou restrições de segurança, por vezes, uma aplicação poderá pretender incluir algo na notificação que não pode ser transmitida através de infraestrutura de notificação do push padrão. Neste tutorial descreve como alcançar a mesma experiência ao enviar informações confidenciais através de uma ligação segura, autenticada entre o dispositivo de cliente e o back-end de aplicação.

O fluxo de alto nível, é da seguinte forma:

1. Aplicação back-end:
    - Armazena seguro carga útil numa base de dados back-end.
    - Envia o ID desta notificação para o dispositivo (não seguras são enviadas informações).
2. A aplicação no dispositivo, quando receber a notificação:
    - O dispositivo de contactos a pedir a carga de útil segura back-end.
    - A aplicação pode mostrar a carga de útil como uma notificação no dispositivo.

É importante ter em atenção que no fluxo de anterior (e neste tutorial), podemos partem do princípio de que o dispositivo armazena token de autenticação em armazenamento local, depois do utilizador inicia sessão. Isto garante uma experiência totalmente totalmente integrada, tal como o dispositivo pode obter seguro carga útil a notificação utilizando este token. Se a sua aplicação não armazenar tokens de autenticação no dispositivo ou se estes tokens podem ser expirados, a aplicação de dispositivo, após receber a notificação deve mostrar uma notificação genérica perguntar ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra a carga útil notificação.

Neste tutorial seguro Push mostra como enviar uma notificação de emissão de forma segura. O tutorial constrói no tutorial [Notificar utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , para que devem concluir os passos que tutorial pela primeira vez.

> [AZURE.NOTE] Neste tutorial assume que criou e configurado o seu centro de notificação, tal como descrito em [Introdução ao notificação concentradores (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projecto iOS

Agora que a sua aplicação de segurança para enviar apenas o *id* de uma notificação que modificou, tem de alterar a sua aplicação iOS para processar essa notificação e uma chamada de retorno seu back-end para obter a mensagem segura para ser apresentado.

Para obter este objetivo, temos de escrever a lógica para obter o assegurar conteúdo a partir da aplicação back-end.

1. Na **AppDelegate.m**, certifique-se de que os registos de aplicação para notificações silenciosos para processa o id da notificação enviados a partir do back-end. Adicione a opção **UIRemoteNotificationTypeNewsstandContentAvailability** no didFinishLaunchingWithOptions:

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. No seu **AppDelegate.m** adicione uma secção de implementação na parte superior com a declaração seguinte:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. Adicionar, em seguida, na secção pós-implementação o código seguinte, substituindo o marcador de posição `{back-end endpoint}` com o ponto final para sua back-end obtido anteriormente:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Agora que temos que processar a notificação de receção e utilize o método acima para obter o conteúdo para apresentar. Em primeiro lugar, temos que ativar a sua aplicação ser executada em segundo plano quando receber uma notificação de emissão do iOS. No **XCode**, selecione o seu projeto de aplicação no painel da esquerda, em seguida, na secção **alvos** a partir do painel central de destino aplicação principal.

5. Em seguida, clique o separador **capacidades** na parte superior do painel central e selecione a caixa de verificação **Notificações remoto** .

    ![][IOS1]


6. No **AppDelegate.m** adicione o seguinte método para processar as notificações push:

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Tenha em atenção que é preferível para processar dos casos de propriedade de cabeçalho de autenticação em falta ou rejeição ao back-end. O processamento específico de nestes casos dependem principalmente em sua experiência de utilizador de destino. É uma opção apresentar uma notificação com um pedido genérico para o utilizador autenticar para obter a notificação real.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, faça o seguinte:

1. No XCode, execute a aplicação num dispositivo iOS física (push notificações não irão funcionar no simulator).

2. Na aplicação iOS IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas tem de ser o mesmo valor.

3. Na aplicação iOS IU, clique em **Iniciar sessão**. Em seguida, clique em **Enviar push**. Deverá visualizar a notificação segura sejam apresentada no Centro de notificação.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
