<properties 
    pageTitle="Como criar e publicar um produto na gestão de API do Azure" 
    description="Saiba como criar e publicar produtos na gestão de API do Azure." 
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

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Como criar e publicar um produto na gestão de API do Azure

Em gestão de API do Azure, um produto contém um ou mais APIs, bem como uma quota de utilização e os termos de utilização. Assim que um produto é publicado, os programadores podem subscrever o produto e começar a utilizar APIs o produto. O tópico fornece um guia para a criação de um produto, adicionar uma API e publicá-lo para programadores.

## <a name="create-product"> </a>Criar um produto

Operações são adicionadas e configuradas para uma API no portal do publisher. Para aceder ao portal do publisher, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Clique em sobre os **produtos** no menu à esquerda para apresentar a página de **produtos** e clique em **Adicionar produto**.

![Produtos][api-management-products]

![Novo produto][api-management-add-new-product]

Introduza um nome descritivo para o produto no campo **nome** e uma descrição do produto no campo **Descrição** .

Os produtos da gestão de API podem ser **aberto** ou **protegido**. Produtos protegidos tem de subscrever antes de poderem ser utilizados, enquanto este estiver aberto podem ser utilizados produtos sem uma subscrição. Selecione **exigir subscrição** para criar um produto protegido que requer uma subscrição. Esta é a predefinição.

Verificar o **exigir a aprovação de subscrição** , se pretender que um administrador para rever e aceitar ou rejeitar tentativas de subscrição para este produto. Se a caixa estiver desmarcada, tentativas de subscrição será aprovado automática. Para mais informações sobre as subscrições, consulte o artigo [subscritores de vista para um produto][].

Para permitir que as contas de programador subscrever várias vezes o produto, selecione a caixa de verificação **Permitir múltiplas subscrições** . Se esta caixa não estiver selecionada, cada conta programador pode subscrever uma única vez para o produto.

![Ilimitado múltiplas subscrições][api-management-unlimited-multiple-subscriptions]

Para limitar a contagem de múltiplas subscrições simultâneas, selecione a caixa de verificação **limitar o número de subscrições do toque** e introduza o limite de subscrição. No exemplo seguinte, subscrições simultâneas estão limitadas a quatro por conta de programador.

![Quatro múltiplas subscrições][api-management-four-multiple-subscriptions]

Assim que estiverem configuradas todas as novas opções de produto, clique em **Guardar** para criar o novo produto.

![Produtos][api-management-products-page]

>Por predefinição novos produtos estão não publicados e são visíveis apenas para o grupo de **administradores** .

Para configurar um produto, clique no nome do produto, no separador **produtos** .

## <a name="add-apis"> </a>Adicionar APIs para um produto

A página de **produtos** contém quatro ligações para a configuração do: **Resumo**, **Definições**, **visibilidade**e **subscritores**. No separador **Resumo** é onde pode adicionar APIs e publicar ou anular a publicação de um produto.

![Resumo][api-management-new-product-summary]

Antes de publicar o seu produto tem de adicionar um ou mais APIs. Para executar esta tarefa, clique em **Adicionar API do produto**.

![Adicionar APIs][api-management-add-apis-to-product]

Selecione as APIs pretendidas e clique em **Guardar**.

## <a name="add-description"> </a>Adicione informações descritivas para um produto

No separador **Definições** permite-lhe fornecem informações detalhadas sobre o produto, tal como a sua finalidade, APIs fornece acesso a e outras informações úteis. O conteúdo é direccionado para os programadores de que vai estar a chamar a API e podem ser escritos em texto simples ou formato HTML.

![Definições de produto][api-management-product-settings]

Selecione **exigir subscrição** para criar um produto protegido que requer uma subscrição para ser utilizado ou, desmarque a caixa de verificação para criar um produto abrir que pode ser chamado sem uma subscrição.

Selecione **exigir a aprovação de subscrição** , se pretender aprovar manualmente todos os pedidos de subscrição do produto. Por predefinição, todas as subscrições do produto são concedidas automaticamente.

Para permitir que as contas de programador subscrever várias vezes o produto, selecione a caixa de verificação **Permitir múltiplas subscrições** e, opcionalmente, especifique um limite. Se esta caixa não estiver selecionada, cada conta programador pode subscrever uma única vez para o produto.

Opcionalmente, preencha o campo de **termos de utilização** que descrevem os termos de utilização para o produto que subscritores tem de aceitar para poder utilizar o produto.

## <a name="publish-product"> </a>Publicar um produto

Antes das APIs num produto podem ser chamadas, tem de ser publicado o produto. No separador **Resumo** para o produto, clique em **Publicar**e, em seguida, clique em **Sim, publicá-la** para confirmar. Para tornar um produto publicado anteriormente privado, clique em **Anular publicação**.

![Publicar produto][api-management-publish-product]

## <a name="make-visible"> </a>Tornar um produto visível para os programadores

No separador **visibilidade** permite-lhe escolher quais as funções conseguem ver os produtos no portal do programador e subscrever o produto.

![Visibilidade do produto][api-management-product-visiblity]

Para ativar ou desativar a visibilidade de um produto para programadores num grupo, selecione ou desmarque a caixa de verificação junto ao grupo e, em seguida, clique em **Guardar**.

>Para mais informações, consulte o artigo [como criar e utilizar grupos para gerir as contas de programador na gestão de API do Azure][].

## <a name="view-subscribers"> </a>Ver subscritores para um produto

O separador **subscritores** apresenta os programadores de subscrever o produto. Os detalhes e definições para cada programador podem ser visualizadas clicando no nome do programador. Neste exemplo os não programadores ainda se subscreveu o produto.

![Programadores][api-management-developer-list]

## <a name="next-steps"> </a>Passos seguintes

Quando são adicionadas as APIs pretendidas e o produto publicado, os programadores podem subscrever o produto e comece a ligar as APIs. Para obter um tutorial demonstra que estes itens, bem como a configuração do produto advanced ver [como criar e configurar definições avançadas de produto na gestão de API do Azure][].

Para obter mais informações sobre como trabalhar com os produtos, consulte o vídeo seguinte.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Subscritores de vista para um produto]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Como criar e utilizar grupos para gerir contas de programador na gestão de API do Azure]: api-management-howto-create-groups.md
[Como criar e configurar definições avançadas de produto na gestão de API do Azure]: api-management-howto-product-with-rules.md 