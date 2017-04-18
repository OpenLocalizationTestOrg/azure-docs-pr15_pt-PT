<properties
    pageTitle="Azure Mobile Cativação iOS SDK chegar a integração | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Como integrar Cativação contactar IOS

Tem de seguir o procedimento de integração descrito no [como integrar Cativação no iOS documento](mobile-engagement-ios-integrate-engagement.md) antes de seguir este guia.

Este documento requer XCode 8. Se dependem XCode 7 na verdade, em seguida, pode utilizar o [iOS Cativação SDK v3.2.4](https://aka.ms/r6oouh). Existe um erro conhecido nesta versão anterior ao executar o em dispositivos iOS 10: não são actioned notificações do sistema. Para corrigir isto, terá de implementar a API preterida `application:didReceiveRemoteNotification:` na sua aplicação delegar da seguinte forma:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Uma vez que este iOS API é preterida **não recomendamos esta solução** como este comportamento, pode alterar qualquer actualização da versão futuras iOS (mesmo menor). Deve mudar para XCode 8 mais cedo possível.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Ativar a sua aplicação receber notificações de emissão silenciosa

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Passos da integração

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporar o SDK do atingir compromisso no seu projeto do iOS

-   Adicione o sdk alcance do projeto Xcode. No Xcode, aceda a **projeto \> adicionar ao projeto** e escolha o `EngagementReach` pasta.

### <a name="modify-your-application-delegate"></a>Modificar o seu delegado de aplicação

-   Na parte superior do seu ficheiro de implementação, importe o módulo Cativação atinja:

        [...]
        #import "AEReachModule.h"

-   No interior do método `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, crie um módulo de atingir e passá-lo à sua linha de inicialização Cativação existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Modificar a cadeia de **'icon.png'** com o nome de imagem que pretende, como o ícone de notificação.
-   Se pretender utilizar a opção *valor distintivo de atualização* no campanhas de atingir ou se pretende utilizar push nativo \<SaaS/alcance API/campanha formato/nativo Push\> campanhas, é necessário que deixe o alcance módulo gerir o ícone do distintivo propriamente dito (-lo será automaticamente desmarcar o distintivo da aplicação e também repor o valor armazenado por Cativação sempre que a aplicação é iniciada ou foregrounded). Isto é feito ao adicionar a linha seguinte após a atingir inicialização de módulo:

        [reach setAutoBadgeEnabled:YES];

-   Se pretender gerir alcance push de dados, tem de deixar delegado aplicação está em conformidade com a `AEReachDataPushDelegate` protocolo. Adicione a linha seguinte após a atingir inicialização de módulo:

        [reach setDataPushDelegate:self];

-   Em seguida, pode implementar os métodos `onDataPushStringReceived:` e `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` no seu delegado de aplicação:

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Categoria

Parâmetro de categoria é opcional quando criar uma campanha de emissão de dados e permite-lhe filtrar dados emite. Isto é útil se pretender emissão diferentes tipos de `Base64` dados e pretende identificar o respetivo tipo antes da análise-los.

**A aplicação está agora pronta para receber e apresentar conteúdo de atingir!**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Como receber anúncios e consultas em qualquer altura

Cativação pode enviar notificações de atingir aos seus utilizadores finais em qualquer altura, utilizando o serviço de notificações Push da Apple.

Para ativar esta funcionalidade, terá de preparar a sua aplicação das notificações push da Apple e modificar o seu delegado de aplicação.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparar a sua aplicação das notificações push da Apple

Siga o guia: [como preparar a sua aplicação de notificações de emissão da Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Adicionar o código do cliente necessário

*Neste momento a aplicação deverá ter um certificado de push da Apple registado o frontend Cativação.*

Se o mesmo não estiver concluído, tem de registar a sua aplicação para receber notificações push.

* Importar o `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

* Adicione a linha seguinte quando a aplicação for iniciada (normalmente em `application:didFinishLaunchingWithOptions:`):

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

Em seguida, tem de fornecer aos Cativação o token de dispositivo devolvido por servidores do Apple. Isto é feito na método chamado `application:didRegisterForRemoteNotificationsWithDeviceToken:` no seu delegado de aplicação:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Por fim, tem de informar o SDK Cativação quando a aplicação recebe uma notificação remota. Para fazê-lo, contacte o método `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` no seu delegado de aplicação:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] O método acima é introduzido no iOS 7. Se está a filtrar iOS < 7, certifique-se implementar o método `application:didReceiveRemoteNotification:` nos seus delegado de aplicação e chamada `applicationDidReceiveRemoteNotification` no EngagementAgent passando nulo em vez do `handler` argumento:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Por predefinição, Cativação atinja controla o completionHandler. Se pretender responder manualmente para o `handler` bloquear no seu código, pode ser efetuada com valor nulo o `handler` argumento e controlo de conclusão de bloco de si próprio. Consulte o artigo o `UIBackgroundFetchResult` tipo para obter uma lista de valores possíveis.


