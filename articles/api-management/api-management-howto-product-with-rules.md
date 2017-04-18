<properties
    pageTitle="Proteger a sua API com a gestão de Azure API | Microsoft Azure"
    description="Saiba como pode proteger o seu API com quotas e limitação políticas (limitar taxa)."
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

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteger o seu API com limites taxa utilizando a gestão de API do Azure

Este guia mostra-lhe como é fácil adicionar protecção para sua back-end API ao configurar as políticas de limite e quota da taxa com a gestão de API do Azure.

Neste tutorial, irá criar um produto "Versão de avaliação gratuita" API que permite que os programadores efetuar chamadas até 10 por minuto e até um máximo de 200 chamadas por semana a sua API utilizando a [taxa de chamada de limite por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e políticas de [quota de utilização do conjunto de por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) . Em seguida, será publicar API e testar a política de limite taxa.

Para mais avançados cenários optimização utilizando as políticas que foram [taxa limite por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , consulte o artigo [pedido avançado limitação com a gestão de API do Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Para criar um produto

Neste passo, irá criar um produto da versão de avaliação gratuita que não exigir a aprovação de subscrição.

>[AZURE.NOTE] Se já tiver um produto configurado e pretender utilizá-lo para este tutorial, pode avançar para a [taxa de chamadas configurar políticas de limite e quota][] e seguir o tutorial a partir desse local com o seu produto em vez do produto de versão de avaliação gratuita.

Para começar a, clique em **Gerir** no Azure clássica do seu serviço de gestão de API. Isto leva-o para o portal do publisher de gestão de API.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Gerir o seu primeiro API na gestão de API do Azure][] .

Clique em **produtos** no menu de **Gestão de API** à esquerda para apresentar a página de **produtos** .

![Adicionar produto][api-management-add-product]

Clique em **Adicionar produto** para apresentar a caixa de diálogo **Adicionar novo produto** .

![Adicionar novo produto][api-management-new-product-window]

Na caixa **título** , escreva a **Versão de avaliação gratuita**.

Na caixa **Descrição** , escreva o seguinte texto:  **subscritores poderão executar 10 chamadas por minuto até um máximo de 200 de chamadas/semana após o qual acesso negado.**

Os produtos da gestão de API podem estar protegidos ou abrirem. Tem de subscrever produtos protegidos antes de poderem ser utilizados. Podem ser utilizados produtos abertos sem uma subscrição. Certifique-se de que **necessitam de subscrição** está selecionada para criar um produto protegido que requer uma subscrição. Esta é a predefinição.

Se pretender que um administrador para rever e aceitar ou rejeitar tentativas de subscrição para este produto, selecione **exigir a aprovação de subscrição**. Se a caixa de verificação não estiver selecionada, tentativas de subscrição será aprovado automática. Neste exemplo, subscrições são automaticamente aprovadas, para que não selecione a caixa.

Para permitir que as contas de programador subscrever várias vezes para o novo produto, selecione a caixa de verificação **Permitir múltiplas subscrições simultâneas** . Neste tutorial não utilizam múltiplas subscrições simultâneas, por isso, deixe desmarcada.

Depois de todos os valores são introduzidos, clique em **Guardar** para criar o produto.

![Produto adicionado][api-management-product-added]

Por predefinição, os novos produtos estão visíveis para os utilizadores do grupo de **administradores** . Vamos para adicionar o grupo de **programadores** . Clique em **Versão de avaliação gratuita**e, em seguida, clique no separador **visibilidade** .

>Em gestão de API, os grupos são utilizados para gerir a visibilidade de produtos para os programadores. Produtos conceder visibilidade a grupos e os programadores podem ver e subscrever os produtos que são visíveis para os grupos na qual pertence. Para mais informações, consulte o artigo [como criar e utilizar grupos no Azure API gestão][].

![Adicionar os programadores grupo][api-management-add-developers-group]

Selecione a caixa de verificação **os programadores** e, em seguida, clique em **Guardar**.

## <a name="add-api"> </a>Para adicionar uma API para o produto

Neste passo do tutorial, vamos adicionar API eco para o novo produto de versão de avaliação gratuita.

>Cada instância do serviço de gestão de API vem com uma API eco que podem ser utilizados para experimentar e saiba mais sobre a gestão de API pré-configurada. Para mais informações, consulte o artigo [Gerir o primeiro API na gestão de API do Azure][].

Clique em **produtos** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Versão de avaliação gratuita** para configurar o produto.

![Configurar o produto][api-management-configure-product]

Clique em **Adicionar API do produto**.

![Adicionar API do produto][api-management-add-api]

Selecione **Eco API**e, em seguida, clique em **Guardar**.

![Adicionar API eco][api-management-add-echo-api]

## <a name="policies"> </a>Para configurar políticas de limite e quota de taxa de chamada

Os limites de taxas e as quotas de estão configuradas no editor de política. Clique em **políticas** no menu de **Gestão de API** à esquerda. Na lista de **produtos** , clique em **Versão de avaliação gratuita**.

![Política de produto][api-management-product-policy]

Clique em **Adicionar política** para importar o modelo de política e começar a criar a taxa de políticas de limite e quota.

![Adicionar política][api-management-add-policy]

Para inserir políticas, posicione o cursor numa secção a **entrada** ou **saída** do modelo de política. Políticas de limite e quota da taxa são políticas de entrada, por isso, posicione o cursor no elemento de entrada.

![Editor de políticas][api-management-policy-editor-inbound]

As duas políticas que está a adicionar neste tutorial são as políticas de [taxas de chamada de limite por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir quota de utilização por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Declarações de política][api-management-limit-policies]

Depois do cursor é posicionado no elemento de política de **entrada** , clique na seta ao lado de **taxas de chamada de limite por subscrição** para inserir o seu modelo de política.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Taxa de chamada de limite por subscrição** podem ser utilizados ao nível do produto e podem também ser utilizados no API e níveis de nome de operação individuais. Neste tutorial, são utilizadas apenas as políticas de nível do produto, por isso, elimina os elementos **api** e **operação** do elemento de **limite da taxa** , por isso, apenas o externo **limite da taxa** elemento mantém-se, conforme mostrado no seguinte exemplo.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Do produto da versão de avaliação gratuita, a taxa de chamada permitido máximo é 10 chamadas por minuto, por isso, escreva **10** como o valor do atributo de **chamadas** e **60** para o atributo **período de renovação** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Para configurar a política de **Definir quota de utilização por subscrição** , posicione o cursor imediatamente abaixo do elemento de recentemente adicionado **limite da taxa** dentro do elemento **entrada** e, em seguida, clique na seta para a esquerda do **conjunto de quota de utilização por subscrição**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Uma vez que esta política também se destina a ser ao nível do produto, elimine os elementos de nome **api** e **funcionamento** , conforme mostrado no seguinte exemplo.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

As quotas de podem ser com base no número de chamadas por intervalo, largura de banda ou ambas. Neste tutorial, vamos não são optimização da largura de banda com base no, por isso, elimina o atributo de **largura de banda** .

    <quota calls="number" renewal-period="seconds">
    </quota>

Do produto da versão de avaliação gratuita, a quota é 200 chamadas por semana. Especificar **200** como o valor do atributo de **chamadas** e, em seguida, especifique **604800** como o valor do atributo **período de renovação** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalos de política estão especificados em segundos. Para calcular o intervalo para uma semana, pode multiplicar o número de dias (7) pelo número de horas por dia (24) pelo número de minutos numa hora (60) pelo número de segundos dentro de um minuto (60): 7 *24* 60 * 60 = 604800.

Quando terminar de configurar a política, deverá corresponder ao exemplo seguinte.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Depois das políticas pretendidas estão configuradas, clique em **Guardar**.

![Guardar a política][api-management-policy-save]

## <a name="publish-product"></a> Para publicar o produto

Agora que a API do são adicionadas e as políticas estão configuradas, tem de ser publicado o produto para que possa ser utilizado por programadores. Clique em **produtos** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Versão de avaliação gratuita** para configurar o produto.

![Configurar o produto][api-management-configure-product]

Clique em **Publicar**e, em seguida, clique em **Sim, publicá-la** para confirmar.

![Publicar produto][api-management-publish-product]

## <a name="subscribe-account"> </a>Para subscrever uma conta do programador para o produto

Agora que o produto é publicado, este fica disponível para ser subscrever e utilizado por programadores.

>Os administradores de uma instância de gestão de API subscrito automaticamente todos os produtos. Neste tutorial passo, podemos irá subscrever uma das contas de programador não administrador para o produto de versão de avaliação gratuita. Se a sua conta de programador for parte da função de administradores, em seguida, pode seguir juntamente com neste passo, apesar de já tiver subscrito.

Clique em **utilizadores** no menu de **Gestão de API** à esquerda e, em seguida, clique no nome da sua conta de programador. Neste exemplo, estamos a utilizar a conta de programador **Nogueira Gragg** .

![Configurar para programadores][api-management-configure-developer]

Clique em **Adicionar a subscrição**.

![Adicionar subscrição][api-management-add-subscription-menu]

Selecione a **Versão de avaliação gratuita**e, em seguida, clique em **subscrever**.

![Adicionar subscrição][api-management-add-subscription]

>[AZURE.NOTE] Neste tutorial, múltiplas subscrições simultâneas não se encontram ativadas para o produto de versão de avaliação gratuita. Se estivessem, deverá ser-lhe para atribuir um nome da subscrição, conforme mostrado no seguinte exemplo.

![Adicionar subscrição][api-management-add-subscription-multiple]

Depois de clicar em **subscrever**, o produto é apresentada na lista de **subscrição** para o utilizador.

![Subscrição adicionada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Para ligar para uma operação e testar o limite da taxa

Agora que o produto de versão de avaliação gratuita está configurado e publicado, que possamos algumas operações de chamadas e testar a política de limite taxa.
Mudar para o portal do programador ao clicar em **Developer portal para** no menu do canto superior direito.

![Portal de programador][api-management-developer-portal-menu]

Clique **APIs** no menu superior e, em seguida, clique em **API eco**.

![Portal de programador][api-management-developer-portal-api-menu]

Clique em **Obter recurso**e, em seguida, clique em **experimentar**.

![Consola aberta][api-management-open-console]

Manter a predefinição valores de parâmetros e, em seguida, selecione a chave de subscrição para o produto de versão de avaliação gratuita.

![Chave de subscrição][api-management-select-key]

>[AZURE.NOTE] Se tiver múltiplas subscrições, certifique-se selecionar a chave para a **Versão de avaliação gratuita**, ou, caso contrário, as políticas que foram configuradas nos passos anteriores não serão em vigor.

Clique em **Enviar**e, em seguida, visualize a resposta. Tenha em atenção o **Estado da resposta** dos **200 OK**.

![Resultados da operação][api-management-http-get-results]

Clique em **Enviar** taxa de juro maior do que a política de limite de taxa de 10 chamadas por minuto. Depois da política de limite taxa for excedida, é devolvido um Estado de resposta de **429 demasiado muitos pedidos** .

![Resultados da operação][api-management-http-get-429]

O **conteúdo da resposta** indica o intervalo de restante antes do número de tentativas será bem sucedido.

Quando a política de limite de taxa de 10 chamadas por minuto é de facto, as chamadas subsequentes efetuadas irão falhar até 60 segundos decorridos do primeiro das chamadas com êxito 10 para o produto antes do limite da taxa foi excedido. Neste exemplo, o intervalo restante é 54 segundos.

## <a name="next-steps"> </a>Passos seguintes

-   Ver uma demonstração da definição de limites de taxas e quotas no vídeo seguinte.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Gerir a sua primeira API na gestão de API do Azure]: api-management-get-started.md
[Como criar e utilizar grupos no Azure API gestão]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurar políticas de limite e quota de taxa de chamada]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
