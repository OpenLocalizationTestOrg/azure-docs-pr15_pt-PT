<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Como criar uma aplicação web que tem de iniciar sessão, inscrição, e gestão de perfil utilizando o Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD B2C: Criar uma aplicação web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Ao utilizar B2C Azure Active Directory (Azure AD), pode adicionar funcionalidades de gestão de identidade poderosas personalizada para a sua aplicação web em alguns passos breves. Este artigo irá explicar como criar uma aplicação web .NET controlador da vista de modelo (MVC) que inclui o utilizador inscrição, iniciar sessão e gestão de perfil. A aplicação irá incluir o suporte para inscrição e iniciar sessão ao utilizar um nome de utilizador ou o e-mail e, ao utilizar contas de rede sociais como o Facebook e o Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um directory B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e mais.  Se ainda não tiver uma, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C. Isto dá informações do Azure AD que precisa de comunicar com a sua aplicação de forma segura. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md).  Não se esqueça de:

- Inclua uma **web app/web API** na aplicação.
- Introduza `https://localhost:44316/` como um **Redirecionar URI**. É o URL predefinido para este exemplo de código.
- Copiar para baixo o **ID da aplicação** que está atribuída a sua aplicação.  Irá necessitá-la mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três identidade experiências: inscrever-se, inicie sessão e editar o perfil. Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD B2C também suporta um combinado inscrever-se ou início de sessão na política que não está disponível neste tutorial.  Inscrever-se ou política de início de sessão é mostrado [neste](active-directory-b2c-devquickstarts-web-dotnet-susi.md)tutorial equivalente.

Quando cria as três políticas, não se esqueça de:

- Selecione **Inscrição de ID de utilizador** ou o **E-Mail de inscrição** no pá de fornecedores de identidade.
- Selecione o **nome a apresentar** e outros atributos de inscrição na política de inscrição.
- Selecione a afirmação de **nome a apresentar** como uma aplicação afirmação em todas as políticas. Pode escolher também em outras afirmações.
- Copie o **nome** de cada política depois de o criar. Terá dos nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar o seu três políticas, está pronto para criar a sua aplicação.  

## <a name="download-the-code-and-configure-authentication"></a>O código de transferir e configurar uma autenticação

