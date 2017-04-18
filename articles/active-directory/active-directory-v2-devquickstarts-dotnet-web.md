<properties
    pageTitle="Aplicação Web do Azure AD 2.0 .NET | Microsoft Azure"
    description="Como criar uma aplicação Web do .NET MVC assina utilizadores com ambos os Account Microsoft pessoal e contas escolar ou profissional."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Adicionar iniciar sessão para uma aplicação web do .NET MVC

Com o ponto final 2.0, pode adicionar rapidamente autenticação as suas aplicações web com o suporte para ambas as contas pessoais do Microsoft e contas escolar ou profissional.  Nas aplicações web do ASP.NET, consegui-lo utilizando software intermédio OWIN da Microsoft incluído no .NET Framework 4,5.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

 Aqui vamos irá criar uma aplicação web que utiliza OWIN para o início de sessão do utilizador, apresentar algumas informações sobre o utilizador e inicie sessão do utilizador terminar a aplicação.
 
 ## <a name="download"></a>Transferir
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

A aplicação completa é fornecida no final deste tutorial também.

## <a name="register-an-app"></a>Registe-se uma aplicação
Criar uma nova aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estes [passos detalhados](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copiar para baixo o **Id da aplicação** atribuído para a sua aplicação, terá de-la mais rapidamente.
- Adicione a plataforma **Web** para a sua aplicação.
- Introduza o correto **Redirecionar URI**. O uri redirecionar indica ao Azure AD onde devem ser direccionadas respostas de autenticação - é a predefinição para este tutorial `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Instalar e configurar uma autenticação OWIN
Aqui, podemos irá configurar o software OWIN intermédio para utilizar o protocolo de autenticação OpenID ligar.  OWIN será utilizada para iniciar sessão e terminar sessão pedidos de problema, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

-   Para começar, abra o `web.config` ficheiro na raiz do projeto e introduza os valores de configuração da sua aplicação na `<appSettings>` secção.
    -   O `ida:ClientId` é o **Id da aplicação** atribuído para a sua aplicação no portal do registo.
    -   O `ida:RedirectUri` é **Redirecionar Uri** introduzidos no portal.

-   Em seguida, adicione os pacotes de NuGet OWIN software intermédio para o projecto utilizando a consola do Gestor de pacote.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Adicionar um "OWIN arranque classe" ao projeto denominada `Startup.cs` direita, clique no projeto--> **Adicionar** --> **Novo Item** --> procurar "OWIN".  O software OWIN intermédio vai invocar a `Configuration(...)` método quando inicia a sua aplicação.
-   Alterar a declaração de classe `public partial class Startup` -já podemos optou por parte desta classe por si noutro ficheiro.  No `Configuration(...)` método, efetuar uma chamada para ConfigureAuth(...) para configurar a autenticação para a sua aplicação web  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros fornece no `OpenIdConnectAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com Azure AD.  Também terá de configurar a autenticação de cookies - o software intermédio OpenID ligar utiliza cookies sob as folhas de rosto.

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
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação
A aplicação está agora corretamente configurada para comunicar com o ponto final 2.0 utilizando o protocolo de autenticação OpenID ligar.  OWIN tem o prestado cuidado de todos os detalhes atrativo de intencionada mensagens de autenticação, validar tokens a partir do Azure AD e manter a sessão do utilizador.  Tudo o que é permanece fornecer aos utilizadores uma forma de início de sessão e terminar sessão.

- Pode utilizar autorizar etiquetas no seu controladores para exigir o registo esse utilizador inicia sessão antes de aceder a uma determinada página.  Abrir `Controllers\HomeController.cs`e adicione o `[Authorize]` etiqueta para o controlador de acerca de.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   Também pode utilizar OWIN diretamente emitir pedidos de autenticação a partir do seu código.  Abrir `Controllers\AccountController.cs`.  Em ações SignIn() e SignOut(), emita OpenID ligar HIP e terminar sessão pedidos, respetivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   Agora, abra `Views\Shared\_LoginPartial.cshtml`.  Este é onde irá mostrar o utilizador ligações de iniciar sessão e terminar sessão da sua aplicação e imprimir o nome do utilizador numa vista.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>Apresentar informações do utilizador
Quando a autenticação de utilizadores com OpenID ligar, o ponto final 2.0 devolve um id_token para a aplicação que contém [em afirmações](active-directory-v2-tokens.md#id_tokens)ou declarações sobre o utilizador.  Pode utilizar estes em afirmações para personalizar a sua aplicação:

- Abrir o `Controllers\HomeController.cs` ficheiro.  Pode aceder a afirmações do utilizador no seu controladores através de `ClaimsPrincipal.Current` objecto principal de segurança.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Executar

Por fim, criar e executar a sua aplicação!   Inicie sessão com uma Account Microsoft pessoal ou uma conta escolar ou profissional e repare como a identidade do utilizador é reflectida na barra de navegação superior.  Tem agora uma aplicação web protegida utilizando protocolos padrão da indústria que podem autenticar os utilizadores com ambas as respetivas contas pessoais e trabalho/escola.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)ou pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Próximos passos

Agora pode mover para tópicos mais avançados.  Pretende experimentar:

[Proteger uma API Web ao ponto final 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obter recursos adicionais, consulte:
- [O Guia do programador 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
