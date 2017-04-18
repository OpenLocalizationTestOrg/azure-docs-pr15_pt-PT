<properties 
    pageTitle="Como personalizar o developer portal de gestão de API do Azure utilizar modelos | Microsoft Azure" 
    description="Saiba como personalizar o developer portal de gestão de API do Azure utilizar modelos." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o developer portal de gestão de API do Azure utilizar modelos

Gestão de API Azure fornece várias funcionalidades de personalização para permitir que os administradores para [Personalizar o aspeto e funcionalidade do portal de programador](api-management-customize-portal.md), bem como personalizar o conteúdo das páginas developer portal utilizando um conjunto de modelos de configurar o conteúdo das páginas próprios. Utilizar [DotLiquid](http://dotliquidmarkup.org/) sintaxe e um conjunto de recursos de cadeia localizados, ícones e controlos da página fornecido, tem flexibilidade ótima para configurar o conteúdo das páginas conforme pretender utilizar estes modelos.

## <a name="developer-portal-templates-overview"></a>Descrição geral dos modelos portal de programador

Modelos de portais do programador são geridos no portal do programador por administradores da instância do serviço de gestão de API. Para gerir modelos de programador, navegue para a instância do serviço de gestão de API no Portal clássica do Azure e clique em **Procurar**.

![Portal de programador][api-management-browse]

Se já estiver no portal do publisher, pode aceder ao portal de programador ao clicar em **Developer portal para**.

![Menu de portal de programador][api-management-developer-portal-menu]

Para aceder a modelos de portal de programador, clique no ícone de personalizar no lado esquerdo para apresentar o menu de personalização e clique em **modelos**.

![Modelos de portais de programador][api-management-customize-menu]

A lista de modelos apresenta várias categorias de modelos que abrangem as páginas diferentes no portal do programador. Cada modelo é diferente, mas os passos para editá-los e publicar as alterações são os mesmos. Para editar um modelo, clique no nome do modelo.

![Modelos de portais de programador][api-management-templates-menu]

Clicar num modelo leva-o para a página de portal de programador é personalizável nesse modelo. Neste exemplo, a **lista de produtos** é apresentado o modelo. O modelo de **lista de produtos** controla a área do ecrã indicado pelo retângulo vermelho. 

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como os modelos de **Perfil de utilizador** , personalizar diferentes partes da mesma página. 

![Modelos de perfil de utilizador][api-management-user-profile-templates]

O editor para cada modelo do portal de programador tem duas secções apresentadas na parte inferior da página. No lado esquerdo apresenta o painel de edição para o modelo e, no lado direito apresenta o modelo de dados para o modelo. 

O modelo de painel de edição contém a marcação que controla o aspeto e comportamento da página correspondente no portal do programador. A marcação no modelo utiliza a sintaxe de [DotLiquid](http://dotliquidmarkup.org/) . Um editor popular para DotLiquid é [DotLiquid para designers de sites](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). São apresentadas todas as alterações ao modelo durante a edição em tempo real no browser, mas não são visíveis para os seus clientes até [Guardar](#to-save-a-template) e [Publicar](#to-publish-a-template) o modelo.

![Markup de modelo][api-management-template]

O painel de **dados do modelo** fornece um guia para o modelo de dados para as entidades que estão disponíveis para utilização num modelo específico. Fornece este guia ao apresentar os dados dinâmicos atualmente apresentados no portal do programador. Pode expandir os painéis do modelo ao clicar no retângulo no canto superior direito do painel de **dados do modelo** .

![Modelo de dados de modelo][api-management-template-data]

No exemplo anterior, existem dois produtos apresentados no portal do programador que foram obtidos a partir dos dados apresentados no painel de **dados do modelo** , conforme mostrado no seguinte exemplo.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

A marcação no modelo de **lista de produtos** processa os dados para fornecer o resultado pretendido por iteração coleção de produtos para apresentar informações e uma ligação para cada produto individual. Nota a `<search-control>` e `<page-control>` elementos na marcação. Estes controlam a apresentação do procurar e paginação controlos na página. `ProductsStrings|PageTitleProducts`for uma referência de localizados cadeia que contém o `h2` texto do cabeçalho da página. Para obter uma lista de cadeia de recursos, controlos de página e ícones disponíveis para utilização nos modelos portal de programador, consulte [referência de modelos portal gestão de API para programadores](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Para guardar um modelo

Para guardar um modelo, clique em Guardar no editor de modelo.

![Guardar modelo][api-management-save-template]

Alterações guardadas não estão em direto no portal do programador até serem publicados.

## <a name="to-publish-a-template"></a>Para publicar um modelo

Podem ser publicados modelos guardados individualmente, ou em todos os conjunto. Para publicar um modelo de individual, clique em publicar no editor de modelo.

![Publicar o modelo][api-management-publish-template]

Clique em **Sim** para confirmar e tornar o modelo direto no portal do programador.

![Confirmar publicar][api-management-publish-template-confirm]

Para publicar todas as versões de modelo atualmente não publicados, clique em **Publicar** na lista de modelos. Não publicados modelos são designados por um asterisco a seguir ao nome do modelo. Neste exemplo, os modelos de **lista de produtos** e **produtos** estão a ser publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **publicar personalizações** para confirmar.

![Confirmar publicar][api-management-publish-customizations]

Modelos recentemente publicados são eficazes imediatamente no portal do programador.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior

Para reverter um modelo para a versão publicada anterior, clique em reverter no editor de modelo.

![Reverter o modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão publicada anteriormente de um modelo é direto no portal do programador uma vez concluída a operação de reverter.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão predefinida

Restaurar modelos para a sua versão predefinida é um processo de dois passos. Primeiro terá de ser restaurados os modelos e, em seguida, tem de ser publicadas as versões restauradas.

Para restaurar um único modelo para a versão predefinida, clique em restaurar no editor de modelo.

![Reverter o modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos para as versões predefinido, clique em **Restaurar modelos predefinidos** na lista modelo.

![Restaurar modelos][api-management-restore-templates]

Os modelos restaurados devem, em seguida, ser publicados individualmente ou ao mesmo tempo ao seguir os passos [para publicar um modelo](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Referência para programadores do portal de modelos

Para obter informações de referência para programadores portais modelos, recursos de cadeia, ícones e controlos de página, consulte o artigo [referência de modelos portal gestão de API para programadores](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Ver uma descrição geral em vídeo

Veja o vídeo seguinte para ver como adicionar uma área de debate e classificações às páginas API e o funcionamento no portal do programador utilizar modelos.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







