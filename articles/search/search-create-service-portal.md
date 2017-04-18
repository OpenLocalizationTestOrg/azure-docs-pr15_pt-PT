<properties
    pageTitle="Criar um serviço de pesquisa do Azure utilizando o Portal do Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Saiba como pode aprovisionar um serviço de pesquisa do Azure utilizando o Portal do Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Criar um serviço de pesquisa do Azure utilizando o Portal do Azure

Este guia irá guiá-lo durante o processo de criação (ou aprovisionamento) num serviço de pesquisa do Azure utilizando o [Portal do Azure](https://portal.azure.com/).

Este guia assume que já tem uma subscrição do Azure e pode iniciar sessão no Portal do Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Localizar o Azure pesquisa no Portal do Azure
1. Aceda ao [Portal do Azure](https://portal.azure.com/) e iniciar sessão no.
1. Clique no sinal de adição ("+") no canto superior esquerdo.
2. Selecione os **dados + armazenamento**.
3. Selecione **Azure de pesquisa**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Selecione um nome de serviço e o ponto final do URL do seu serviço
1. O nome do serviço farão parte do URL de ponto final do seu serviço de pesquisa do Azure contra a qual vai fazer chamadas API para gerir e utilizar o serviço de pesquisa.
2. Escreva o seu nome de serviço no campo **URL** . O nome do serviço:
  * só tem de conter letras em minúsculas, dígitos ou traços ("-")
  * Não é possível utilizar um travessão ("-") como o primeiro 2 caracteres ou o último caráter individual
  * não pode conter hífenes consecutivos ("–")
  * é limitado entre 2 e 60 carateres de comprimento


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Selecione uma subscrição onde irão manter o seu serviço
Se tiver mais do que uma subscrição, pode selecionar qual irá incluir este serviço de pesquisa do Azure.

## <a name="select-a-resource-group-for-your-service"></a>Selecione um grupo de recursos do seu serviço
Criar um novo grupo de recursos ou selecione uma existente. Um grupo de recursos é uma coleção de serviços Azure e recursos que são utilizados em conjunto. Por exemplo, se estiver a utilizar o Azure pesquisa para indexar uma base de dados do SQL, em seguida, ambos os serviços deverá parte do mesmo grupo de recursos.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Selecione a localização onde o seu serviço será alojado
Como um serviço Azure, Azure pesquisa está disponível para ser alojados em centros de dados em todo o mundo. Tenha em atenção que [podem ser os preços](https://azure.microsoft.com/pricing/details/search/) por geográficos.

## <a name="select-your-pricing-tier"></a>Selecione a camada comparar
[Pesquisa do Azure previewhttp em várias camadas comparar](https://azure.microsoft.com/pricing/details/search/): livre, Basic ou padrão. Cada camada tem as suas próprias [capacidade e os limites](search-limits-quotas-capacity.md). Consulte o artigo [Escolher uma camada ou SKU comparar](search-sku-tier.md) para obter orientações.

Neste caso, podemos escolheu a camada padrão para o nosso serviço.

## <a name="select-the-create-button-to-provision-your-service"></a>Selecione o botão "Criar" para aprovisionar o seu serviço

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Dimensionar o seu serviço

Depois do serviço está aprovisionado, poderá dimensioná-o para corresponder às suas necessidades. Se tiver escolhido a camada padrão do seu serviço de pesquisa do Azure, pode dimensionar o seu serviço em duas dimensões: réplicas e a partições. Se tiver escolhido a camada básica, só pode adicionar réplicas.

Permitir a *__partições__* seu serviço armazenar e pesquisar através de mais documentos.

*__Réplicas__* permitir que o seu serviço processar uma carga de consultas de pesquisa - [um serviço requer 2 réplicas para alcançar um SLA só de leitura e requer 3 réplicas para alcançar um SLA de leitura/escrita](https://azure.microsoft.com/support/legal/sla/search/v1_0/)superior.

1. Aceda a pá de gestão do seu serviço de pesquisa do Azure no Portal do Azure.
2. Na pá **Definições** , selecione **escala**.
3. Pode dimensionar o seu serviço adicionando réplicas ou a partições.
  * Não consigo dimensionar o seu serviço anteriores pesquisa 36 unidades. O número total de unidades de pesquisa é o produto dos seus réplicas e a partições (réplicas * partições = Total unidades de pesquisa).
  * Se tiver escolhido a camada básica, apenas pode dimensionar para 3 réplicas. Serviços básicos estão vinculados para uma única partição.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Seguinte
Depois de aprovisionar um serviço de pesquisa do Azure, estará pronto para [definir um índice de pesquisa do Azure](search-what-is-an-index.md) para que possa carregar e procurar os seus dados.

Consulte o artigo [Introdução ao Azure pesquisa no portal](search-get-started-portal.md) para obter uma iniciação rápida.
