<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Os tipos de aplicações que pode ser criados no B2C de diretório ativa Azure."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure B2C diretório ativos: Tipos de aplicações

Azure Active Directory (Azure AD) B2C suporta a autenticação para uma variedade de arquitecturas de aplicação Moderno. Todos os sejam baseiam os protocolos de padrão do setor [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID ligar](active-directory-b2c-reference-protocols.md). Este documento descreve brevemente os tipos de aplicações que podem ser criados, independentemente da plataforma ou idioma que preferir. Também ajuda-o a compreender os cenários de alto nível antes de [começar a criação de aplicações](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>Noções básicas
Deverá estar registado junto a cada app que utiliza o Azure AD B2C no seu [diretório B2C](active-directory-b2c-get-started.md) através de do [Portal do Azure](https://portal.azure.com/). O processo de registo de aplicação recolhe e atribui alguns valores para a sua aplicação:

- Um **ID da aplicação** que identifica exclusivamente a sua aplicação.
- **Redirecionar URI** que podem ser utilizados para direcionar respostas para a sua aplicação.
- Outros valores de cenário específicos. Para obter mais detalhes, saiba como [registar uma aplicação](active-directory-b2c-app-registration.md).

Após a aplicação está registada, comunica com o Azure AD ao enviar pedidos para o ponto final do Azure AD 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Cada pedido que é enviado para o Azure AD B2C Especifica uma **política**. Uma política de controla o comportamento do Azure AD. Também pode utilizar estes pontos finais para criar um conjunto altamente personalizável de experiências do utilizador. Políticas comuns incluem inscrição, iniciar sessão e políticas de perfil-editar. Se não estiver familiarizado com as políticas, deverá ler sobre o Azure AD B2C [quadro de política extensible](active-directory-b2c-reference-policies.md) antes de continuar.

A interação do cada aplicação com um ponto final de 2.0 segue um padrão de alto nível semelhante:

1. A aplicação encaminha os utilizadores para o ponto final 2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O utilizador conclui a política de acordo com a definição de política.
4. A aplicação recebe algum tipo de token de segurança do ponto final 2.0.
5. A aplicação utiliza o token de segurança para aceder a informações protegidas ou um recurso protegido.
6. O servidor de recursos valida o token de segurança para confirmar que é possível conceder acesso.
7. A aplicação periodicamente atualiza o token de segurança.

<!-- TODO: Need a page for libraries to link to -->
Estes passos podem ser ligeiramente com base no tipo de aplicação que estiver a compilar. Bibliotecas de abrir origem podem também abordar os detalhes por si.

## <a name="web-apps"></a>Aplicações Web
Para web apps (incluindo .NET, PHP, Java, Rubi, Python e Node.js) que estão alojados num servidor e acedidos através de um browser, Azure AD B2C suporta [OpenID ligar](active-directory-b2c-reference-protocols.md) para todas as experiências do utilizador. Isto inclui iniciar sessão, inscrição, gestão e de perfil. Na implementação do Azure AD B2C do OpenID Connect, a aplicação web do inicia estes experiências do utilizador através da emissão pedidos de autenticação para Azure AD. O resultado do pedido é uma `id_token`. Este token de segurança representa a identidade do utilizador. Também fornece informações sobre o utilizador a forma de afirmações:

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

Saiba mais sobre os tipos de tokens e em afirmações para uma aplicação no [B2C token de referência](active-directory-b2c-reference-tokens.md).

Numa aplicação web, cada execução de uma [política de](active-directory-b2c-reference-policies.md) leva-o até estes passos de alto nível:

![Imagem de pistas de diagrama de aplicação Web](./media/active-directory-b2c-apps/webapp.png)

Validação do `id_token` ao utilizar uma chave pública assinatura que é recebida a partir do Azure AD é suficiente para verificar a identidade do utilizador. Este procedimento também define um cookie de sessão que pode ser utilizado para identificar o utilizador sobre os pedidos subsequentes página.

Para ver este cenário em ação, experimente um das amostras de início de sessão no código de aplicação web no nosso [que introdução ao secção](active-directory-b2c-overview.md#getting-started).

Para além de facilitar simple iniciar sessão, uma aplicação de servidor web também poderá ter de aceder a um serviço web de back-end. Neste caso, a aplicação web pode executar um [fluxo OpenID ligar](active-directory-b2c-reference-oidc.md) de um pouco diferentes e adquirir tokens utilizando códigos de autorização e atualize tokens. Este cenário é descrito na seguinte [secção APIs da Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>APIs da Web
Pode utilizar o Azure AD B2C para proteger os serviços web, tal como web RESTful sua aplicação API. Web APIs pode utilizar OAuth 2.0 para proteger os respetivos dados, ao receber pedidos de HTTP utilizando tokens de autenticação. O autor da chamada de um web API acrescenta um token no cabeçalho da autorização de um pedido de HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API, em seguida, pode utilizar o token para verificar a identidade a API do autor da chamada e para extrair informações sobre o autor da chamada de afirmações que são codificadas no token de. Saiba mais sobre os tipos de tokens e em afirmações para uma aplicação no [Azure AD B2C token de referência](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD B2C atualmente suporta apenas web APIs que são acedidos pelos seus próprios clientes conhecidos. Por exemplo, a aplicação completa pode incluir uma aplicação do iOS, uma aplicação do Android e um web API do back-end. Esta arquitetura é totalmente suportada. Permitir que um parceiro cliente como noutra aplicação iOS aceder à mesma web que API atualmente não é suportada. Todos os componentes da sua aplicação completo têm de partilhar um ID única aplicação.

Uma web API pode receber tokens de vários tipos de clientes, incluindo aplicações web, ambiente de trabalho e aplicações móveis, única página aplicações, daemons do lado do servidor e outros web APIs. Eis um exemplo do fluxo de concluída para uma aplicação web que liga um web API:

![Imagem do Web App da Web API pistas de diagrama](./media/active-directory-b2c-apps/webapi.png)

Para saber mais sobre códigos de autorização, tokens de atualização e os passos de obtenção tokens, leia sobre o [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como pode proteger um web API através do Azure AD B2C, consulte o artigo da web tutoriais API nosso [Introdução ao secção](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Mobile e apps nativos
As aplicações que são instaladas em dispositivos, como as aplicações de ambiente de trabalho e móveis, muitas vezes necessitam aceder a serviços back-end ou web APIs em nome de utilizadores. Pode adicionar experiências de gestão de identidades personalizada ao seu aplicações nativas e segura chamada serviços de back-end utilizando o Azure AD B2C e o [fluxo de código de autorização de OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

Deste fluxo, a aplicação executa [políticas](active-directory-b2c-reference-policies.md) e recebe uma `authorization_code` a partir do Azure AD depois do utilizador conclui a política. O `authorization_code` representa permissão a aplicação para ligar a serviços de back-end em nome de utilizador que é iniciada. A aplicação, em seguida, pode trocar o `authorization_code` em segundo plano para uma `id_token` e um `refresh_token`.  Pode utilizar a aplicação a `id_token` para autenticar uma back-end da web API nos pedidos HTTP. Pode também utilizar o `refresh_token` para obter um novo `id_token` quando expira um mais antigo.

> [AZURE.NOTE]
    Azure AD B2C atualmente suporta apenas os tokens que são utilizados para aceder a um serviço de back-end web da aplicação. Por exemplo, a aplicação completa pode incluir uma aplicação do iOS, uma aplicação do Android e um web API do back-end. Esta arquitetura é totalmente suportada. Atualmente, o permitindo que a sua aplicação iOS aceder a um web API do parceiro utilizando tokens de acesso OAuth 2.0 não é suportada. Todos os componentes da sua aplicação completo têm de partilhar um ID única aplicação.

![Imagem de pistas de diagrama de aplicação nativa](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitações atuais
Azure AD B2C atualmente não suporta os seguintes tipos de aplicações, mas que estão na roadmapy. Limitações adicionais e restrições relacionadas com a Azure AD B2C estão descritas na [restrições e limitações](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Única página de aplicações (JavaScript)
Muitas aplicações modernas tem uma única página aplicação front-end escrito principalmente em JavaScript. Muitas vezes utilizam um quadro como AngularJS, Ember.js ou Durandal. Ficará disponível o serviço do Azure AD suporta estas aplicações ao utilizar o fluxo de implícito OAuth 2.0. No entanto, este fluxo ainda não está disponível no Azure AD B2C.

### <a name="daemonsserver-side-apps"></a>Aplicações do lado do daemons/servidor
As aplicações que contenham processos de execução longa ou que funcionem sem a presença de um utilizador também precisa de uma forma para aceder a recursos seguros, tais como web APIs. Estas aplicações podem autenticar e obter tokens utilizando identidade a aplicação (em vez de identidade delegada de um utilizador) e ao utilizar o cliente OAuth 2.0 fluxo de credenciais.

Este fluxo atualmente não é suportado pelo Azure AD B2C. Estas aplicações podem obter tokens apenas depois de um fluxo de utilizador interactivo ocorreu.

### <a name="web-api-chains-on-behalf-of-flow"></a>Web API cadeias (em nome de fluxo de)
Muitos arquitecturas incluem uma web API que necessita para ligar a outra descendentes web API, onde ambos estão protegidos por Azure AD B2C. Este cenário é comuns em nativos clientes que têm uma Web API back-end. Isto, em seguida, liga-lhe um serviço online da Microsoft como a API do Azure AD Graph.

Este cenário interligadas web API pode ser suportado utilizando conceder de credenciais de tipo de ligação OAuth 2.0 JWT, também conhecido como o fluxo em nome de.  No entanto, o fluxo em nome de não está atualmente implementado B2C de AD o Azure.
