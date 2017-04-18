<properties
    pageTitle="Como implementar uma instância do serviço de gestão do Azure API para várias regiões Azure"
    description="Saiba como implementar uma instância do serviço de gestão do Azure API para regiões Azure múltiplos." 
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

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implementar uma instância do serviço de gestão do Azure API para várias regiões Azure

Gestão de API suporta a implementação de região com várias que permite aos fabricantes de API distribuir um serviço de gestão de API única por qualquer número de regiões Azure pretendidas. Isto ajuda a reduzir o pedido de latência cobrada pelos distribuídos geograficamente consumidores API e também melhora a disponibilidade do serviço, se uma região ficar offline. 

Quando um serviço de gestão de API é criado inicialmente, contém apenas uma [unidade][] e encontra-se numa única região Azure, que está designada como a região principal. Regiões adicionais podem ser adicionados facilmente através do Portal clássica Azure. Servidor de gateway de gestão de API é implementada para cada região e tráfego de chamada vai ser encaminhado para o gateway mais próximo. Se uma região ficar offline, o tráfego é automaticamente novamente direccionado para o próximo gateway mais próximo. 

> [AZURE.IMPORTANT] Implementação da região com várias só está disponível na camada **[Premium][]** .

## <a name="add-region"> </a>Implementar uma instância do serviço de gestão de API para uma nova região

Para começar a, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Navegue para o separador de **escala** no Azure clássica Portal para a instância do serviço de gestão de API. 

![Separador Escala][api-management-scale-service]

Para implementar uma nova região, clique na lista pendente abaixo da região principal e selecione uma região a partir da lista.

![Adicionar região][api-management-add-region]

Assim que a região estiver selecionada, escolha o número de unidades região novo a partir da lista pendente de unidade.

![Especifique as unidades][api-management-select-units]

Assim que as regiões pretendidas e as unidades estiverem configuradas, clique em **Guardar**.

## <a name="remove-region"> </a>Eliminar uma instância do serviço de gestão de API a partir de uma região

Para remover uma instância do serviço de gestão de API de uma região, navegue para o separador de **escala** no Azure clássica Portal para a instância do serviço de gestão de API. 

![Separador Escala][api-management-scale-service]

Clique no **X** à direita da região pretendida para a remover.  

![Remover região][api-management-remove-region]

Assim que são removidas as regiões pretendidas, clique em **Guardar**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Introdução ao Azure API gestão]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unidade]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

