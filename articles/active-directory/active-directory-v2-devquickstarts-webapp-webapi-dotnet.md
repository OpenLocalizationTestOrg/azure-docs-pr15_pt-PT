<properties
    pageTitle="Aplicação Web do Azure AD 2.0 .NET | Microsoft Azure"
    description="Como criar uma aplicação Web do .NET MVC essa web chamadas services utilizando contas do Microsoft pessoal e de trabalho ou contas escolares para iniciar sessão."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>Chamar uma web API a partir de uma aplicação web do .NET

Com o ponto final 2.0, pode rapidamente adicionar autenticação as suas aplicações web e APIs da web com o suporte para ambas as contas Microsoft pessoais e contas escolar ou profissional.  Aqui, podemos irá criar uma aplicação web do MVC que assina os utilizadores na utilização de OpenID ligar, com algumas ajuda a partir do software de OWIN intermédio da Microsoft.  A aplicação web irá obter tokens de acesso OAuth 2.0 para um web api protegidos por 2.0 OAuth que lhe permite criar, ler e eliminar um determinado utilizador "lista de acções a fazer".

Neste tutorial irá focar-se principalmente sobre como utilizar MSAL para adquirir e utilizar tokens de acesso no web app, descrito na completo [aqui](active-directory-v2-flows.md#web-apps).  Como a pré-requisitos, poderá pretender pela primeira vez Saiba como [Adicionar básica iniciar sessão para uma aplicação web](active-directory-v2-devquickstarts-dotnet-web.md) ou como [seguro corretamente um web API](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Transferir o código de exemplo

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Em alternativa, pode [Transferir a aplicação completa como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou clonar a aplicação concluída:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registe-se uma aplicação
Criar uma nova aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estes [passos detalhados](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copiar para baixo o **Id da aplicação** atribuído para a sua aplicação, terá de-la mais rapidamente.
- Criar uma **Aplicação secreta** do tipo de **palavra-passe** e copiar para baixo o valor mais tarde
- Adicione a plataforma **Web** para a sua aplicação.
- Introduza o correto **Redirecionar URI**. O uri redirecionar indica ao Azure AD onde devem ser direccionadas respostas de autenticação - é a predefinição para este tutorial `https://localhost:44326/`.


## <a name="install-owin"></a>Instalar OWIN
Adicionar os pacotes de NuGet OWIN software intermédio para o `TodoList-WebApp` utilizando a consola do pacote Gestor do projeto.  O software OWIN intermédio será utilizado para iniciar sessão e terminar sessão pedidos de problema, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Início de sessão do utilizador
Agora, configure o software OWIN intermédio para utilizar o [protocolo de autenticação OpenID ligar](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Abrir o `web.config` ficheiro na raiz da `TodoList-WebApp` project e introduza os valores de configuração da sua aplicação na `<appSettings>` secção.
    -   O `ida:ClientId` é o **Id da aplicação** atribuído para a sua aplicação no portal do registo.
    - O `ida:ClientSecret` é a **Aplicação secreta** que criou no portal do registo.
    -   O `ida:RedirectUri` é **Redirecionar Uri** introduzido no portal.
- Abrir o `web.config` ficheiro na raiz da `TodoList-Service` do project e substituir o `ida:Audience` com o mesmo **Id da aplicação** que em cima.


- Abra o ficheiro `App_Start\Startup.Auth.cs` e adicionar `using` declarações para as bibliotecas de cima.
- No mesmo ficheiro, implementar o `ConfigureAuth(...)` método.  Os parâmetros fornece no `OpenIDConnectAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Utilize MSAL para obter tokens de acesso
No `AuthorizationCodeReceived` notificação, queremos utilizar [OAuth 2.0 em conjunto com OpenID ligar](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) para resgatar authorization_code para um token de acesso ao serviço de lista de acções a fazer.  MSAL pode facilitar este processo para si:

- Em primeiro lugar, instale a versão de pré-visualização do MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- E adicionar outra `using` instrução para o `App_Start\Startup.Auth.cs` ficheiro para MSAL.
- Agora adicione um novo método de `OnAuthorizationCodeReceived` processador de eventos.  Este processador irá utilizar MSAL para adquirir um token de acesso para a API da lista de acções a fazer e irá guardar o token na cache de tokens de MSAL mais tarde:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- No web apps, MSAL tem uma cache de tokens extensible que pode ser utilizada para armazenar tokens.  Este exemplo implementa o `NaiveSessionCache` que utiliza armazenamento da sessão http aos tokens de cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Ligar para a Web API
Está na altura realmente utilizar access_token adquiridos no passo 3.  Abra a aplicação web `Controllers\TodoListController.cs` ficheiro, o que faz de todos os pedidos de CRUD para a API da lista de acções a fazer.

- Pode utilizar MSAL novamente aqui para obter access_tokens da cache de MSAL.  Em primeiro lugar, adicione uma `using` declaração do MSAL a este ficheiro.

    `using Microsoft.Identity.Client;`

- No `Index` do ação, MSAL de utilização `AcquireTokenSilentAsync` método para obter uma access_token que podem ser utilizados para ler dados a partir do serviço de lista de acções a fazer:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- O exemplo adiciona token resultante ao pedido HTTP GET como o `Authorization` cabeçalho, que utiliza o serviço de lista de itens pendentes para autenticar o pedido.
- Se o serviço de lista de itens pendentes devolve um `401 Unauthorized` resposta, access_tokens no MSAL ter tornar-se inválidas por algum motivo.  Neste caso, deve largar qualquer access_tokens da cache de MSAL e apresentar uma mensagem que precisam de iniciar sessão novamente, que irá reiniciar o fluxo de aquisição token de utilizador.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- Da mesma forma, se MSAL não conseguir devolver uma access_token por qualquer motivo, deverá indique ao utilizador para iniciar sessão novamente.  Este é tão simple como captura qualquer `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- O mesmo exacto `AcquireTokenSilentAsync` a chamada é implementd na `Create` e `Delete` ações.  No web apps, pode utilizar este método MSAL para obter access_tokens sempre que precisá-los na sua aplicação.  MSAL irá encarregam-se de adquirir, colocação em cache e atualizar tokens por si.

Por fim, criar e executar a sua aplicação!  Inicie sessão com uma Account Microsoft ou uma conta do Azure AD e repare como a identidade do utilizador é reflectida na barra de navegação superior.  Adicionar e eliminar alguns itens de lista de itens pendentes do utilizador para ver que o 2.0 OAuth protegido API chamadas em ação.  Tem agora uma aplicação web & web API, ambos protegidos utilizando protocolos padrão da indústria, que podem autenticar os utilizadores com ambas as respetivas contas pessoais e trabalho/escola.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Próximos passos

Para obter recursos adicionais, consulte:
- [O Guia do programador 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
