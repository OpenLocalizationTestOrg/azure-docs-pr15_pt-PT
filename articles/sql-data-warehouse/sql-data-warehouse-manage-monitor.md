<properties
   pageTitle="Monitorizar a sua carga de trabalho utilizando DMVs | Microsoft Azure"
   description="Saiba como monitorizar a sua carga de trabalho utilizando DMVs."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Monitorizar a sua carga de trabalho utilizando DMVs

Este artigo descreve como utilizar vistas dinâmicas de gestão (DMVs) para monitorizar a sua carga de trabalho e investigar a execução da consulta no armazém de dados do SQL Azure.

## <a name="permissions"></a>Permissões

Para consultar DMVs neste artigo, necessita de permissão de estado da vista da base de dados ou controlo. Normalmente concede estado da base de dados vista é a permissão preferida, tal como é muito mais restritivo.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Ligações do monitor

Todos os inícios de sessão ao armazém de dados SQL são registados [sys.dm_pdw_exec_sessions][].  Das DMV contém os última 10.000 os inícios de sessão.  O session_id é a chave primária e está atribuído sequencialmente para cada novo início de sessão.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitor a execução da consulta

Todas as consultas executadas no armazém de dados SQL são registadas [sys.dm_pdw_exec_requests][].  Das DMV contém as consultas última 10.000 executadas.  O request_id exclusivamente identifica cada consulta e é a chave primária para das DMV.  O request_id é atribuído sequencialmente para cada nova consulta e é o prefixo www.AMAZON.com/using-Microsoft-InfoPath-2010-SharePoint/dp/0735662061/REF=SR_1_1?IE=UTF8&QID=1335208925&SR=8-1, o que significa a ID da consulta.  Consultar das DMV para um determinado session_id mostra todas as consultas de um determinado início de sessão.

>[AZURE.NOTE] Procedimentos armazenados utilizam múltiplos IDs pedido.  ID de pedido de são atribuídos por ordem sequencial. 

Eis os passos a seguir para investigar planos de execução de consulta e as horas de uma consulta específica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSO 1: Identificar a consulta que pretende investigar

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Dos resultados da consulta anterior, de **Nota o ID do pedido** da consulta que pretender para investigar.

Consultas no seu estado **suspenso** estão a ser em fila de espera devido a simultaneidade limites. Estas consultas também são apresentados na consulta aguarda sys.dm_pdw_waits com um tipo de UserConcurrencyResourceType. Consulte [Gestão de simultaneidade do e carga de trabalho][] para obter mais detalhes sobre os limites de simultaneidade. Também podem esperar consultas para outras razões, como de bloqueios do objeto.  Se a sua consulta está à espera de um recurso, consulte o artigo [consultas de Investigating aguardar recursos][] mais abaixo neste artigo.

Para simplificar a pesquisa de uma consulta na tabela sys.dm_pdw_exec_requests, utilize a [etiqueta][] para atribuir um comentário a sua consulta que pode ser procurada na vista de sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>PASSO 2: Investigar o plano de consulta

Utilize o ID de pedir para obter o plano de SQL (DSQL) distribuído a consulta a partir do [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL está a demorar mais tempo do que o esperado, a causa pode ser um plano complexo com muitos passos DSQL ou apenas uma passo a demorar muito tempo.  Se o plano é muitos passos com várias operações de mover, considere otimizá as distribuições de tabela para reduzir movimento de dados. Este artigo de [distribuição de tabela][] explica porque é que os dados têm de ser movidos para resolver uma consulta e explica algumas estratégias de distribuição para minimizar movimento de dados.

Para investigar ainda mais detalhes sobre um único passo, a coluna *operation_type* do passo de consulta de execução longa e tenha em atenção o **Índice passo**:

- Avance para o passo 3a para **operações de SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Avance para o passo 3. oB para **operações de movimento de dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>PASSO 3a: investigar SQL nas bases de dados distribuídos

Utilize o ID do pedido e o índice do passo para obter detalhes de [sys.dm_pdw_sql_requests][], que contém informações de execução do passo de consulta em todas as bases de dados distribuídos.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando o passo de consulta está em execução, [DBCC PDW_SHOWEXECUTIONPLAN][] podem ser utilizados para obter o plano de estimada do SQL Server da cache de plano do SQL Server para o passo em execução numa distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>PASSO 3: investigar movimento de dados nas bases de dados distribuídos

Utilize o ID do pedido e o índice do passo para obter informações sobre um passo de movimento de dados em execução no cada distribuição partir [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Consulte a coluna *total_elapsed_time* para ver se um determinado distribuição está a demorar significativamente maior do que outras pessoas para movimento de dados.
- Para a distribuição de execução longa, consulte a coluna *rows_processed* para ver se o número de linhas a ser movido dessa distribuição é significativamente maior do que outras pessoas. Se Sim, isto pode indicar distorção dos dados subjacentes.

Se estiver a executar a consulta, [DBCC PDW_SHOWEXECUTIONPLAN][] podem ser utilizados para obter o plano de estimada do SQL Server da cache de plano do SQL Server para o passo SQL atualmente em execução dentro de uma distribuição específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Monitorizar consultas em espera

Se descobrir que a consulta não está a fazer progresso porque está à espera de um recurso, eis uma consulta que mostre todos os recursos de que uma consulta está à espera.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta ativamente está à espera de recursos a partir de outra consulta, em seguida, o estado será **AcquireResources**.  Se a consulta tiver todos os recursos, o estado ser **concedido**.

## <a name="next-steps"></a>Próximos passos
Consulte [as vistas do sistema][] para obter mais informações sobre DMVs.
Consulte o artigo [Armazém de dados SQL melhores práticas][] para obter mais informações sobre procedimentos recomendados

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md
[Vistas do sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribuição de tabela]: ./sql-data-warehouse-tables-distribute.md
[Gestão de simultaneidade do e carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Investigar consultas a aguardar por recursos]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ETIQUETA]: https://msdn.microsoft.com/library/ms190322.aspx
