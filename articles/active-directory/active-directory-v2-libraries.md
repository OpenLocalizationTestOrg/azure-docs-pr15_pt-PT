<properties
   pageTitle="Bibliotecas de autenticação de 2.0 do Azure Active Directory | Microsoft Azure"
   description="Cliente compatível com bibliotecas e bibliotecas de software intermédio server e biblioteca relacionada, origem e as ligações de amostras, para o ponto final do Azure Active Directory 2.0."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticação de 2.0 do Azure Active Directory
O ponto final do Azure Active Directory (Azure AD) 2.0 suporta os protocolos de OAuth 2.0 e OpenID ligar 1.0 de norma da indústria. Pode utilizar várias bibliotecas Microsoft e outras organizações com o ponto final 2.0.

Quando constrói uma aplicação que utiliza o ponto final 2.0, recomendamos que utilize bibliotecas que são escritas por especialistas de domínio do protocolo que seguem uma metodologia de ciclo de vida de desenvolvimento da segurança (SDL), como [o seguido Microsoft][Microsoft-SDL]. Se decidir suporte de mão código para os protocolos, recomendamos que siga metodologia SDL e presta atenção fechar para as considerações de segurança nas especificações padrões para cada protocolo.

## <a name="types-of-libraries"></a>Tipos de bibliotecas

Azure AD 2.0 funciona com dois tipos de biblioteca:

- **Bibliotecas de cliente**. Clientes nativos e servidores utilizam bibliotecas do cliente para obter tokens de acesso para ligar a um recurso, tal como do Microsoft Graph.
- **Bibliotecas de software intermédio server**. Aplicações Web utilizam bibliotecas de software intermédio do servidor para o início de sessão do utilizador. Web APIs utilizam bibliotecas de software intermédio do servidor para validar os tokens que são enviadas por clientes nativos ou por outros servidores de.

## <a name="library-support"></a>Suporte de biblioteca
Uma vez que pode escolher qualquer biblioteca compatíveis com as normas quando utiliza o ponto final 2.0, é importante saber onde obter suporte. Para problemas e pedidos de funcionalidades no código de biblioteca, contacte o proprietário da biblioteca. Para problemas e pedidos de funcionalidades na implementação do lado do serviço de protocolo, contacte a Microsoft.

Bibliotecas de surgir em duas categorias de suporte:

- **Microsoft suportadas**. A Microsoft fornece correções para estas bibliotecas e tenha feito SDL diligenciar nestas bibliotecas.
- **Compatíveis**. A Microsoft testado estas bibliotecas em cenários básicos e confirmado que funcionam com o ponto final 2.0. A Microsoft não fornece correções para estas bibliotecas e já não o fez consultar estas bibliotecas. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de origem de abrir a biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto final 2.0, consulte as secções seguintes neste artigo.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente suportadas Microsoft
| Plataforma| Nome da biblioteca| Transferir | Código fonte | Exemplo |
| :-: | :-: | :-: | :-: | :-: |
| .NET, da loja Windows, Xamarin | Biblioteca de autenticação da Microsoft (MSAL) para .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL para .NET (GitHub)][ClientLib-NET-Repo] | [Exemplo de cliente de estação de trabalho nativos do Windows][ClientLib-NET-Sample] |
| NODE.js | Microsoft Azure Active Directory Passport.js Plug-in | [Azure-Passport-AD (npm)][ClientLib-Node-Lib] | [Azure-Passport-AD (GitHub)][ClientLib-Node-Repo] | Brevemente |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de software intermédio servidor Microsoft suportadas
| Plataforma| Nome da biblioteca| Transferir | Código fonte | Exemplo |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4. x | OWIN OpenID ligue-se o software intermédio para ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Project Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Exemplo de aplicação Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4. x | OWIN tipo de ligação OAuth software intermédio para ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Project Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Exemplo de Web API][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET core | OWIN OpenID ligue-se o software intermédio para .NET Core | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Exemplo de aplicação Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET core | OWIN tipo de ligação OAuth software intermédio para .NET Core | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Brevemente |
| NODE.js | Microsoft Azure Active Directory Passport.js Plug-in | [Azure-Passport-AD (npm)][ServerLib-Node-Lib] | [Azure-Passport-AD (GitHub)][ServerLib-Node-Repo] | [Exemplo de aplicação Web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatíveis
| Plataforma| Nome da biblioteca | Versão testado | Código fonte | Exemplo |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Exemplo de aplicação nativa](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Exemplo de aplicação nativa](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | Brevemente |
| Balão de Python | [Balão OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Balão OAuthlib](https://github.com/lepture/flask-oauthlib) | Brevemente |
| Rubi | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[Oauth2 ainda de OmniAuth](https://github.com/intridea/omniauth-oauth2) | Brevemente |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de software intermédio servidor compatíveis
Brevemente

## <a name="related-content"></a>Conteúdo relacionado
Para mais informações sobre o ponto final do Azure AD 2.0, consulte o artigo [Descrição geral do Azure AD aplicação modelo 2.0][AAD-App-Model-V2-Overview].

Para nos ajudar a refinar e dar forma a nossa conteúdo, utilize a funcionalidade de comentários Disqus no final deste artigo para fornecer comentários.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