O código para este exemplo [são mantidas no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Para criar a amostra à medida que avança, pode [Transferir o projecto estrutura como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

O exemplo preenchido também está [disponível como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) ou na `complete` ramo do mesmo repositório de.

Depois de transferir o código de exemplo, abra o ficheiro de .sln Visual Studio para começar a utilizar.

A aplicação comunica com Azure AD B2C ao enviar mensagens de autenticação que especifique a política que pretendem executar como parte do pedido de HTTP. Para aplicações web do .NET, pode utilizar o software de OWIN intermédio da Microsoft para enviar pedidos de autenticação OpenID ligar, executar políticas, gerir sessões de utilizador e muito mais.

Para começar, adicione os pacotes de NuGet de software intermédio OWIN ao projeto utilizando a consola do Visual Studio pacote gestor.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Em seguida, abra o `web.config` de ficheiros na raiz do projeto e introduza os valores de configuração da sua aplicação na `<appSettings>` secção, substituir os valores existentes.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, adicione uma classe de arranque OWIN ao projeto denominado `Startup.cs`. Com o botão direito no projeto, selecione **Adicionar** e **Novo Item**e, em seguida, procure "OWIN." **Certifique-se alterar a declaração de classe `public partial class Startup` **. Vamos implementada parte desta classe por si noutro ficheiro. O software OWIN intermédio vai invocar a `Configuration(...)` método quando inicia a sua aplicação. Este método, fazer uma chamada para `ConfigureAuth(...)`, onde pode configurar a autenticação para a sua aplicação.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros fornece no `OpenIdConnectAuthenticationOptions` servir coordenadas para a sua aplicação para comunicar com Azure AD. Também precisa de configurar a autenticação de cookies. O software intermédio OpenID ligar utiliza os cookies para manter as sessões de utilizador, entre outras coisas.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Enviar pedidos de autenticação para Azure AD
A aplicação está agora corretamente configurada para comunicar com Azure AD B2C ao utilizar o protocolo de autenticação OpenID ligar.  OWIN tem o prestado cuidado de todos os detalhes de intencionada mensagens de autenticação, validar tokens a partir do Azure AD e manter a sessão do utilizador.  Tudo o que é permanece Iniciar fluxo de cada utilizador.

Quando um utilizador selecciona **Inscrever-se**, **início de sessão**ou **Editar o perfil** na aplicação web, a ação associada é chamada no `Controllers\AccountController.cs`. Em cada um dos casos, pode utilizar métodos OWIN incorporados para acionar a política direita:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

Também pode utilizar uma `[Authorize]` tag no seu controladores que requer a execução de uma política de determinadas se o utilizador não iniciou sessão no. Abrir `Controllers\HomeController.cs` e adicione o `[Authorize]` etiqueta para o controlador de créditos.  OWIN irá selecionar a última política configurada para executar quando a etiqueta autorizar é chamada.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Também pode utilizar OWIN para terminar a sessão o utilizador a partir da aplicação. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Apresentar informações do utilizador
Quando autenticar utilizadores utilizando a ligação OpenID, Azure AD devolve um token de ID da aplicação que contém **afirmações**. Estes são declarações sobre o utilizador. Pode utilizar em afirmações para personalizar a sua aplicação.  

Abrir o `Controllers\HomeController.cs` ficheiro. Pode aceder a afirmações de utilizador no seu controladores através de `ClaimsPrincipal.Current` objecto principal de segurança.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Pode aceder a qualquer reclamação que recebe a aplicação da mesma forma.  Uma lista de todas as afirmações que recebe a aplicação está disponível para si na página **afirmações** .

## <a name="run-the-sample-app"></a>Executar a aplicação de exemplo

Por fim, pode criar e executar a sua aplicação. Inscreva-se para a aplicação utilizando um nome de utilizador ou endereços de e-mail. Terminar sessão e volta a iniciar sessão como o mesmo utilizador. Edite o perfil desse utilizador. Terminar sessão e inscrever-se como um utilizador diferente. Tenha em atenção que as informações apresentadas no separador **afirmações** correspondem às informações de que configurou no seu políticas.

## <a name="add-social-idps"></a>Adicionar IDPs sociais

Atualmente, a aplicação suporta apenas utilizador inscrição e iniciar sessão utilizando **contas locais**. Estes são contas armazenadas no seu diretório B2C que utilizam um nome de utilizador e palavra-passe. Ao utilizar o Azure AD B2C, pode adicionar suporte para outros **fornecedores de identidade** (IDPs) sem alterar qualquer uma das seu código.

Para adicionar IDPs sociais para a sua aplicação, comece por seguir as instruções detalhadas nos seguintes artigos. Para cada IDP que pretende suportar, tem de registar uma aplicação desse sistema e obter um ID de cliente.

- [Configurar o Facebook como uma IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar o LinkedIn, como um IDP](active-directory-b2c-setup-li-app.md)

Depois de adicionar os fornecedores de identidade ao diretório da sua B2C, tem de editar cada uma das suas três políticas para incluir as novas IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de guardar as políticas, execute a aplicação novamente.  Deverá ver os novos IDPs adicionados como iniciar sessão e opções de inscrição em cada uma das sua identidade experiências.

Pode experimentar as políticas e observar o efeito na sua aplicação de exemplo. Adicionar ou remover IDPs, manipular afirmações da aplicação ou alterar os atributos de inscrição. Experimente até que possa ver como políticas, pedidos de autenticação e OWIN associam em conjunto.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Também pode clonar a partir do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
