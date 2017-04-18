<properties
    pageTitle="Azure AD 2.0 .NET Web API | Microsoft Azure"
    description="Como construir uma .NET MVC Web Api que aceita tokens a partir de ambas as Account Microsoft pessoal e contas escolar ou profissional."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Proteger uma web MVC API

Com o Azure Active Directory o ponto final 2.0, pode proteger um API do Web utilizando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , permitindo aos utilizadores com conta Microsoft de pessoal e trabalho ou escola contas para o access segura a API do seu Web.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

No web do ASP.NET APIs, consegui-lo utilizando software intermédio OWIN da Microsoft incluído no .NET Framework 4,5.  Aqui vamos utilizar OWIN para criar uma "Lista fazer" MVC Web API que permite que os clientes criar e ler tarefas a partir da lista de acções a fazer de um utilizador.  Web API irá validar que pedidos recebidos contêm um token de acesso válido e rejeitar quaisquer pedidos de que não validada numa rota protegida.  Este exemplo foi criado utilizando o Visual Studio 2015.

## <a name="download"></a>Transferir
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

A aplicação de estrutura inclui todos os códigos de texto automático para uma API simple, mas não é apresentado todas as peças relacionados com a identidade. Se não quiser segui-los, em vez disso, pode clonar ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registe-se uma aplicação
Criar uma nova aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estes [passos detalhados](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copiar para baixo o **Id da aplicação** atribuído para a sua aplicação, terá de-la mais rapidamente.

Esta solução do visual studio também contém um "TodoListClient", que é uma aplicação WPF simple.  O TodoListClient é utilizado para demonstrar como um utilizador sinais-in e como um cliente pode emitir pedidos para a API do seu Web.  Neste caso, a TodoListClient e o TodoListService são representados pela aplicação do mesmo.  Para configurar o TodoListClient, também deve:

- Adicione a plataforma **móvel** para a sua aplicação.


## <a name="install-owin"></a>Instalar OWIN

Agora que já se registou uma aplicação, tem de configurar a sua aplicação para comunicar com o ponto final 2.0 para validar pedidos recebidos e de tokens.

- Para começar, abra a solução e adicione os pacotes de NuGet de software intermédio OWIN ao projeto TodoListService utilizando o consola do Gestor de pacote.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurar a autenticação do token

- Adicionar uma classe de arranque OWIN ao projecto TodoListService denominado `Startup.cs`.  Clique no projeto--> **Adicionar**direita --> **Novo Item** --> procurar "OWIN".  O software OWIN intermédio vai invocar a `Configuration(…)` método quando inicia a sua aplicação.
- Alterar a declaração de classe `public partial class Startup` -já podemos optou por parte desta classe por si noutro ficheiro.  No `Configuration(…)` método, efetuar uma chamada para ConfgureAuth(...) para configurar a autenticação para a sua aplicação web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(…)` método, que irá configurar a API Web para aceitar tokens a partir do ponto final 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- Agora, pode utilizar `[Authorize]` atributos para proteger os controladores e ações com autenticação de tipo de ligação OAuth 2.0.  Decorar a `Controllers\TodoListController.cs` classe com uma tag autorizar.  Isto vai forçar o utilizador para iniciar sessão antes de aceder a essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando um autor autorizado com êxito invoca um da `TodoListController` APIs, a ação poderá ter acesso às informações sobre o autor da chamada.  OWIN oferece um acesso aos créditos dentro de ligação OAuth através de `ClaimsPrincpal` objeto.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Por fim, abra o `web.config` de ficheiros na raiz do projeto TodoListService e introduza os valores de configuração na `<appSettings>` secção.
  - O `ida:Audience` é o **Id da aplicação** da aplicação que introduziu no portal.

## <a name="configure-the-client-app"></a>Configurar a aplicação de cliente
Antes de poder ver o serviço de lista Todo em ação, tem de configurar o cliente de lista de Todo para que possa obter tokens a partir do ponto final 2.0 e efetuar chamadas para o serviço.

- No TodoListClient project, abra `App.config` e introduza os valores de configuração na `<appSettings>` secção.
  - O `ida:ClientId` Id da aplicação que copiou a partir do portal.

Por fim, limpar, criar e executar cada projeto!  Tem agora uma .NET MVC Web API que aceita tokens a partir de ambas as contas pessoais do Microsoft e contas escolar ou profissional.  Inicie sessão na TodoListClient e ligar a web api para adicionar tarefas à lista de itens pendentes do utilizador.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)ou pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Próximos passos
Agora pode mover para tópicos adicionais.  Pretende experimentar:

[Chamar uma Web API a partir de uma aplicação Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Para obter recursos adicionais, consulte:
- [O Guia do programador 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
