<properties 
    pageTitle="Introdução API aplicações | Microsoft Azure" 
    description="Saiba como Azure aplicação de serviço de ajuda a desenvolver sistema anfitrião e consuma RESTful APIs." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/23/2016" 
    ms.author="rachelap"/>

# <a name="api-apps-overview"></a>Descrição geral de API aplicações

Aplicações de API na aplicação de serviço de Azure fornecem funcionalidades que tornam mais fácil de desenvolver sistema anfitrião e consuma APIs na nuvem e no local. API Apps obter segurança grau da empresa, controlo de acesso simples, conectividade híbrido, geração SDK automática e integração simples com [Aplicações de lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md).

[Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md) é uma plataforma totalmente gerida para web, móvel e cenários de integração. API aplicações é uma das quatro tipos de aplicação oferecidos pelo [Serviço de aplicação do Azure](../app-service/app-service-value-prop-what-is.md).

![Tipos de aplicação na aplicação de serviço do Azure](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## <a name="why-use-api-apps"></a>Porquê utilizar API aplicações?

Aqui estão algumas das principais funcionalidades, de API aplicações:

- **Trazer o seu API existente como-é** -não tem de alterar qualquer uma do código no seu APIs existentes para tirar partido das aplicações de API – basta implementar o seu código a uma aplicação do API. A API pode utilizar qualquer idioma ou framework suportados pelo serviço de aplicação, incluindo ASP.NET e c#, Java, PHP, Node.js e Python.

- **Consumo de fácil** - suporte integrado para [metadados Swagger API](http://swagger.io/) faz com que o APIs facilmente consumíveis por uma variedade de clientes.  Gere automaticamente o código do cliente para sua APIs numa variedade de idiomas, incluindo c#, Java e Javascript. Configure facilmente [CORS](app-service-api-cors-consume-javascript.md) sem alterar o seu código. Para mais informações, consulte o artigo [iniciador de aplicações de API de serviço de metadados para geração de deteção e avaliação de código API](app-service-api-metadata.md) e [consumir uma aplicação de API a partir de JavaScript utilizando CORS](app-service-api-cors-consume-javascript.md). 

- **Controlo de acesso simples** - proteger uma aplicação de API a partir do access não autenticados com sem alterações e o seu código. Serviços de autenticação incorporados seguro APIs de acesso através de outros serviços ou ao clientes que representa os utilizadores. Fornecedores de identidade suportados incluem o Azure Active Directory, Facebook, Twitter, Google e Account Microsoft. Os clientes podem utilizar a biblioteca de autenticação do Active Directory (ADAL) ou o SDK de aplicações do telemóvel. Para mais informações, consulte o artigo [autenticação e autorização para aplicações de API na aplicação de serviço de Azure](app-service-api-authentication.md).

- **Integração do visual Studio** - ferramentas dedicadas no Visual Studio simplificar o trabalho criar, implementar, consumir, depuração e gerir aplicações de API. Para mais informações, consulte o artigo [anunciar o SDK do Azure 2.8.1 para .NET](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Integração com aplicações de lógica** - API aplicações que criar pode ser consumida por [Aplicações de lógica de serviço de aplicação](../app-service-logic/app-service-logic-what-are-logic-apps.md).  Para mais informações, consulte o artigo [utilizar a API personalizado alojado no serviço de aplicação com aplicações de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md) e [novo esquema versão 2015-08-01-pré-visualizar](../app-service-logic/app-service-logic-schema-2015-08-01.md).

Além disso, uma aplicação de API pode tirar partido das funcionalidades disponibilizadas pelos [Web Apps](../app-service-web/app-service-web-overview.md) e [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md). O inverso também é verdadeiro: Se utilizar uma aplicação web ou a aplicação móvel para alojar uma API,-pode tirar partido das funcionalidades de API aplicações como metadados Swagger para o cliente geração de código e CORS para o acesso ao browser de domínios. A única diferença entre os tipos de três aplicação (web, móvel, de API) é o nome e o ícone utilizado para os mesmos no portal do Azure.

## <a name="whats-the-difference-between-api-apps-and-azure-api-management"></a>O que é a diferença entre as aplicações de API e gestão de API do Azure?

[Gestão de API do Azure](../api-management/api-management-key-concepts.md) e API Apps são complementares serviços:

* Gestão de API é sobre a gestão de APIs. Colocar um front-end da gestão de API numa API para a utilização do monitor do computador e limitação, manipular entrada e saída, consolidar várias APIs um ponto final e assim sucessivamente. APIs a ser geridas podem ser alojadas em qualquer lugar.
* API aplicações é de cerca de alojamento APIs. O serviço inclui funcionalidades que facilitam a desenvolver e consome APIs, mas não ações os tipos de monitorização, limitação, manipular ou consolidar essa gestão API faz. Se não precisa de funcionalidades de gestão de API, pode alojar APIs nas aplicações de API sem utilizar a API gestão.

Vejamos um diagrama que ilustra de gestão de API utilizada para APIs alojados nas aplicações de API e qualquer outro local.

![Gestão de Azure API e API Apps](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Algumas funcionalidades de gestão de API e API Apps têm funções semelhantes.  Por exemplo, ambos podem automatizar CORS suporte. Quando utilizar os serviços de dois em conjunto, utilizaria de gestão de API para CORS uma vez que funciona como o front-end às suas aplicações de API. 

## <a name="getting-started"></a>Introdução

Para começar a API Apps ao implementar o código de exemplo para uma, consulte o tutorial para independentemente framework preferir:

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [NODE.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Para fazer perguntas sobre aplicações API, comece um tópico no [Fórum de API aplicações](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps). 
