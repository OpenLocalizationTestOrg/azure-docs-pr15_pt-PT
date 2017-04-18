<properties
    pageTitle="Limites de recursos de base de dados do Azure SQL"
    description="Esta página descreve alguns limites de recursos comuns para a base de dados do SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Limites de recursos base de dados SQL Azure

## <a name="overview"></a>Descrição geral

Base de dados SQL Azure gere os recursos disponíveis para uma base de dados utilizando dois mecanismos diferentes: **Imposição de limites**e **Governação de recursos** . Este tópico explica duas seguintes áreas principais da gestão de recursos.

## <a name="resource-governance"></a>Governação de recursos
Uma dos objetivos do projeto das camadas de serviço Basic, padrão e Premium é para a base de dados do Azure SQL comportam-se como se a base de dados está a ser executado no seu próprio computador, completamente isolado a partir de outras bases de dados. Governação recurso emula este comportamento. Se a utilização de recursos agregado atingir o máxima CPU disponível, memória, e de registo/s e recursos e de dados/s atribuídos à base de dados, governação recurso irá fila consultas em execução e atribuir recursos às consultas em fila à medida que libertam.

Como num computador dedicado, recorrendo a todos os recursos disponíveis irá resultar em execução de um mais longa de consultas, que podem resultar em tempos limite de comando no cliente atualmente em execução. Aplicações com repetir agressivas lógica e aplicações que executar consultas de base de dados com uma alta frequência podem encontrar mensagens de erros ao tentar executar novas consultas quando atingiu o limite de pedidos em simultâneo.

### <a name="recommendations"></a>Recomendações:
Monitorize a utilização de recursos, bem como os tempos de resposta média de consultas quando chegar a utilização máxima de uma base de dados. Se se deparar com mais elevadas latências de consulta geralmente tem três opções:

1.  Reduza a quantidade de pedidos recebidos à base de dados para impedir que o limite de tempo e a desorganizadas para cima de pedidos de.

2.  Atribua um nível de desempenho superior à base de dados.

3.  Optimize as consultas para reduzir a utilização de recursos de cada consulta. Para mais informações, consulte a secção consulta Tuning/Hinting no artigo orientações de desempenho de base de dados de SQL Azure.

## <a name="enforcement-of-limits"></a>Imposição dos limites
Recursos que não seja CPU, memória, e de registo/s e e/s de dados são impostos por negação de novos pedidos de quando são atingidos os limites. Os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md) , consoante o limite que foi atingido.

Por exemplo, o número de ligações a uma base de dados do SQL e o número de pedidos em simultâneo que podem ser processados está restrito. Base de dados SQL permite que o número de ligações para a base de dados para ser maior do que o número de pedidos em simultâneo para suportar o agrupamento de ligações. Enquanto a quantidade de ligações que estão disponíveis facilmente pode ser controlada através da aplicação, a quantidade de pedidos de paralelas é frequentemente vezes mais difícil para estimar e para controlar. Especialmente durante cargas de pico quando a aplicação seja envia demasiados pedidos ou a base de dados atinge respectivo recurso limites e inicia a empilhar threads de trabalho devido mais longa consultas em execução, podem ser encontrados erros.

## <a name="service-tiers-and-performance-levels"></a>Camadas de serviços e níveis de desempenho

Existem camadas de serviços e níveis de desempenho para ambos os agrupamentos de base de dados e elásticos autónomo.

### <a name="standalone-databases"></a>Autónomo bases de dados

Para uma base de dados autónomo, os limites de uma base de dados são definidos pelo nível de camadas e desempenho de serviço da base de dados. A tabela seguinte descreve as características da básico, padrão e Premium bases de dados, com diversas larguras níveis de desempenho.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Conjuntos de dados flexível

[Conjuntos de dados flexível](sql-database-elastic-pool.md) partilhar recursos através de bases de dados no conjunto. A tabela seguinte descreve as características da agrupamentos de base de dados flexível Basic, padrão e Premium.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Para uma definição de cada recurso listado nas tabelas anterior expandida, consulte o artigo descrições [capacidades de camada de serviço e os limites](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Para obter uma descrição geral das camadas de serviço, consulte o artigo [camadas de serviços de base de dados de SQL Azure e níveis de desempenho](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Outros limites de base de dados SQL

| Área | Limite | Descrição |
|---|---|---|
| Bases de dados utilizando automatizadas exportar por subscrição | 10 | Exportar automatizado permite-lhe criar uma agenda de cópias de segurança as bases de dados do SQL personalizada. Para obter mais informações, consulte o artigo [bases de dados SQL: suporte para automatizado exportações de base de dados SQL](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Base de dados por servidor | Até 5000 | Até 5000 bases de dados são permitidas por servidor em servidores V12. |  
| DTUs por servidor | 45000 | 45000 DTUs estão disponíveis por servidor em servidores V12 para bases de dados de aprovisionamento, flexível agrupamentos e armazém de dados. |



## <a name="resources"></a>Recursos

[Subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md)

[Camadas de serviços de base de dados do Azure SQL e níveis de desempenho](sql-database-service-tiers.md)

[Mensagens de erro para os programas de cliente da base de dados SQL](sql-database-develop-error-messages.md)
