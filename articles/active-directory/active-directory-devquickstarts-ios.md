<properties
    pageTitle="Azure AD iOS introdução | Microsoft Azure"
    description="Como criar uma aplicação do iOS que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar uma aplicação iOS do Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL, para iOS clientes que precisam de aceder recursos protegidos.  Objetivo de exclusivo do ADAL vida é tornam mais fácil para a sua aplicação obter tokens de acesso.  Para demonstrar tal como é fácil, aqui vamos irá criar uma aplicação de lista de itens pendentes do objectivo C que:

-   Obtém acesso tokens para chamar a API do Azure AD Graph utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Procura um diretório para utilizadores com um determinado alias.

Para criar a aplicação de trabalho concluída, terá:

2. Registe-se a sua aplicação com Azure AD.
3. Instalar e configurar ADAL.
5. Utilize ADAL para obter tokens a partir do Azure AD.

Para começar a, [Transferir a estrutura da aplicação](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Também terá de um inquilino do Azure AD na qual pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [Saiba como obter uma](active-directory-howto-tenant.md).

> [AZURE.TIP] Experimente a pré-visualização do nosso novo [developer portal para](https://identity.microsoft.com/Docs/iOS) irá ajudá-lo a começar a trabalhar rapidamente com o Azure Active Directory em apenas alguns minutos!  Portal do programador irá guiá-lo durante o processo de registo de uma aplicação e integrar o seu código Azure AD.  Quando tiver terminado, terá uma aplicação simple que pode autenticar os utilizadores no seu inquilino e back-end que podem aceitar tokens e efetua validação. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. determinar o seu URI redirecionar serão para iOS*

Para poder de forma segura iniciar as aplicações em determinados cenários SSO que necessitamos que cria um **Redirecionar URI** num formato específico. Um URI redirecionar é utilizado para se certificar de que os tokens de regressar à aplicação correto que pedidas-los.

O formato de iOS para um URI redirecionar é:

```
<app-scheme>://<bundle-id>
```

-   **esquema de aap** - isto é registada no seu projeto XCode. É como noutras aplicações podem efectuar uma chamada. Pode encontrar esta em Info.plist -> tipos de URL -> identificador de URL. Deve criar uma, se ainda não tiver um ou mais configurado.
-   **id de pacote** - este é o identificador de pacote que se encontram em "identidade" un as definições de projecto no XCode.

Um exemplo para este código de guia de introdução seria: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. registar a aplicação de DirectorySearcher iniciou*
Para ativar a sua aplicação obter tokens, primeiro terá registá-lo no seu inquilino do Azure AD e conceder-lhe permissões de acesso a API do Azure AD Graph:

-   Inicie sessão no Portal de gestão do Azure
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione um inquilino nas quais se registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e criar uma nova **Aplicação de cliente nativa**.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **Redirecionar Uri** é uma combinação de esquema e a cadeia que irá utilizar Azure AD para devolver tokens respostas.  Introduza um valor específico para a sua aplicação com base nas informações acima.
-   Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de **Configurar** .
- Também no separador **Configurar** , localize a secção "Permissões para outras aplicações".  Para a aplicação "Azure Active Directory", adicione a permissão de **diretório da organização acesso Your** em **Permissões do delegado**.  Isto permitirá a sua aplicação consultar a API do gráfico para os utilizadores.

## <a name="3-install--configure-adal"></a>*3. instalar e configurar ADAL*
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escreva o código de identidade.  Para ADAL possam comunicar com o Azure AD, é necessário fornecer-lhe algumas informações sobre o registo de aplicação.
-   Comece por adicionar ADAL ao projeto DirectorySearcher iniciou utilizando Cocapods.

```
$ vi Podfile
```
Adicione o seguinte para este podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Carrega agora podfile utilizando cocoapods. Isto vai criar uma área de trabalho XCode nova que irá carregar.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   No project guia de introdução, abra o ficheiro plist `settings.plist`.  Substitua os valores dos elementos na secção para refletir os valores de entrada no Portal do Azure.  O código fizer referência a estes valores sempre que utiliza ADAL.
    -   O `tenant` é o domínio do seu inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `clientId` é clientId da sua aplicação que copiou a partir do portal.
    -   O `redirectUri` é o redirecionamento url registado no portal.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. utilizar ADAL para obter Tokens de AAD*
O princípio básico atrás ADAL é que sempre que a sua aplicação necessita de um token de acesso, basta chama um completionBlock `+(void) getToken : `, e ADAL é que os restantes.  

-   No `QuickStart` project, abrir `GraphAPICaller.m` e localize o `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` comentário na parte superior.  Este é o local onde a passar ADAL as coordenadas através de um CompletionBlock para comunicar com o Azure AD e indique-lo como tokens em cache.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Agora precisamos de utilizar este token para procurar utilizadores no gráfico. Localizar o `// TODO: implement SearchUsersList` commentThis método torna num pedido GET à API do Azure AD Graph a consulta para os utilizadores cuja UPN começa com o termo de pesquisa determinado.  Para consultar a API do gráfico, precisar de incluir um access_token no, mas o `Authorization` cabeçalho do pedido - isto é que ADAL entra em.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Quando a aplicação os pedidos de um token ao contactar o suporte `getToken(...)`, ADAL tentará para devolver um token sem perguntar ao utilizador para introduzir as credenciais.  Se ADAL determinar que o utilizador tem de iniciar sessão obter um token, irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do utilizador e devolver um token relativamente a autenticação com êxito.  Se ADAL não conseguir devolver um token por qualquer motivo, irá gerar um `AdalException`.
- Repare que o `AuthenticationResult` objeto contiver um `tokenCacheStoreItem` objeto que pode ser utilizado para recolher informações poderá ter a sua aplicação.  No guia de introdução, `tokenCacheStoreItem` é utilizada para determinar se já tiver ocorrido authenitcation.


## <a name="step-5-build-and-run-the-application"></a>Passo 5: Criar e executar a aplicação



Parabéns! Agora ter uma aplicação do iOS de trabalhar com a capacidade de autenticar os utilizadores, contacte segura APIs da Web OAuth 2.0 a utilizar e obter informação básica sobre o utilizador.  Se ainda não o fez, é agora o tempo para preencher o inquilino com alguns utilizadores.  Execute a aplicação de guia de introdução e inicie sessão com uma desses utilizadores.  Pesquisa para outros utilizadores com base na sua UPN.  Feche a aplicação e executá-lo novamente.  Repare como permanece intacta a sessão do utilizador.

ADAL torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  Tratará de todo o trabalho modificado por si - gestão da cache, suporte do protocolo OAuth, apresentar o utilizador com um início de sessão da IU, atualizar tokens expiradas e muito mais.  Tudo o que realmente precisa de saber for uma chamada de API única, `getToken`.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Cenários adicionais
Agora pode mover para cenários adicionais.  Pretende experimentar:

- [Proteger um Node.JS da Web API com Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
- Saiba [como ativar a publicação em aplicação SSO IOS utilizando ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
