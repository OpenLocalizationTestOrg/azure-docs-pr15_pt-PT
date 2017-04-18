<properties
    pageTitle="Monitorizar o armazenamento de na memória XTP | Microsoft Azure"
    description="Estimativa e monitor armazenamento incorporado na memória XTP utilizarem, a capacidade de; resolver o erro de capacidade 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Armazenamento de na memória OLTP monitor

Ao utilizar [OLTP na memória](sql-database-in-memory.md), dados em tabelas de memória otimizado e variáveis de tabela encontra-se na armazenamento OLTP na memória. Cada camada de serviço Premium tem um tamanho de armazenamento de OLTP na memória máximo, o que é documentado no [artigo camadas de serviços de base de dados SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Assim que este limite é excedido, inserir e atualizar operações podem começar a falhar (com erro 41823). Nesse momento é irá precisar de para quer eliminar os dados para recuperar a memória ou atualizar a camada de desempenho da sua base de dados.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinar se dados cabem dentro o remate de armazenamento na memória

Determinar o remate de armazenamento: consulte o [artigo camadas de serviços de base de dados do SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) para maiúsculas o armazenamento de diferentes camadas de serviço de Premium.

Estimar requisitos de memória para uma funciona de memória otimizado tabela da mesma forma para SQL Server, tal como é que a base de dados do SQL Azure. Demore alguns minutos para rever esse tópico no [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tenha em atenção que a tabela e linhas de variáveis de tabela, bem como índices, contam o número por defeito até o tamanho de dados de utilizador máx. Além disso, ALTER TABLE tem espaço suficiente para criar uma nova versão do toda a tabela e os seus índices.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Pode monitorizar a utilização de armazenamento na memória como uma percentagem de [armazenamento da maiúscula para a camada de desempenho](sql-database-service-tiers.md#service-tiers-for-single-databases) no Azure [portal](https://portal.azure.com/): 

- No pá da base de dados, localize a caixa de utilização de recursos e clique em Editar.
- Em seguida, selecione a métrica do `In-Memory OLTP Storage percentage`.
- Para adicionar um alerta, clique na caixa de utilização de recursos para abrir o pá métrica, em seguida, clique no alerta de adicionar.

Ou utilizar a seguinte consulta para mostrar a utilização de armazenamento na memória:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corrigir situações de memória esgotada - erro 41823

Executar memória esgotada resultados na INSERT, ATUALIZAR e criar operações a falhar com a mensagem de erro 41823.

Mensagem de erro 41823 indica as tabelas de memória otimizado e variáveis de tabela tem excedido o tamanho máximo.

Para resolver este erro, quer:


- Eliminar dados a partir das tabelas de memória otimizado potencialmente descarregar os dados a tabelas tradicionais, baseadas no disco; ou,
- Atualize a camada de serviço para uma com armazenamento suficiente na memória para os dados que precisa para manter em tabelas de memória otimizado.

## <a name="next-steps"></a>Próximos passos
Recursos adicionais sobre sobre como [monitorizar o base de dados do Azure SQL utilizar vistas de gestão de dinâmicos](sql-database-monitoring-with-dmvs.md)
