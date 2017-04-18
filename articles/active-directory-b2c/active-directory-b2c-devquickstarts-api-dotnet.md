<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Como construir uma .NET a API do Web utilizando o Azure Active Directory B2C, protegido pela utilização de tokens de acesso OAuth 2.0 para autenticação."
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure B2C diretório ativos: Criar um web API do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Azure Active Directory (Azure AD) B2C, pode proteger um web API utilizando tokens de acesso OAuth 2.0. Estes tokens permitir que as aplicações de cliente que utilizar B2C do Azure AD para autenticar à API. Este artigo mostra-lhe como criar uma API "lista de acções a fazer".NET controlador da vista de modelo (MVC) que permite aos utilizadores tarefas CRUD. Web API é protegida utilizando Azure AD B2C e só permite autenticados utilizadores para gerir a sua lista de itens pendentes.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um directório B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e mais. Se ainda não tiver uma, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C. Isto dá informações do Azure AD que precisa de comunicar com a sua aplicação de forma segura. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). Não se esqueça de:

- Inclua uma **aplicação web** ou **web API** na aplicação.
- Utilizar **Redirecionar identificador de recursos uniforme** `https://localhost:44316/` para a aplicação web. Esta é a localização predefinida do cliente de aplicação web para que este exemplo de código.
- Copie o **ID da aplicação** que está atribuída a sua aplicação. Terá de-la mais tarde.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). O cliente neste exemplo código contém três identidade experiências: inscrever-se, inicie sessão e editar o perfil. Terá de criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando cria as políticas de três, não se esqueça de:

- Selecione **Inscrição de ID de utilizador** ou **E-Mail de inscrição** no pá de fornecedores de identidade.
- Selecione o **nome a apresentar** e outros atributos de inscrição na política de inscrição.
- Selecione em afirmações **nome a apresentar** e o **ID do objeto** como afirmações da aplicação para cada política. Pode escolher também em outras afirmações.
- Copie o **nome** de cada política depois de o criar. Tem os seguintes nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de ter criado com êxito três políticas, está pronto para criar a sua aplicação.

## <a name="download-the-code"></a>Transferir o código

O código para este tutorial [são mantidas no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para criar a amostra à medida que avança, pode [Transferir um projeto de estrutura como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

A aplicação completa também está [disponível como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou na `complete` ramo do mesmo repositório de.

Depois de transferir o código de exemplo, abra o ficheiro de .sln Visual Studio para começar a utilizar. O ficheiro de solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp`é uma aplicação web MVC que o utilizador interage com. `TaskService`é web de back-end a aplicação API que armazena a lista de itens pendentes de cada utilizador.

## <a name="configure-the-task-web-app"></a>Configurar a aplicação web de tarefa

Quando um utilizador interage com `TaskWebApp`, o cliente envia pedidos para Azure AD e obtém novamente tokens que podem ser utilizadas para ligar para o `TaskService` web API. Para o utilizador de início de sessão e obter tokens, tem de fornecer `TaskWebApp` com algumas informações sobre a sua aplicação. No `TaskWebApp` projeto, abrir o `web.config` ficheiro na raiz do projeto e substitua os valores na `<appSettings>` secção.  Pode deixar a `AadInstance`, `RedirectUri`, e `TaskServiceUrl` valores como-é.

```
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
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Este artigo abrange modular a `TaskWebApp` cliente.  Para saber como criar uma aplicação web utilizando o Azure AD B2C, consulte o [Nosso tutorial do .NET web app](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Proteger a API

Quando tiver um cliente que liga a API em nome de utilizadores, pode proteger `TaskService` utilizando tokens de tipo de ligação OAuth 2.0. A API pode aceitar e validar os tokens ao utilizar a Interface de Web aberta da Microsoft para biblioteca .NET (OWIN).

### <a name="install-owin"></a>Instalar OWIN
Comece por pipeline de autenticação de OWIN OAuth de instalar:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Introduza os detalhes do seu B2C
Abrir o `web.config` ficheiro na raiz da `TaskService` project e substitua os valores na `<appSettings>` secção. Estes valores serão utilizados em toda a biblioteca de API e OWIN.  Pode deixar a `AadInstance` valor inalterado.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de arranque OWIN
Adicionar uma classe de arranque OWIN para o `TaskService` projeto denominado `Startup.cs`.  Com o botão direito no projeto, selecione **Adicionar** e **Novo Item**e, em seguida, procure OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurar uma autenticação OAuth 2.0
Abra o ficheiro `App_Start\Startup.Auth.cs`e implementar o `ConfigureAuth(...)` método:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Proteger o controlador de tarefa
Após a aplicação estiver configurada para utilizar a autenticação do token 2.0, pode proteger o seu da web API adicionando um `[Authorize]` etiqueta para o controlador de tarefa. Este é o controlador onde todos os manipulação de lista de acções a fazer ocorre o mais, para que deverá proteger o controlador de inteiro ao nível de classe. Também pode adicionar a `[Authorize]` etiqueta para ações individuais para ter mais controlo extensivamente.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obter informações de utilizador a partir do token
`TasksController`armazena tarefas numa base de dados onde cada tarefa tem um utilizador associado proprietária "" da tarefa. O proprietário é identificado por do utilizador **ID do objeto**. (Este é o motivo pelo qual necessário para adicionar o ID de objecto como uma aplicação afirmação em todas as suas políticas.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Executar a aplicação de exemplo

Por fim, criar e executar ambos `TaskWebApp` e `TaskService`. Inscreva-se para a aplicação, utilizando um endereço de e-mail ou o nome de utilizador. Criar algumas tarefas na lista de tarefas do utilizador e repare como são permanentes da API do mesmo depois de parar e reinicie o cliente.

## <a name="edit-your-policies"></a>Editar as suas políticas

Depois de ter protegido uma API utilizando Azure AD B2C, pode experimentar as políticas da sua aplicação e ver os efeitos (ou falta seu) no API. Pode manipular afirmações de aplicação nas políticas e alterar as informações de utilizador que está disponíveis na web API. Quaisquer afirmações que adicionar irão estar disponíveis para a .NET MVC da web API na `ClaimsPrincipal` objecto, conforme descrito neste artigo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
