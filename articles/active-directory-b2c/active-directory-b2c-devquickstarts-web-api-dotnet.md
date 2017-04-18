<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Como criar uma aplicação web que liga um web API utilizando o Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C: Chamar uma web API a partir de uma aplicação web do .NET

Ao utilizar B2C Azure Active Directory (Azure AD), pode adicionar funcionalidades de gestão de identidade de gestão personalizada poderosas às suas aplicações web e web APIs alguns passos breve. Este artigo irá discutir como criar uma aplicação web .NET controlador da vista de modelo (MVC) "lista de itens pendentes" que chama um web API utilizando tokens de tipo de ligação

Este artigo não aborda como implementar o início de sessão no, inscrição e gestão de perfil com Azure AD B2C. Foca-se na chamada web APIs depois do utilizador já está autenticado. Se ainda não o fez, deverá começar com o [.NET web app introdução tutorial](active-directory-b2c-devquickstarts-web-dotnet.md) saber mais sobre as noções básicas do Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um directory B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos.  Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e muito mais.  Se ainda não tiver uma, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C. Isto dá informações do Azure AD que precisa de comunicar com a sua aplicação de forma segura. Neste caso, a web app e o web API irão ser representado por única **ID da aplicação**, uma vez que incluem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). Não se esqueça de:

- Inclua uma **web app/web API** na aplicação.
- Introduza `https://localhost:44316/` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
- Copie o **ID da aplicação** que está atribuída a sua aplicação. Também terá este mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação web contém três identidade experiências: inscrever-se, inicie sessão e editar o perfil. Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando cria as três políticas, não se esqueça de:

- Selecione o **nome a apresentar** e outros atributos de inscrição na política de inscrição.
- Selecione os **nome a apresentar** e o **ID do objeto** afirmações da aplicação em todas as políticas. Pode escolher também em outras afirmações.
- Copie o **nome** de cada política depois de o criar. Deve ter o prefixo `b2c_1_`. Terá dos nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de ter criado as políticas de três, está pronto para criar a sua aplicação.

Tenha em atenção que este artigo não aborda como utilizar as políticas que acabou de criar. Para saber mais sobre como funcionam as políticas de Azure AD B2C, comece com o [.NET web app introdução tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Transferir o código

O código para este tutorial [são mantidas no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para criar a amostra à medida que avança, pode [Transferir o projecto estrutura como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

A aplicação completa também está [disponível como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou na `complete` ramo do mesmo repositório de.

Depois de transferir o código de exemplo, abra o ficheiro de .sln Visual Studio para começar a utilizar.

## <a name="configure-the-task-web-app"></a>Configurar a aplicação web de tarefa

Para obter `TaskWebApp` para comunicar com Azure AD B2C, tem de fornecer alguns parâmetros comuns. No `TaskWebApp` projeto, abrir o `web.config` ficheiro na raiz do projeto e substitua os valores na `<appSettings>` secção. Pode deixar a `AadInstance`, `RedirectUri`, e `TaskServiceUrl` valores tal como estão.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Obter tokens de acesso e ligar a tarefa API

Esta secção irá explica como utilizar o token recebido durante a iniciar sessão com o Azure AD B2C para poder aceder a um web API também é protegido com Azure AD B2C.

Este artigo abrange os detalhes de como seguro API. Para saber como uma web API autentica segura pedidos utilizando Azure AD B2C, consulte o artigo [da web API introdução artigo](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Guardar o início de sessão no token

Em primeiro lugar, autenticar o utilizador (utilizando qualquer uma das suas políticas) e receber um token de início de sessão a partir do Azure AD B2C.  Se não tiver a certeza como executar políticas, regresse e tente [.NET web app introdução tutorial](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre as noções básicas do Azure AD B2C.

Abra o ficheiro `App_Start\Startup.Auth.cs`.  Existe uma alteração importante que tem de efetuar o `OpenIdConnectAuthenticationOptions` -tem de definir `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Obter um token nos controladores

O `TasksController` é responsável para comunicar com a web API, enviar pedidos de HTTP à API para ler, criar e eliminar tarefas.  Becuase que API está protegida por Azure AD B2C, tem de obter pela primeira vez o token que guardou no passo acima.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

O `BootstrapContext` contém o token de que o utilizador tiver adquirido através da execução de uma das suas políticas B2C de início de sessão.

### <a name="read-tasks-from-the-web-api"></a>Ler tarefas a partir da web API

Quando tiver um token, pode anexá-lo para o protocolo HTTP `GET` pedir na `Authorization` cabeçalho para ligar a forma segura `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Criar e eliminar tarefas na web API

Siga o mesmo padrão quando envia `POST` e `DELETE` pedidos para a web API, utilizando o `BootstrapContext` para obter o token de início de sessão. Vamos implementado a ação criar por si. Que pode experimentar acabamento a ação de eliminar no `TasksController.cs`.

## <a name="run-the-sample-app"></a>Executar a aplicação de exemplo

Por fim, criar e executar a aplicação. Inscrever-se e inicie sessão e criar tarefas para o utilizador tiver sessão iniciada no. Terminar sessão e inicie sessão como um utilizador diferente. Crie tarefas para esse utilizador. Repare como as tarefas são armazenado por utilizador no API, porque a API extrai a identidade do utilizador do token recebe.

Para sua referência, o exemplo concluída [é fornecida como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
