<properties
    pageTitle="Azure AD 2.0 aplicação iOS do | Microsoft Azure"
    description="Como criar uma aplicação para iOS que inicia sessão em utilizadores com ambos os conta Microsoft pessoal e contas escolar ou profissional ao utilizar bibliotecas de terceiros."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar iniciar sessão para uma aplicação do iOS através de uma biblioteca de terceiros com API Graph utilizando o ponto final 2.0

A plataforma de identidade do Microsoft utiliza padrões abertos como oauth2 ainda e OpenID ligar. Os programadores podem utilizar qualquer biblioteca que pretendem integrar com os nossos serviços. Para ajudar os programadores a utilizar o nossa plataforma com outras bibliotecas, podemos escreveu tutoriais alguns como esta para demonstrar como configurar bibliotecas de terceiros para ligar a plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação de oauth2 RFC6749 ainda](https://tools.ietf.org/html/rfc6749) podem ligar a plataforma de identidade da Microsoft.

Com a aplicação que cria destas instruções, os utilizadores podem iniciar sessão na sua organização e, em seguida, procure outras pessoas na sua organização ao utilizar a API do gráfico.

Se estiver habituado a oauth2 ainda ou OpenID ligar, muita esta configuração de exemplo poderá não adequadas para si. Recomendamos que leia [protocolos 2.0 - OAuth 2.0 autorização de código de um fluxo](active-directory-v2-protocols-oauth-code.md) de fundo.


> [AZURE.NOTE]
    Algumas funcionalidades da nossa plataforma que têm uma expressão nas normas oauth2 ainda ou OpenID ligar, como o acesso condicional e gestão da política Intune, requerem a utilizar o nossa abrir origem das bibliotecas de identidade do Microsoft Azure.

O ponto final 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades.

> [AZURE.NOTE]
    Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Transferir o código do GitHub
O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Também tem apenas pode transferir o exemplo e começar imediatamente:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registe-se uma aplicação
Criar uma nova aplicação no [portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga os passos detalhados sobre como [registar uma aplicação com o ponto final 2.0](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copie o **Id da aplicação** que está atribuída a sua aplicação de uma vez que irá precisar mais rapidamente.
- Adicione a plataforma **móvel** para a sua aplicação.
- Copie o **URI redirecionar** a partir do portal. Tem de utilizar o valor predefinido de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Transferir a biblioteca de NXOAuth2 de terceiros e criar uma área de trabalho

Para este tutorial, irá utilizar OAuth2Client a partir do GitHub, que é uma biblioteca de oauth2 ainda para o Mac OS X e iOS (cacau e cacau toque). Esta biblioteca é baseada no rascunho 10 da especificação de oauth2 ainda. Esta versão implementa o perfil de aplicação nativa e suporta o ponto final de autorização do utilizador. Estas são todas as coisas que vai precisar de integrar com a plataforma de identidade da Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Adicionar a biblioteca ao seu projeto utilizando CocoaPods

CocoaPods é um Gestor de dependência para Xcode projetos. Gere automaticamente os passos da instalação anterior.

```
$ vi Podfile
```
1. Adicione o seguinte para este podfile:

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Carregue o podfile utilizando CocoaPods. Isto vai criar uma área de trabalho Xcode nova que que irá carregar.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorar a estrutura do projeto

A seguinte estrutura está configurada para o nosso project na estrutura:

- Uma vista de modelo global de com uma pesquisa UPN
- Uma vista de detalhes para os dados sobre o utilizador selecionado
- Uma vista de início de sessão onde um utilizador pode iniciar sessão aplicação para consultar o grafo do

Vamos irá mover para os diversos ficheiros na estrutura para adicionar autenticação. Outras partes do código, tal como o código do visual, não relacionados com a identidade, mas são fornecidos por si.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurar o ficheiro settings.plst na biblioteca

-   No project guia de introdução, abra o `settings.plist` ficheiro. Substitua os valores dos elementos na secção para refletir os valores que utilizou no portal do Azure. O código irá referenciar estes valores sempre que utiliza a biblioteca de autenticação do Active Directory.
    -   O `clientId` é o ID de cliente da sua aplicação que copiou a partir do portal.
    -   O `redirectUri` é o URL de redirecionar o portal fornecido.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurar a biblioteca de NXOAuth2Client no seu LoginViewController

A biblioteca de NXOAuth2Client requer alguns valores para configurar o. Depois de concluir esta tarefa, pode utilizar o token adquirido para chamar a API do gráfico. Uma vez que `LoginView` será chamado a qualquer altura precisamos de autenticar, faz sentido para colocar os valores de configuração para esse ficheiro.

- Vamos adicionar alguns valores para o `LoginViewController.m` ficheiro para definir o contexto para autenticação e autorização. Obter mais detalhes sobre os valores siga o código.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Vamos ver detalhes sobre o código.

A primeira cadeia destina-se `scopes`.  O `User.Read` valor permite-lhe o perfil do utilizador com sessão iniciada na base de ler.

Pode obter mais informações sobre todos os âmbitos disponíveis na [Microsoft Graph âmbitos de permissão](https://graph.microsoft.io/docs/authorization/permission_scopes).

Para `authURL`, `loginURL`, `bhh`, e `tokenURL`, deve utilizar os valores fornecidos anteriormente. Se utilizar a abrir origem das bibliotecas de identidade do Microsoft Azure, isso significa iremos utilizar estes dados para baixo para si, utilizando o nosso ponto final de metadados. Conseguimos o disco rígido trabalho extrair estes valores por si.

O `keychain` valor é o contentor que a biblioteca NXOAuth2Client irá utilizar para criar uma porta-chaves para armazenar o seu tokens. Se gostaria de sessão único (SSO) transmite a inúmeros aplicações, pode especificar a porta-chaves mesmo em cada uma das suas aplicações e pedir a utilização dessa porta-chaves no seu direitos Xcode. Isto é explicado na documentação da Apple.

Os restantes estes valores são necessários para utilizar a biblioteca e criar locais onde pode conter valores para o contexto.

### <a name="create-a-url-cache"></a>Criar uma cache de URL

Dentro de `(void)viewDidLoad()`, que sempre chama-se depois da vista é carregada, o código seguinte prémios uma cache para os nossos utilização.

Adicione o seguinte código:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Criar uma vista da Web para iniciar sessão

Uma vista da Web pode pedir ao utilizador adicionais fatores como mensagem de texto SMS (se configurado) ou voltar a mensagens de erro ao utilizador. Seguem-configure-da vista Web para cima e, em seguida, mais tarde, escrever código para processar as chamadas de retorno que irão acontecer na vista a Web dos serviços de identidade.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Substituir os métodos de vista da Web para processar a autenticação

Para indicar a vista da Web, o que acontece quando necessita de um utilizador para iniciar sessão como abordado anteriormente, pode colar o código seguinte.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Escrever código para processar o resultado do pedido oauth2 ainda

O código seguinte processará a redirectURL que devolve da vista a Web. Se a autenticação não foi efetuada com êxito, o código tentará novamente. Entretanto, a biblioteca irá fornecer o erro que pode ver na consola do ou lidar modo assíncrono.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurar o contexto OAuth (denominados arquivo de contas)

Aqui pode ligar a `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` no arquivo de conta partilhada para cada serviço que pretende que a aplicação possam aceder. O tipo de conta é uma cadeia que será utilizada como um identificador para um determinado serviço. Uma vez que está a aceder à API do gráfico, o código que se refere a-la como `"myGraphService"`. Em seguida, configurar um observador que irá indicar que nada muda com o token de. Depois de receber o token, é devolvido o utilizador novamente para o `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurar a vista do modelo global para procurar e apresentar os utilizadores a partir da API do gráfico

Uma aplicação do controlador de vista de modelo global (MVC) que apresenta os dados devolvidos na grelha de está fora do âmbito destas instruções e tutoriais online muitos explicam como criar um. Todos os este código está no ficheiro de estrutura. No entanto, terá de lidar com algumas coisas nesta aplicação MVC:

* Interceptar quando um utilizador escreve algo no campo de pesquisa
* Fornecer um objeto de dados para o MasterView para que possa apresentar os resultados na grelha de

Vamos faça aqueles abaixo.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Adicionar uma verificação para ver se tem sessão iniciada

A aplicação não pouco se o utilizador não iniciou sessão no, para que fique inteligente verificar se já existe um token na cache. Caso contrário, redirecionar para o LoginView para o utilizador para iniciar sessão. Se recuperar, a melhor forma de efetuar ações quando carrega uma vista é utilizar a `viewDidLoad()` método que Apple fornece-nos.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Atualizar a vista de tabela quando for recebidos dados

Quando a API Graph devolve dados, é necessário apresentar os dados. Para simplificar, eis todos os códigos para atualizar a tabela. Pode colar apenas os valores à direita no seu código de texto automático MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Fornece uma maneira de chamar a API do gráfico quando alguém tipos no campo de pesquisa

Quando um utilizador escreve uma pesquisa na caixa, tem de shove que à Graph API. O `GraphAPICaller` classe, que irá criar o código seguinte, separa a funcionalidade de pesquisa da apresentação. Por agora, vamos escrever o código de feeds de carateres de pesquisa para a API do gráfico. Vamos fazê-lo ao fornecer um método chamado `lookupInGraph`, que assume a cadeia que pretendemos para procurar.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Escrever uma classe Helper para aceder a API do gráfico

Este é o essencial nossa aplicação. Considerando que os restantes foram inserir código no padrão MVC predefinido a partir da Apple, aqui é escrever código para consultar o gráfico como os tipos de utilizador e, em seguida, voltar esses dados. Eis o código e uma explicação detalhada segue-lo.

### <a name="create-a-new-objective-c-header-file"></a>Criar um novo ficheiro de cabeçalho de objectivo C

O nome do ficheiro `GraphAPICaller.h`e adicione o seguinte código.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Aqui pode ver que um método especificado leva-o até uma cadeia e devolve um completionBlock. Este completionBlock, poderá ter estimado, será atualizado a tabela ao fornecer um objeto com dados preenchidos em tempo real de como o utilizador procura.


### <a name="create-a-new-objective-c-file"></a>Criar um novo ficheiro de objectivo C

O nome do ficheiro `GraphAPICaller.m`e adicione o seguinte método.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Vejamos este método detalhadamente.

O essencial este código está a `NXOAuth2Request`, o método que utiliza os parâmetros que já tiver definido no ficheiro settings.plist.

O primeiro passo é construir a chamada Graph API à direita. Uma vez que está a telefonar `/users`, especificar que por acrescentá-la para o recurso de API do gráfico juntamente com a versão. Faz sentido para colocar estes num ficheiro de definições de externa, uma vez que estes podem alterar a API evolução deste.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Em seguida, tem de especificar parâmetros também irá fornecer à chamada API do gráfico. É *muito importante* que não introduz os parâmetros o ponto final de recurso porque que é apagou para todos os carateres conforme não URI o tempo de execução. Todos os códigos de consulta devem ser fornecidos nos parâmetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Poderá reparar em isto chama um `convertParamsToDictionary` método que ainda não escrita ainda. Vamos fazê-lo agora no final do ficheiro:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Em seguida, vamos utilizar a `NXOAuth2Request` método para voltar a partir da API no formato JSON dados.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Por fim, vamos ver como devolver os dados para o MasterViewController. Os dados devolve como serializado e tem de ser serialização e carregados num objeto que pode consumir o MainViewController. Para o efeito, a estrutura tem um `User.m/h` ficheiro que cria um objeto de utilizador. Preencher o objeto com informações do grafo do utilizador.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Executar o exemplo

Se tiver utilizado a estrutura ou seguidos juntamente com a sua aplicação deverá agora executar instruções passo a passo. Inicie o simulator e clique em **Iniciar sessão** para utilizar a aplicação.

## <a name="get-security-updates-for-our-product"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando o [TechCenter de segurança](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
