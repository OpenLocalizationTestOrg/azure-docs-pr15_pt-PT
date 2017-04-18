<properties
    pageTitle="Azure AD .NET introdução | Microsoft Azure"
    description="Como criar uma aplicação Web do .NET MVC integra-se com o Azure AD para iniciar sessão."
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>Web do ASP.NET sessão da aplicação e terminar sessão com o Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD torna simples e de fácil utilizam recursos externos de gestão de identidades do web app, fornecendo única sessão e terminar sessão com apenas algumas linhas de código.  Nas aplicações web do Asp.NET, que pode efetuar este utilizando implementação da orientadas por Comunidade OWIN software intermédio incluído no .NET Framework 4,5 da Microsoft.  Aqui vamos utilizar OWIN para:
-   Inicie sessão do utilizador para a aplicação a utilizar o Azure AD como o fornecedor de identidade.
-   Apresenta algumas informações sobre o utilizador.
-   Inicie sessão do utilizador terminar a aplicação.

Para poder fazer isto, terá:

1. Registar uma aplicação do Azure AD
2. Configure a sua aplicação para utilizar pipeline de autenticação de OWIN.
3. Utilize OWIN para emitir pedidos de início de sessão e terminar sessão para Azure AD.
4. Imprima dados sobre o utilizador.

Para começar a, [Transferir a estrutura da aplicação](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  Também terá de um inquilino do Azure AD na qual registar a aplicação.  Se ainda não tiver uma, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. registar uma aplicação do Azure AD*
Para ativar a sua aplicação autenticar os utilizadores, primeiro precisa registar uma nova aplicação no seu inquilino.

- Inicie sessão no Portal de gestão Azure.
- No painel de navegação esquerdo, clique no **Active Directory**.
- Selecione o inquilino onde pretender registar a aplicação.
- Clique no separador **aplicações** e clique em Adicionar nível a parte inferior.
- Siga os pedidos e crie uma nova **aplicação Web e/ou WebAPI**.
    - O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **URL de início de sessão** é o URL de base da sua aplicação.  Predefinição a estrutura é `https://localhost:44320/`.
    - O **ID de aplicação URI** é um identificador exclusivo para a sua aplicação.  A Convenção é utilizar `https://<tenant-domain>/<app-name>`, por exemplo,`https://contoso.onmicrosoft.com/my-first-aad-app`
- Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de configurar.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurar a aplicação para utilizar o pipeline de autenticação OWIN*
Aqui, podemos irá configurar o software OWIN intermédio para utilizar o protocolo de autenticação OpenID ligar.  OWIN será utilizada para iniciar sessão e terminar sessão pedidos de problema, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

-   Para começar, adicione os pacotes de NuGet OWIN software intermédio para o projecto utilizando a consola do Gestor de pacote.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Adicionar uma classe de arranque OWIN ao projecto denominado `Startup.cs` direita, clique no projeto--> **Adicionar** --> **Novo Item** --> procurar "OWIN".  O software OWIN intermédio vai invocar a `Configuration(...)` método quando inicia a sua aplicação.
-   Alterar a declaração de classe `public partial class Startup` -já podemos optou por parte desta classe por si noutro ficheiro.  No `Configuration(...)` método, efetuar uma chamada para ConfgureAuth(...) para configurar a autenticação para a sua aplicação web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros fornece no `OpenIDConnectAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com Azure AD.  Também terá de configurar a autenticação de cookies - o software intermédio OpenID ligar utiliza cookies sob as folhas de rosto.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Por fim, abra o `web.config` ficheiro na raiz do projeto e introduza os valores de configuração na `<appSettings>` secção.
    -   A aplicação `ida:ClientId` é o Guid que copiou do Portal do Azure no passo 1.
    -   O `ida:Tenant` é o nome do seu inquilino do Azure AD, por exemplo, "contoso.onmicrosoft.com".
    -   O `ida:PostLogoutRedirectUri` indica ao Azure AD onde deve ser redirecionado um utilizador depois de um pedido de terminar sessão concluída com êxito.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. utilizar OWIN para emitir pedidos de início de sessão e terminar sessão para Azure AD*
A aplicação está agora corretamente configurada para comunicar com Azure AD utilizando o protocolo de autenticação OpenID ligar.  OWIN tem o prestado cuidado de todos os detalhes atrativo de intencionada mensagens de autenticação, validar tokens a partir do Azure AD e manter a sessão do utilizador.  Tudo o que é permanece fornecer aos utilizadores uma forma de início de sessão e terminar sessão.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   Agora, abra `Views\Shared\_LoginPartial.cshtml`.  Este é onde irá mostrar o utilizador ligações de iniciar sessão e terminar sessão da sua aplicação e imprimir o nome do utilizador numa vista.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4. apresentar informações do utilizador*
Quando a autenticação de utilizadores com OpenID ligar, Azure AD devolve um id_token para a aplicação que contém "em afirmações" ou declarações sobre o utilizador.  Pode utilizar estes em afirmações para personalizar a sua aplicação:

- Abrir o `Controllers\HomeController.cs` ficheiro.  Pode aceder a afirmações do utilizador no seu controladores através de `ClaimsPrincipal.Current` objecto principal de segurança.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Por fim, criar e executar a sua aplicação!  Se ainda não o fez, é agora o tempo para criar um novo utilizador no inquilino com um *. domínio onmicrosoft.com.  Inicie sessão com esse utilizador e repare como a identidade do utilizador é reflectida na barra de navegação superior.  Terminar sessão e volta a iniciar sessão como outro utilizador no seu inquilino.  Se está a dar particularmente ambiciosos, registe-se e execute outra instância desta aplicação (com a sua própria clientId) e veja Consulte único-início de sessão em ação.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Agora pode mover para tópicos mais avançados.  Pretende experimentar:

[Proteger uma Web API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
