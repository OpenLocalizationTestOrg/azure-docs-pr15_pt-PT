<properties
    pageTitle="Tipos do ponto final 2.0 | Microsoft Azure"
    description="Os tipos de cenários suportados pelo ponto final do Azure AD 2.0 e apps."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="types-of-apps-for-the-v20-endpoint"></a>Tipos de aplicações para o ponto final 2.0
O ponto final 2.0 suporta a autenticação para uma variedade de arquitecturas de aplicação moderna, as quais sejam baseiam os protocolos de padrão do setor [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) e/ou [OpenID ligar](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Este documento descreve brevemente os tipos de aplicações que podem ser criados, independentemente da plataforma ou idioma que preferir.  Irá ajudá-lo a perceber os cenários de alto níveis antes de a [passar para a direita para o código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas
Cada aplicação que utiliza o ponto final 2.0 terá de ser registado na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  O processo de registo de aplicação irá recolher e atribuir alguns valores para a sua aplicação:

- Um **Id da aplicação** que identifica exclusivamente a sua aplicação
- **Redirecionar URI** que podem ser utilizados para direcionar respostas para a sua aplicação
- Alguns outros valores de cenário específicos.  Para obter mais detalhes, saiba como [registar uma aplicação](active-directory-v2-app-registration.md).

Depois de registar, a aplicação comunica com Azure AD ao enviar pedidos para o ponto final do Azure Active Directory 2.0.  Fornecemos quadros abrir origem e de bibliotecas encarregam dos detalhes destes pedidos ou pode implementar a lógica de autenticação por intencionada pedidos para estes pontos finais:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Aplicações Web
Para aplicações web (.NET, PHP, Java, Rubi, Python, nó, etc) que são acedidas através de um browser, pode efetuar utilizador iniciar sessão com [A ligação OpenID](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  No OpenID ligar-se a aplicação web recebe um `id_token`, um token de segurança que verifica a identidade do utilizador e fornece informações sobre o utilizador a forma de afirmações:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Pode obter informações sobre todos os tipos de tokens e em afirmações para uma aplicação na [referência token 2.0](active-directory-v2-tokens.md).

Em aplicações de servidor web, o fluxo de autenticação de início de sessão no leva-o até estes passos de alto níveis:

![Imagem de pistas de diagrama de aplicação Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

A validação de id_token utilizar uma chave pública assinatura recebida a partir do ponto final 2.0 é suficiente para garantir a identidade do utilizador e definir uma sessão de cookies que pode ser utilizada para identificar o utilizador sobre os pedidos subsequentes página.

Para ver este cenário em ação, experimente um das amostras de início de sessão no código de aplicação web no nossa secção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .

Para além de simple iniciar sessão, uma aplicação de servidor web também poderá ter de aceder a algumas outras serviço web como uma REST API.  Neste caso a aplicação de servidor web pode participar num combinado OpenID ligar & OAuth 2.0 fluxo, utilizando o [código de autorização de 2.0 OAuth fluxo](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Este cenário é abrangido abaixo nosso [tópico de introdução ao WebAPI Web App](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web
Pode utilizar o ponto final 2.0 para proteger os serviços de web, assim, tal como a RESTful Web API sua aplicação.  Em vez de cookies id_tokens e de sessão, Web APIs utilize access_tokens OAuth 2.0 para proteger os seus dados e autenticar pedidos recebidos.  O autor da chamada de um Web API acrescenta uma access_token no cabeçalho da autorização de um pedido de HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web, em seguida, pode utilizar o access_token para verificar a identidade a API do autor da chamada e extrair informações sobre o autor da chamada de afirmações que são codificadas na access_token.  Pode obter informações sobre todos os tipos de tokens e em afirmações para uma aplicação na [referência token 2.0](active-directory-v2-tokens.md).

API de Web conceder utilizadores power para optar ativamente por participar-no/optar ativamente de determinadas funcionalidades ou dados por expor permissões, caso contrário, conhecidas como [âmbitos](active-directory-v2-scopes.md).  Para uma aplicação chamada adquirir permissão para um âmbito, o utilizador tem o seu consentimento para o âmbito durante um fluxo.  O ponto final 2.0 irá encarregam-se de perguntar ao utilizador para a permissão e de gravação essas permissões access_tokens todos os que recebe a API Web.  Todas as necessidades de Web API de se preocupar é validar access_tokens que recebe em cada chamada e efetuar as verificações de autorização adequada.

API de Web pode receber access_tokens de todos os tipos de aplicações, incluindo aplicações web do servidor, ambiente de trabalho e aplicações móveis, única página aplicações, daemons de lado do servidor e até mesmo outras APIs da Web.  O fluxo de alto nível para autenticação de api web é da seguinte forma:

![Imagem de pistas de diagrama de Web API](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para saber mais sobre authorization_codes, refresh_tokens e os passos detalhados do introdução access_tokens, leia sobre o [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para saber como seguro uma web api com oauth2 ainda access_tokens, consulte os exemplos de código do web api na nossa [secção introdução](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Mobile e apps nativos
As aplicações que são instaladas num dispositivo, como as aplicações de ambiente de trabalho e móveis, muitas vezes necessitam aceder a serviços de back-end ou APIs da Web que armazenar dados e executar várias funções em nome de um utilizador.  Estas aplicações podem adicionar a iniciar sessão e autorização para serviços de back-end utilizando o [código de autorização de 2.0 OAuth fluxo](active-directory-v2-protocols-oauth-code.md).  

No, este fluxo de uma aplicação recebe um authorization_code do ponto final 2.0 ao utilizador iniciar sessão, que representa a permissão a aplicação para ligar a serviços de back-end em nome de utilizador tiver sessão iniciada no atualmente.  A aplicação, em seguida, pode trocar authoriztion_code em segundo plano para uma access_token OAuth 2.0 e um refresh_token.  A aplicação pode utilizar o access_token para autenticar a Web APIs nos pedidos HTTP e pode utilizar o refresh_token para obter access_tokens novo quando expiram mais antigos.

![Imagem de pistas de diagrama de aplicação nativa](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Única página de aplicações (javascript)
Muitas aplicações modernas tem uma única página aplicação (SPA) front-end escrito principalmente em javascript frequentemente utilizando quadros e como AngularJS, Ember.js, Durandal, etc.  O ponto final do Azure AD 2.0 suporta estas aplicações utilizar o [Fluxo implícito do token 2.0](active-directory-v2-protocols-implicit.md).

Deste fluxo, a aplicação recebe tokens da 2.0 autorizar ponto final diretamente, sem executar qualquer trocas de servidor para servidor de back-end.  Esta opção permite-todos os lógica de autenticação e processamento de sessão para tirar coloque completamente no cliente do javascript, sem efetuar redirecionamentos página extra.

![Imagem de pistas de diagrama de fluxo implícito](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver este cenário em ação, experimente um dos exemplos de código de aplicação única página no nosso secção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .

### <a name="daemonsserver-side-apps"></a>Aplicações de lado daemons/servidor
As aplicações que contenham processos longa ou que funcionem sem a presença de um utilizador também precisa de uma maneira de aceder a recursos seguros, tal como APIs da Web.  Estas aplicações podem autenticar e obter tokens utilizando identidade a aplicação (em vez de identidade delegada de um utilizador) utilizando o cliente OAuth 2.0 fluxo de credenciais.

Neste fluxo, a aplicação obtém tokens por interagir diretamente com o `/token` ponto final:

![Imagem do daemon aplicação pistas de diagrama](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Para criar uma aplicação daemon, consulte o documeenation de credenciais de cliente no nossa secção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) ou referir-se para [esta aplicação de exemplo .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Limitações atuais
Estes tipos de aplicações não são atualmente suportados pelo ponto final 2.0, mas são no guia.  Limitações adicionais e restrições para o ponto final 2.0 são descritas no [artigo de limitações 2.0](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Web interligada APIs (em nome de)
Muitos arquitecturas incluem uma API Web que necessita para ligar a outra descendentes Web API, ambos protegidos pelo ponto final 2.0.  Este cenário é comuns em nativos clientes que têm um Web API do back-end, que por sua vez chama um serviço de Microsoft Online como o Office 365 ou a API do gráfico.

Este cenário Web API interligado pode ser suportado utilizando a conceder OAuth 2.0 Jwt portadores credenciais, caso contrário, conhecida como de [Um fluxo de On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  No entanto, o fluxo em nome de não está atualmente implementado o ponto final 2.0.  Para ver como funciona o deste fluxo no Azure AD ficará disponível de serviço, consulte o artigo [em nome de código de exemplo no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
