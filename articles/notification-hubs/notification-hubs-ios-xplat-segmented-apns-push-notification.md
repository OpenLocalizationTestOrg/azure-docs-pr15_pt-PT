<properties
    pageTitle="Notificação concentradores força News Tutorial - iOS"
    description="Saiba como utilizar o Azure Service Bus notificação concentradores enviar notificações de notícias de divisão para dispositivos iOS."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar concentradores de notificação para enviar notícias de última hora

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como utilizar o Azure notificação concentradores difundir força as notificações de notícias para uma aplicação do iOS. Quando estiver concluído, serão poderá para se registar no força de categorias de notícias que lhe interessam e receber apenas as notificações push para as categorias. Este cenário é um padrão comum para aplicações muitos onde notificações tem de ser enviadas para grupos de utilizadores que tenham declarados anteriormente interesse nos mesmos, por exemplo, o leitor RSS, aplicações para ventoinhas música, etc.

Cenários de difusão estão ativados para inclusão de um ou mais _etiquetas_ ao criar um registo no centro do notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que tenham registados para a etiqueta irão receber a notificação. Porque etiquetas são simplesmente cadeias, não tem de ser aprovisionada com antecedência. Para mais informações sobre etiquetas, consulte [notificação concentradores encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Pré-requisitos

Este tópico constrói a aplicação que criou no [artigo Introdução ao concentradores de notificação][get-started]. Antes de iniciar este tutorial, tem já concluiu [começar com notificação concentradores][get-started].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria à aplicação

É o primeiro passo adicionar os elementos da IU ao seu guião gráfico existente que permitem ao utilizador selecionar categorias para registar. As categorias seleccionadas por um utilizador são armazenadas no dispositivo. Quando inicia a aplicação, um registo de dispositivo é criado no seu centro de notificação com as categorias selecionados como etiquetas.

1. No seu MainStoryboard_iPhone.storyboard adicione os seguintes componentes da biblioteca do objeto:
    + Uma etiqueta com texto como "Força News",
    + Etiquetas com as textos de categoria "World", "Políticas de alto nível", "Empresas", "Tecnologia", "Ciência", "Desportos"
    + Parâmetros de seis, um por categoria, defina cada parâmetro **Estado** para ser **desativada** por predefinição.
    + O botão denominada "Subscrever"

    O storyboard deverá ter o aspeto da seguinte forma:

    ![][3]

2. No editor de assistente, crie mercados para todos os parâmetros e chame-los "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"


3. Crie uma ação para o seu botão denominado "subscrever". O ViewController.h devem conter o seguinte:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Criar uma nova **Cacau toque classe** denominado `Notifications`. Copie o código seguinte na secção interface do ficheiro Notifications.h:

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Adicione a diretiva de importar seguintes para Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copie o código seguinte na secção de implementação do ficheiro Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Esta classe utiliza armazenamento local para armazenar e obter as categorias de notícias que irão receber este dispositivo. Além disso, contém um método para se registar nestas categorias utilizando um [modelo de](notification-hubs-templates-cross-platform-push-messages.md) registo.

7. No ficheiro de AppDelegate.h, adicione uma declaração de importação para Notifications.h e adicionar uma propriedade de uma instância a classe de notificações de:

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. Método de **didFinishLaunchingWithOptions** no AppDelegate.m, adicione o código para iniciar a instância de notificações no início do método.  
 
    `HUBNAME`e `HUBLISTENACCESS` (definido no hubinfo.h) já deve ter o `<hub name>` e `<connection string with listen access>` marcadores de posição substituídos com o seu nome de concentrador de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anterior

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Porque credenciais são distribuídas com uma aplicação de cliente não são geralmente seguras, deverá apenas distribuir a chave para ouvir acesso com a sua aplicação de cliente. Ouvir permite acesso a sua aplicação para se registar no notificações, mas os registos existentes não pode ser modificada e as notificações não podem ser enviadas. A tecla de acesso total é utilizada num serviço de back-end segura para envio de notificações e alterar os registos existentes.


9. Método de **didRegisterForRemoteNotificationsWithDeviceToken** no AppDelegate.m, substitua o código do método com o código seguinte para passar o token de dispositivo para a classe de notificações. A classe de notificações fará registar para notificações com as categorias. Se o utilizador altera seleções de categoria, chamamos a `subscribeWithCategories` método em resposta ao botão **subscrever** atualizá-las.

    > [AZURE.NOTE] Uma vez que o token de dispositivo atribuído ao Apple emissão notificação de serviço (APNS) pode provável que em qualquer altura, deverá registar para notificações com frequência evitar falhas de notificação. Este exemplo regista para notificação sempre que inicia a aplicação. Para as aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para manter a largura de banda se menos de um dia passou desde o registo anterior.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Repare que neste momento não existem deve ser sem outro código no método **didRegisterForRemoteNotificationsWithDeviceToken** .

10. Métodos que se seguem já devem ser presentes no AppDelegate.m de concluir [Introdução ao notificação concentradores] [ get-started] tutorial.  Caso contrário, adicioná-los.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Este método trata as notificações recebidas quando a aplicação estiver em execução, apresentando uma simples **UIAlert**.

11. No ViewController.m, adicione uma instrução de importação para AppDelegate.h e copie o código seguinte para o método gerados pelo XCode **subscrever** . Este código irão atualizar o registo de notificação para utilizar as novas tags de categoria, o utilizador tiver optado por na interface de utilizador.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Este método cria uma **NSMutableArray** das categorias e utiliza a classe de **notificações** para armazenar a lista no armazenamento local e regista as tags correspondentes com o seu centro de notificação. Quando categorias são alteradas, o registo é recriado com novas categorias.

12. Na ViewController.m, adicione o código seguinte no método **viewDidLoad** para definir a interface de utilizador com base nas categorias de versão guardadas anteriormente.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



A aplicação agora pode armazenar um conjunto de categorias no armazenamento local dispositivo utilizado para registar o concentrador de notificação sempre que inicia a aplicação.  O utilizador pode alterar a seleção de categorias de tempo de execução e clique no método de **subscrever** a actualizar o registo para o dispositivo. Em seguida, irá atualizar a aplicação para enviar as notificações de notícias força diretamente na própria aplicação.


##<a name="optional-sending-tagged-notifications"></a>(opcional) Enviar notificações com tags

Se não tiver acesso a Visual Studio, pode avançar para a secção seguinte e enviar notificações a partir da aplicação propriamente dita. Pode também enviar a notificação de modelos adequado a partir do [Portal clássica Azure] utilizando o separador de depuração para o seu centro de notificação. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificações a partir do dispositivo

Normalmente notificações vão ser enviadas por um serviço de back-end, mas, pode enviar notificações de notícias força diretamente a partir da aplicação. Para fazer isto podemos irão atualizar o `SendNotificationRESTAPI` método que recomendamos definidos em [Introdução ao notificação concentradores] [ get-started] tutorial.


1. No ViewController.m update a `SendNotificationRESTAPI` método como se segue para que aceita um parâmetro para a etiqueta de categoria e envia uma notificação a inicial [modelo](notification-hubs-templates-cross-platform-push-messages.md) .

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

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



2. No ViewController.m Atualize a ação de **Enviar notificação** , conforme mostrado no código que se segue. Para que irá enviar notificações utilizar cada uma tag individualmente e enviar para múltiplas plataformas.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Reconstruir o seu projeto e certifique-se de que tem sem erros de compilação.


##<a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. Prima o botão executar para criar o projecto e iniciar a aplicação. Selecione algumas opções de notícias de divisão para subscrever e, em seguida, prima o botão **subscrever** . Deverá ver uma caixa de diálogo que indica que as notificações foi subscreveu o.

    ![][1]

    Quando escolher **subscrever**, a aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados a partir do hub a notificação.

2. Introduza uma mensagem ser enviada como notícias de última hora, em seguida, prima o botão **Enviar notificação** . Em alternativa, execute a aplicação de consola do .NET para gerar notificações.

    ![][2]


3. Cada dispositivo subscrever o últimas notícias irão receber as notificações de notícias força que apenas enviou.



## <a name="next-steps"></a>Próximos passos

Neste tutorial, vamos aprendeu como difundir notícias de última hora por categoria. Considere a concluir uma das seguintes tutoriais que destacam outros cenários de notificação concentradores avançados:

+ **[Utilizar notificação concentradores difundir notícias de última hora localizados]**

    Saiba como expandir a aplicação de notícias de divisão para ativar o envio de notificações localizadas.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizar notificação concentradores difundir notícias de última hora localizados]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure Portal clássico]: https://manage.windowsazure.com
