<properties
   pageTitle="Exemplos de código do Azure Active Directory | Microsoft Azure"
   description="Um índice de exemplos de código do Azure Active Directory, organizados por cenário."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Exemplos de código do Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Pode utilizar o Microsoft Azure Active Directory (Azure AD) para adicionar a autorização de autenticação e de aplicações web e web APIs. Esta secção fornece hiperligações para exemplos de código que lhe mostram como o fazer e fragmentos de código que pode utilizar nas suas aplicações. Na página de amostra de código, encontrará leitura detalhada-me tópicos de ajudam com os requisitos, instalação e configuração da compilação. E o código é comentado para ajudar a compreender as secções críticas.

Para compreender o cenário básico para cada tipo de exemplo, consulte o artigo cenários de autenticação para Azure AD.

Contribuir para a nossa amostras no GitHub: [Microsoft Azure Active Directory amostras e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Browser para a aplicação Web

Estes exemplos mostram como escrever uma aplicação web que o direciona o browser do utilizador para iniciar sessão-los no Azure AD.



| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Web App-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Utilize OpenID ligar (ASP.Net OpenID ligar OWIN software intermédio) para autenticar os utilizadores a partir de um inquilino do Azure AD.
| C# / .NET | [Web App-MultiTenant OpenIdConnect DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Um inquilino com várias aplicação web .NET MVC que utiliza OpenID ligar (ASP.Net OpenID ligar OWIN software intermédio) para autenticar os utilizadores a partir de múltiplos inquilinos do Azure AD.
| C# / .NET | [Web App-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Utilize WS-Federation (ASP.Net WS Federation OWIN software intermédio) para autenticar os utilizadores a partir de um inquilino do Azure AD.






## <a name="single-page-application-spa"></a>Aplicação única página (SPA)

Este exemplo mostra como escrever uma aplicação de única página protegida com Azure AD.  

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| JavaScript, c# / .NET | [SinglePageApp DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Utilize ADAL para JavaScript e Azure AD para proteger uma aplicação baseada em AngularJS única página implementada com um ASP.NET web API back-end.


## <a name="native-application-to-web-api"></a>Aplicação nativa Web API

Estes exemplos de código mostram como criar aplicações de cliente nativo que chamam web APIs que estão protegidos por Azure AD. Utilizam a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Utilize o plug-in ADAL para Apache Cordova para criar uma aplicação de Apache Cordova que liga um web API e utiliza Azure AD para autenticação.
| C# / .NET | [NativeClient DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Uma aplicação de .NET WPF chamadas a um web API que está protegida utilizando Azure AD.
| C# / .NET | [NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Uma aplicação da loja Windows que chamadas a um web API que está protegida com Azure AD.
| C# / .NET | [NativeClient-WebAPI MultiTenant WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Uma aplicação da loja Windows chamar uma inquilino com várias da web API que está protegida com Azure AD.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Uma aplicação de cliente nativo que liga um web API, que obtém um token para funcionar em nome de utilizador original e, em seguida, utiliza o token para ligar a outra web API.
| C# / .NET | [NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Uma aplicação da loja Windows para Windows Phone 8.1 chamadas a um web API que é protegida pelo Azure AD.
| ObjC | [NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) | Uma aplicação do iOS chamadas a um web API que requer o Azure AD para autenticação.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Uma aplicação de cliente nativo que inclui uma lógica para processar um token JWT numa web API, em vez de utilizar o software OWIN intermédio.
| C# / Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Um enlace Xamarin para o nativo Azure AD autenticação biblioteca (ADAL) para a biblioteca Android.
| C# / Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Um enlace Xamarin para o nativo Azure AD autenticação biblioteca (ADAL) para iOS.
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Um projeto de Xamarin que destinos cinco plataformas e chamadas a um web API que é protegido pelo Azure AD.
| C# / .NET | [NativeClient sem periféricos DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Uma aplicação nativa que efetua a autenticação não interativa e chamadas a um web API que é protegida pelo Azure AD.



## <a name="web-application-to-web-api"></a>Aplicação Web para Web API

Estes exemplos de código Mostrar como utilizar [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para criar aplicações web que chamar web APIs que estão protegidos por Azure AD.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Web App-WebAPI OpenIDConnect DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Ligar uma web API com sessão iniciada no permissões do utilizador.
|  C# / .NET | [Web App-WebAPI-oauth2 ainda-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Ligar uma web API com as permissões da aplicação.
| C# / .NET | [Web App-WebAPI-oauth2 ainda-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Adicione autorização com [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para uma aplicação web existente para que possa ligar um web API.
| JavaScript | [WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configure um serviço de REST API que está integrado com o Azure AD para protecção API. Inclui um servidor de Node.js com uma API Web.
| C# / .NET | [Web App-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  Um inquilino com várias MVC aplicação que utiliza OpenID ligar (ASP.Net OpenID ligar OWIN software intermédio) para autenticar os utilizadores a partir de um inquilino do Azure AD web. Utiliza um código de autorização para invocar a API do gráfico.

## <a name="server-or-daemon-application-to-web-api"></a>Servidor ou Daemon aplicação Web API

Estes exemplos de código mostram como criar uma aplicação daemon ou um servidor que obtém recursos a partir de um API utilizando a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Daemon DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Uma aplicação de consola liga-lhe uma web API. A credencial de cliente é uma palavra-passe.
| C# / .NET | [Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Uma aplicação de consola que liga um web API. A credencial de cliente é um certificado.


## <a name="calling-azure-ad-graph-api"></a>Chamar API do Azure AD Graph

Estes exemplo código Mostrar como criar aplicações que chamam a API do Azure AD Graph para ler e escrever dados de diretório.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| Java | [Web App-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Uma aplicação web que utiliza a API do gráfico para aceder aos dados do Azure AD diretório.
| PHP | [Web App-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Uma aplicação web que utiliza a API do gráfico para aceder aos dados do Azure AD diretório.
| C# / .NET | [Web App-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Uma aplicação web que utiliza a API do gráfico para aceder aos dados do Azure AD diretório.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Esta aplicação consola demonstra chamadas de leitura e escrita comuns à Graph API e mostra como executar a atribuição de licenças de utilizador e atualizar um utilizador miniatura da fotografia e ligações.
| C# / .NET | [ConsoleApp-GraphAPI DiffQuery DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Uma aplicação de consola que utiliza a consulta diferença na API do gráfico para obter periódicos alterações a objetos de utilizador num inquilino Azure AD.
| C# / .NET | [Web App-GraphAPI DirectoryExtensions DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Uma aplicação de MVC utiliza consultas Graph API para gerar um organograma simples da empresa.
| PHP | [Web App-GraphAPI DirectoryExtensions PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Uma aplicação de PHP que liga a API do gráfico para registar uma extensão e, em seguida, ler, atualizar e eliminar os valores no atributo extensão.


## <a name="authorization"></a>Autorização

Estes exemplos de código mostram como utilizar o Azure AD para autorização.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Web App-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Execute o controlo de acesso baseado em funções (RBCA) utilizando afirmações de grupo do Azure Active Directory numa aplicação que está integrada com o Azure AD.
| C# / .NET | [Web App-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Execute o controlo de acesso baseado em funções (RBCA) utilizar funções de aplicação do Azure Active Directory numa aplicação que está integrada com o Azure AD.


## <a name="legacy-walkthroughs"></a>Tutoriais legado

Estas instruções passo a passo utiliza tecnologia ligeiramente mais antiga, mas ainda poderá ser de interesse.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Autorização baseadas em ACL e baseado em funções numa aplicação do Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Execute a autorização de baseado em funções (RBCA) e autorização com base em ACL numa aplicação que está integrada com o Azure AD.
| C# / .NET |  [Autenticação AAL - aplicação da loja Windows serviço REST-](http://go.microsoft.com/fwlink/?LinkId=330605) |  Utilize a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) (anteriormente AAL) loja versão Beta do Windows para adicionar as capacidades de autenticação de utilizador para uma aplicação da loja Windows.
| C# / .NET | [Autenticação de ADAL - aplicação nativa para serviço REST - com AAD através da caixa de diálogo do Browser](http://go.microsoft.com/fwlink/?LinkId=259814) |  Utilize a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) para adicionar as capacidades de autenticação de utilizador para um cliente WPF.
| C# / .NET | [Autenticação de ADAL - aplicação nativa para serviço REST - com ACS através da caixa de diálogo do Browser](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Utilize a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e [2.0 de serviço de controlo de acesso (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para adicionar funcionalidades de autenticação de utilizador para um cliente WPF.
| C# / .NET | [ADAL - autenticação de servidor para servidor](http://go.microsoft.com/fwlink/?LinkId=259816) | Utilize a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) para proteger chamadas de serviço a partir de um processo de lado do servidor para um serviço de MVC4 Web API REST.
| C# / .NET | [Adicionar início de sessão para a aplicação Web utilizando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configure uma aplicação do .NET para efetuar web serviço single sign-on contra no diretório da sua empresa Azure AD.
| C# / .NET | [Desenvolver aplicações Web do inquilino com várias com Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Utilize o Azure AD para adicionar ao serviço single sign-on e as capacidades de acesso do diretório de uma aplicação do .NET para trabalhar em toda a várias organizações.
JAVA | [Aplicação de exemplo Java para Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) | Utilize a API do gráfico para aceder a dados de diretório a partir do Azure AD.
PHP | [Aplicação de exemplo PHP para Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Utilize a API do gráfico para aceder a dados de diretório a partir do Azure AD.
| C# / .NET | [Aplicação de exemplo para o Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) | Utilize a API do gráfico para aceder a dados de diretório a partir do Azure AD.
| C# / .NET | [Aplicação de exemplo para a consulta de diferença do Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) | Utilize a consulta diferença na API do gráfico para obter periódicas alterações a objetos de utilizador num inquilino Azure AD.
| C# / .NET | [Aplicação de exemplo para integrar o pedido de inquilino com várias nuvem de Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Integre uma aplicação do inquilino com várias Azure AD.
| C# / .NET | [Proteger uma aplicação da loja Windows e o serviço de Web REST utilizando Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Criar um recurso simples web API e uma aplicação de cliente da loja Windows Azure AD a utilizar e a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md).
| C# / .NET| [Utilizar o gráfico de API para consultar Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configure uma aplicação do Microsoft .NET para utilizar a API do Azure AD Graph para aceder aos dados de um diretório de inquilino do Azure AD.

## <a name="see-also"></a>Consulte também

##### <a name="other-resources"></a>Outros recursos

[Guia do programador do Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API Conceptual e referência](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca de API Helper do Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