### <a name="full-example"></a>Exemplo completo

Eis um exemplo completo da integração:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se tiver a sua própria implementação UNUserNotificationCenterDelegate

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

##<a name="how-to-customize-campaigns"></a>Como personalizar campanhas

### <a name="notifications"></a>Notificações

Existem dois tipos de notificações: notificações de sistema e na aplicação.

Notificações de sistema são processadas pelo iOS e não podem ser personalizadas.

Notificações de na aplicação são constituídas por uma vista que dinamicamente é adicionada à janela da aplicação atual. Esta opção é denominada uma sobreposição de notificação. As Sobreposições de notificação são perfeitas para a integração do fast porque não necessitam de modificar qualquer vista na sua aplicação.

#### <a name="layout"></a>Esquema

Para modificar o aspeto das suas notificações na aplicação, pode simplesmente modificar o ficheiro `AENotificationView.xib` às suas necessidades, desde que a manter os valores de etiqueta e tipos dos subviews existentes.

Por predefinição, as notificações de na aplicação são apresentadas na parte inferior do ecrã. Se preferir apresentá-los na parte superior do ecrã, edite o fornecido `AENotificationView.xib` e altere o `AutoSizing` propriedade da vista principal para que possam ser mantida na parte superior da sua superview.

#### <a name="categories"></a>Categorias

Quando a modificar o esquema fornecido, é modificar o aspeto de todas as notificações. Categorias permitem-lhe definir várias alvo o seguinte aspeto (possivelmente comportamentos) de notificações. Pode ser especificada uma categoria quando criar uma campanha de atingir. Tenha em atenção que categorias também permitem-lhe personalizar anúncios e consultas, que é descrita mais tarde neste documento.

Para registar um processador de categoria para as suas notificações, é necessário adicionar uma chamada assim que o módulo alcance é inicializado.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`tem de ser uma instância de um objeto que está em conformidade com o protocolo `AENotifier`.

Pode implementar os métodos de protocolo por si próprio ou pode optar por reimplement a classe existente `AEDefaultNotifier` que já executa a maior parte do trabalho.

Por exemplo, se pretende redefinir a vista de notificação para uma categoria específica, pode seguir este exemplo:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Este exemplo simple de categoria partem do princípio de que tem um ficheiro denominado `MyNotificationView.xib` no seu pacote de aplicação principal. Se o método não consiga encontrar um correspondente `.xib`, não será apresentada a notificação e Cativação saída de uma mensagem na consola do.

O ficheiro fornecido bico deve que diz respeito as seguintes regras:

-   Deverá conter apenas numa vista.
-   Subviews deve ser dos mesmos tipos como aqueles dentro do ficheiro bico fornecidos com o nome`AENotificationView.xib`
-   Subviews deve ter as mesmas marcas como aqueles dentro de fornecidos com o nome de ficheiro de bico`AENotificationView.xib`

> [AZURE.TIP] Basta copiar o ficheiro bico fornecido, denominado `AENotificationView.xib`e começar a trabalhar a partir daí. No entanto, tenha o cuidado, a vista dentro este ficheiro bico está associada à classe `AENotificationView`. Esta classe redefinidas o método `layoutSubViews` para mover e redimensionar os seus subviews de acordo com o contexto. Poderá pretender substituí-la com um `UIView` ou escolares vista personalizada.

Se precisar de mais aprofundada personalização do seu notificações (se pretender, por exemplo para carregar a sua vista diretamente a partir do código), recomenda-se para veja a documentação de código e escolares fornecido origem do `Protocol ReferencesDefaultNotifier` e `AENotifier`.

Tenha em atenção que pode utilizar o mesmo notificador para várias categorias.

Também pode redefinidas notificador predefinido da seguinte forma:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Processamento de notificação

Ao utilizar a categoria predefinida, alguns métodos de ciclo de vida são designado pelo `AEReachContent` objeto para estatísticas do relatório e actualizar o estado de campanha:

-   Quando a notificação é apresentada na aplicação, o `displayNotification` método denomina (que relatórios estatísticas) por `AEReachModule` se `handleNotification:` devolve `YES`.
-   Se a notificação é fechada, a `exitNotification` método é chamado, é comunicada estatística e campanhas seguintes agora podem ser processadas.
-   Se a notificação é clicada, `actionNotification` é chamado estatística for comunicada e é efetuada a ação associada.

Se a implementação do `AENotifier` ignora o comportamento predefinido, tem de chamar estes métodos de ciclo de vida por si. Os exemplos seguintes mostram alguns casos onde o comportamento predefinido é ignorado:

-   Não a expandir `AEDefaultNotifier`, por exemplo, o processamento de categoria de raiz implementada.
-   Overrode `prepareNotificationView:forContent:`, certifique-se de que pelo menos mapear `onNotificationActioned` ou `onNotificationExited` para um dos seus U.I controlos.

> [AZURE.WARNING] Se `handleNotification:` inicia uma exceção, o conteúdo é eliminada e `drop` é chamado, isto é comunicado nas estatísticas e campanhas seguintes agora podem ser processadas.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Incluir o aviso como parte de uma vista existente

As Sobreposições de são perfeitas para uma rápida integração, mas podem, por vezes, não ser convenientes ou podem ter efeitos lado indesejados.

Se não estiver satisfeito com o sistema de sobreposição em algumas das suas vistas, pode personalizá-lo para estas vistas.

Pode decidir incluir os nossos esquema notificação nas suas vistas existentes. Para fazê-lo, há dois estilos de implementação:

1.  Adicionar a vista de notificação utilizando o construtor de interface

    -   *Construtor de Interface* de aberta
    -   Colocar um 320 x 60 (ou 768 x 60 se estiver a iPad) `UIView` onde pretende que a notificação que seja apresentado
    -   Definir o valor de etiqueta para esta vista aos: **36822491**

2.  Adicione a vista de notificação através de programação. Adicione apenas o código seguinte quando tiver sido inicializada a sua vista:

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`acção de macro pode ser encontrada na `AEDefaultNotifier.h`.

