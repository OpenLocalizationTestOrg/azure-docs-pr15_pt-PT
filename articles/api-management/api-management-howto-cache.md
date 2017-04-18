<properties
    pageTitle="Adicionar a colocação em cache para melhorar o desempenho na gestão de API do Azure | Microsoft Azure"
    description="Saiba como melhorar a latência, carregam o consumo de largura de banda e o serviço web para chamadas de serviço de gestão de API."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar a colocação em cache para melhorar o desempenho na gestão de API do Azure

Operações em gestão de API podem ser configuradas para resposta colocação em cache. Resposta colocação em cache pode significativamente reduzir a latência da API, consumo de largura de banda e web carga de serviço de dados que não forem alterados frequentemente.

Este guia mostra-lhe como adicionar a resposta colocação em cache para sua API e configurar políticas para as operações de eco API de exemplo. Em seguida, pode ligar a operação a partir do portal de programador para verificar a colocação em cache em ação.

>[AZURE.NOTE] Para obter informações sobre colocação em cache dos itens por chave utilizando expressões de política, consulte o artigo [Personalizar colocação em cache na gestão de API do Azure](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir os passos neste guia, tem de ter uma instância do serviço de gestão de API com uma API e configurado um produto. Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

## <a name="configure-caching"> </a>Configurar uma operação de colocação em cache

Neste passo, irá rever as definições de cache da operação de **Obter recursos (em cache)** da amostra API eco.

>[AZURE.NOTE] Cada instância do serviço de gestão de API vem pré-configurado com um eco API que podem ser utilizados para experimentar e saiba mais sobre a gestão de API. Para mais informações, consulte o artigo [Introdução ao Azure API gestão][].

Para começar a, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API.

![Portal do Publisher][api-management-management-console]

Clique **APIs** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **API eco**.

![API eco][api-management-echo-api]

Clique no separador de **operações** e, em seguida, clique em operação do **Recurso obter (em cache)** a partir da lista de **operações** .

![Operações de API eco][api-management-echo-api-operations]

Clique no separador de **Colocação em cache** para ver as definições de colocação em cache para esta operação.

![Separador colocação em cache][api-management-caching-tab]

Para ativar colocação em cache para uma operação, selecione a caixa de verificação **Ativar** . Neste exemplo, a colocação em cache está ativada.

Cada resposta operação é codificada, com base em valores nos campos **variação por parâmetros de cadeia de consulta** e **variação por cabeçalhos** . Se quiser várias respostas com base no parâmetros de cadeia de consulta ou cabeçalhos em cache, pode configurá-los nestes dois campos.

**Duração** Especifica o intervalo de expiração das respostas em cache. Neste exemplo, o intervalo é **3600** segundos, que é equivalente a uma hora.

Utilizar a configuração de colocação em cache neste exemplo, o primeiro pedido para a operação de **Obter recursos (em cache)** devolve uma resposta de serviço back-end. Esta resposta vai ser colocadas em cache, codificada pelos cabeçalhos especificados e parâmetros de cadeia de consulta. As chamadas subsequentes para a operação, com parâmetros correspondentes, terá a resposta em cache devolvida, até que o intervalo de duração da cache expirou.

## <a name="caching-policies"> </a>Reveja as políticas de colocação em cache

Neste passo, reveja as definições de colocação em cache para a operação de **Obter recursos (em cache)** da amostra API eco.

Quando as definições de cache esteja configuradas para uma operação no separador de **Colocação em cache** , políticas de colocação em cache são adicionadas a operação de. Estas políticas possam ser visualizadas e editadas no editor de política.

Clique em **políticas** a partir do menu de **Gestão de API** à esquerda e, em seguida, selecione **eco API / obter o recurso (em cache)** a partir da lista pendente de **operação** .

![Operação de âmbito da política][api-management-operation-dropdown]

Esta ação apresentará as políticas para esta operação no editor de política.

![Editor de políticas de gestão de API][api-management-policy-editor]

A definição de política para esta operação inclui as políticas que definem a configuração de colocação em cache que foram revistas utilizando o separador de **Colocação em cache** no passo anterior.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] As alterações efetuadas para as políticas de colocação em cache no editor de política serão refletidas no separador **Colocação em cache** de uma operação e vice versa.

## <a name="test-operation"> </a>Uma operação de chamadas e testar a colocação em cache

Para ver a colocação em cache em ação, iremos pode ligar a operação a partir do portal de programador. Clique em **Developer portal para** no menu superior direito.

![Portal de programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e, em seguida, selecione **Eco API**.

![API eco][api-management-apis-echo-api]

>Se tiver apenas uma API configurada ou visível à sua conta, em seguida, clicando em APIs leva-o diretamente para as operações para esse API.

Selecione a operação de **Obter recursos (em cache)** e, em seguida, clique em **Consola aberta**.

![Consola aberta][api-management-open-console]

Consola do permite-lhe invocar operações diretamente a partir do portal de programador.

![Consola][api-management-console]

Manter os valores predefinidos para **parâmetro1** e **param2**.

Selecione a chave pretendida a partir da lista pendente de **chave de subscrição** . Se a sua conta tiver apenas uma subscrição, já de ser selecionada.

Introduza **sampleheader:value1** na caixa de texto **do pedido cabeçalhos** .

Clique em **HTTP Get** e tome nota dos cabeçalhos de resposta.

Introduza **sampleheader:value2** na caixa de texto **do pedido cabeçalhos** e, em seguida, clique em **HTTP Get**.

Note que o valor de **sampleheader** ainda é **valor1** na resposta. Experimente alguns valores de diferentes e tenha em atenção que a resposta em cache da primeira chamada é devolvida.

Introduza **25** para o campo **param2** e, em seguida, clique em **HTTP Get**.

Note que o valor de **sampleheader** na resposta é agora **valor2**. Visto que os resultados de operação são codificados pela cadeia de consulta, a resposta em cache anterior não foi devolvida.

## <a name="next-steps"> </a>Passos seguintes

-   Para mais informações sobre as políticas de colocação em cache, consulte o artigo [políticas de colocação em cache][] na [referência da política de gestão de API][].
-   Para obter informações sobre colocação em cache dos itens por chave utilizando expressões de política, consulte o artigo [Personalizar colocação em cache na gestão de API do Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao Azure API gestão]: api-management-get-started.md

[Referência da política de gestão de API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Políticas de colocação em cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
