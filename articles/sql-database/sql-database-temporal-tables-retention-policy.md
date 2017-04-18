<properties
   pageTitle="Gerir históricos dados nas tabelas Temporal com política de retenção | Microsoft Azure"
   description="Saiba como utilizar a política de retenção temporal para manter os dados históricos sob o seu controlo."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerir históricos dados nas tabelas Temporal com política de retenção

Tabelas temporais podem aumentar o tamanho da base de dados mais de tabelas normais, especialmente se manter dados histórico para um período de tempo. Por conseguinte, a política de retenção de dados histórico é um aspecto importante de planeamento e gestão do ciclo de vida de cada tabela temporal. Temporais tabelas na base de dados do SQL Azure vir através de retenção de fácil utilização que o ajuda a realizar esta tarefa.

Retenção histórico temporal pode ser configurado ao nível da tabela individual, que permite aos utilizadores criar envelhecimento flexível políticas. Aplicar retenção temporal é um processo simple: requer apenas um parâmetro para definir durante a alteração de criação ou esquema da tabela.

Depois de definir política de retenção, base de dados do SQL Azure iniciará regularmente verificar se existem linhas históricas que são elegíveis para limpeza de dados automática. Identificação de linhas correspondentes e os respetivos remoção da tabela Histórico ocorrerem transparente, a tarefa de fundo que é agendada e executada pelo sistema. Condição de idade para as linhas da tabela histórico está marcado com base na coluna que representa o final do período SYSTEM_TIME. Se o período de retenção, por exemplo, estiver definido para seis meses, as linhas da tabela elegíveis para limpeza satisfaçam a condição seguinte:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

No exemplo acima é assumido que **ValidTo** coluna corresponde ao final do período SYSTEM_TIME.

##<a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção?

Antes de configurar a política de retenção para uma tabela temporal, consulte pela primeira vez se temporal retenção histórica é activado *ao nível da base de dados*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Base de dados sinalizador **is_temporal_history_retention_enabled** está definido para Ativado por predefinição, mas os utilizadores podem alterá-lo com a instrução ALTER DATABASE. Também automaticamente está definido para desligado após a operação [aponte restauro de tempo](sql-database-point-in-time-restore-portal.md) . Para ativar a limpeza de retenção de histórico temporal para a base de dados, execute a seguinte instrução:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Pode configurar retenção para tabelas temporais mesmo se **is_temporal_history_retention_enabled** está DESATIVADA, mas limpeza automática para as linhas envelhecidas não será acionada nesse caso.


Política de retenção está configurada durante a criação de tabela, especificando o valor do parâmetro HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Base de dados SQL Azure permite-lhe especificar o período de retenção utilizando unidades de tempo diferentes: dias, semanas, meses e anos. Se HISTORY_RETENTION_PERIOD for omitido, é considerada retenção INFINITA. Também pode utilizar palavras-chave INFINITO explicitamente.

Em alguns cenários, poderá pretender configurar retenção após a criação de tabela ou alterar anteriormente valor configurado. Neste caso utilize instrução ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  Definir SYSTEM_VERSIONING para desligado *não preserva* valor do período de retenção. Definir SYSTEM_VERSIONING para Ativado sem HISTORY_RETENTION_PERIOD explicitamente especificada resultar no período de retenção INFINITO.

Para rever o estado atual da política de retenção, utilize a seguinte consulta associa sinalizador de activação de retenção temporal ao nível da base de dados com períodos de retenção para tabelas individuais:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>Como base de dados SQL elimina envelhecido linhas?

O processo de limpeza depende do esquema de índice remissivo da tabela histórico. É importante para Repare que *apenas histórico tabelas com um índice agrupado (B árvore ou columnstore) podem ter política de retenção finita configurada*. É criada uma tarefa de fundo para executar a limpeza de dados envelhecidos para todas as tabelas temporais com período de retenção finita.
Lógica de limpeza para o índice agrupadas rowstore (árvore de B) elimina antiguidade linha na blocos mais pequenos (até 10 K) minimizar pressão no registo de base de dados e subsystem e/s. Apesar de lógica de limpeza utiliza índice B árvore necessário, ordem de eliminações de linhas com mais de período de retenção não é possível garantir bem. Por conseguinte, *não têm qualquer dependência na ordem de limpeza nas suas aplicações*.

