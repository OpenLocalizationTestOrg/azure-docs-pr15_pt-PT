<properties
    pageTitle="Azure B2C diretório ativos: Chamar uma web API a partir de uma aplicação do iOS utilizar bibliotecas de terceiros | Microsoft Azure"
    description="Este artigo mostrar-lhe como criar uma aplicação de lista de itens pendentes do iOS que liga um Node.js da web API utilizando tokens de tipo de ligação OAuth 2.0 através de uma biblioteca de terceiros"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< etiquetas ms.service= "ativo-diretório b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic= "herói artigo"

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C: Ligar para um web API de uma aplicação iOS através de uma biblioteca de terceiros

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

A plataforma de identidade do Microsoft utiliza padrões abertos como oauth2 ainda e OpenID ligar. Esta opção permite-programadores tirar partido qualquer biblioteca pretendem integrar com os nossos serviços. Para ajudar os programadores a utilizar o nossa plataforma com outras bibliotecas podemos escreveu tutoriais alguns como esta para demonstate como configurar bibliotecas de terceiros para ligar a plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação de oauth2 RFC6749 ainda](https://tools.ietf.org/html/rfc6749) poderão ligar-se para a plataforma Microsoft Identity.


Se estiver habituado a oauth2 ainda ou OpenID ligar muita esta configuração de exemplo não pode fazer muito sentido para si. Recomendamos que veja uma breve [Descrição geral do protocolo que podemos tenha aqui documentados](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Algumas funcionalidades da nossa plataforma que têm uma expressão nessas normas, tal como a gestão de políticas de acesso condicional e Intune, requerem a utilizar o nossa abrir origem das bibliotecas de identidade do Microsoft Azure. 
   
Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pela plataforma B2C.  Para determinar se deve utilizar a plataforma B2C, leia sobre as [limitações B2C](active-directory-b2c-limitations.md).


## <a name="get-an-azure-ad-b2c-directory"></a>Obter um directory B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e mais. Se não tiver um já, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C. Isto dá informações do Azure AD que precisa de comunicar com a sua aplicação de forma segura. A aplicação e web API são representados por um único **ID da aplicação** neste caso, uma vez que incluem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). Não se esqueça de:

- Inclua um **dispositivo móvel** na aplicação.
- Copie o **ID da aplicação** que está atribuída a sua aplicação. Também terá este mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém uma experiência de identidade: uma combinada iniciar sessão e inscrição. Tem de criar esta política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando criar a política, não se esqueça de:

- Selecione o **nome a apresentar** e os atributos de inscrição no seu política.
- Selecione os **nome a apresentar** e o **ID do objeto** afirmações da aplicação em todas as políticas. Pode escolher também outros créditos.
- Copie o **nome** de cada política depois de o criar. Deve ter o prefixo `b2c_1_`.  Terá do nome da política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de ter criado as políticas, está pronto para criar a sua aplicação.


## <a name="download-the-code"></a>Transferir o código

O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  A seguir ao longo, pode [Transferir a aplicação como um. zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) ou cloná-lo:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Ou transferir o código concluído e começar imediatamente: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Transferir o nxoauth2 da biblioteca de terceiros e iniciar uma área de trabalho

Para este tutorial utilizamos OAuth2Client a partir do GitHub, uma biblioteca de oauth2 ainda para o Mac OS X & iOS (cacau & cacau toque). Esta biblioteca é baseada no rascunho 10 da especificação de oauth2 ainda. Esta versão implementa o perfil de aplicação nativa e suporta o ponto final de autorização de utilizador final. Estas são todas as coisas que irá precisamos ordem para integrat com plataforma de identidade do Microsoft.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Adicionar a biblioteca ao seu projeto com o CocoaPods

CocoaPods é um Gestor de dependência para Xcode projetos. Gere automaticamente os passos acima da instalação.

```
$ vi Podfile
```
Adicione o seguinte para este podfile:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Carrega agora podfile utilizando cocoapods. Isto vai criar uma área de trabalho XCode nova que irá carregar.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>A estrutura do projeto

Temos a seguinte estrutura configurar para os nossos projeto na estrutura:

* **Vista do modelo global** com um painel de tarefas
* Uma **Vista de tarefas de adicionar** para os dados sobre a tarefa selecionada
* Uma **Vista de início de sessão** que permite que um utilizador para iniciar sessão na aplicação.

Vamos irá saltar para os diversos ficheiros do projeto para adicionar autenticação. Outras partes do código como o código do visual não está germano a identidade e são fornecidos por si.

## <a name="create-the-settingsplist-file-for-your-application"></a>Criar o `settings.plist` ficheiro para a sua aplicação

É mais fácil configurar a aplicação se temos uma localização centralizada para colocar o nossos valores de configuração. Também ajuda-o a compreender o que faz cada definição na sua aplicação. Vamos irá tirar partido da *Lista de propriedades* como uma maneira de fornecer estes valores para a aplicação.

* Criar/abrir o `settings.plist` de ficheiros em `Supporting Files` na sua área de trabalho de aplicação

* Introduza os seguinte valores (Recomendamos irá analisá-los em detalhe mais cedo)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Vamos aceda na estas detalhadamente.


Para `authURL`, `loginURL`, `bhh`, `tokenURL` notará que precisa preencher o nome do seu inquilino. Este é o nome do inquilino do seu inquilino do B2C que foi atribuído a si. Por exemplo, `kidventusb2c.onmicrosoft.com`. Se utiliza o nossa abrir origem das bibliotecas de identidade do Microsoft Azure podemos seria separar estes dados para baixo para a usar o nosso ponto final de metadados. Conseguimos o disco rígido trabalho extrair estes valores por si.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

O `keychain` valor é o contentor que a biblioteca NXOAuth2Client irá utilizar para criar uma porta-chaves para armazenar o seu tokens. Se gostaria de obter SSO das várias aplicações vários pode especificar a porta-chaves mesmo em cada uma das suas aplicações, bem como pedir a utilização dessa porta-chaves no seu entitements XCode. Isto é abrangido na documentação da Apple.

O `<policy name>` no final de cada URL são os locais onde pretende colocados a política que criou acima. A aplicação irá efetuar a chamada estas políticas consoante o fluxo.

O `taskAPI` é o ponto final resto chamamos com o token de B2C quer adicionar tarefas ou consulta existente. Esta tenha sido configurada especificamente para que este exemplo. Não tem de alterá-la para o exemplo funcione.

Os restantes estes valores são necessários para utilizar a biblioteca e criar simplesmente locais onde pode conter valores para o contexto.

Agora que temos a `settings.plist` ficheiro criado, precisamos código lê-la.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Configurar uma classe AppData para ler os nossos definições

Vamos tornar um ficheiro simple que apenas analisa os nossos `settngs.plist` ficheiro que criou acima e efetuar essas definições está disponível no futuro a qualquer classe. Uma vez que não queremos criar uma nova cópia dos dados sempre que uma classe pede, iremos utilizar um padrão de Singleton e só devolver na mesma instância criada cada vez que um pedido de for feito para que as definições

* Criar um `AppData.h` ficheiro:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Criar um `AppData.m` ficheiro:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Agora, vamos possa obter facilmente os nossos dados de ao contactar o suporte simplesmente `  AppData *data = [AppData getInstance];` em qualquer um dos nossos classes à medida que vai ver abaixo.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configurar a biblioteca de NXOAuth2Client no seu AppDelegate

A biblioteca de NXOAuthClient requer alguns valores para configurar o. Uma vez ou seja concluída pode utilizar o token de que está adquiriram para chamar a API REST. Uma vez que recomendamos como saber que o `AppDelegate` será chamado qualquer altura, podemos carregar a aplicação faz sentido podemos de colocar a nossa valores configuração para esse ficheiro.
* Abrir `AppDelegate.m` ficheiro

* Importe alguns ficheiros de cabeçalho que utilizamos mais tarde.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Adicionar o `setupOAuth2AccountStore` método na AppDelegate

Precisamos de criar um AccountStore e, em seguida, o feed os dados que acabou de ler na partir do `settings.plist` ficheiro.

Eis algumas coisas que deve ter em mente sobre o serviço de B2C neste momento que irá tornar mais compreensíveis este código:


1. Azure AD B2C utiliza a *política* tal como fornecido pelos parâmetros de consulta para o seu pedido de serviço. Esta opção permite-Azure Active Directory funcionar como um serviço independente apenas para a sua aplicação. Para fornecer estes parâmetros precisamos de fornecer extra de consulta a `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` método com os nossos parâmetros de política personalizada. 

2. Azure AD B2C utiliza âmbitos na maior parte da mesma forma como outros servidores de oauth2 ainda. No entanto uma vez que a utilização de B2C é quanto acerca de um utilizador como aceder a recursos de autenticação algumas âmbitos realmente são necessários para que o fluxo funcionar corretamente. Este é o `openid` âmbito. Os nossos identidade Microsoft SDK fornece automaticamente a `openid` âmbito para si, para que não irá ver que no nossa configuração SDK. No entanto, uma vez que estamos a utilizar uma biblioteca de terceiros, precisamos de especificar este âmbito.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Em seguida, certifique-se de que telefonar-AppDelegate em `didFinishLaunchingWithOptions:` método. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Criar um `LoginViewController` escolares que utilizamos para processar pedidos de autenticação

Utilizamos uma vista da Web para o início de sessão de conta. Esta opção permite--na perguntar ao utilizador para adicionais fatores como mensagem de texto SMS (se configurado) ou conceda mensagens de erro ao utilizador. Aqui vamos definir para cima a vista da Web e, em seguida, mais tarde, escrever código para processar as chamadas de retorno que irão acontecer na vista a Web a identidade do serviço da Microsoft.

* Criar um `LoginViewController.h` classe

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Vamos criar cada um dos seguintes métodos abaixo.

> [AZURE.NOTE] 
    Certifique-se de que vincular o `loginView` para a vista da Web real que está no interior do seu storyboard. Caso contrário não tem uma vista da Web que pode pop-up quando é tempo para autenticar.

* Criar um `LoginViewController.m` classe

* Adicionar algumas variáveis para executar o estado como podemos autenticar

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Substituir os métodos de vista da Web para processar a autenticação

Precisamos de saber a vista da Web o comportamento que queremos quando um utilizador tem de iniciar sessão como de outros fabricantes referidos acima. Pode simplesmente cortar e colar o código abaixo.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Escrever código para processar o resultado do pedido oauth2 ainda

Vamos terá de código que irá processam o redirectURL que vêm novamente para a vista da Web. Se não foi efetuada com êxito, podemos tentará novamente. Entretanto, a biblioteca irá fornecer o erro que pode ver na consola do ou lidar asyncronously. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configure as fábricas de notificação.

Vamos criar o mesmo método fizemos na `AppDelegate` acima, mas desta vez, vamos adicionar alguns `NSNotification`s para diga-no que se passa no nosso serviço. Vamos configurar um observador que irá dê-na que nada muda com o token de. Depois de recebemos o token podemos regresse ao utilizador para o `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Adicionar código que trata o utilizador sempre que um pedido é iniciado para nativo de início de sessão

Vamos criar um método que será chamado sempre que temos um pedido de autenticação. Este será o método que realmente cria uma vista da Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Por fim, vamos chamar todos estes métodos podemos escreveu acima sempre o `LoginViewController` for carregada. Vamos fazê-lo adicionando estes métodos para os nossos `viewDidLoad` método Apple dá-nos

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Agora terminar com a criação de forma principal que podemos irá interagir com os nossos aplicação para iniciar sessão. Depois de ter feito podemos, irá precisamos de utilizar os nossos tokens que recebeu. Para que vamos criar algum código helper que irá levantar REST APIs-nos utilizando esta biblioteca.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Criar um `GraphAPICaller` classe para lidar com os nossos pedidos para um REST API

Temos uma configuração carregada sempre que recomendamos carregar a nossa aplicação. Agora precisamos de fazer algo com o mesmo assim que temos um token. 

* Criar um `GraphAPICaller.h` ficheiro

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Que vê deste código que recomendamos vão criar dois métodos: uma para obter as tarefas a partir de uma API e outro para adicionar tarefas à API.

Agora que recomendamos configurou o nosso interface, vamos adicionar a aplicação real:

* Criar um`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Executar a aplicação de exemplo

Por fim, criar e executar a aplicação no Xcode. Se inscrever ou iniciar sessão aplicação e criar tarefas de um utilizador com sessão iniciada. Terminar sessão e volta a iniciar sessão como um utilizador diferente e criar tarefas para esse utilizador.

Repare que as tarefas são armazenado por utilizador no API, porque a API extrai a identidade do utilizador do token de acesso que receba.


## <a name="next-steps"></a>Próximos passos

Agora pode mover para tópicos B2C mais avançados. Poderá tentar:

[Ligar uma Node.js da web API a partir de uma aplicação web do Node.js]()

[Personalizar UX para uma aplicação do B2C]()
