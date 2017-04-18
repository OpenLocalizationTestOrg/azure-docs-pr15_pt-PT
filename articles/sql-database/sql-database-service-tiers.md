<properties
    pageTitle="Opções de desempenho de base de dados SQL &: camadas de serviço | Microsoft Azure"
    description="Compare funcionalidades de continuidade de desempenho e empresas base de dados SQL das camadas de serviço para equilibrar custo e a capacidade à medida que dimensionar."
    keywords="Opções de base de dados, o desempenho da base de dados"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções de base de dados SQL e o desempenho: compreender o que está disponível em cada camada de serviço

[Base de dados do SQL Azure](sql-database-technical-overview.md) oferece três camadas de serviço com vários níveis de desempenho para processar diferentes cargas de trabalho. Cada nível de desempenho disponibiliza um conjunto crescente de recursos concebido para entregar débito superior de cada vez. Pode gerir cada base de dados na sua própria [camada de serviços](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) com o seu próprio nível de desempenho. Também pode gerir várias bases de dados num [conjunto de flexível](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) com um conjunto de recursos partilhado. Os recursos disponíveis para autónomo bases de dados são expressa em termos de unidades da transação da base de dados (DTUs) e para agrupamentos flexível em termos flexível DTUs ou eDTUs. Para saber mais sobre DTUs e eDTUs, consulte o artigo [o que é um DTU](sql-database-what-is-a-dtu.md). 

Em ambos os casos, as camadas de serviço incluem **básicas**, **padrão**e **Premium**. Opções de base de dados nestes camadas são semelhantes para autónomo bases de dados e flexível conjuntos de dados, mas existem considerações adicionais para agrupamentos flexível. Este artigo fornece detalhes das camadas de serviço para autónomo bases de dados e flexível conjuntos de dados.

## <a name="service-tiers-and-database-options"></a>Camadas de serviços e opções de base de dados
Básicas, Standard, e camadas de serviços Premium todos têm um tempo de utilização SLA de 99,99% e oferecem desempenho previsível, opções de continuidade do negócio flexível, funcionalidades de segurança e faturação preço por hora. A tabela seguinte fornece exemplos das camadas melhor adequados para cargas de trabalho de outra aplicação.

| Camada de serviços | Destino cargas de trabalho |
|---|---|
| **Básicas** | Adequadas para uma base de dados pequenas, normalmente uma única ativa operação num dado momento de suporte. Alguns exemplos incluem as bases de dados utilizados para testes ou desenvolvimento, ou pequena com pouca frequência utilizadas aplicações. |
| **Padrão** | A opção para ir para a maioria das aplicações de nuvem, suporte várias consultas em simultâneo. Alguns exemplos incluem as aplicações do grupo de trabalho ou na web. |
| **Premium** | Concebido para alta transaccional volume, vários utilizadores em simultâneo de suporte e exigindo o nível mais alto continuidade das funcionalidades de business. Exemplos são as aplicações críticas missão de suporte de bases de dados. |

