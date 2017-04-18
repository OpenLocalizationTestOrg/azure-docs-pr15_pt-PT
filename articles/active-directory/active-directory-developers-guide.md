<properties
   pageTitle="Guia do Azure Active Directory para programadores | Microsoft Azure"
   description="Este artigo fornece um guia abrangente para recursos para programadores orientados para o Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Guia do Azure Active Directory para programadores

## <a name="overview"></a>Descrição geral
Como gestão de identidades como uma plataforma de serviço (IDMaaS), o Azure Active Directory (AD) fornece os programadores de uma forma eficaz de integre gestão de identidades nas suas aplicações. Os seguintes artigos fornecem descrições gerais sobre pós-implementação e principais funcionalidades do Azure AD. Sugerimos que lê-las na ordem ou ir para a [Introdução ao](#getting-started) se estiver pronto para aprofundar.


1. [Os benefícios da integração do Azure AD](./develop/active-directory-how-to-integrate.md): descobrir por que motivo a integração com o Azure AD oferece a melhor solução para o início de sessão seguro e autorização.

1. [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md): tirar partido de autenticação simplificado no Azure AD para fornecer o início de sessão na sua aplicação.

1. [Integração de aplicações com o Azure AD](active-directory-integrating-applications.md): Saiba como adicionar, atualizar e remover aplicações a partir do Azure AD e sobre as diretrizes de imagem corporativa para aplicações integradas.

1. [API do Azure AD Graph](active-directory-graph-api.md): utilizar a API do Azure AD Graph programaticamente ao Azure AD através de pontos finais de REST API. A API do Azure AD Graph também está acessível através do [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fornece uma API unificada que permite o acesso a várias serviço de nuvem Microsoft APIs, através de um ponto final de REST API único e com um token de acesso único.

1. [Bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md): facilmente autenticar os utilizadores obtenham tokens de acesso ao utilizar bibliotecas de autenticação do Azure AD para .NET, JavaScript, objectivo-C, Android e muito mais.


## <a name="getting-started"></a>Introdução

Nestes tutoriais estão adaptadas do múltiplas plataformas e podem ajudá-lo a começar rapidamente desenvolver com o Azure Active Directory. Como pré-requisito, tem de [obter um inquilino do Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guias de guia de aplicação Mobile e o PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Universal do Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Universal do Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrar diretamente OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Guias de guia de aplicação Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![NODE.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![Ligar OpenID](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[NODE.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrar diretamente OpenID ligar](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Guias de guia da Web API

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![NODE.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[NODE.js](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Consultar o Guia do guia de introdução de diretório

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API do gráfico](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Procedimentos

Estes artigos descrevem como executar tarefas específicas utilizando o Azure Active Directory:

- [Obter um inquilino do Azure AD](active-directory-howto-tenant.md)
- [Qualquer utilizador do Azure AD utilizando o padrão de inquilino com várias aplicações de início de sessão](active-directory-devhowto-multi-tenant-overview.md)
- Ativar a publicação em aplicação SSO utilizando ADAL, [Android](active-directory-sso-android.md) e dispositivos [iOS](active-directory-sso-ios.md)
- [Tornar a sua aplicação AppSource certificados para Azure AD](active-directory-devhowto-appsource-certified.md)
- [Liste a sua aplicação na Galeria de aplicação do Azure AD](active-directory-app-gallery-listing.md)
- [Submeter aplicações web do Office 365 para o Dashboard vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Compreender manifesto da aplicação Azure Active Directory](active-directory-application-manifest.md)
- [Compreender as diretrizes de imagem corporativa para os botões de aquisição iniciar sessão e de aplicação na sua aplicação de cliente](active-directory-branding-guidelines.md)
- [Pré-visualização: Como criar aplicações que inicie sessão utilizadores com contas tanto pessoais e escolar ou profissional](active-directory-appmodel-v2-overview.md)
- [Pré-visualização: Como criar aplicações que inscrever-se e consumidores de início de sessão](../active-directory-b2c/active-directory-b2c-overview.md)
- [Pré-visualização: configurar as durações tokens no Azure AD](active-directory-configurable-token-lifetimes.md) através do PowerShell. Consulte o artigo [operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e a [entidade de política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) para obter detalhes sobre como configurar através a API do Azure AD Graph.

## <a name="reference"></a>Referência

Estes artigos fornecem uma referência de foundation para o resto e biblioteca de autenticação APIs, protocolos, erros, exemplos de código e os pontos finais.  

###  <a name="support"></a>Suporte
- [Perguntas com tag](http://stackoverflow.com/questions/tagged/azure-active-directory): soluções de localizar Azure Active Directory na pilha de conteúdo adicional ao procurar etiquetas [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
- Consulte o artigo [Glossário de programador do Azure AD](active-directory-dev-glossary.md) para obter definições de alguns dos termos frequentemente utilizados relacionados com a programação de aplicações e integração.

### <a name="code"></a>Código

- [Bibliotecas de abrir origem do Azure Active Directory](http://github.com/AzureAD): é a forma mais fácil para localizar a origem de uma biblioteca utilizando a nossa [lista de biblioteca](active-directory-authentication-libraries.md).

- [Azure Active Directory amostras](https://github.com/azure-samples?query=active-directory): é a forma mais fácil para navegar na lista de amostras, utilizando o [índice de exemplos de código](active-directory-code-samples.md).

- [Active Directory autenticação biblioteca (ADAL) para o .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - documentação de referência está disponível para [a versão mais recente principal](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) e a [versão anterior principal](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API do gráfico

- [Graph API referência](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência do resto do Azure Active Directory Graph API. [Ver a experiência de referência interactiva API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Graph API âmbitos de permissão](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): OAuth 2.0 âmbitos de permissão que são utilizados para controlar o acesso de que tem uma aplicação aos dados de directório num inquilino.

### <a name="authentication-and-authorization-protocols"></a>Autenticação e autorização protocolos

- [Sobreposição de chave de iniciar sessão no Azure AD](active-directory-signing-key-rollover.md): Saiba mais sobre como iniciar sessão Azure AD cadence sobreposição chave e como atualizar a chave para os cenários de aplicação mais comuns.

- [Protocolo OAuth 2.0: utilizar a conceder de código autorização](active-directory-protocols-oauth-code.md): pode utilizar conceder do código de autorização do protocolo OAuth 2.0, para autorizar o acesso às aplicações Web e de inquilinos APIs da Web no seu Azure Active Directory.

- [Protocolo OAuth 2.0: Noções sobre a conceder implícito](active-directory-dev-understanding-oauth2-implicit-grant.md): Saiba mais sobre conceder autorização implícito, e se está à direita para a sua aplicação.

- [Protocolo OAuth 2.0: serviço para chamadas de serviço utilizando credenciais de cliente](active-directory-protocols-oauth-service-to-service.md): de credenciais do cliente OAuth 2.0 conceder permite a um serviço web (um confidencial cliente) para utilizar a suas própria credenciais para autenticar durante a chamada de outro serviço web, em vez de um utilizador a representar. Neste cenário, o cliente é normalmente um serviço web camada, um serviço de daemon ou o Web site.

- [Protocolo OpenID ligar 1.0: iniciar sessão e a autenticação](active-directory-protocols-openid-connect-code.md): protocolo de OpenID ligar 1.0 expande OAuth 2.0 para utilização como protocolo de autenticação. Uma aplicação de cliente pode receber uma id_token para gerir o processo de início de sessão ou aumentar o fluxo de código de autorização para receber um id_token e a autorização o código.

- [Referência de protocolo SAML 2.0](active-directory-saml-protocol-reference.md): SAML 2.0 o protocolo permite que as aplicações fornecer uma experiência de início de sessão único aos seus utilizadores.

- [Protocolo de Federação WS 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory suporta a Federação WS 1.2 de acordo com a Web Serviços de Federação versão 1.2 especificação. Para mais informações sobre o documento de metadados de Federação, consulte o artigo [Metadados de Federação](active-directory-federation-metadata.md).

- [Tipos de token e afirmação suportados](active-directory-token-and-claims.md): pode utilizar este guia para compreender e avaliar afirmações em tokens de SAML 2.0 e JSON Web Tokens (JWT).

## <a name="videos"></a>Vídeos

### <a name="build"></a>Criar

Estas apresentações de descrição geral sobre como desenvolver aplicações utilizando o Azure Active Directory da funcionalidade altifalantes que trabalhem diretamente na equipa de engenharia. As apresentações de folha de rosto tópicos fundamentais, incluindo IDMaaS, autenticação, Federação de identidade e o início de sessão único.

- [Identidade da Microsoft: Estado da União e futura orientação](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Gestão de identidades como um serviço para aplicações modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Desenvolver aplicações web moderna com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Desenvolver aplicações nativas modernas com o Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure sexta-feira
[Azure sexta-feira](https://azure.microsoft.com/documentation/videos/azure-friday/) é uma periódica sexta-feira 1:1 série de vídeos que é dedicada a colocar breve (10 – 15 minutos) proceda à audição com especialistas numa variedade de tópicos Azure.  Utilize a funcionalidade de filtro de serviços na página para ver todos os vídeos do Azure Active Directory.

- [Azure identidade 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure identidade 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure identidade 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Rede social

- [Blogue de equipa do Active Directory](http://blogs.technet.com/b/ad/): A evolução mais recente no mundo do Azure Active Directory.

- [Blogue de equipa do Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informações do Azure Active Directory que seja específicas para a API do gráfico.

- [Identidade da nuvem](http://www.cloudidentity.net): ideias sobre a gestão de identidades como um serviço, a partir de um PM diretório principal Azure Active.  

- [Azure Active Directory no Twitter](https://twitter.com/azuread): anúncios do Azure Active Directory no 140 caracteres ou menos.

## <a name="windows-server-on-premises-development"></a>Windows Server no local desenvolvimento
Para obter orientações sobre a utilização do desenvolvimento do Windows Server e serviços de Federação do Active Directory (ADFS), consulte:

- [AD FS cenários para programadores](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): fornece uma descrição geral dos componentes de AD FS e como funciona, com detalhes sobre os cenários de autenticação suportados/autorização.
- [Tutoriais do AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): uma lista de artigos de guia passo a passo, que disponibiliza instruções passo a passo sobre como implementar os fluxos de autenticação/autorização relacionados.
