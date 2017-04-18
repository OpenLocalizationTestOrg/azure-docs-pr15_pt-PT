<properties 
    pageTitle="Como criar APIs na gestão de API do Azure" 
    description="Saiba como criar e configurar APIs no Azure API gestão." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-apis-in-azure-api-management"></a>Como criar APIs na gestão de API do Azure

Uma API na gestão de API representa um conjunto de operações que pode ser chamado por aplicações de cliente. APIs novos são criados no portal do publisher e, em seguida, são adicionadas as operações pretendidas. Assim que as operações forem adicionadas, API é adicionada a um produto e pode ser publicada. Assim que uma API é publicada, pode subscrever e utilizado por programadores.

Este guia mostra o primeiro passo no processo: como criar e configurar uma nova API na gestão de API. Para mais informações sobre como adicionar operações e publicar um produto, consulte o artigo [como adicionar operações para uma API][] e [como criar e publicar um produto][].

## <a name="create-new-api"> </a>Criar uma nova API

APIs são criados e configurados no portal do publisher. Para aceder ao portal do publisher, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Clique **APIs** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Adicionar API**.

![Criar API][api-management-create-api]

Utilize a janela **Adicionar nova API** para configurar a nova API.

![Adicionar a nova API][api-management-add-new-api]

Os campos seguintes são utilizados para configurar a nova API.

-   **Nome da Web API** fornece um nome único e descritivo para a API. É apresentada no portais de programador e publisher.
-   **URL do serviço Web** referencia o serviço HTTP API de execução. Gestão de API reencaminha os pedidos para este endereço.
-   **URL da Web API sufixo** é anexado ao URL de base para o serviço de gestão de API. A base URL é comuns para todos os APIs alojados por uma instância do serviço de gestão de API. Gestão de API distinga APIs pelo respetivo sufixo e, consequentemente, o sufixo deve ser exclusivo para cada API para um determinado fabricante.
-   **Esquema de URL da Web API** determina quais os protocolos que podem ser utilizados para aceder a API. HTTPs é especificado por predefinição.
-   Para adicionar opcionalmente esta nova API para um produto, clique em **produtos (opcionais)** pendente e selecione um produto. Este passo pode ser repetido várias vezes para adicionar a API para vários produtos.

Assim que estiverem configurados os valores pretendidos, clique em **Guardar**. Quando a nova API estiver criada, é apresentada a página de resumo para a API no portal do publisher.

![Resumo API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Definições configurar API

Pode utilizar o separador **Definições** para confirmar e editar a configuração para uma API. **Nome da Web API**, **URL do serviço Web**e **sufixo URL da Web API** são inicialmente definidos quando a API é criada e pode ser modificada aqui. **Descrição** fornece uma descrição opcional e **esquema de URL da Web API** determina quais os protocolos que podem ser utilizados para aceder a API.

![Definições de API][api-management-api-settings]

Para configurar a autenticação de gateway para o serviço de back-end execução API, selecione o separador **segurança** . Menu **com as credenciais** pendente pode ser utilizado para configurar uma autenticação **básica HTTP** ou **certificados de cliente** . Para utilizar autenticação básica HTTP, basta introduza as credenciais pretendidas. Para obter informações sobre como utilizar a autenticação de certificado de cliente, consulte o artigo [como proteger os serviços de back-end utilizando a autenticação de certificado de cliente na gestão de API do Azure][].

No separador **segurança** pode também ser utilizado para configurar a **autorização de utilizador** utilizando OAuth 2.0. Para mais informações, consulte o artigo [como autorizar utilizando OAuth 2.0 no Azure API gestão de contas de programador][].

![Definições de autenticação básica][api-management-api-settings-credentials]

Clique em **Guardar** para guardar as alterações que efetuar para as definições de API.

## <a name="next-steps"> </a>Passos seguintes

Quando é criada uma API e as definições de configuradas, os passos são adicionar as operações à API, adicione a API para um produto e publicá-lo para que fique disponível para programadores. Para obter mais informações, consulte os artigos seguintes.

-   [Como adicionar operações a uma API][]
-   [Como criar e publicar um produto][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Como adicionar operações a uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md

[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Como proteger os serviços de back-end utilizando cliente de autenticação de certificados na gestão de API do Azure]: api-management-howto-mutual-certificates.md
[Como autorizar utilizar OAuth 2.0 no Azure API gestão de contas de programador]: api-management-howto-oauth2.md