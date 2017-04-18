<properties
   pageTitle="Limites de capacidade de armazenamento de dados SQL | Microsoft Azure"
   description="Valores máximos para ligações, bases de dados, tabelas e consultas para armazém de dados SQL."
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
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacidade de armazenamento de dados SQL

As tabelas seguintes contêm os valores máximos permitidos para vários componentes do armazém de dados do SQL Azure.


## <a name="workload-management"></a>Gestão de carga de trabalho

| Categoria            | Descrição                                       | Máximo            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unidades de armazém de dados (DWU)][]| Máximo DWU para um único armazém de dados SQL | 6000               |
| [Unidades de armazém de dados (DWU)][]| Máximo DWU para um única SQL server         | 6000 por predefinição<br/><br/> Por predefinição, cada SQL server (por exemplo, myserver.database.windows.net) tem uma Quota de 45,000 DTU que lhe permite até 6000 DWU. Esta quota é simplesmente um limite de segurança. Pode aumentar a sua quota através da [criação de um bilhetes de suporte][] e ao selecionar a *Quota de* como o tipo de pedido.  Para calcular o DTU necessidades, multiplique o 7.5 pelo total que DWU conforme necessário. Pode ver o consumo de DTU atual a partir do pá de servidor do SQL no portal. Bases de dados em pausa e retomadas contam para a quota DTU. |
| Ligação de base de dados | Sessões abertas em simultâneo                          | 1024<br/><br/>Suportamos um máximo de ligações activas 1024, cada uma das quais pode submeter pedidos para uma base de dados do SQL Data Warehouse ao mesmo tempo. Tenha em atenção que não existem limites no número de consultas que realmente podem ser executados em simultâneo. Quando é excedido o limite de simultaneidade, o pedido de informações compõem fila interna onde aguarda até ser processada.|
| Ligação de base de dados | Máximo de memória para declarações preparadas            | 20 MB              |
| [Gestão de carga de trabalho][] | Máximas consultas em simultâneo                    | 32<br/><br/> Por predefinição, armazém de dados SQL pode executar um máximo de 32 consultas em simultâneo e filas restante consultas.<br/><br/>O nível de simultaneidade poderão diminuir quando os utilizadores são atribuídos a uma classe recurso superior ou quando armazém de dados SQL está configurado com DWU baixa. Algumas consultas, como das DMV consultas, são sempre permitidas para executar.|
| [Tempdb][]          | Tamanho máximo de Tempdb                                | 399 GB por DW100. Por conseguinte, na DWU1000 Tempdb é dimensionado de modo a TB 3,99 |


## <a name="database-objects"></a>Objetos de base de dados

| Categoria          | Descrição                                  | Máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Base de dados          | Tamanho máximo                                     | TB 240 comprimido no disco<br/><br/>Este espaço é independente do espaço tempdb ou o registo e, por conseguinte, este espaço dedicado às tabelas permanentes.  Columnstore agrupadas compressão é estimado em 5 X.  Este compressão permite a base de dados aumentar a aproximadamente 1 PB quando todas as tabelas estão agrupada columnstore (o tipo de tabela predefinido).|
| Tabela             | Tamanho máximo                                     | 60 TB comprimido no disco   |
| Tabela             | Tabelas por bases de dados                          | mil milhões de 2          |
| Tabela             | Colunas por tabela                            | 1024 colunas       |
| Tabela             | Bytes por coluna                             | Dependente na coluna [tipo de dados][].  Limite é 8000 para tipos de dados CARÁCT, 4000 para nvarchar ou 2 GB para tipos de dados máximo.|
| Tabela             | Bytes por linha, tamanho definido                  | 8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma forma, tal como está para SQL Server com a compressão de página. Como o SQL Server, armazém de dados SQL suporta o armazenamento de linha de texto em excesso que permite aos **colunas de comprimento variável** ser seguia desativar linha. Quando as linhas de comprimento variável são seguia desativar linha, apenas a raiz 24 byte é armazenada no registo principal. Para mais informações, consulte o artigo do MSDN de [linha de texto em excesso dados excedem 8 KB][] .|
| Tabela             | Partições por tabela                    | 15.000<br/><br/>Para alto desempenho, recomendamos que minimizar o número de partições precisa enquanto ainda seus requisitos de negócio de suporte. O número de partições cresce, o overhead para operações de linguagem DDL (Data Definition) e dados manipulação idioma (LMG) cresce bem como faz com que um desempenho mais lento.|
| Tabela             | Carateres por valor de limite de partição.| 4000 |
| Índice remissivo             | Índices de colunas agrupadas não por tabela.        | 999<br/><br/>Aplica-se para tabelas de rowstore apenas.|
| Índice remissivo             | Índices agrupados por tabela.            | 1<br><br/>Aplica-se para tabelas rowstore e columnstore.|
| Índice remissivo             | Tamanho de chave do índice.                          | 900 bytes.<br/><br/>Aplica-se ao rowstore índices apenas.<br/><br/>Índices de colunas de varchar com um tamanho máximo de mais de 900 bytes podem ser criados se os dados existentes nas colunas não ultrapasse 900 bytes quando o índice é criado. No entanto, inserir, mais tarde ou ações de ATUALIZAÇÃO nas colunas que fazem com que o tamanho total exceda 900 bytes irão falhar.|
| Índice remissivo             | Colunas de chave por índice remissivo.                   | 16<br/><br/>Aplica-se ao rowstore índices apenas. Índices de columnstore agrupadas incluem todas as colunas.|
| Estatísticas        | Tamanho dos valores da coluna combinado.      | 900 bytes.         |
| Estatísticas        | Colunas por objeto estatísticas.           | 32                 |
| Estatísticas        | Estatísticas criadas em colunas por tabela. | 30.000            |
| Procedimentos armazenados | Número máximo de níveis de aninhamento.               | 8                 |
| Vista              | Colunas por vista                         | 1.024             |


