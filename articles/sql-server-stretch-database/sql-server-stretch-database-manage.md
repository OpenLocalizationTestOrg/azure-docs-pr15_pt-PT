<properties
    pageTitle="Gerir e resolver problemas de base de dados esticar | Microsoft Azure"
    description="Saiba como gerir e resolver problemas esticar base de dados."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Gerir e resolver problemas esticar base de dados

Para gerir e resolver problemas de esticar base de dados, utilize as ferramentas e métodos descritos neste tópico.

## <a name="manage-local-data"></a>Gerir dados locais

### <a name="LocalInfo"></a>Obter informações sobre tabelas ativadas para esticar base de dados e bases de dados locais
Abra o catálogo vistas **sys.databases** e **sys.tables** para ver informações sobre esticar\-com capacidade de bases de dados do SQL Server e tabelas. Para obter mais informações, consulte o artigo [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Para ver quanto espaço um esticar\-activada tabela estiver a utilizar no SQL Server, execute a seguinte instrução.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gerir a migração de dados

### <a name="check-the-filter-function-applied-to-a-table"></a>Verificar a função de filtro aplicada a uma tabela
Abrir a vista de catálogo **sys.remote\_dados\_arquivo\_tabelas** e verificar o valor da **filtro\_predicado** coluna para identificar a função que está a utilizar para selecionar linhas para migrar esticar base de dados. Se o valor for nulo, toda a tabela é elegível para ser migrado. Para obter mais informações, consulte o artigo [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) e [Selecione linhas para migrar utilizando uma função de filtro](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Verificar o estado da migração de dados
Selecione tarefas **| Esticar | Monitor** para uma base de dados SQL Server Management Studio para monitorizar a migração de dados no Monitor de base de dados esticar. Para obter mais informações, consulte o artigo [Monitor e resolver problemas de migração de dados (base de dados esticar)](sql-server-stretch-database-monitor.md).

Em alternativa, abra a vista de gestão de dinâmica **sys.dm\_db\_rda\_migração\_Estado** para ver quantas secções e linhas de dados foram migradas.

### <a name="Firewall"></a>Resolver problemas de migração de dados
Para sugestões de resolução de problemas, consulte o artigo [Monitor e resolver problemas de migração de dados (base de dados esticar)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gerir dados remota

### <a name="RemoteInfo"></a>Obter informações sobre bases de dados remotos e tabelas utilizadas pelo esticar base de dados
Abrir as vistas de catálogo **sys.remote\_dados\_arquivo\_bases de dados** e **sys.remote\_dados\_arquivo\_tabelas** para ver informações sobre a bases de dados remotos e as tabelas na qual os dados migrados estão armazenados. Para obter mais informações, consulte o artigo [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Para ver quanto espaço está a utilizar uma tabela com capacidade de esticar no Azure, execute a seguinte instrução.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Eliminar dados migrados  
Se quiser eliminar dados que já tem sido migrados para Azure, siga os passos descritos na [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gerir esquema de tabela

### <a name="dont-change-the-schema-of-the-remote-table"></a>Não altere o esquema da tabela remoto
Não altere o esquema de uma tabela Azure remoto que está associada uma tabela do SQL Server configurada para esticar base de dados. Em particular, não modificar o nome ou o tipo de dados de uma coluna. A funcionalidade de base de dados esticar faz suposições vários sobre o esquema da tabela remota em relação o esquema de tabela do SQL Server. Se alterar o esquema remoto, esticar base de dados deixa de funcionar para a tabela alterada.

### <a name="reconcile-table-columns"></a>Reconciliar colunas da tabela  
Se tiver eliminado acidentalmente colunas da tabela remota, executar **sp_rda_reconcile_columns** para adicionar colunas à tabela remota que existam na esticar\-com capacidade de tabela do SQL Server, mas não na tabela remota. Para obter mais informações, consulte o artigo [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] Quando **sp_rda_reconcile_columns** recria colunas que eliminou acidentalmente a partir da tabela remota, não restaura os dados que estava anteriormente nas colunas eliminadas.

**sp_rda_reconcile_columns** não eliminar colunas da tabela remota existentes na tabela remota, mas não na esticar\-com capacidade de tabela do SQL Server. Se existirem colunas na tabela Azure remota que já não existem na esticar\-com permissão para SQL Server tabela, estas colunas extra não impede que esticar base de dados a funcionar normalmente. Opcionalmente, pode remover as colunas adicionais manualmente.  

## <a name="manage-performance-and-costs"></a>Gerir custos e desempenho  

### <a name="troubleshoot-query-performance"></a>Resolver problemas de desempenho da consulta
Consultas que incluem esticar\-tabelas activadas são esperadas para efetuar mais lentamente do que que tinham antes das tabelas foram ativadas para esticar. Se diminui significativamente o desempenho da consulta, reveja os seguintes problemas possíveis.

-   É o seu servidor Azure numa região geográfica diferente que o SQL Server? Configure o seu servidor Azure para estar na mesma região geográfica como o SQL Server para reduzir a latência da rede.

-   Poderão ter degradado as condições de rede. Contacte o administrador de rede para obter informações sobre problemas recentes ou de corrente.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Aumentar o nível de desempenho Azure para recurso\-intensivos operações, tais como a indexação
Quando cria, reconstruir ou reorganizar um índice remissivo numa tabela de grandes dimensões que esteja configurado para esticar base de dados e prevê a necessidade frequente consultar os dados migrados no Azure durante este período de tempo, considere aumentar o nível de desempenho da correspondente remoto Azure base de dados para a duração da operação de. Para obter mais informações sobre níveis de desempenho e preços, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Não é possível interromper o serviço de base de dados do SQL Server esticar no Azure  
 Certifique-se de que seleciona o desempenho adequado e preços nível. Se aumentar o nível de desempenho temporariamente para um recurso\-intensa operação, restaurá-la ao nível anterior depois de concluída a operação. Para obter mais informações sobre níveis de desempenho e preços, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Alterar o âmbito de consultas  
 Consultas contra esticar\-tabelas activadas devolvem dados locais e remotos por predefinição. Pode alterar o âmbito de consultas para todas as consultas por todos os utilizadores, ou apenas para uma única consulta por um administrador.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Alterar o âmbito de consultas para todas as consultas por todos os utilizadores  
 Para alterar o âmbito de todas as consultas por todos os utilizadores, execute o procedimento armazenado **sys.sp_rda_set_query_mode**. Pode reduzir o âmbito para apenas dos dados locais de consulta, desativar todas as consultas ou restaurar a predefinição. Para obter mais informações, consulte o artigo [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Alterar o âmbito de consultas para uma única consulta por um administrador  
 Para alterar o âmbito de uma única consulta por um membro da função proprietário, adicione o * *com \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)* * dica de consulta para a instrução SELECT. A dica de consulta REMOTE_DATA_ARCHIVE_OVERRIDE pode ter os seguintes valores.  
 -   **LOCAL_ONLY**. Consulta apenas dos dados locais.  

 -   **REMOTE_ONLY**. Consulta apenas dados remotos.  

 -   **STAGE_ONLY**. Apenas os dados na tabela onde fases de base de dados esticar linhas elegível para migração e mantém linhas migradas para o período especificado após a migração de consulta. Esta sugestão de consulta é a única forma de tabela de transição da consulta.  

Por exemplo, a seguinte consulta devolve apenas resultados local.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Tornar actualizações administrativas e eliminações  
 Por predefinição, não pode ATUALIZAR ou eliminar linhas que são elegíveis para migração ou linhas que já foram migradas, num esticar\-com capacidade de tabela. Quando tiver de corrigir um problema, um membro da função proprietário pode executar uma operação de ATUALIZAÇÃO ou eliminar adicionando o * *com \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)* * dica de consulta a declaração de. A dica de consulta REMOTE_DATA_ARCHIVE_OVERRIDE pode ter os seguintes valores.  
 -   **LOCAL_ONLY**. Atualizar ou eliminar apenas dos dados locais.  

 -   **REMOTE_ONLY**. Atualizar ou eliminar apenas dados remotos.  

 -   **STAGE_ONLY**. Atualizar ou eliminar apenas os dados na tabela onde fases de base de dados esticar linhas elegível para migração e mantém linhas migradas para o período especificado após a migração.  

## <a name="see-also"></a>Consulte também

[Esticar Monitor de base de dados](sql-server-stretch-database-monitor.md)

[Cópia de segurança com capacidade de esticar as bases de dados](sql-server-stretch-database-backup.md)

[Restaurar esticar com permissão para bases de dados](sql-server-stretch-database-restore.md)
