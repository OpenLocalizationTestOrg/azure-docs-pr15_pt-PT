<properties
    pageTitle="Azure AD .NET introdução | Microsoft Azure"
    description="Como construir uma .NET MVC Web API que integra-se com o Azure AD para autenticação e autorização."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Proteger uma API Web utilizando tokens de tipo de ligação a partir do Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se estiver a compilar uma aplicação que fornece acesso aos recursos protegidos terá de saber como pode proteger esses recursos a partir do access indevida.
Azure AD torna simples e de fácil proteger um web API utilizar Tokens de acesso OAuth portadores 2.0 com apenas algumas linhas de código.

Nas aplicações web do Asp.NET, que pode efetuar este utilizando implementação da orientadas por Comunidade OWIN software intermédio incluído no .NET Framework 4,5 da Microsoft.  Aqui vamos utilizar OWIN para criar um web "Para fazer lista" API que:
-   Atribui quais API está protegida.
-   Validar que as chamadas da Web API contêm um Token de acesso válido.

Para poder fazer isto, terá:

1. Registar uma aplicação do Azure AD
2. Configure a sua aplicação para utilizar pipeline de autenticação de OWIN.
3. Configurar uma aplicação de cliente para ligar para fazer lista Web API

Para começar a, [Transferir a estrutura da aplicação](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Cada é uma solução do Visual Studio 2013.  Também terá de um inquilino do Azure AD na qual registar a aplicação.  Se ainda não tiver uma, [Saiba como obter uma](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. registar uma aplicação do Azure AD*
Para proteger a sua aplicação, primeiro terá de criar uma aplicação no seu inquilino e fornecer Azure AD com algumas partes principais de informações.

-   Inicie sessão no [Portal de gestão do Azure](https://manage.windowsazure.com)
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione um inquilino nas quais se registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e crie uma nova **aplicação Web e/ou WebAPI**.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais.  Introduza "A lista de itens pendentes serviço".
    -   O **Redirecionar Uri** é uma combinação de esquema e a cadeia que Azure AD utilizaria para devolver qualquer tokens sua aplicação pedida. Introduza `https://localhost:44321/` para este valor.
-   Quando terminar de registo, navegue para o separador de **Configurar** e localize o campo **ID URI de aplicação** .  Introduza um identificador de inquilino específicos para este valor, por exemplo`https://contoso.onmicrosoft.com/TodoListService`
- Guarde a configuração.  Deixe o portal aberta - também terá de para registar a sua aplicação cliente em breve.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurar a aplicação para utilizar o pipeline de autenticação OWIN*

Agora que já se registou uma aplicação com Azure AD, tem de configurar a sua aplicação para comunicar com o Azure AD para validar pedidos recebidos e de tokens.

-   Para começar, abra a solução e adicione os pacotes de NuGet de software intermédio OWIN ao projeto TodoListService utilizando o consola do Gestor de pacote.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Adicionar uma classe de arranque OWIN ao projecto TodoListService denominado `Startup.cs`.  Clique no projeto--> **Adicionar**direita --> **Novo Item** --> procurar "OWIN".  O software OWIN intermédio vai invocar a `Configuration(…)` método quando inicia a sua aplicação.
-   Alterar a declaração de classe `public partial class Startup` -já podemos optou por parte desta classe por si noutro ficheiro.  No `Configuration(…)` método, efetuar uma chamada para ConfgureAuth(...) para configurar a autenticação para a sua aplicação web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(…)` método.  Os parâmetros fornece no `WindowsAzureActiveDirectoryBearerAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   Agora, pode utilizar `[Authorize]` atributos para proteger os controladores e ações com autenticação de portadores JWT.  Decorar a `Controllers\TodoListController.cs` classe com uma tag autorizar.  Isto vai forçar o utilizador para iniciar sessão antes de aceder a essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando um autor autorizado com êxito invoca um da `TodoListController` APIs, a ação poderá ter acesso às informações sobre o autor da chamada.  OWIN oferece um acesso aos créditos dentro de ligação OAuth através de `ClaimsPrincpal` objeto.  
- Um requisito comum para web APIs encontra-se validar os "âmbitos" presentes no token de - Isto assegura que o utilizador final deu para as permissões necessárias para aceder ao serviço de lista Todo:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Por fim, abra o `web.config` de ficheiros na raiz do projeto TodoListService e introduza os valores de configuração na `<appSettings>` secção.
  - O `ida:Tenant` é o nome do seu inquilino do Azure AD, por exemplo, "contoso.onmicrosoft.com".
  - O `ida:Audience` é o URI de ID da aplicação da aplicação que introduziu no Portal do Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. configurar uma aplicação de cliente e executar o serviço*
Antes de poder ver o serviço de lista Todo em ação, tem de configurar o cliente de lista de Todo para que possa obter tokens de AAD e efetuar chamadas para o serviço.

- Navegar de volta para o [Portal de gestão do Azure](https://manage.windowsazure.com)
- Criar uma nova aplicação no seu inquilino do Azure AD e selecione a **Aplicação de cliente nativo** na linha de comandos do resultante.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   Introduza `http://TodoListClient/` para o valor de **Uri redirecionar** .
- Quando terminar de registo, AAD atribuirá sua aplicação de um único **Id de cliente**. Terá este valor nos próximos passos, por isso, copie-a partir do separador de configurar.
- Também no separador **Configurar** , localize a secção "Permissões para outras aplicações". Clique em "Adicionar aplicação". Selecione "Todas as aplicações" no menu pendente "Mostrar" e clique na marca de verificação maiúscula. Localize e clique no seu para fazer lista serviço e clique na marca de verificação da parte inferior para adicionar a aplicação. Selecione "Para fazer lista serviço de acesso" do menu pendente "Permissões de delegado" e guarde a configuração.


- No Visual Studio, abra `App.config` na TodoListClient do project e introduza os valores de configuração na `<appSettings>` secção.
  - O `ida:Tenant` é o nome do seu inquilino do Azure AD, por exemplo, "contoso.onmicrosoft.com".
  - O `ida:ClientId` ID da aplicação que copiou do Portal do Azure.
  - O `todo:TodoListResourceId` é o URI de ID da aplicação da aplicação para o fazer de serviço de lista que tenha introduzido no Portal do Azure.

Por fim, limpar, criar e executar cada projeto!  Se ainda não o fez, é agora o tempo para criar um novo utilizador no inquilino com um *. domínio onmicrosoft.com.  Iniciar sessão para o cliente de lista de fazer com que o utilizador e adicionar algumas tarefas a fazer para a lista do utilizador.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Agora pode mover a mais cenários de identidade adicionais.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