## <a name="loads"></a>Cargas

| Categoria          | Descrição                                  | Máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Polybase cargas    | Bytes por linha                                | 32.768<br/><br/>Polybase cargas estão limitadas a carregar mais pequeno do que 32K ambas as linhas e não é possível carregar para VARCHR(MAX), tipo ou varbinary (Max).  Enquanto este limite existe hoje, será removido bastante mais rapidamente.<br/><br/>


## <a name="queries"></a>Consultas

| Categoria          | Descrição                                  | Máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Consulta             | Consultas em fila em tabelas do utilizador.               | 1000               |
| Consulta             | Em simultâneo consultas de vistas do sistema.          | 100                |
| Consulta             | Consultas em fila em vistas de sistema               | 1000               |
| Consulta             | Parâmetros máximos                           | 2098               |
| Batch             | Tamanho máximo                                 | 65, 536 * 4096        |
| SELECIONADOS resultados    | Colunas por linha                              | 4096<br/><br/>Nunca pode ter mais do que 4096 colunas por linha no resultado SELECT. Não há garantias que pode ter sempre 4096. Se o plano de consulta necessitar de uma tabela temporária, pode ser aplicada as 1024 colunas por tabela máxima.|
| SELECIONE            | Subconsultas aninhadas                            | 32<br/><br/>Nunca pode ter mais de 32 subconsultas aninhadas numa instrução SELECT. Não há garantias que pode ter sempre 32. Por exemplo, uma associação pode introduzir uma subconsulta para o plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível.|
| SELECIONE            | Colunas por associação                             | 1024 colunas<br/><br/>Nunca pode ter mais do que 1024 colunas na associação. Não há garantias que pode ter sempre 1024. Se o plano de associação necessitar de uma tabela temporária com mais colunas do que o resultado de associação, o limite de 1024 aplica-se para a tabela temporária. |
| SELECIONE            | Bytes por agrupar por colunas.                  | 8060<br/><br/>As colunas na cláusula GROUP BY podem ter um máximo de 8060 bytes.|
| SELECIONE            | Bytes por ordem por colunas                   | 8060 bytes.<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes.|
| Identificadores e constantes por declaração | Número de identificadores referenciadas e constantes. | 65.535<br/><br/>Armazém de dados SQL limita o número de identificadores e constantes contida numa única expressão de uma consulta. Este limite é 65.535. Superior a este número resulta num erro do SQL Server 8632. Para obter mais informações, consulte o artigo [erro interno: foi atingido um limite de serviços de expressão][].|


## <a name="metadata"></a>Metadados

| Vista de sistema                        | Número máximo de linhas |
| :--------------------------------- | :------------|
| sys.dm_pdw_component_health_alerts | 10.000       |
| sys.dm_pdw_dms_cores               | 100          |
| sys.dm_pdw_dms_workers             | Número total dos trabalhadores DMS para a mais recente 1000 pedidos de SQL. |
| sys.dm_pdw_errors                  | 10.000       |
| sys.dm_pdw_exec_requests           | 10.000       |
| sys.dm_pdw_exec_sessions           | 10.000       |
| sys.dm_pdw_request_steps           | Número total de passos para os pedidos SQL de 1000 mais recentes que estão armazenados no sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs           | 10.000       |
| sys.dm_pdw_sql_requests            | Os mais recentes 1000 SQL os pedidos de que estão armazenados no sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Próximos passos
Para mais informações de referência, consulte o artigo [Descrição geral de referência do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Unidades de armazém de dados (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Descrição geral de referência do armazém de dados SQL]: ./sql-data-warehouse-overview-reference.md
[Gestão de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[tipo de dados]: ./sql-data-warehouse-tables-data-types.md
[criar um bilhetes de suporte]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Linha de texto em excesso dados que excedam 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Erro interno: foi atingido um limite de serviços de expressão]: https://support.microsoft.com/kb/913050
