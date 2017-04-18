<properties
    pageTitle="Autenticação e autorização para aplicações de API na aplicação de serviço de Azure | Microsoft Azure"
    description="Saiba mais sobre os serviços de autenticação e autorização que aplicação de serviço de Azure fornece API aplicações."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="rachelap"/>

# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Autenticação e autorização para aplicações de API na aplicação de serviço do Azure

## <a name="overview"></a>Descrição geral 

> [AZURE.NOTE] Este tópico será migrado para consolidada [autenticação do serviço de aplicação / autorização](../app-service/app-service-authentication-overview.md) tópico que abrange Web, Mobile e API Apps.

Aplicação de serviço de Azure oferece serviços de autenticação e autorização incorporados que implementam [OAuth 2.0](#oauth) e [OpenID ligar](#oauth). Este artigo descreve as opções que estão disponíveis para as aplicações de API na aplicação de serviço de Azure e serviços.

O diagrama seguinte ilustra algumas características principais de autenticação de aplicação de serviço:

* -Pré-processa pedidos recebidos de API, que significa que funciona com qualquer idioma ou framework suportados pelo serviço de aplicação.
* Dá-lhe várias opções para trabalhar quanto autenticação pretende fazer no seu próprio código.
* Funciona para o utilizador final e autenticação da conta de serviço. 
* Suporta cinco fornecedores de identidade: Azure Active Directory, Facebook, Google, Twitter e Account Microsoft.
* Funciona da mesma para API aplicações, Web Apps e Mobile Apps.

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>Desconhecido de idioma

Processamento de autenticação de aplicação de serviço acontece antes de pedidos de chegar a sua aplicação API, o que significa que funcionam as funcionalidades de autenticação para as aplicações de API escritas em qualquer idioma ou framework.  A API pode ser baseada no ASP.NET, Java, Node.js ou qualquer quadro que suporta a aplicação de serviço.

Aplicação de serviço transmite num token de web JSON (JWT) no cabeçalho da autorização de um pedido de HTTP e código escrito em qualquer idioma ou framework pode obter as informações que necessita do token de. Além disso, aplicação de serviço de dá-lhe acesso mais fácil para os créditos utilizados com mais frequência definindo algumas cabeçalhos especiais, como as seguintes:

* X-MS-CLIENTE-CAPITAL-NAME
* X-MS-CLIENTE-CAPITAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
API do .NET, pode utilizar o `Authorize` atributo e de autorização extensivamente facilmente pode escrever código com base em afirmações porque informações em afirmações são preenchidas por si em aulas .NET.

## <a name="multiple-protection-options"></a>Várias opções de proteção

Aplicação de serviço pode impedir que os pedidos HTTP anónimos chegar a sua aplicação API, pode passar todos os pedidos e validar os tokens para pedidos incluírem-los ou -pode permitir que o através de todos os pedidos de sem efetuar qualquer uma ação nos mesmos:

1. Permitir apenas autenticados pedidos alcançar a sua aplicação API.

    Se um pedido anónimo é recebido a partir de um browser, o serviço de aplicação irá redirecioná a uma página de início de sessão para o fornecedor de autenticação (Azure AD, Google, Twitter, etc.) que escolher. 

    Com esta opção, não precisa de escrever um código de autenticação de todo na sua aplicação e código de autorização é simplificado porque são fornecidas as afirmações mais importantes nos cabeçalhos de HTTP.

2. Permitir que todos os pedidos chegar a sua aplicação API, mas validar pedidos autenticados e passar ao longo de informações de autenticação nos cabeçalhos de HTTP.

    Esta opção permite-lhe mais flexibilidade no processamento de pedidos anónimos, mas tem de escrever código se pretende impedir que utilizadores anónimos de utilizar a API. Uma vez que as afirmações mais populares são transmitidas nos cabeçalhos de pedidos de HTTP, o código de autorização é relativamente simple.
    
3. Permitir que todos os pedidos para aceder a API, não efetuar nenhuma ação nas informações de autenticação nos pedidos.

    Esta opção mantém as tarefas de autenticação e autorização totalmente por excesso para o seu código da aplicação.

No [portal do Azure](https://portal.azure.com/), selecionar a opção que pretende na **autenticação / autorização** pá.

![](./media/app-service-api-authentication/authblade.png)

Para opções de 1 e 2, ative a **Aplicação do serviço de autenticação**e na lista pendente de **ação a tomar quando não é autenticado pedido** escolha **Iniciar sessão no** ou **Permitir pedidos (nenhuma acção)**.  Se optar por **Iniciar sessão**, tem de escolher um fornecedor de autenticação e configurar esse fornecedor.

![](./media/app-service-api-authentication/actiontotake.png)

Para obter informações detalhadas sobre como configurar a autenticação, consulte o artigo [como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). O artigo aplica-se a aplicações API, bem como as aplicações móveis, e -ligações para outros artigos para os outros fornecedores de autenticação.
 
## <a id="internal"></a>Autenticação da conta de serviço

Cenários internos, como para chamar a partir de uma aplicação de API para outra aplicação API funciona com autenticação de aplicação de serviço. Neste cenário, obter um token utilizando as credenciais para uma conta de serviço em vez de credenciais de utilizador final. Uma conta de serviço também conhecido como é um *serviço principal* no Azure Active Directory e autenticação utilizar essa conta também conhecido como é um cenário de serviço de serviço. 

Cenários de serviço de serviço, proteger a aplicação de API chamada utilizando o Azure Active Directory e fornecer um token de autorização principal de serviço AAD ao ligar para a aplicação de API. Obter um token ao fornecer o cliente de ID e cliente secreta a partir da aplicação AAD. Sem código especial do Azure só é necessário, tais como utilizada para ser VERDADEIRO de acordo com o token de Zumo de serviços móveis. Um exemplo deste cenário utilizar aplicações do ASP.NET API é abrangido pelo tutorial [autenticação principal para as aplicações de API do serviço](app-service-api-dotnet-service-principal-auth.md).

Se pretender gerir um cenário de serviço a serviço sem utilizando a autenticação do serviço de aplicação, pode utilizar certificados de cliente ou de autenticação básica. Para obter informações sobre os certificados de cliente no Azure, consulte o artigo [Como para configurar comum autenticação TLS para Web Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para obter informações sobre a autenticação básica no ASP.NET, consulte o artigo [Filtros de autenticação ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticação da conta serviço a partir de uma aplicação de lógica de aplicação de serviço para uma aplicação de API é um caso especial que é explicado em [utilizar a API personalizado alojado no serviço de aplicação com aplicações de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="mobile-client-authentication"></a>Autenticação do cliente móvel

Para obter informações sobre como gerir autenticação a partir de dispositivos móveis, consulte a [documentação sobre a autenticação para aplicações móveis](../app-service-mobile/app-service-mobile-ios-get-started-users.md). Autenticação da aplicação de serviço funciona da mesma forma para aplicações móveis e API apps.
  
## <a name="more-information"></a>Obter mais informações

Para obter mais informações sobre a autenticação e de autorização de na aplicação de serviço de Azure, consulte os seguintes recursos:

* [Autenticação do serviço de aplicação de expansão / autorização](/blog/announcing-app-service-authentication-authorization/)
* [Como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Inclui ligações para os outros fornecedores de autenticação na parte superior da página). 

Para obter mais informações sobre OAuth 2.0, OpenID ligar e JSON Web Tokens (JWT), consulte os seguintes recursos.

* [Introdução ao token 2.0] (http://shop.oreilly.com/product/0636920021810.do "Introdução ao token 2.0") 
* [Introdução a oauth2 ainda, OpenID ligar e JSON Web Tokens (JWT) - PluralSight curso](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Criar e proteger uma RESTful API para vários clientes no ASP.NET - PluralSight curso](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Para obter mais informações sobre o Azure Active Directory, consulte os seguintes recursos.

* [Cenários do Azure AD](http://aka.ms/aadscenarios)
* [Guia dos Azure AD os programadores](http://aka.ms/aaddev)
* [Exemplos do Azure AD](http://aka.ms/aadsamples)

## <a name="next-steps"></a>Próximos passos

Este artigo explicou funcionalidades de autenticação e autorização do serviço de aplicação que pode utilizar para as aplicações de API. O próximo tutorial na série de introdução ao obter mostra como implementar a [autenticação de utilizador em aplicações de API do serviço de aplicação](app-service-api-dotnet-user-principal-auth.md).
