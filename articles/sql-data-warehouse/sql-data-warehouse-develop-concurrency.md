<properties
   pageTitle="Gestão de simultaneidade do e carga de trabalho no armazém de dados SQL | Microsoft Azure"
   description="Compreenda a gestão de simultaneidade do e carga de trabalho no armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestão de simultaneidade do e carga de trabalho no armazém de dados SQL

A entrega de desempenho previsível a escala, armazém de dados do Microsoft Azure SQL ajuda-o a controlar os níveis de simultaneidade e atribuições de recursos, como memória e de prioridade de CPU. Este artigo apresenta-lhe os conceitos de gestão de simultaneidade do e carga de trabalho, que explica como ambas as funcionalidades foram implementadas e como pode controlá-los no seu armazém de dados. Gestão de carga de trabalho do armazém de dados do SQL destina-se para o ajudar a suportar ambientes de vários utilizadores. Não se destina para inquilinos com várias cargas de trabalho.

## <a name="concurrency-limits"></a>Limites de simultaneidade

Armazém de dados SQL permite até 1.024 ligações em simultâneo. Todas as 1.024 ligações podem submeter consultas em simultâneo. No entanto, para otimizar o débito, armazém de dados SQL poderá fila de espera algumas consultas para assegurar que cada consulta receba uma conceder mínima de memória. Colocação, ocorre o tempo de execução de consulta. Por consultas em fila quando são atingidos os limites de simultaneidade, armazém de dados SQL pode aumentar débito total, certificando-se que ativa consultas obtêm acesso aos recursos de memória muito necessária.  

Limites de simultaneidade são regulados pelos dois conceitos: *consultas em simultâneo* e *faixas de simultaneidade*. Para uma consulta executar, deve executar dentro do limite de simultaneidade de consulta e a alocação da ranhura simultaneidade.

