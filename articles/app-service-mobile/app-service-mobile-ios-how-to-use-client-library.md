<properties
    pageTitle="Como utilizar iOS SDK para as aplicações móveis do Azure"
    description="Como utilizar iOS SDK para as aplicações móveis do Azure"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Como utilizar iOS biblioteca do cliente para as aplicações móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina-lhe executar cenários comuns utilizando o mais recente [Azure Mobile aplicações iOS SDK][1]. Se estiver familiarizado com as aplicações móveis do Azure, primeiro concluída [Azure Mobile aplicações guia] para criar um back-end, criar uma tabela e transferir um projeto de Xcode iOS pré-concebidos. Neste guia, iremos concentrar-no iOS do lado do cliente SDK. Para saber mais sobre o SDK do lado do servidor para o back-end, consulte o HOWTOs de SDK do servidor.

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o cliente do iOS SDK se encontra aqui: [iOS Azure Mobile aplicações cliente referência][2].

## <a name="supported-platforms"></a>Plataformas suportadas

O iOS SDK suporta objectivo-C projetos, projetos Swift 2.2 e Swift 2.3 projetos para iOS versões 8.0 ou posteriores.

A autenticação de "servidor de fluxo de caixa" utiliza uma vista da Web para a IU apresentada.  Se o dispositivo não é possível apresentar uma vista da Web IU, em seguida, é necessário outro método de autenticação que esteja fora do âmbito do produto.  
Este SDK, pelo que não é adequado para o tipo de observação ou da mesma forma dispositivos restritos.

##<a name="Setup"></a>Configuração e pré-requisitos

Este guia assume que criou back-end com uma tabela. Este guia assume que a tabela que tem o mesmo esquema como as tabelas nesses tutoriais. Este guia também partem do princípio que no seu código referenciar `MicrosoftAzureMobile.framework` e importar `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Como: Criar cliente

Para aceder a um back-end de aplicações do Azure Mobile no seu projeto, criar uma `MSClient`. Substituir `AppUrl` com o URL da aplicação. Pode deixar `gatewayURLString` e `applicationKey` vazia. Se configurar um gateway para autenticação, povoar `gatewayURLString` com o URL de gateway.

**Objectivo-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Como: criar a referência de tabela

Para o access ou uma atualização de dados, criar uma referência para a tabela de back-end. Substituir `TodoItem` com o nome da tabela

**Objectivo-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Como: consultar dados

Para criar uma consulta de base de dados, consultar o `MSTable` objeto. A seguinte consulta obtém todos os itens no `TodoItem` e regista o texto de cada item.

**Objectivo-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Como: filtrar dados devolvidos

Para filtrar os resultados, existem muitas opções disponíveis.

Para filtrar utilizando um predicado, utilize uma `NSPredicate` e `readWithPredicate`. Os seguintes filtros devolvido dados para localizar apenas os itens Todo incompletos.

**Objectivo-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Como: utilizar MSQuery

Para executar uma consulta complexa (incluindo a ordenação e paginação), criar uma `MSQuery` objecto, diretamente ou utilizando um predicado:

**Objectivo-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`permite-lhe controlar várias comportamentos de consulta.

* Especificar ordem de resultados
* Limitar os campos para devolver
* Limitar os registos quantos para devolver
* Especificar a contagem total em resposta
* Especificar parâmetros de cadeia de consulta personalizadas no pedido
* Aplicar funções adicionais

Executar uma `MSQuery` consulta ao contactar o suporte `readWithCompletion` no objeto.

## <a name="sorting"></a>Como: ordenar dados com MSQuery

Para ordenar os resultados, vamos ver um exemplo. Para ordenar por campo 'texto' por ordem ascendente, em seguida por descendente «concluída», invocar `MSQuery` desta forma:

**Objectivo-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Como: limitar os campos e expandir parâmetros da cadeia de consulta com MSQuery

