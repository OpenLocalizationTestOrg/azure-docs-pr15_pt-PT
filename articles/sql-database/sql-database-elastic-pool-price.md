<properties
    pageTitle="Preço de agrupamento flexível de base de dados SQL e desempenho"
    description="Informação de preços específico de agrupamentos de base de dados flexível."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Faturação do conjunto de base de dados flexível e informação de preços

Agrupamentos de base de dados flexível são faturados pelas seguintes características:

- Um conjunto de dados flexível é faturado relativamente a sua criação, mesmo quando existem bases de dados no conjunto.
- Um conjunto de dados flexível é faturado por hora. Esta é a mesma frequência de medição para níveis de desempenho das bases de dados simples.
- Se um agrupamento de flexível é redimensionado para uma nova quantidade de eDTUs, em seguida, o agrupamento não é faturado de acordo com a nova quantidade de eDTUS até que esteja concluída a operação de redimensionamento. Isto segue o mesmo padrão como alterar o nível de desempenho das autónomo bases de dados.


- O preço de um conjunto de flexível é baseado no número de eDTUs do conjunto de. O preço de um conjunto de flexível é independentemente do número e a utilização das bases de dados flexível dentro da mesma.
- Preço é calculado pelo (número de agrupamento eDTUs) x (preço unitário por eDTU).

Preço unitário eDTU para um agrupamento de flexível é mais elevado que preço unitário DTU para uma base de dados autónomo na mesma camada de serviço. Para obter detalhes, consulte o artigo [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 


Para compreender as camadas eDTUs e serviço, consulte o artigo [Opções de base de dados SQL e o desempenho](sql-database-service-tiers.md).

## <a name="next-steps"></a>Próximos passos

- [Criar um conjunto de dados da base de dados flexível](sql-database-elastic-pool-create-portal.md)
- [Monitorizar, gerir e dimensionar um agrupamento de base de dados flexível](sql-database-elastic-pool-manage-portal.md)
- [Opções de base de dados SQL e o desempenho: compreender o que está disponível em cada camada de serviço](sql-database-service-tiers.md)
- [Script do PowerShell para identificar as bases de dados adequados para um agrupamento de base de dados flexível](sql-database-elastic-pool-database-assessment-powershell.md)
