<properties
    pageTitle="Monitorizar o desempenho da base de dados na base de dados do SQL Azure | Microsoft Azure"
    description="Saiba mais sobre as opções para a sua base de dados com ferramentas Azure e vistas dinâmicas de gestão de monitorização."
    keywords="base de dados de monitorização de desempenho de bases de dados da nuvem"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorizar o desempenho da base de dados numa base de dados do Azure SQL
Monitorizar o desempenho de uma base de dados SQL Azure começa com monitorização de utilização do recurso relativamente ao nível de desempenho de base de dados que escolher. Monitorização-lo a determinar se a base de dados tem o excesso de capacidade ou está a ter problemas porque são tirar o máximo partido de recursos e, em seguida, decida se se trata de tempo para ajustar o nível de desempenho e [camadas de serviço](sql-database-service-tiers.md) da base de dados. Pode monitorizar a base de dados utilizando ferramentas gráficas no [portal do Azure](https://portal.azure.com) ou [vistas de gestão de dinâmicos](https://msdn.microsoft.com/library/ms188754.aspx)em SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorizar bases de dados utilizando o portal do Azure

No [portal do Azure](https://portal.azure.com/), pode monitorizar a utilização de um único da base de dados ao selecionar a base de dados e clicar no gráfico de **monitorização** . Isto reúne uma janela de **métrica** que pode alterar ao clicar no botão **Editar gráfico** . Adicione as seguintes métricas:

- Percentagem de CPU
- Percentagem DTU
- Percentagem de es de dados
- Percentagem do tamanho da base de dados

Depois de adicionar estes métricas, pode continuar a visualizá-los no gráfico com obter mais detalhes sobre a janela de **métrica** **monitorização** . Todas as quatro métricas mostram a percentagem de utilização média em relação a **DTU** da base de dados. Consulte o artigo [camadas de serviço](sql-database-service-tiers.md) para obter detalhes sobre DTUs.

![Camadas de monitorização de desempenho de base de dados de serviço.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Também pode configurar alertas de métricas de desempenho. Clique no botão **Adicionar alerta** na janela do **métrica** . Siga o Assistente para configurar o alerta. Tem a opção de alerta se métricas excederem um determinado limiar ou se a métrica do descer abaixo de um determinado limiar.

Por exemplo, se pretender a carga de trabalho na sua base de dados para aumentar as, pode escolher configurar um alerta de correio eletrónico sempre que a sua base de dados atinge 80% em qualquer um das métricas de desempenho. Pode utilizar este como um alerta para descobrir quando poderá ter de mudar para o próximo nível superior do desempenho.

As métricas de desempenho também podem ajudar a determinar se for possível alterar para um nível de desempenho inferior. Partem do princípio de que está a utilizar uma base de dados S2 padrão e todas as métricas de desempenho mostram que a base de dados em média não utilize mais de 10% a qualquer momento. É provável que a base de dados irão funcionar corretamente no S1 padrão. No entanto, tenha em atenção de cargas de trabalho que picos ou flutuam antes de efetuar a decisão para mover para um nível de desempenho inferior.

## <a name="monitor-databases-using-dmvs"></a>Monitorizar bases de dados utilizando DMVs

A mesma métrica que é exposta no portal do também está disponível através de vistas de sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) na base de dados lógicos **principal** do seu servidor e [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) da base de dados do utilizador. Utilize **sys.resource_stats** se precisar de monitorizar menos granulares dados ao longo de um período de tempo. Utilize **sys.dm_db_resource_stats** se precisar de monitorizar os dados mais granulares um período de tempo mais pequenas. Para obter mais informações, consulte [Orientações de desempenho de base de dados de SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **sys.dm_db_resource_stats** devolve um resultado vazio definir quando utilizada num Web e empresas edition bases de dados, que são foi removidas.

Para agrupamentos de base de dados flexível, pode monitorizar bases de dados individuais no conjunto de com as técnicas descritas nesta secção. Mas também pode monitorizar o conjunto como um todo. Para obter informações, consulte o artigo [Monitor e gerir um agrupamento de base de dados flexível](sql-database-elastic-pool-manage-portal.md).
