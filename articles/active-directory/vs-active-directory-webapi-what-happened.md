<properties
    pageTitle="O que aconteceu ao meu projecto WebApi (Visual Studio Azure Active Directory ligado serviço) | Microsoft Azure "
    description="Descreve o que acontece ao seu projeto MVC WebApi que ligue à Azure AD utilizando o Visual Studio"
  services="active-directory"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projecto WebApi (Visual Studio Azure Active Directory ligado serviço)

> [AZURE.SELECTOR]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>Referências foram adicionadas

###<a name="nuget-package-references"></a>Referências de pacote NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>Referências de .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>Alterações de código

###<a name="code-files-were-added-to-your-project"></a>Ficheiros de código foram adicionados ao projeto

Uma classe de arranque de autenticação, **App_Start/Startup.Auth.cs** foi adicionada ao projeto que contém a lógica de arranque para a autenticação do Azure AD.

###<a name="startup-code-was-added-to-your-project"></a>Código de arranque foi adicionado ao projeto

Se já tinha uma classe de arranque no seu projeto, o método de **configuração** foi atualizado para incluir uma chamada para `ConfigureAuth(app)`. Caso contrário, uma classe de arranque foi adicionada ao seu projeto.


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>O ficheiro App ou Web. config tem valores de configuração da nova.

Foram adicionadas as seguintes entradas de configuração.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>Uma aplicação do Azure AD foi criada

Uma aplicação do Azure AD foi criada no diretório que selecionou no assistente.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se posso selecionada *desactivar a autenticação de contas de utilizador Individual*, quais as alterações adicionais foram efetuadas à minha projeto?
Referências de pacote NuGet foram removidas e ficheiros foram removidos e cópias de segurança. Consoante o estado do seu projeto, poderá ter de remover manualmente referências adicionais ou ficheiros, ou modificar o código conforme adequado.

###<a name="nuget-package-references-removed-for-those-present"></a>Referências de pacote NuGet removidas (para os apresentar)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>Ficheiros de código de cópia de segurança e removidas (para os apresentar)

Cada um dos seguintes ficheiros foi cópia de segurança e removida do projeto. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz da diretório do projeto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>Ficheiros de código de cópia de segurança (para os apresentar)

Cada um dos seguintes ficheiros foi cópia de segurança antes de serem substituídos. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz da diretório do projeto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se posso selecionada *Ler diretório dados*, quais as alterações adicionais foram efetuadas à minha projeto?

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram efetuadas alterações adicionais à sua App ou Web. config

Foram adicionadas as seguintes entradas de configuração adicionais.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>A aplicação do Azure Active Directory tiver sido atualizada
A aplicação do Azure Active Directory tiver sido atualizada para incluir a permissão de *leitura diretório dados* e uma tecla adicional foi criada que foi utilizado, em seguida, como *Ida: palavra-passe* na `web.config` ficheiro.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