> [AZURE.NOTE] O notificador predefinido Deteta automaticamente que o esquema da notificação está incluído nesta vista e não irá acrescentar uma sobreposição do mesmo.

### <a name="announcements-and-polls"></a>Anúncios e consultas

#### <a name="layouts"></a>Esquemas

Pode modificar os ficheiros `AEDefaultAnnouncementView.xib` e `AEDefaultPollView.xib` como manter os valores de etiqueta e tipos dos subviews existentes.

#### <a name="categories"></a>Categorias

##### <a name="alternate-layouts"></a>Esquemas alternativas

Como as notificações, categoria da campanha pode ser utilizada para têm esquemas alternativos para o seu anúncios e inquéritos.

Para criar uma categoria para um anúncio, tem de expandir **AEAnnouncementViewController** e, em registe-a assim que tiver sido inicializado o módulo alcance:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Sempre que um utilizador clicar numa notificação de um anúncio com a categoria "meu\_categoria", o controlador de vista registados (nesse caso `MyCustomAnnouncementViewController`) será inicializado ao contactar o método `initWithAnnouncement:` e a vista será adicionada à janela da aplicação atual.

Na implementação da `AEAnnouncementViewController` classe tiver de ler a propriedade `announcement` para iniciar a sua subviews. Considere o exemplo abaixo, onde duas etiquetas são inicializado utilizando `title` e `body` propriedades do `AEReachAnnouncement` classe:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Se não pretende carregar as vistas por si, mas pretende reutilizar o esquema de vista de anúncio predefinido, basta fazer com que o controlador de vista personalizada expande a classe fornecida `AEDefaultAnnouncementViewController`. Duplicar nesse caso, o ficheiro bico `AEDefaultAnnouncementView.xib` e mude o nome para que possa ser carregado pelo controlador de vista personalizada (para um controlador denominado `CustomAnnouncementViewController`, deverá contactar o seu ficheiro bico `CustomAnnouncementView.xib`).

Para substituir a categoria de predefinido de anúncios, basta registe-se o controlador de vista personalizada para a categoria definida na `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Inquéritos podem ser personalizados da mesma forma:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Desta vez, o fornecido `MyCustomPollViewController` tem expandir `AEPollViewController`. Ou pode optar por alargar a partir do controlador de predefinido: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] Não se esqueça de chamada quer `action` (`submitAnswers:` para os controladores de vista personalizada inquérito) ou `exit` método antes do controlador de vista está fechado. Caso contrário, estatísticas não serão enviadas (ou seja, não analytics da campanha) e campanhas nenhumas seguinte mais não serão notificadas até que seja reiniciado o processo de aplicação.

##### <a name="implementation-example"></a>Exemplo de implementação

Nesta implementação é carregada a vista personalizada anúncio de nascimento de um ficheiro de IX b externos.

Como para a personalização de notificação avançadas, recomenda-se para ver o código fonte de implementação padrão.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