A tarefa de limpeza para o columnstore agrupada remove toda [a grupos de linha](https://msdn.microsoft.com/library/gg492088.aspx) ao mesmo tempo (normalmente conter 1 milhão de linhas, cada), que é muito eficiente, especialmente quando dados histórico são gerados um elevado ritmo.

![Retenção columnstore agrupadas](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


Compressão de excelência em dados e eficiente retenção limpeza torna agrupado columnstore índice uma escolha perfeita para cenários quando sua carga de trabalho rapidamente gera quantidade elevada de dados de histórico. Esse padrão é normal intenso [processamento transaccional das cargas de trabalho que utilizam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para o registo de alterações e de auditoria, análise de tendências ou IoT ingestão de dados.

##<a name="index-considerations"></a>Considerações de índice remissivo

A tarefa de limpeza para tabelas com índice agrupado rowstore requer índice para começar com a coluna correspondente no final do período SYSTEM_TIME. Se não existir esse índice, não poderá configurar o período de retenção finita:

*Msg 13765 16 de nível de estado 1 <br> </br> período de retenção finita Falha ao definir tabela temporal com versões sistema 'temporalstagetestdb.dbo.WebsiteUserInfo' porque a tabela Histórico 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' não contém índice agrupado necessário. Considere criar uma columnstore agrupada ou um índice de árvore B começando com a coluna que corresponde ao fim da SYSTEM_TIME período, na tabela histórico.*

É importante para Repare que a tabela de histórico predefinido criada por base de dados do SQL Azure já tem agrupadas índice, que é compatível com para a política de retenção. Se tentar remover essa índice numa tabela com o período de retenção finita, operação irá falhar com o seguinte erro:

*Msg 13766 16 de nível de estado 1 <br> </br> não é possível largar o índice agrupado 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque está a ser utilizado para automática limpeza de dados antiguidade. Considere a definição HISTORY_RETENTION_PERIOD para INFINITO na tabela temporal com versões sistema correspondente se precisar de largar este índice.*

Limpeza no índice columnstore agrupadas optimizada funciona se históricas linhas são inseridas ordem ascendente (ordenadas pelo fim da coluna de período), que é sempre as maiúsculas/minúsculas, quando a tabela histórico é povoada exclusivamente pelo mecanismo de SYSTEM_VERSIONIOING. Se as linhas na tabela histórico não são ordenadas por fim da coluna de período (que pode ser as maiúsculas/minúsculas se migrados o dados históricos existentes), deve recriar Índice columnstore agrupadas na parte superior de índice remissivo de rowstore de árvore B é ordenado corretamente, para obter um desempenho ideal.

Evite a reformulação repetida índice columnstore agrupadas na tabela histórico com o período de retenção finita, uma vez que podem ser alteradas a ordenação dos grupos de linha naturalmente impostos pela operação de controlo de versões de sistema. Se precisar da reconstruir Índice columnstore agrupadas na tabela Histórico, fazê-lo a voltar a criar na parte superior de compatível com índice de árvore B, preservação de ordenação na rowgroups necessárias para limpeza de dados normal. A mesma abordagem deverá ser reencaminhada, se criar tabela temporal com tabela de histórico existente que tenha agrupadas índice de coluna sem ordem de garantia dados:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Quando o período de retenção finita estiver configurado para a tabela histórico com o índice columnstore agrupadas, não é possível criar índices B árvore adicionais que não sejam agrupadas nessa tabela:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Uma tentativa executar acima declaração irá falhar com o seguinte erro:

*Msg 13772 16 de nível de estado 1 <br> </br> não é possível criar índice não agrupado numa tabela Histórico temporal 'WebsiteUserInfoHistory' uma vez que tem finita período de retenção e índice columnstore agrupadas definido.*

##<a name="querying-tables-with-retention-policy"></a>Consultar tabelas com política de retenção

Todas as consultas na tabela temporal filtrarem automaticamente históricas linhas que correspondam a política de retenção finita, para evitar resultados inesperados e inconsistentes, uma vez que podem ser eliminadas envelhecidas linhas pela tarefa de limpeza, *em qualquer ponto de altura e em ordem arbitrário*.

A imagem seguinte mostra o plano de consulta para uma consulta simple:

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

O plano de consulta inclui adicional filtro aplicado aos fim da coluna de período (ValidTo) no operador "Agrupadas digitalizar do índice remissivo" na tabela Histórico (realçado). Este exemplo assume esse período da retenção de 1 mês foi definido WebsiteUserInfo tabela.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se consulta tabela Histórico diretamente, poderá ver linhas que são mais antigas do que retenção especificado período, mas sem qualquer garantia para os resultados da consulta de forma repetida. A imagem seguinte mostra o plano de execução de consulta para a consulta na tabela Histórico sem adicionais filtros aplicados:

![Consultar o histórico sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não confiar lógica de negócio na tabela Histórico além do período de retenção de leitura poderá obter resultados inesperados ou inconsistentes. Recomenda-se para utilizar consultas temporais com cláusula para SYSTEM_TIME para analisar dados em tabelas temporais.

##<a name="point-in-time-restore-considerations"></a>Aponte em considerações de restauro de tempo

Quando criar nova base de dados ao [restaurar a base de dados existente para um ponto específico no tempo](sql-database-point-in-time-restore-portal.md), tem retenção temporal desactivada ao nível da base de dados. (sinalizador**is_temporal_history_retention_enabled** definida como desligado). Esta funcionalidade permite-lhe examinar todas as linhas históricas relativamente a restaurar, sem preocupações que antiguidade linhas são removidas antes de passar para a consulta-los. Pode utilizá-lo para *inspecionar dados histórico além do período de retenção configurado*.

Digamos que uma tabela temporal tem retenção de um mês período especificada. Se a base de dados foi criada na camada de serviço Premium, teria poderá criar cópia de base de dados com o estado de base de dados para cima para 35 dias novamente no passado. Que eficazmente seria lhe permite analisar históricas linhas com até 65 dias consultando diretamente a tabela histórico.

Se pretende ativar limpeza de retenção temporal, execute a seguinte instrução Transact-SQL a seguir ao ponto no restauro de tempo:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Próximos passos

Para saber como utilizar tabelas Temporal nas suas aplicações Consulte o artigo [Introdução ao Temporal tabelas na base de dados do SQL Azure](sql-database-temporal-tables.md).

Visite 9 de canal para ouvir uma [história de sucesso do cliente real temporal pós-implementação](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e ver uma [live demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obter informações detalhadas sobre tabelas Temporal, reveja a [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