Para limitar os campos a ser devolvido numa consulta, especifique os nomes dos campos na propriedade **selectFields** . Este exemplo devolve apenas o texto e os campos concluídos:

**Objectivo-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Para incluir os parâmetros de cadeia de consulta adicionais no pedido de servidor (por exemplo, uma vez que um script do lado do servidor personalizado utiliza-los), povoar `query.parameters` desta forma:

**Objectivo-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Como: configurar o tamanho da página

Com as aplicações móveis do Azure, o tamanho da página controla o número de registos que são extraídos uma vez das tabelas back-end. Uma chamada para `pull` dados, em seguida, teriam batch dos dados, com base neste tamanho de página até que não existem mais registos de separar.

É possível configurar um tamanho de página utilizando **MSPullSettings** conforme apresentado abaixo. O tamanho de página predefinido é 50 e exemplo abaixo transforma-se para 3.

Pode configurar um tamanho de página diferentes por motivos de desempenho. Se tiver um grande número de registos de dados pequenas, um tamanho de página alta reduz o número de ida de servidor. 

Esta definição controla o tamanho de página no lado do cliente. Se o cliente pedir um tamanho de página maior do que suporta o back-end aplicações Mobile, o tamanho da página está limitado a máximo que back-end está configurado para suportar. 

Esta definição também é o _número_ de registos de dados, não o _tamanho de byte_.

