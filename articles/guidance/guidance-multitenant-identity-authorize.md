<properties
   pageTitle="Autorização nas aplicações multi-inquilino | Microsoft Azure"
   description="Como efetuar autorização numa aplicação multi-inquilino"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Autorização baseadas no recurso e baseado em funções nas aplicações multi-inquilino

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo faz [parte de uma série]. Também existe uma [aplicação de exemplo] concluída que acompanha nesta série.

Os nossos [implementação de referência] é uma aplicação do ASP.NET Core 1.0. Neste artigo Abordaremos duas abordagens gerais autorização, utilizando a autorização APIs fornecidos no ASP.NET Core 1.0.

-   **Autorização baseado em funções**. Autorizar uma ação baseada em funções atribuídas a um utilizador. Por exemplo, algumas ações necessitam de uma função de administrador.
-   **Autorização com base em recursos**. Autorizar uma ação com base num recurso em particular. Por exemplo, todos os recursos tem um proprietário. O proprietário pode eliminar o recurso; não consegue a outros utilizadores.

Uma aplicação típica irá empregam uma mistura dos dois. Por exemplo, para eliminar um recurso, o utilizador tem de ser o recurso titular _ou_ administrador.


## <a name="role-based-authorization"></a>Autorização baseado em funções

Os [Inquéritos brinquedos] [ Tailspin] aplicação define as seguintes funções:

- Administrador. Pode executar todas as operações CRUD qualquer inquérito que pertence a esse inquilino.
- Criador de blocos. Pode criar novos inquéritos
- Leitor. Pode ler qualquer inquéritos que pertencem a esse inquilino

Funções de aplicam a _utilizadores_ da aplicação. Na aplicação inquéritos, um utilizador é um administrador, Criador de blocos ou leitor.

Para um debate de como definir e gerir funções, consulte [funções da aplicação].

Independentemente de como gerir as funções, o código de autorização terá uma aspeto semelhante. ASP.NET Core 1.0 apresenta uma abstracção denominada [políticas de autorização de][policies]. Com esta funcionalidade, definir políticas de autorização no código e, em seguida, aplique essas exigências ações controlador. A política é desacoplada a partir do controlador de.

### <a name="create-policies"></a>Criar políticas

Para definir uma política, criar uma classe que implementa `IAuthorizationRequirement`. É mais fácil deriva `AuthorizationHandler`. No `Handle` método, examine a claim(s) relevantes.

Eis um exemplo a partir da aplicação de inquéritos brinquedos:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Consulte o artigo [SurveyCreatorRequirement.cs]

Esta classe define o requisito de um utilizador para criar um novo inquérito. O utilizador tem de ser na função SurveyAdmin ou SurveyCreator.

Na sua aula de arranque, defina uma política com nome que inclua um ou mais requisitos. Se existirem vários requisitos, o utilizador tem de cumprir _cada_ requisito para ser autorizados. O código seguinte define duas políticas:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Consulte o artigo [Startup.cs]

Este código também define o esquema de autenticação, que indica que software autenticação intermédio deve ser executadas se falha de autorização de ASP.NET. Neste caso, vamos especificar o software de intermédio de autenticação de cookies, porque o autenticação de cookie software intermédio pode redirecionar o utilizador a uma página de "Proibido". A localização da página proibido estiver definida na opção AccessDeniedPath para software de intermédio cookie; consulte [configurar o software intermédio autenticação].

### <a name="authorize-controller-actions"></a>Autorize ações controlador

Por fim, para autorizar uma ação num controlador de MVC, definir a política na `Authorize` atributo:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Em versões anteriores do ASP.NET, teria de defina a propriedade de **funções** no atributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Isto ainda é suportado no ASP.NET Core 1.0, mas tem algumas desvantagens em comparação com políticas de autorização de:

-   Parte do princípio de um tipo de afirmação específico. Podem verificar políticas para qualquer tipo de afirmação. As funções são apenas um tipo de afirmação.
-   O nome da função é codificado para o atributo. Com as políticas, a lógica de autorização é tudo num só local, tornando mais fácil para atualizar ou carga mesmo a partir das definições de configuração.
-   As políticas permitem decisões de autorização mais complexas (por exemplo, idade > = 21) que não pode ser expressa por associação de função simples.

## <a name="resource-based-authorization"></a>Autorização de recursos com base

_Recursos com base autorização_ ocorre sempre que a autorização depende de um recurso de específico, será afetado por uma operação. Na aplicação brinquedos inquéritos, cada inquérito tem um proprietário e contribuintes zero-para-muitos.

-   O proprietário pode ler, atualizar, eliminar, publicar e anular a publicação o inquérito.
-   O proprietário pode atribuir contribuintes ao inquérito.
-   Os contribuintes podem ler e atualizar o inquérito.

Note que "proprietário" e "contribuinte" não são funções da aplicação; estes estão guardados por inquérito, da base de dados de aplicação. Para verificar se um utilizador pode eliminar um inquérito, por exemplo, a aplicação verifica se o utilizador é o proprietário para esse inquérito.

No ASP.NET Core 1.0, implemente autorização com base em recurso ao decorrentes da **AuthorizationHandler** e substituir o método **alça** .

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Repare que esta classe vivamente está escrita para objetos de inquérito.  Registe-se a classe no DI no arranque:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Para executar verificações de autorização, utilize a interface de **IAuthorizationService** , pode inserir na sua controladores. O código seguinte verifica se um utilizador pode ler um inquérito:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Uma vez que recomendamos passar num `Survey` objecto, esta chamada vai invocar a `SurveyAuthorizationHandler`.

No seu código autorização, uma boa abordagem é agregação: todas as permissões do utilizador baseado em funções e com base no recurso e, em seguida verificação Definir a função de agregação contra a operação desejada.
Eis um exemplo a partir da aplicação de inquéritos. A aplicação define vários tipos de permissões:

- Admin
- Contribuinte
- Criador de blocos
- Proprietário
- Leitor

A aplicação também define um conjunto de operações possíveis em inquéritos:

- Criar
- Leitura
- Actualização
- Eliminar
- Publicar
- Unpublsh

O código seguinte cria uma lista de permissões para um determinado utilizador e o inquérito. Tenha em atenção que este código analisa funções de aplicação do utilizador e os campos proprietário/contribuinte o inquérito.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Consulte o artigo [SurveyAuthorizationHandler.cs].

Numa aplicação do inquilino com várias, tem de garantir que as permissões não "fugas" aos dados de outro inquilino. Na aplicação inquéritos, a permissão de contribuinte é permitida através de inquilinos &mdash; pode atribuir a alguém a partir de outro inquilino como um contriubutor. Os outros tipos de permissão estão limitados a recursos que pertencem ao inquilino desse utilizador. Para impor a este requisito, o código verifica o ID do inquilino antes de conceder a permissão. (O `TenantId` de campo como atribuído quando é criado o inquérito.)

O passo seguinte é consultar a operação (ler, atualizar, eliminar, etc) contra as permissões. A aplicação de inquéritos implementa este passo utilizando uma tabela de referência de funções:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Próximos passos

- Leia o seguinte artigo nesta série: [proteger back-end da web API numa aplicação multi-inquilino][web-api]
- Para saber mais sobre a autorização de recursos com base em ASP.NET 1.0 principais, consulte o artigo [Recursos com base autorização][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de uma série]: guidance-multitenant-identity.md
[Funções da aplicação]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[implementação de referência]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.CS]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configurar o software de autenticação intermédio]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[aplicação de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
