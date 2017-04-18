<properties
   pageTitle="Bibliotecas de autenticação do Azure Active Directory | Microsoft Azure"
   description="A biblioteca de autenticação do Azure AD (ADAL) permite que o cliente os programadores de aplicações autenticar facilmente os utilizadores na nuvem ou no local Active Directory (AD) e, em seguida, obtenha tokens de acesso para proteger API chamadas."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticação do Azure Active Directory

A autenticação do Azure AD biblioteca (ADAL) permite que os programadores de aplicação de cliente autenticar facilmente os utilizadores na nuvem ou no local Active Directory (AD) e, em seguida, obtenha tokens de acesso para proteger API chamadas. ADAL tem muitas funcionalidades que a autenticação tornar mais fácil para os programadores, tal como suporte assíncrono, uma cache token configurável que armazena tokens de acesso e tokens de atualização, a atualização token automática quando um token de acesso expira e um token de atualização está disponível e muito mais. Por processamento a maior parte da complexidade, ADAL pode ajudar a concentrar-se um programador nas lógica empresarial na sua aplicação e proteger facilmente os recursos sem ser um especialista em segurança.

ADAL está disponível numa variedade de plataformas.

|Plataforma|Nome do pacote|Cliente/servidor|Transferir|Código fonte|Documentação & amostras|
|---|---|---|---|---|---|
|Cliente .NET, da loja Windows, UWP, Xamarin iOS e Android|Biblioteca de autenticação do Active Directory (ADAL) para .NET v3 |Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentação](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET cliente, da loja Windows, Windows Phone 8.1 |Biblioteca de autenticação do Active Directory (ADAL) para .NET v2 |Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Documentação](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Biblioteca de autenticação do Active Directory (ADAL) para JavaScript|Cliente|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Exemplo: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Biblioteca de autenticação do Active Directory (ADAL) para objectivo-C|Cliente|[ADAL para objectivo-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL para objectivo-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Exemplo: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Biblioteca de autenticação do Active Directory (ADAL) para Android|Cliente|[ADAL para Android (o repositório Central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL para Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Exemplo: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|NODE.js|Biblioteca de autenticação do Active Directory (ADAL) para Node.js|Cliente|[ADAL para Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL para Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Exemplo: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|NODE.js|Software intermédio de autenticação do Microsoft Azure Active Directory Passport para nó|Cliente|[Azure Active Passport de diretório para Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory para Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Biblioteca de autenticação do Active Directory (ADAL) para Java|Cliente|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensões de protocolo de identidade para o Microsoft .NET Framework 4,5|Servidor|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Azure extensões de modelo de identidade AD para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Processador Token de Web JSON para o Microsoft .net Framework 4,5|Servidor|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Azure extensões de modelo de identidade AD para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Software intermédio OWIN que permite uma aplicação utilizar tecnologia da Microsoft para autenticação.|Servidor|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Software intermédio OWIN que permite uma aplicação utilizar OpenIDConnect para autenticação.|Servidor|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemplo: [Web App-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Software intermédio OWIN que permite uma aplicação utilizar WS Federation para autenticação.|Servidor|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemplo: [Web App-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Cenários

Seguem-se três cenários comuns na qual ADAL pode ser utilizado para autenticação.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticação de utilizadores de uma aplicação de cliente para um recurso remoto

Neste cenário, um programador tem um cliente, como uma aplicação de WPF, o que precisa de aceder a um recurso remoto protegido por Azure AD, tal como uma web API. Ele tem uma subscrição do Azure, sabe como invocar descendentes web API e sabe o inquilino do Azure AD que utiliza a web API. Como resultado, ele pode utilizar ADAL para facilitar a autenticação com Azure AD ao totalmente delegar a experiência de autenticação para ADAL ou ao processamento explicitamente as credenciais de utilizador. ADAL torna mais fácil autenticar o utilizador, obter um token de acesso e token de atualização a partir do Azure AD e, em seguida, utilize o token de acesso para tornar pede para a web API.

Para um exemplo de código que demonstra este cenário utilizando a autenticação do Azure AD, consulte o artigo [Nativo cliente WPF aplicação Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticação de uma aplicação de servidor para um recurso remoto

Neste cenário, um programador tem uma aplicação em execução num servidor que precisa de aceder a um recurso remoto protegido por Azure AD, tal como uma web API. Ele tem uma subscrição do Azure, sabe como invocar o serviço descendentes e sabe que utiliza o inquilino do Azure AD a API de web. Como resultado, ele pode utilizar ADAL para facilitar a autenticação com Azure AD através do explicitamente tratamento de credenciais da aplicação. ADAL torna mais fácil obter um token a partir do Azure AD utilizando credenciais de cliente a aplicação e, em seguida, utilizar esse token para tornar pede para a web API. ADAL também processa a gerir o tempo de vida do token de acesso ao colocação em cache-lo e renová-lo conforme necessário. Para um exemplo de código que demonstra neste cenário, consulte o artigo [Aplicação da consola à API dos Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticação de uma aplicação de servidor em nome de um utilizador para aceder a um recurso remoto

Neste cenário, um programador tem uma aplicação em execução num servidor que precisa de aceder a um recurso remoto protegido por Azure AD, tal como uma web API. O pedido também tem de ser efetuadas em nome de um utilizador no Azure AD. Ele tem uma subscrição do Azure, sabe como invocar descendentes web API e sabe Azure AD inquilinos utilizada pelo serviço. Assim que o utilizador é autenticado para a aplicação web, a aplicação pode obter um código de autorização para o utilizador a partir do Azure AD. A aplicação web, em seguida, pode utilizar ADAL para obter um token de acesso e atualizar token em nome de um utilizador com as credenciais de código e o cliente de autorização associadas à aplicação a partir do Azure AD. Assim que estiver a aplicação web possuir o token de acesso, pode ligar a web API, até o token de expira. Quando o token de expira, a aplicação web pode utilizar ADAL para obter um novo token de acesso ao utilizar a atualização token que foi recebida anteriormente.


## <a name="see-also"></a>Consulte também

[Guia do Programador de Azure Active Directory](active-directory-developers-guide.md)

[Cenários de autenticação para o Azure Active directory](active-directory-authentication-scenarios.md)

[Exemplos de código do Azure Active Directory](active-directory-code-samples.md)