Se aumentar o tamanho da página cliente, [também deve aumentar o tamanho da página no servidor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Objectivo-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Como: Inserir dados

Para inserir uma nova linha de tabela, crie uma `NSDictionary` e invocar `table insert`. Se [Esquema dinâmico] estiver ativada, o back-end móvel do serviço de aplicação do Azure gera automaticamente novas colunas com base na `NSDictionary`.

Se `id` é não fornecidos, back-end gera automaticamente um novo ID exclusivo. Fornecer o seu próprio `id` para utilizar o correio eletrónico endereços, nomes de utilizador, ou a sua própria página personalizada valores como ID. Fornecer o seu próprio ID poderá facilidade associações e lógica de base de dados orientados para negócios.

O `result` contém o item novo que tenha sido inserido. Dependendo do seu lógica de servidor, poderá ter dados adicionais ou modificados em comparação com o que foi passado para o servidor.

**Objectivo-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Como: modificar os dados

Para atualizar uma linha existente, modificar um item e chamada `update`:

**Objectivo-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Em alternativa, fornece o ID de linha e o campo actualizado:

**Objectivo-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

No mínimo, o `id` atributo tem de estar definido quando efetuar atualizações.

##<a name="deleting"></a>Como: eliminar dados

Para eliminar um item, invocar `delete` com o item:

**Objectivo-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Em alternativa, elimine, fornecendo um ID de linha:

**Objectivo-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

No mínimo, o `id` atributo tem de estar definido quando fizer elimina.

##<a name="customapi"></a>Como: chamar API personalizado

Com uma API personalizada, pode expor qualquer funcionalidade back-end. Não tem de mapear para uma operação de tabela. Não só é obter maior controlo sobre mensagens, poderá até mesmo leitura/conjunto cabeçalhos e alterar o formato do corpo de resposta. Para saber como criar uma API personalizada em back-end, leia [APIs personalizado](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Para ligar a uma API personalizada, contacte o `MSClient.invokeAPI`. O pedido e a resposta conteúda são tratadas como JSON. Para utilizar outros tipos de multimédia, [utilizar a outros sobrecarga de `invokeAPI` ] [ 5].  Para tornar um `GET` pedir em vez de um `POST` pedido de parâmetro conjunto `HTTPMethod` para `"GET"` e parâmetro `body` para `nil` (uma vez que os pedidos GET não possui organismos de mensagem.) Se o seu API personalizado suportar outros verbos HTTP, alterar `HTTPMethod` corretamente.

**Objectivo-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Como: modelos de push Register para enviar notificações em diferentes plataformas

Para registar modelos, passe modelos com o seu método de **client.push registerDeviceToken** na sua aplicação de cliente.

**Objectivo-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Os modelos são do tipo NSDictionary e podem conter vários modelos no seguinte formato:

**Objectivo-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Todas as etiquetas são removidas do pedido de segurança.  Para adicionar etiquetas a instalações ou modelos dentro instalações, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure][4].  Para enviar notificações utilizando estes modelos registados, trabalhar com [Notificação concentradores APIs][3].

##<a name="errors"></a>Como: processar erros

Ao ligar para um back-end móvel do Azure aplicação de serviço, o bloco de conclusão contém um `NSError` parâmetro. Quando ocorre um erro, este parâmetro é não nulo. No seu código, deve verificar este parâmetro e processar o erro, conforme necessário, tal como demonstrado de fragmentos de código anterior.

O ficheiro [`<WindowsAzureMobileServices/MSError.h>`] [6] define as constantes `MSErrorResponseKey`, `MSErrorRequestKey`, e `MSErrorServerItemKey`. Para obter mais dados relacionados com o erro:

**Objectivo-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Além disso, o ficheiro define constantes cada código de erro:

**Objectivo-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Como: autenticar utilizadores com a biblioteca de autenticação do Active Directory

Pode utilizar o Active Directory autenticação biblioteca (ADAL) para iniciar sessão os utilizadores na sua aplicação utilizando o Azure Active Directory. Autenticação de fluxo de clientes utilizando um fornecedor de identidades SDK é preferível a utilizar o `loginWithProvider:completion:` método.  Autenticação de fluxo de cliente fornece um funcionalidade UX mais nativo e permite para uma personalização mais adicionais.

1. Configurar a sua aplicação móvel back-end AAD iniciar sessão, seguindo [como configurar o serviço de aplicação para o início de sessão do Active Directory] [ 7] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação de cliente nativa. Para iOS, recomendamos que o redirecionamento URI é do formulário `<app-scheme>://<bundle-id>`. Para obter mais informações, consulte o [Guia de introdução do ADAL iOS][8].

2. Instale ADAL utilizando Cocoapods. Edite o seu Podfile para incluir a seguinte definição, substituindo **O PROJETO** com o nome do seu projeto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e a vagem:

        pod 'ADALiOS'

3. Utilizando o Terminal executar `pod install` do diretório que contém o seu projeto e, em seguida, abra a área de trabalho do Xcode gerada (e não o projecto).

4. Adicione o seguinte código à aplicação, de acordo com o idioma que está a utilizar. Em cada, tornar estes substituições:

    * Substitua **Aqui de certificação de inserir** com o nome do inquilino na qual aprovisionado que a aplicação. O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado a partir do separador de domínio no seu Azure Active Directory no [Azure clássico portal do].
    * Substitua **Inserir recurso-ID aqui** o ID do cliente para a sua aplicação móvel do back-end. Pode obter o ID de cliente a partir do separador **Avançadas** , em **Definições do Azure Active Directory** , no portal.
    * Substitua o ID do cliente que copiou da aplicação de cliente nativo **Inserir cliente-ID aqui** .
    * Substitua **Inserir-REDIRECT-URI-aqui** ponto final de _/.auth/login/done_ do seu site, utilizando o esquema de HTTPS. Este valor deverá ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_.

**Objectivo-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Como: autenticar os utilizadores com o SDK do Facebook para iOS

Pode utilizar o SDK do Facebook para iOS para inicie sessão os utilizadores na sua aplicação utilizando o Facebook.  Utilizar uma autenticação de fluxo de cliente é preferível a utilizar o `loginWithProvider:completion:` método.  A autenticação de fluxo de cliente fornece um funcionalidade UX mais nativo e permite para uma personalização mais adicionais.

1. Configurar a sua aplicação móvel back-end início de sessão no Facebook seguindo [como configurar o serviço de aplicação para o início de sessão do Facebook] [ 9] tutorial.

2. Instalar o SDK do Facebook para iOS seguindo o [SDK do Facebook para iOS - introdução ao] [ 10] documentação. Em vez de criar uma aplicação, pode adicionar a plataforma iOS para o registo existente. 

3. Documentação do Facebook inclui algum código objectivo-C na aplicação delegado. Se estiver a utilizar **Swift**, pode utilizar as seguintes traduções para AppDelegate.swift:
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Para além de adicionar `FBSDKCoreKit.framework` ao seu projeto, adicione também uma referência a `FBSDKLoginKit.framework` da mesma forma. 

4. Adicione o seguinte código à aplicação, de acordo com o idioma que está a utilizar. 

**Objectivo-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Como: autenticar utilizadores com ferro Twitter para iOS

Pode utilizar ferro para iOS para inicie sessão os utilizadores na sua aplicação utilizando Twitter. Autenticação de fluxo de cliente é preferível a utilizar o `loginWithProvider:completion:` método, tal como é fornece um funcionalidade UX mais nativo e permite adicionais de personalização.

1. Configure a sua aplicação móvel back-end início de sessão no Twitter ao seguir o tutorial de [como configurar o serviço de aplicação para o início de sessão do Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) .

2. Adicione ferro ao seu projeto ao seguir a documentação de [ferro para iOS - introdução] e configurar TwitterKit.

    > [AZURE.NOTE] Por predefinição, o ferro cria uma aplicação do Twitter para si. Pode evitar criar uma aplicação ao registar-se a chave de consumidor e o segredo consumidor que criou anteriormente utilizando os seguinte fragmentos de código.  Em alternativa, pode substituir os valores de chave do consumidor e consumidor secreta que fornece a aplicação de serviço com os valores que verá no [Dashboard de ferro]. Se selecionar esta opção, certifique-se definir o URL de chamada de retorno para um valor de marcador de posição, tais como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Se optar por utilizar os segredos que criou anteriormente, adicione o código seguinte ao seu delegado de aplicação:
    
    **Objectivo-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Swift**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Adicione o seguinte código à aplicação, de acordo com o idioma que está a utilizar. 

**Objectivo-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Como: autenticar os utilizadores com o Google início de sessão no SDK para iOS

Pode utilizar o Google início de sessão no SDK para iOS para inicie sessão os utilizadores na sua aplicação utilizando uma conta do Google.  Google recentemente anunciada alterações às suas políticas de segurança OAuth.  Estas alterações de política serão as necessitam do Google SDK no futuro.

1. Configure a sua aplicação móvel do back-end para o início de sessão do Google ao seguir o tutorial de [como configurar o serviço de aplicação para o início de sessão do Google](app-service-mobile-how-to-configure-google-authentication.md) .

2. Instale o SDK do Google para iOS ao seguir a documentação de [sessão do Google para iOS - iniciar integrar](https://developers.google.com/identity/sign-in/ios/start-integrating) . Também pode ignorar a secção "Autenticar com o servidor de back-end um".

3. Adicione o seguinte ao seu delegado `signIn:didSignInForUser:withError:` método, de acordo com o idioma que está a utilizar.

**Objectivo-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Certifique-se de que também adiciona o seguinte procedimento para `application:didFinishLaunchingWithOptions:` no seu delegado de aplicação, substituindo "SERVER_CLIENT_ID" com o mesmo ID que utilizou para configurar a aplicação de serviço no passo 1.

**Objectivo-C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Swift**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Adicione o seguinte código à sua aplicação de um UIViewController que implementa o `GIDSignInUIDelegate` protocolo, de acordo com o idioma que está a utilizar.  Tem sessão iniciada antes de a ser assinado novamente e, apesar de não precisar de introduzir novamente as suas credenciais, verá uma caixa de diálogo consentimento.  Chame apenas este método quando tiver expirado o token de sessão.
 
 **Objectivo-C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Swift**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Início do Azure rápida de aplicações móveis]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinâmica]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Dashboard de ferro]: https://www.fabric.io/home
[Ferro para iOS - introdução]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
