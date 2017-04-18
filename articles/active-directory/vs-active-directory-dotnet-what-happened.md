<properties
    pageTitle="O que aconteceu ao meu projecto MVC (Visual Studio Azure Active Directory ligado serviço) | Microsoft Azure "
    description="Descreve o que acontece ao seu projeto MVC quando se liga à Azure AD, utilizando os serviços do Visual Studio ligado"
    services="active-directory"
    documentationCenter="na"
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

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projecto MVC (Visual Studio Azure Active Directory ligado serviço)?

> [AZURE.SELECTOR]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>Referências foram adicionadas

### <a name="nuget-package-references"></a>Referências de pacote NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Referências de .NET

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Foi adicionado código

### <a name="code-files-were-added-to-your-project"></a>Ficheiros de código foram adicionados ao projeto

Uma classe de arranque de autenticação, **App_Start/Startup.Auth.cs** foi adicionada ao projeto que contém a lógica de arranque para a autenticação do Azure AD. Além disso, uma classe controlador, Controllers/AccountController.cs foi adicionada que contém métodos **SignIn()** e **SignOut()** . Por fim, vista parcial, **Views/Shared/_LoginPartial.cshtml** foi adicionada que contém uma ligação de ação para o início de sessão do/encerrar.

### <a name="startup-code-was-added-to-your-project"></a>Código de arranque foi adicionado ao projeto

Se já tinha uma classe de arranque no seu projeto, o método de **configuração** foi atualizado para incluir uma chamada para **ConfigureAuth(app)**. Caso contrário, uma classe de arranque foi adicionada ao seu projeto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>O seu App ou Web. config tem valores de configuração da nova

Foram adicionadas as seguintes entradas de configuração.


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Uma aplicação do Azure Active Directory (AD) foi criada
Uma aplicação do Azure AD foi criada no diretório que selecionou no assistente.

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se posso selecionada *desactivar a autenticação de contas de utilizador Individual*, quais as alterações adicionais foram efetuadas à minha projeto?
Referências de pacote NuGet foram removidas e ficheiros foram removidos e cópias de segurança. Consoante o estado do seu projeto, poderá ter de remover manualmente referências adicionais ou ficheiros, ou modificar o código conforme adequado.

### <a name="nuget-package-references-removed-for-those-present"></a>Referências de pacote NuGet removidas (para os apresentar)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Ficheiros de código de cópia de segurança e removidas (para os apresentar)

Cada um dos seguintes ficheiros foi cópia de segurança e removida do projeto. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz da diretório do projeto.

- **App_Start\IdentityConfig.CS**
- **Controllers\ManageController.CS**
- **Models\IdentityModels.CS**
- **Models\ManageViewModels.CS**

### <a name="code-files-backed-up-for-those-present"></a>Ficheiros de código de cópia de segurança (para os apresentar)

Cada um dos seguintes ficheiros foi cópia de segurança antes de serem substituídos. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz da diretório do projeto.

- **Startup.CS**
- **App_Start\Startup.auth.CS**
- **Controllers\AccountController.CS**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se posso selecionada *Ler diretório dados*, quais as alterações adicionais foram efetuadas à minha projeto?

Foram adicionadas referências adicionais.

###<a name="additional-nuget-package-references"></a>Referências de pacote NuGet adicionais

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>Referências .NET adicionais

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>Ficheiros de código adicionais foram adicionados ao projeto

Foram adicionados dois ficheiros para suportar a colocação em cache token: **Models\ADALTokenCache.cs** e **Models\ApplicationDbContext.cs**.  Um controlador adicional e vista foram adicionados para ilustrar aceder a informações de perfil de utilizador utilizando o Microsoft graph Azure APIs.  Estes ficheiros são **Controllers\UserProfileController.cs** e **Views\UserProfile\Index.cshtml**.

###<a name="additional-startup-code-was-added-to-your-project"></a>Código de arranque adicional foi adicionado ao projeto

No ficheiro de **startup.auth.cs** , um novo objeto **OpenIdConnectAuthenticationNotifications** foi adicionado no membro de **notificações** do **OpenIdConnectAuthenticationOptions**.  Isto é para ativar o código de acesso OAuth a receber e exchange-lo para um token de acesso.

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram efetuadas alterações adicionais à sua App ou Web. config

Foram adicionadas as seguintes entradas de configuração adicionais.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Foram adicionadas as seguintes secções de configuração e a cadeia de ligação.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>A aplicação do Azure Active Directory tiver sido atualizada
A aplicação do Azure Active Directory tiver sido atualizada para incluir a permissão de *leitura diretório dados* e uma tecla adicional foi criada que foi utilizado, em seguida, como o *ida: ClientSecret* no ficheiro **Web. config** .

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
