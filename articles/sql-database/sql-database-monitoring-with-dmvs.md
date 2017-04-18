<properties
   pageTitle="Monitorização de base de dados do Azure SQL utilizar vistas de gestão de dinâmica | Microsoft Azure"
   description="Saiba como detetar e diagnosticar problemas de desempenho comuns utilizando vistas de gestão de dinâmica para monitorizar a base de dados do Microsoft Azure SQL."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Base de dados do SQL Azure utilizando vistas dinâmicas de gestão de monitorização

Base de dados do Microsoft Azure SQL permite a um subconjunto de vistas de gestão de dinâmica para diagnosticar problemas de desempenho, podem ser causados por bloqueados ou de execução longa consultas, congestionamento dos recursos, planos de consulta fraca e assim sucessivamente. Este tópico fornece informações sobre como detetar problemas comuns de desempenho ao utilizar vistas de gestão de dinâmicos.

Base de dados SQL parcialmente suporta três categorias de vistas de gestão de dinâmica:

- Vistas de gestão de dinâmico relacionados com a base de dados.
- Vistas de gestão de dinâmico relacionados com a execução.
- Vistas de gestão dinâmica relacionados com da transação.

Para obter informações detalhadas sobre as vistas de gestão de dinâmicos, consulte o artigo [dinâmicas de gestão de vistas e funções (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) no SQL Server Books Online.

## <a name="permissions"></a>Permissões

Base de dados SQL, consultar uma vista de gestão de dinâmica requer permissões de **Estado da base de dados de vista** . A permissão de **Estado da base de dados vista** devolve informações sobre todos os objetos da base de dados atual.
Para conceder permissão ao **Estado da base de dados vista** a um utilizador de base de dados específica, execute a seguinte consulta:

```GRANT VIEW DATABASE STATE TO database_user; ```

Uma instância do SQL Server no local, vistas de gestão de dinâmica devolvem informações de estado do servidor. Base de dados SQL, eles devolvem informações em relação à sua lógico base de dados atual apenas.

## <a name="calculating-database-size"></a>Calcular o tamanho da base de dados

A seguinte consulta devolve o tamanho da base de dados (em megabytes):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

A seguinte consulta devolve o tamanho de objetos individuais (em megabytes) na sua base de dados:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Ligações de monitorização

Pode utilizar a vista de [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) para obter informações sobre as ligações estabelecida a um servidor de base de dados do Azure SQL específico e os detalhes de cada ligação. Além disso, a vista de [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) é útil para obter informações sobre todas as ligações de utilizador ativo e tarefas internas.
A seguinte consulta obtém informações sobre a ligação atual:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Quando executar a **sys.dm_exec_requests** e **vistas de sys.dm_exec_sessions**, se tiver permissão de **Estado da base de dados de vista** na base de dados, consulte executar todas as sessões da base de dados; caso contrário, verá apenas a sessão actual.

## <a name="monitoring-query-performance"></a>Monitorizar o desempenho da consulta

Lenta ou longo a executar consultas pode consumir recursos de sistema significativos. Esta secção demonstra como utilizar as vistas de gestão de dinâmica para detetar alguns problemas de desempenho comuns de consulta. Uma referência mais antiga, mas ainda úteis para resolver o problema, é o artigo de [Resolução de problemas de desempenho do SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) no Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Localizar consultas início N

O exemplo seguinte devolve informações sobre as principais cinco consultas classificados ao tempo médio de CPU. Este exemplo agrega as consultas de acordo com o respectivo hash de consulta, para que as consultas logicamente equivalentes são agrupadas por sua consumo de recursos acumulado.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitorização consultas bloqueadas

Consultas lentas ou de execução longa podem contribuir para consumo excessiva de recursos e ser consequência de consultas bloqueadas. Pode ser a causa do bloqueio estrutura da aplicação baixa, planos de consulta incorretas, a falta de índices útil e assim sucessivamente. Pode utilizar a vista de sys.dm_tran_locks para obter informações sobre a atividade de bloqueio atual na base de dados SQL Azure. Por exemplo, código consulte [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) no SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorizar os planos de consulta

Um plano de consulta ineficaz também pode aumentar o consumo de CPU. O exemplo seguinte utiliza a vista de [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) para determinar qual o query utiliza a CPU mais acumulada.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Consulte também

[Introdução à base de dados SQL](sql-database-technical-overview.md)
