<properties 
   pageTitle="Monitorizar a utilização e estatísticas de um serviço de pesquisa do Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem" 
   description="Registar o tamanho do recurso consumo e índice remissivo para pesquisa Azure, um serviço de pesquisa na nuvem alojado no Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Controle a utilização e estatísticas de um serviço de pesquisa do Azure

Controlar o crescimento de índices e tamanho do documento pode ajudá-lo importante ajustar a capacidade de antes de atingir o limite superior que já foram estabelecidas do seu serviço. 

Para monitorizar a utilização de recursos, contagens e estatísticas do seu serviço de facilmente são visualizadas no [Portal do Azure](https://portal.azure.com), mas também pode obter as informações através de programação se estiver a criar uma ferramenta de administração do serviço personalizado. Este artigo abrange os passos para ambas as técnicas.

Também pode utilizar a nova funcionalidade de análise de tráfego de pesquisa para informações para atividade ao nível do índice. Visite [A análise de tráfego de pesquisa para pesquisa Azure](search-traffic-analytics.md) para começar a utilizar.

##<a name="view-counts-and-metrics-in-the-portal"></a>Ver as contagens e métricas no portal 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Abra o dashboard de serviço do seu serviço de pesquisa do Azure. Os mosaicos para o serviço podem ser encontrados na Home page ou, pode navegar para o serviço de procurar na JumpBar. Consulte o artigo [criar um serviço](search-create-service-portal.md) para obter instruções passo a passo.

A secção de utilização inclui um indicador que mostra-lhe qual a parte dos recursos disponíveis estão atualmente em utilização.

  ![][1]

Recuperar a que o serviço partilhado tem um máximo de uma réplica e partição cada um. Para além disso, só pode suportar 10.000 documentos em total ou de 50 MB de dados, que vier primeiro.

##<a name="get-index-statistics-using-the-rest-api"></a>Obter as estatísticas de índice remissivo utilizar a API REST

A pesquisa Azure REST API e o .NET SDK fornecem acesso de programação ao métricas de serviço.  Se estiver a utilizar [Os indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para carregar um índice remissivo a partir de base de dados do SQL Azure ou DocumentDB, uma API adicional está disponível para obter os números que necessita. 

  + [Obter estatísticas de índice remissivo](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Contagem de documentos](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obter o estado do indexador](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Próximos passos

Reveja os [limites e a capacidade](search-limits-quotas-capacity.md) para determinar a combinação de partições e réplicas que terá de se forem suficiente capacidade existente. 

Aceda a [Gerir o serviço de pesquisa no Microsoft Azure](search-manage.md) para obter mais informações sobre a administração de serviço.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