- As consultas em simultâneo são as consultas executar ao mesmo tempo. Armazém de dados SQL suporta até 32 consultas em simultâneo nos tamanhos DWU maiores.
- Faixas de simultaneidade foram atribuídas com base em DWU. Cada DWU 100 fornece 4 faixas de simultaneidade. Por exemplo, um DW100 atribui 4 faixas de simultaneidade e DW1000 atribui 40. Cada consulta consome um ou mais simultaneidade ranhura, dependente a [classe de recursos](#resource-classes) da consulta. Consultas em execução na classe de recursos smallrc consumam uma ranhura simultaneidade. Consultas a ser executada numa classe recurso superior consumam mais faixas de simultaneidade.

A tabela seguinte descreve os limites para consultas em simultâneo e faixas de simultaneidade em vários tamanhos DWU.

### <a name="concurrency-limits"></a>Limites de simultaneidade

|  DWU   | Consultas em simultâneo máximo  | Faixas de simultaneidade atribuídas |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Quando uma das seguintes limiares for cumprida, novas consultas estão em fila de espera e são executadas numa base First primeiro-in.  Como terminar uma consultas e o número de consultas e faixas desce abaixo dos limites, são lançadas consultas em fila de espera. 

> [AZURE.NOTE]  *Selecione* consultas a ser executada exclusivamente em vistas de gestão de dinâmica (DMVs) ou vistas de catálogo não são regidas pelos qualquer um dos limites simultaneidade. Pode monitorizar o sistema independentemente o número de consultas de executar no mesmo.

## <a name="resource-classes"></a>Classes de recursos

Recurso classes ajuda a que controlar a alocação de memória e ciclos de CPU atribuídos a uma consulta. Quatro classes de recursos pode atribuir a um utilizador sob a forma de *funções de base de dados*. As classes de quatro recurso são **smallrc**, **mediumrc**, **largerc**e **xlargerc**. Os utilizadores smallrc são atribuídos uma menor quantidade de memória e podem tirar partido de simultaneidade superior. Em contrapartida, os utilizadores atribuídos a xlargerc são dadas grandes quantidades de memória e, consequentemente, menos as consultas podem ser executados em simultâneo.

Por predefinição, cada utilizador é um membro da categoria pequenas recurso, smallrc. O procedimento `sp_addrolemember` é utilizado para aumentar a classe de recursos, e `sp_droprolemember` é utilizado para diminuir a classe de recursos. Por exemplo, este comando aumentaria a classe de recursos do loaduser para largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

É aconselhável permanentemente atribuir utilizadores a classe de recursos em vez de alterar as respetivas aulas do recurso. Por exemplo, cargas a tabelas columnstore agrupadas criar índices de alta qualidade quando atribuída mais memória. Para garantir que cargas têm acesso à memória superior, criar um utilizador especificamente para carregar os dados e atribuir definitivamente este utilizador a uma classe superior do recurso.

Existem alguns tipos de consultas que não beneficiam de uma maior alocação de memória. O sistema irá ignorar a respetiva alocação de classe de recurso e executar sempre estas consultas na classe de recursos pequenas em vez disso. Se estas consultas sempre executados na classe de recursos pequenas, podem executar quando faixas de simultaneidade são sob pressão e não consumam faixas mais do que necessário. Para mais informações, consulte [exceções de classe do recurso](#query-exceptions-to-concurrency-limits) .

Alguns obter mais detalhes sobre a classe de recursos:

- *Alterar a função* permissão é necessária para alterar a classe de recursos de um utilizador.  
- Apesar de poder adicionar um utilizador para uma ou mais das categorias recurso superiores, os utilizadores terão os atributos de classe de recursos mais alta aos quais estão atribuídos. Isto é, se um utilizador é atribuído a mediumrc e largerc, a classe de recursos superior (largerc) é a classe de recursos que será respeitada.  
- Não pode ser alterada a classe de recursos do utilizador administrativas do sistema.

Para obter um exemplo detalhado, consulte [exemplo de classe de recurso de utilizador de alterar](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Alocação de memória

Existem vantagens e desvantagens para aumentar a classe de recursos de um utilizador. Aumentar uma classe de recurso para um utilizador irá dar-o seu acesso de consultas para a mais memória, que poderá vermelha consultas executar mais rápido.  No entanto, superiores classes de recursos também Reduza o número de consultas em simultâneo que podem ser executados. Este é o compromisso entre atribuir grandes quantidades de memória a uma consulta única ou permitir que outros consultas, que também precisa de atribuições de memória, para ser executado em simultâneo. Se um utilizador é dado alta alocações de memória para uma consulta, outros utilizadores não terão acesso ao mesmo que a memória para executar uma consulta.

A tabela seguinte mapas a memória alocada para cada distribuição por classe DWU e de recursos.

### <a name="memory-allocations-per-distribution-mb"></a>Atribuições de memória por distribuição (MB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1.600   |
| DW500  |   100   |    400   |    800  |  1.600   |
| DW600  |   100   |    400   |    800  |  1.600   |
| DW1000 |   100   |    800   |  1.600  |  3.200   |
| DW1200 |   100   |    800   |  1.600  |  3.200   |
| DW1500 |   100   |    800   |  1.600  |  3.200   |
| DW2000 |   100   |  1.600   |  3.200  |  6,400   |
| DW3000 |   100   |  1.600   |  3.200  |  6,400   |
| DW6000 |   100   |  3.200   |  6,400  |  12,800  |

A partir da tabela anterior, pode ver que uma consulta em execução num DW2000 na classe de recursos xlargerc teria acesso 6.400 MB de memória dentro de cada uma das bases de dados distribuídos 60.  No armazém de dados do SQL, existem 60 distribuições. Por conseguinte, para calcular a alocação de memória total de uma consulta de uma classe de dado recurso, os valores acima devem ser multiplicados pelo 60.

### <a name="memory-allocations-system-wide-gb"></a>Memória alocações ao nível do sistema (GB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

Esta tabela de atribuições de memória todo o sistema, pode ver que uma consulta em execução num DW2000 na classe xlargerc recurso é atribuída um total de 375 GB de memória (6.400 MB * 60 distribuições / 1.024 para converter GB) sobre a totalidade do seu armazém de dados SQL.

## <a name="concurrency-slot-consumption"></a>Consumo de ranhura simultaneidade

Armazém de dados SQL concede mais memória para consultas a ser executada em categorias recurso superiores. Memória é um recurso fixo.  Por conseguinte, a memória mais atribuída por consulta, podem executar as consultas em simultâneo menos. A tabela seguinte reiterates todos os conceitos anterior numa única vista que apresenta o número de faixas de simultaneidade disponíveis por DWU e as faixas média consumidas por cada classe de recursos.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Alocação e consumo de faixas de simultaneidade

|  DWU   | Máximas consultas em simultâneo  | Faixas de simultaneidade atribuídas | Faixas utilizadas por smallrc |  Faixas utilizadas por mediumrc |  Faixas utilizadas por largerc |  Faixas utilizadas por xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


A partir nesta tabela, pode ver essa execução armazém de dados SQL como DW1000 atribui um máximo de 32 consultas em simultâneo e um total de faixas de simultaneidade 40. Se todos os utilizadores estiverem em execução no smallrc, 32 consultas em simultâneo seriam permitidas uma vez que cada consulta seria consumir 1 ranhura de simultaneidade. Se todos os utilizadores de um DW1000 estavam em execução no mediumrc, cada consulta será alocada 800 MB por distribuição para uma atribuição de memória total de 47 GB por consulta e simultaneidade seria limitada a 5 utilizadores (40 faixas de simultaneidade / 8 faixas por utilizador mediumrc).

## <a name="query-importance"></a>Consulta importância

Armazém de dados SQL implementa classes de recursos ao utilizar grupos de carga de trabalho. Existem um total de oito grupos de carga de trabalho que controlam o comportamento das classes recurso através de vários tamanhos DWU. Para qualquer DWU, armazém de dados SQL utiliza apenas quatro dos grupos oito carga de trabalho. Isto faz sentido porque cada grupo de carga de trabalho está atribuído a uma das quatro classes recurso: smallrc mediumrc, largerc, ou xlargerc. A importância da compreender os grupos de carga de trabalho é que algumas destes grupos de carga de trabalho estão definidas para maior *importância*. Importância é utilizada para CPU agendamento. Consultas executadas com importância alta receberão mais de três vezes ciclos de CPU daqueles com importância Média. Por conseguinte, mapeamentos de ranhura simultaneidade também determinam a prioridade da CPU. Quando uma consulta consome faixas de 16 ou mais, esta é executada como tendo importância alta.

A tabela seguinte mostra os mapeamentos de importância para cada grupo de carga de trabalho.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapeamentos de grupo de carga de trabalho para faixas de simultaneidade e importância

| Grupos de carga de trabalho | Mapeamento de ranhura simultaneidade | MB / distribuição | Mapeamento de importância |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Médio       |
| SloDWGroupC01   |            2             |         200       |       Médio       |
| SloDWGroupC02   |            4             |         400       |       Médio       |
| SloDWGroupC03   |            8             |         800       |       Médio       |
| SloDWGroupC04   |           16             |       1.600       |       Alto         |
| SloDWGroupC05   |           32             |       3.200       |       Alto         |
| SloDWGroupC06   |           64             |       6,400       |       Alto         |
| SloDWGroupC07   |          128             |      12,800       |       Alto         |

A partir do gráfico **alocação e consumo de faixas de simultaneidade** , pode ver que uma DW500 utiliza 1, 4, 8 ou faixas de simultaneidade 16 para smallrc, mediumrc, largerc e xlargerc, respetivamente. Pode procurar esses valores no gráfico anterior para localizar a importância para cada categoria de recursos.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 o mapeamento de classes de recursos para importância

| Classe de recursos | Grupo de carga de trabalho | Faixas de simultaneidade utilizadas | MB / distribuição | Importância |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Médio   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Médio   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Médio   |
| xlargerc       | SloDWGroupC04  |          16            |        1.600      |   Alto     |


Pode utilizar a seguinte consulta das DMV para ver as diferenças de alocação de recursos de memória em detalhe da perspetiva de regulador recurso, ou para analisar e histórica do active a utilização dos grupos de carga de trabalho quando resolução de problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Consultas que respeitem os limites de simultaneidade

Maior parte das consultas são regida pelos classes de recursos. Estas consultas tem ajustado no ambos os em simultâneo consulta e simultaneidade ranhura limiares. É possível selecionar um utilizador excluir uma consulta a partir do modelo de ranhura simultaneidade.

Para reafirmam, seguintes declarações respeitem classes de recursos:

- SELECIONE INSERIR
- ACTUALIZAÇÃO
- ELIMINAR
- SELECIONAR (quando consultar tabelas de utilizador)
- ALTER RECONSTRUIR DE ÍNDICE REMISSIVO
- REORGANIZAR DE ÍNDICE REMISSIVO ALTER
- ALTER TABELA RECONSTRUIR
- CRIAR ÍNDICE
- CRIAR ÍNDICE COLUMNSTORE AGRUPADAS
- CRIAR TABELA COMO SELECIONAR (CTAS)
- Carregamento de dados
- Operações de movimento dados realizadas por serviço de dados de movimento (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceções de consulta para limites de simultaneidade

Algumas consultas não atender a classe de recursos que é atribuído ao utilizador. Estes exceções para os limites de simultaneidade forem efetuadas quando os recursos de memória necessários para um determinado comando são baixos, geralmente porque o comando é uma operação de metadados. O objetivo destes exceções é evitar maiores atribuições de memória consultas que nunca terá-los. Nestes casos, a classe de recurso pequenas predefinida (smallrc) é sempre utilizada, independentemente da classe de recursos reais atribuída ao utilizador. Por exemplo, `CREATE LOGIN` sempre será executada em smallrc. Os recursos necessários para realizar esta operação são muito baixos, para que não faz sentido para incluir a consulta no modelo de ranhura de simultaneidade.  Estas consultas também não são limitadas pelo limite de simultaneidade do 32 utilizador, um número ilimitado destas consultas pode executar para cima para o limite de sessão de 1.024 sessões.

As seguintes instruções não respeitar classes de recursos:

- Criar ou DROP TABLE
- ALTER TABLE... MUDAR, DIVIDIR ou PARTIÇÃO de impressão em série
- DESATIVAR DE ÍNDICE REMISSIVO ALTER
- ÍNDICE DE LISTA PENDENTE
- CRIAR, ATUALIZAR ou LARGAR estatísticas
- TRUNCAR TABELA
- AUTORIZAÇÃO ALTER
- CRIAR O INÍCIO DE SESSÃO
- CRIAR, alterar ou DROP USER
- CRIAR, alterar ou LARGAR procedimento
- Criar ou LARGAR vista
- INSERIR VALORES
- SELECIONE a partir de vistas de sistema e DMVs
- EXPLICAR
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Alterar um exemplo de classe de recursos do utilizador

1. **Criar início de sessão:** Abrir uma ligação à base de dados **principal** no SQL server que aloja a sua base de dados do armazém de dados SQL e execute os seguintes comandos.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] É aconselhável criar um utilizador da base de dados principal para os utilizadores do armazém de dados do SQL Azure. Criar um utilizador no modelo global permite que um utilizador para iniciar sessão utilizando ferramentas como SSMS sem especificar um nome de base de dados.  Também permite-lhes utilizar o Explorador de objeto para ver todas as bases de dados no SQL server.  Para obter mais detalhes sobre como criar e gerir utilizadores, consulte o artigo [seguro uma base de dados no armazém de dados do SQL][].

2. **Utilizador criar armazém de dados SQL:** Abrir uma ligação à base de dados **SQL armazém de dados** e execute o seguinte comando.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Conceder permissões:** O exemplo seguinte concede `CONTROL` da base de dados do **SQL armazém de dados** . `CONTROL`na base de dados do nível é equivalente a proprietário no SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Aumentar a classe de recursos:** Utilize a seguinte consulta para adicionar um utilizador a uma função de gestão de carga de trabalho superior.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Diminuir classe de recursos:** Utilize a seguinte consulta para remover um utilizador a partir de uma função de gestão de carga de trabalho.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] Não é possível remover um utilizador do smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>Deteção de consulta em fila de espera e outros DMVs

Pode utilizar o `sys.dm_pdw_exec_requests` das DMV para identificar consultas que estão a aguardar na fila de simultaneidade. Consultas aguardar uma ranhura simultaneidade terá um Estado de **suspensa**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Funções de gestão de carga de trabalho podem ser visualizadas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

A seguinte consulta mostra qual a função de cada utilizador está atribuído a.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

Armazém de dados SQL tem os seguintes tipos de esperar:

- **LocalQueriesConcurrencyResourceType**: consultas que sentar fora do âmbito ranhura simultaneidade. Consultas das DMV e sistema tais como funções `SELECT @@VERSION` alguns exemplos de consultas locais.
- **UserConcurrencyResourceType**: consultas que sentar dentro a arquitetura de ranhura simultaneidade. Consultas de tabelas do utilizador final representam exemplos que utilizariam este tipo de recurso.
- **DmsConcurrencyResourceType**: aguarda resultante de operações de movimento de dados.
- **BackupConcurrencyResourceType**: este espera indica que uma base de dados está a ser cópia de segurança. O valor máximo para este tipo de recurso é 1. Se tiverem sido pedidas várias cópias de segurança ao mesmo tempo, as outras serão fila de espera.

O `sys.dm_pdw_waits` das DMV podem ser utilizada para ver quais os recursos um pedido de está à espera.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

O `sys.dm_pdw_resource_waits` das DMV mostra apenas o recurso aguarda até média consumido por uma consulta determinada. Tempo de espera recurso medidas apenas o tempo a aguardar por recursos a ser fornecido por oposição a tempo de espera sinal, que é o tempo necessário para os servidores de SQL subjacentes agendar a consulta para a CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

O `sys.dm_pdw_wait_stats` das DMV podem ser utilizada para análise de tendências de histórico de espera.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre a gestão de utilizadores de base de dados e segurança, consulte o artigo [seguro uma base de dados no armazém de dados do SQL][]. Para obter mais informações sobre como maior recurso classes pode melhorar a qualidade de índice remissivo columnstore agrupadas, consulte [Rebuilding índices melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Proteger uma base de dados no armazém de dados SQL]: ./sql-data-warehouse-overview-manage-security.md
[A reformulação repetida de índices para melhorar a qualidade do segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Proteger uma base de dados no armazém de dados SQL]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
