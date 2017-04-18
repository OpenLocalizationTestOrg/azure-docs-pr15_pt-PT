<properties
    pageTitle="Enviar notificações push para iOS com Azure notificação concentradores | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do iOS."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="notificações push notificação, as notificações push, ios notificações de emissão"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Enviar notificações push para iOS com concentradores de notificação do Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

Este tutorial mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do iOS. Crie uma aplicação para iOS em branco que recebe as notificações push utilizando o [serviço de notificações de Push da Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Quando tiver terminado, poderá utilizar o seu centro de notificação para difundir notificações push para todos os dispositivos de executar a sua aplicação.

## <a name="before-you-begin"></a>Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código para este tutorial completa pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ [Serviços móveis iOS SDK versão 1.2.4]
+ Versão mais recente do [Xcode]
+ Um iOS 8 (ou versão posterior)-noutros dispositivos
+ [Programa de programador do Apple](https://developer.apple.com/programs/) associação.

   > [AZURE.NOTE] Devido a requisitos de configuração para as notificações push, tem de implementar e testar as notificações push num dispositivo iOS física (iPhone ou iPad) em vez do iOS Simulator.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais concentradores de notificação para as aplicações do iOS.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o seu centro de notificação para iOS notificações de emissão

Esta secção apresenta-lhe através da criação de um concentrador de notificação de novo e configurar a autenticação com APNS utilizando o certificado de push **. p12** que criou. Se pretender utilizar um concentrador de notificação que já criou, pode avançar para o passo 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Clique no botão de <b>Serviços de notificação</b> na pá <b>Definições</b> e, em seguida, selecione <b>Apple (APNS)</b>. Clique no <b>Certificado carregar</b> e selecione o ficheiro <b>. p12</b> que exportou anterior. Certifique-se de que também especificar a palavra-passe correta.</p>
<p>Certifique-se selecionar o modo <b>Sandbox</b> dado que se trata de desenvolvimento. Utilize apenas a <b>produção</b> se pretende enviar notificações push para os utilizadores que comprou a sua aplicação a partir da loja.</p>
</li>
</ol>
&emsp;&emsp;![Configurar APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Configurar certificação de APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



O concentrador de notificação agora está configurado para funcionar com APNS e tiver as cadeias de ligação para registar a sua aplicação e enviar as notificações push.

##<a name="connect-your-ios-app-to-notification-hubs"></a>Ligar a sua aplicação iOS a notificação concentradores

1. Na Xcode, criar um novo projeto do iOS e selecione o modelo de **Aplicação de vista simples** .

    ![Xcode - vista única aplicação][8]

2. Quando definir as opções para o novo projeto, certifique-se utilizar o mesmo **Nome de produto** e **Identificador de organização** que utilizou quando tiver sido definida anteriormente o ID do pacote no portal do programador da Apple.

    ![Xcode - opções do project][11]

3. Em **destinos**, clique em nome do seu projeto, clique no separador **Definições construir** e expanda **Identidade de iniciar sessão de código**e, em seguida, em **Depurar**, defina a sua identidade assinatura em código. Ativar/desativar **níveis** de **básico** para **todos os**e definir **Aprovisionamento perfil** para o perfil de aprovisionamento que criou anteriormente.

    Se não vir o novo perfil de aprovisionamento que criou no Xcode, experimente atualizar os perfis de para a sua identidade assinatura. Clique em **Xcode** na barra de menus, clique em **Preferências**, clique no separador **conta** , clique no botão **Ver detalhes** , clique em sua identidade assinatura e, em seguida, clique no botão Atualizar no canto inferior direito.

    ![Xcode - aprovisionamento de perfil][9]

4. Transfira o [iOS de serviços móveis SDK versão 1.2.4] e deszipe o ficheiro. Na Xcode, com o botão direito do projeto e clique na opção de **Adicionar ficheiros** para adicionar a pasta **WindowsAzureMessaging.framework** ao projeto Xcode. Selecione **copiar itens se for necessário**e, em seguida, clique em **Adicionar**.

    >[AZURE.NOTE] Os controladores de notificação SDK atualmente não suporta bitcode no Xcode 7.  **Ativar Bitcode** tem de definir para **não** nas **Opções de compilação** para o seu projeto.

    ![Azure SDK descomprimir o ficheiro.][10]

5. Adicionar um novo ficheiro de cabeçalho ao seu projeto com o nome `HubInfo.h`. Este ficheiro mantenha a tecla as constantes para o seu centro de notificação.  Adicione as seguintes definições e substitua os marcadores de posição de literal de cadeia com o seu *nome do concentrador* e *DefaultListenSharedAccessSignature* indicados anteriormente.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Abrir o `AppDelegate.h` ficheiro adicionar diretivas das seguintes importar do:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. No seu `AppDelegate.m file`, adicione o seguinte código na `didFinishLaunchingWithOptions` método com base na sua versão do iOS. Este código regista o identificador de dispositivo com APNs:

    Para iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Para versões de iOS antes de 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. No mesmo ficheiro, adicione os seguintes métodos. Este código liga-se para o concentrador de notificação utilizando as informações de ligação que especificou no HubInfo.h. Em seguida, dá-token de dispositivo para o concentrador de notificação para que o concentrador de notificação possam enviar notificações:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. No mesmo ficheiro, adicione o seguinte método para apresentar uma **UIAlert** se a notificação é recebida enquanto a aplicação está ativa:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Criar e executar a aplicação no seu dispositivo para verificar que não existem sem falhas.

## <a name="send-test-push-notifications"></a>Enviar teste as notificações push


Pode testar receber notificações na sua aplicação ao enviar notificações push no [Portal do Azure] através da secção de **resolução de problemas** no pá concentrador (utilize a opção *Enviar testar* ).

![Portal Azure - enviar de teste][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>(Opcional) Enviar notificações push a partir da aplicação

>[AZURE.IMPORTANT] Este exemplo de envio de notificações a partir da aplicação de cliente é fornecido para conhecer apenas efeitos. Uma vez que este procedimento vai requerer a `DefaultFullSharedAccessSignature` estar presentes na aplicação de cliente, que expõe o seu centro de notificação para o risco que um utilizador poderá ter acesso ao enviar notificações não autorizadas aos seus clientes.

Se pretender enviar notificações push do dentro de uma aplicação, esta secção fornece um exemplo de como o fazer utilizando a interface do resto.

1. No Xcode, abra `Main.storyboard` e adicione os seguintes componentes de IU a partir da biblioteca de objeto para permitir que o utilizador envie as notificações push na aplicação:

    - Uma etiqueta com sem texto da etiqueta. Será utilizada para relatar erros nos enviar notificações. A propriedade de **linhas** deve estar definida como **0** , para que a mesma será automaticamente tamanho restringido a margens esquerdas e direita e parte superior da vista.
    - Definir um campo de texto com texto de **marcador de posição** para **Introduzir mensagem de notificação**. Restringir o campo imediatamente abaixo da etiqueta conforme apresentado abaixo. Defina o controlador de ver como o delegado de saída.
    - Restringido um botão intitulado **Enviar notificação** imediatamente abaixo do campo de texto e no Centro de horizontal.

    A vista deverá ter o aspeto da seguinte forma:

    ![Estruturador de fluxos de Xcode][32]


2. [Adicionar tomadas](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) para o campo de etiqueta e texto ligado a sua vista e atualizar o `interface` definição para suportar `UITextFieldDelegate` e `NSXMLParserDelegate`. Adicione as declarações de três propriedade mostradas abaixo para o ajudar a chamar REST API e a resposta de análise de suporte.

    O ficheiro ViewController.h deverá ter o aspeto da seguinte forma:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Abrir `HubInfo.h` e adicione as seguintes constantes que serão utilizadas para o envio de notificações a sua concentrador. Substitua a cadeia de marcador de posição literal a cadeia de ligação *DefaultFullSharedAccessSignature* real.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Adicione o seguinte `#import` declarações para sua `ViewController.h` ficheiro.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. No `ViewController.m` adicione o seguinte código para a implementação de interface. Este código irá analisar a cadeia de ligação *DefaultFullSharedAccessSignature* . Tal como mencionado na [referência REST API](http://msdn.microsoft.com/library/azure/dn495627.aspx), estes informações analisadas serão utilizadas para gerar um token de SA para o cabeçalho do pedido de **autorização** .

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. No `ViewController.m`, atualize o `viewDidLoad` método para analisar a cadeia de ligação quando a vista for carregada. Também adicione os métodos de utilitário, apresentados abaixo, para a implementação de interface.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. No `ViewController.m`, adicione o seguinte código para a implementação de interface para gerar o token de autorização de SA que será fornecido no cabeçalho da **autorização** , tal como mencionado na [Referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. CTRL + arrastar de **Enviar notificação** botão ' para `ViewController.m` para adicionar uma ação denominada **SendNotificationMessage** para o evento **De toque para baixo** . Atualize o método com o seguinte código para enviar a notificação de utilizar a API REST.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. No `ViewController.m`, adicione o seguinte método de delegado para suportar a fechar o teclado para o campo de texto. CTRL + arrastar a partir do campo de texto para o ícone do controlador de vista no designer interface para definir o controlador de ver como o delegado de saída.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. No `ViewController.m`, adicione os seguintes métodos de delegado para suportar a resposta de análise utilizando `NSXMLParser`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Criar o projecto e certifique-se de que não existem erros.


> [AZURE.NOTE] Se se deparar com um erro de compilação no Xcode7 sobre o suporte de bitcode, deverá alterar as **Definições de compilação** > **Bitcode ativar (ENABLE_BITCODE)** para **n** no Xcode. O SDK do concentradores notificação atualmente não suporta bitcode. 

Pode encontrar todos os payloads possíveis notificação na Apple no [Local e guia de programação de notificação de notificações Push].


##<a name="checking-if-your-app-can-receive-push-notifications"></a>Verificar se a sua aplicação pode receber notificações push

Para testar as notificações push IOS, tem de implementar a aplicação para um dispositivo iOS física. Não é possível enviar as notificações push da Apple utilizando o iOS Simulator.

1. Executar a aplicação e certifique-se de que o registo é concluída com êxito e, em seguida, prima **OK**.

    ![iOS aplicação de teste de registo de notificação de emissão][33]

2. Pode enviar uma notificação de emissão de teste a partir do [Portal do Azure], conforme descrito acima. Se tiver adicionado o código para o envio de notificações push na aplicação, toque no interior do campo de texto para introduzir uma mensagem de notificação. Em seguida, prima o botão **Enviar** , o teclado ou o botão de **Enviar notificação** na vista para enviar a mensagem de notificação.

    ![Aplicação de emissão Enviar teste da notificação do iOS][34]

3. A notificação de push é enviada para todos os dispositivos que estão registados para receber notificações da partir do concentrador de notificação específico.

    ![Aplicação de emissão receber teste da notificação do iOS][35]


##<a name="next-steps"></a>Próximos passos

Neste exemplo simples, difundida notificações push para todos os seus dispositivos iOS registado. Sugerimos como um passo seguinte na sua aprendizagem que prosseguir para o tutorial [Azure notificação concentradores notificar utilizadores para iOS com back-end do .NET] , que irá guiá-lo através da criação de back-end para enviar notificações push utilizar etiquetas. 

Se pretender segmentar os seus utilizadores por grupos de interesse, pode para além disso mover para o tutorial [Concentradores de notificação de utilização para enviar notícias de última hora] . 

Para obter informações gerais sobre concentradores de notificação, consulte o artigo [Notificação concentradores orientações].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Serviços móveis iOS SDK versão 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notificação concentradores orientações]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure notificação concentradores notificar utilizadores para iOS com back-end do .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Utilizar concentradores de notificação para enviar notícias de última hora]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local e guia de programação de notificações de emissão]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal do Azure]: https://portal.azure.com