>[AZURE.NOTE] Edições Web e empresas são foi removidas. Leia as [Perguntas mais frequentes do pôr do sol](https://azure.microsoft.com/pricing/details/sql-database/web-business/) se planeia continuar a utilizar o edições Web e empresas.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Camadas de serviços de base de dados autónomo e níveis de desempenho
Para bases de dados autónomo, existem vários níveis de desempenho dentro de cada camada de serviço. Tem a flexibilidade de poder escolher o nível mais adequado às necessidades da sua carga de trabalho. Se precisar de dimensionar para cima ou para baixo, pode alterar facilmente as camadas da base de dados. Consulte o artigo [Alterar camadas de serviços de base de dados e níveis de desempenho](sql-database-scale-up.md) para obter detalhes.

Características de desempenho listadas aqui aplicam-se a bases de dados criadas utilizando [V12 de base de dados do SQL](sql-database-v12-whats-new.md). Independentemente do número de bases de dados alojadas, um conjunto de recursos garantido obtém a sua base de dados e as características de desempenho esperado da sua base de dados não são afetadas.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte o artigo [funcionalidades de camada de serviço e os limites](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Camadas de serviços de agrupamento flexível desempenho e em eDTUs
Para além de criar e dimensionamento uma base de dados autónomo, também tem a opção de gestão de várias bases de dados dentro de um [conjunto de flexível](sql-database-elastic-pool.md). Todas as bases de dados num conjunto de dados flexível partilham um conjunto comum de recursos. As características do desempenho são medidas por *Flexível unidades da transação da base de dados* (eDTUs). Como com bases de dados autónomo, agrupamentos de surgir em três camadas de serviço: **básicas**, **padrão**e **Premium**. Para conjuntos de dados, estas camadas de três serviços ainda definem os limites gerais de desempenho e várias funcionalidades.

Conjuntos de dados permitem bases de dados para partilhe e consuma recursos DTU sem necessitar de atribuir um nível de desempenho específicos para cada base de dados no conjunto. Por exemplo, uma base de dados autónomo num conjunto de dados padrão pode aceder a partir de eDTUs 0 para o eDTU de base de dados máximo que configurar quando configura o agrupamento de. Conjuntos de dados permitem a várias bases de dados com variados das cargas de trabalho para utilizar de forma eficaz eDTU recursos disponíveis para o conjunto completo. Consulte o artigo [preço e desempenho considerações para um agrupamento de flexível](sql-database-elastic-pool-guidance.md) para obter detalhes.

A tabela seguinte descreve as características da camadas de serviços de agrupamento.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada base de dados dentro de um conjunto também cumpre as características de base de dados autónomo para essa camada. Por exemplo, o agrupamento de básico tem um limite para o número máximo de sessões por conjunto de 4800-28800, mas uma base de dados individual dentro de um conjunto básico tem um limite de base de dados de 300 sessões.

## <a name="choosing-a-service-tier"></a>Escolher uma camada de serviço

Para decidir uma camada de serviço, comece por para determinar se a base de dados deverá ser uma base de dados do programa autónomo ou ser parte de um conjunto flexível. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Escolher uma camada de serviço para uma base de dados autónomo

Para decidir uma camada de serviço para uma base de dados autónomo, comece por para determinar as funcionalidades de base de dados que tem de escolher a edição de base de dados SQL:

- Tamanho (máximo de 2 GB para Basic, 250 GB máxima para a norma e 500 GB para o valor máximo de 1 TB para Premium - dependendo do nível de desempenho) de base de dados
- Período da retenção de cópia de segurança da base de dados (7 dias para Basic, 35 dias para o padrão e 35 dias para Premium)

Assim que tiver determinado a edição de base de dados SQL, está pronto para determinar o nível de desempenho para a base de dados (o número de DTUs). Se pode estima e, em seguida [Dimensionar para cima ou para baixo dinamicamente](sql-database-scale-up.md) com base na experiência real. Também pode utilizar a [DTU Calculadora](http://dtucalculator.azurewebsites.net/) de aproximação do número de DTUs conforme necessário. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Escolher uma camada de serviço para um agrupamento de base de dados flexível.

Para decidir na camada serviço para um agrupamento de base de dados flexível, comece por para determinar as funcionalidades de base de dados que tem de escolher a camada de serviço para o conjunto.

- Tamanho da base de dados (2 GB para Basic, 250 GB para padrão e 500 GB para Premium)
- Período da retenção de cópia de segurança da base de dados (7 dias para Basic, 35 dias para o padrão e 35 dias para Premium)
- Número de bases de dados por conjunto (400 por Basic, 400 por padrão e 50 para Premium)
- Máximo de armazenamento por agrupamento (117 GB para Basic, 1200 para padrão e 750 Premium)

Assim que tiver determinado a camada de serviço para o conjunto de, está pronto para determinar o nível de desempenho para o conjunto (eDTUs). Se pode estima e, em seguida, [Dimensionar para cima ou dimensionar dinamicamente para baixo](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) com base na experiência real. Pode utilizar a [DTU Calculadora](http://dtucalculator.azurewebsites.net/) de aproximação do número de DTUs necessárias para cada base de dados num conjunto de dados para ajudar a determinar o limite superior para o conjunto.

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre como preços para estes camadas no [Preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Obter informações sobre os detalhes dos [conjuntos de dados flexível](sql-database-elastic-pool-guidance.md) e [preço e desempenho considerações para agrupamentos flexível](sql-database-elastic-pool-guidance.md).
- Saiba como [Monitor, gerir e redimensionar flexível agrupamentos](sql-database-elastic-pool-manage-portal.md) e [monitorizar o desempenho do autónomo bases de dados](sql-database-single-database-monitor.md).
- Agora que sabe sobre as camadas de base de dados SQL, experimente-os com uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e saiba [como criar a sua primeira base de dados do SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Padrões de design para aplicações do inquilinos com várias SaaS utilizando a base de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Curso vídeo do Microsoft Virtual Academy sobre as capacidades de base de dados flexível na base de dados do SQL Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
