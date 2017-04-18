<properties
    pageTitle="Todos os tópicos do serviço de armazenamento de dados SQL | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço Azure com o nome armazém de dados SQL que existem no http://azure.microsoft.com/documentation/articles/, título e descrição."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Todos os tópicos do serviço de armazenamento de dados do SQL Azure

Este tópico indica cada tópico que se aplica diretamente para o serviço de **Armazenamento de dados SQL** do Azure. Pode procurar esta página Web para palavras-chave utilizando **Ctrl + F**, para localizar os tópicos de interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Cópias de segurança do armazém de dados SQL](sql-data-warehouse-backups.md) | Saiba mais sobre cópias de segurança do armazém de dados SQL da base de dados incorporados que permitem-lhe restaurar um armazém de dados do SQL Azure para um ponto de restauro ou uma região geográfica diferente. |


## <a name="updated-articles-sql-data-warehouse"></a>Artigos atualizados, armazém de dados SQL

Esta secção apresenta artigos que foram atualizados recentemente, onde a atualização foi grande ou significativo. Para cada artigo actualizado, é apresentado um fragmento aproximado do texto markdown adicionados. Os artigos foram atualizados no intervalo de datas do **2016-08-22** **2016-10 -**05.

| &nbsp; | Artigo | Texto actualizado, fragmento | Actualizada quando |
| --: | :-- | :-- | :-- |
| 2 | [Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-Para controlar bytes e ficheiros r.command SELECT, s.request_id, r.status, contagem (distinta input_name) como nbr_files, somar (s.bytes_processed) / 1024/1024 como gb_processed a partir do sys.dm_pdw_exec_requests r associação interna sys.dm_pdw_dms_external_work g no r.request_id = s.request_id onde r. Etiqueta = ' CTAS: carga ASC. DimProduct ' ou r. Etiqueta = ' CTAS: carga ASC. GROUP BY r.command dos FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, gb_processed desc;  | 2016-09-07 |
| 3 | [Restaurar armazém de dados SQL](sql-data-warehouse-restore-database-overview.md) | **Pode restaurar um armazém de dados em pausa?** Para restaurar um armazém de dados está em pausa, tem de pela primeira vez colocá-lo novamente online. Assim que o armazenamento de dados estiver novamente online, tem de sete dias de pontos de restauro à escolha. **Restaurar para uma região geo redundantes** Se estiver a utilizar o armazenamento geo redundantes, pode restaurar o armazenamento de dados para o seu centro de dados emparelhado numa região geográfica diferente. O armazenamento de dados é restaurado a partir da última cópia de segurança diária. **Restaurar a linha cronológica** Pode restaurar uma base de dados para qualquer ponto de restauro nos últimos sete dias. Instantâneos iniciar cada quatro para oito horas e estão disponíveis para sete dias. Quando um instantâneo for mais antigo do que sete dias, expire e o seu ponto de restaurar já não está disponível. **Restaurar os custos** O encargo de armazenamento para o armazém de dados restaurados é faturado a taxa de juro armazenamento do Windows Azure Premium. Se interromper um armazém de dados restaurados, que lhe ser cobrada armazenamento a taxa de armazenamento do Windows Azure Premium. A vantagem de interromper é que não é gratuitas | 2016-09-29 |





## <a name="get-started"></a>Introdução ao

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 4 | [Autenticação para armazém de dados do Azure SQL](sql-data-warehouse-authentication.md) | Azure autenticação para armazém de dados do SQL Azure Active Directory (AAD) e o SQL Server. |
| 5 | [Práticas recomendadas para armazém de dados do SQL Azure](sql-data-warehouse-best-practices.md) | Recomendações e as melhores práticas que deve saber à medida que desenvolvam soluções para armazém de dados do SQL Azure. Estes irão ajudá-lo ser efetuada com êxito. |
| 6 | [Controladores para armazém de dados do Azure SQL](sql-data-warehouse-connection-strings.md) | Cadeias de ligação e os controladores para armazém de dados SQL |
| 7 | [Ligar ao armazenamento de dados do Azure SQL](sql-data-warehouse-connect-overview.md) | Como localizar o servidor de cadeia de ligação e nome para o seu armazém de dados do SQL Azure |
| 8 | [Analisar dados com o Azure máquina aprendizagem](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Utilize o Azure máquina de formação para criar uma modelo com base em dados armazenados no armazém de dados do SQL Azure de aprendizagem automática aspeto do Office. |
| 9 | [Consulta armazém de dados do SQL Azure (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | Consultar armazém de dados do SQL Azure com o sqlcmd utilitário de linha de comandos. |
| 10 | [Criar uma base de dados do SQL Data Warehouse utilizando Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) | Saiba como criar um armazém de dados do SQL Azure com TSQL |
| 11 | [Como criar um bilhetes de suporte para armazém de dados SQL](sql-data-warehouse-get-started-create-support-ticket.md) | Como criar um bilhetes de suporte no armazém de dados do SQL Azure. |
| 12 | [Carregar os dados com dados Azure fábrica](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Saiba como carregar os dados com a fábrica de dados do Azure |
| 13 | [Carregar os dados com PolyBase no armazém de dados SQL](sql-data-warehouse-get-started-load-with-polybase.md) | Saiba o que é o PolyBase e como utilizá-lo para cenários de armazenamento de dados. |
| 14 | [Criar um armazém de dados do Azure SQL](sql-data-warehouse-get-started-provision.md) | Saiba como criar um armazém de dados do SQL Azure no portal do Azure |
| 15 | [Criar armazém de dados SQL através do PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Criar armazém de dados SQL utilizando o PowerShell |
| 16 | [Visualizar dados com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualizar dados armazém de dados SQL com o Power BI |
| 17 | [Consulta Azure SQL dados armazém (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Consulta SQL armazém de dados com o Visual Studio. |



## <a name="develop"></a>Desenvolver

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 18 | [Otimizar as transações para armazém de dados SQL](sql-data-warehouse-develop-best-practices-transactions.md) | Melhores práticas orientações sobre atualizações da transação eficiente de escrita no armazém de dados do SQL Azure |
| 19 | [Gestão de simultaneidade do e carga de trabalho no armazém de dados SQL](sql-data-warehouse-develop-concurrency.md) | Compreenda a gestão de simultaneidade do e carga de trabalho no armazém de dados do SQL Azure para desenvolver soluções. |
| 20 | [Criar tabela como seleccione (CTAS) no armazém de dados SQL](sql-data-warehouse-develop-ctas.md) | Sugestões para codificação com a tabela de criar como instrução select de (CTAS) no armazém de dados do SQL Azure para desenvolver soluções. |
| 21 | [SQL dinâmico no armazém de dados SQL](sql-data-warehouse-develop-dynamic-sql.md) | Sugestões para utilizar o SQL dinâmico no armazém de dados do SQL Azure para desenvolver soluções. |
| 22 | [Agrupar por opções no armazém de dados SQL](sql-data-warehouse-develop-group-by-options.md) | Sugestões para implementar o grupo pelas opções no armazém de dados do SQL Azure para desenvolver soluções. |
| 23 | [Utilizar etiquetas para consultas instrument no armazém de dados do SQL](sql-data-warehouse-develop-label.md) | Sugestões para utilizar etiquetas para consultas instrument no armazém de dados do SQL Azure para desenvolver soluções. |
| 24 | [Ciclos no armazém de dados SQL](sql-data-warehouse-develop-loops.md) | Sugestões para ciclos de Transact-SQL e substituir cursores no armazém de dados do SQL Azure para desenvolver soluções. |
| 25 | [Procedimentos armazenados no armazém de dados SQL](sql-data-warehouse-develop-stored-procedures.md) | Sugestões para implementar o procedimentos armazenados no armazém de dados do SQL Azure para desenvolver soluções. |
| 26 | [Transações no armazém de dados SQL](sql-data-warehouse-develop-transactions.md) | Sugestões para implementar o transações no armazém de dados do SQL Azure para desenvolver soluções. |
| 27 | [Esquemas definidos pelo utilizador no armazém de dados SQL](sql-data-warehouse-develop-user-defined-schemas.md) | Sugestões para utilizar esquemas de Transact-SQL no armazém de dados do SQL Azure para desenvolver soluções. |
| 28 | [Atribuir as variáveis nos armazém de dados SQL](sql-data-warehouse-develop-variable-assignment.md) | Sugestões para atribuir Transact-SQL as variáveis nos armazém de dados do SQL Azure para desenvolver soluções. |
| 29 | [Vistas no armazém de dados SQL](sql-data-warehouse-develop-views.md) | Sugestões para utilizar vistas Transact-SQL no armazém de dados do SQL Azure para desenvolver soluções. |
| 30 | [Decisões de estrutura e coding técnicas para armazém de dados SQL](sql-data-warehouse-overview-develop.md) | Os conceitos de desenvolvimento, decisões de projeto, recomendações e coding técnicas para armazém de dados SQL. |



## <a name="manage"></a>Gerir

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 31 | [Gerir power cluster no armazém de dados do SQL Azure (descrição geral)](sql-data-warehouse-manage-compute-overview.md) | Escala de desempenho saída funcionalidades no armazém de dados do SQL Azure. Dimensionar saída, ajustando DWUs ou interromper e retomar cluster recursos poupar nos custos. |
| 32 | [Gerir power cluster no armazém de dados do SQL Azure (Azure portal)](sql-data-warehouse-manage-compute-portal.md) | Azure tarefas portais para gerir calcular power. Recursos de cluster de escala, ajustando DWUs. Ou, interromper e retomar cluster recursos poupar nos custos. |
| 33 | [Gerir power cluster no armazém de dados do SQL Azure (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Tarefas do PowerShell para gerir calcular power. Recursos de cluster de escala, ajustando DWUs. Ou, interromper e retomar cluster recursos poupar nos custos. |
| 34 | [Gerir power cluster no Azure SQL dados armazém (REST)](sql-data-warehouse-manage-compute-rest-api.md) | Tarefas do PowerShell para gerir calcular power. Recursos de cluster de escala, ajustando DWUs. Ou, interromper e retomar cluster recursos poupar nos custos. |
| 35 | [Gerir power cluster no armazém de dados do SQL Azure (T SQL)](sql-data-warehouse-manage-compute-tsql.md) | Tarefas de Transact-SQL (T-SQL) para o desempenho de escala de saída, ajustando DWUs. Guarde os custos ao dimensionamento durante o que não sejam de pico vezes. |
| 36 | [Monitorizar a sua carga de trabalho utilizando DMVs](sql-data-warehouse-manage-monitor.md) | Saiba como monitorizar a sua carga de trabalho utilizando DMVs. |
| 37 | [Gerir bases de dados no armazém de dados do SQL Azure](sql-data-warehouse-overview-manage.md) | Descrição geral das gerir bases de dados do SQL armazém de dados. Inclui ferramentas de gestão, DWUs e desempenho de escala de saída, resolução de problemas de desempenho da consulta, estabelecer políticas de bom nível de segurança e restaurar uma base de dados a partir de danos nos dados ou de uma falha de regional. |
| 38 | [Monitorizar consultas de utilizador no armazém de dados do SQL Azure](sql-data-warehouse-overview-manage-user-queries.md) | Descrição geral da considerações, melhores práticas e tarefas de monitorização consultas de utilizador no armazém de dados do SQL Azure |
| 39 | [Restaurar armazém de dados SQL](sql-data-warehouse-restore-database-overview.md) | Descrição geral das opções de restauro de base de dados para recuperar uma base de dados no armazém de dados do SQL Azure. |
| 40 | [Restaurar um armazém de dados do Azure SQL (Portal)](sql-data-warehouse-restore-database-portal.md) | Azure tarefas portais para restaurar um armazém de dados do SQL Azure. |
| 41 | [Restaurar um armazém de dados do Azure SQL (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Tarefas do PowerShell para restaurar um armazém de dados do SQL Azure. |
| 42 | [Restaurar um armazém de dados do Azure SQL (REST API)](sql-data-warehouse-restore-database-rest-api.md) | Tarefas de REST API para restaurar um armazém de dados do SQL Azure. |



## <a name="tables-and-indexes"></a>Tabelas e os índices

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 43 | [Tipos de dados para tabelas no armazém de dados SQL](sql-data-warehouse-tables-data-types.md) | Começar a trabalhar com tipos de dados para tabelas armazém de dados do SQL Azure. |
| 44 | [Distribuir a tabelas no armazém de dados SQL](sql-data-warehouse-tables-distribute.md) | Introdução ao distribuir a tabelas no armazém de dados do SQL Azure. |
| 45 | [Indexação tabelas no armazém de dados SQL](sql-data-warehouse-tables-index.md) | Introdução ao tabela indexação no armazém de dados do SQL Azure. |
| 46 | [Descrição geral de tabelas no armazém de dados SQL](sql-data-warehouse-tables-overview.md) | Introdução ao Azure SQL dados armazém tabelas. |
| 47 | [Criar a partições tabelas no armazém de dados SQL](sql-data-warehouse-tables-partition.md) | Introdução ao tabela partições no armazém de dados do SQL Azure. |
| 48 | [Gerir estatísticas sobre tabelas no armazém de dados SQL](sql-data-warehouse-tables-statistics.md) | Introdução ao estatísticas sobre tabelas no armazém de dados do SQL Azure. |
| 49 | [Tabelas temporárias no armazém de dados SQL](sql-data-warehouse-tables-temporary.md) | Introdução ao temporárias tabelas no armazém de dados do SQL Azure. |



## <a name="integrate"></a>Integrar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 50 | [Utilizar dados Azure fábrica com armazém de dados SQL](sql-data-warehouse-integrate-azure-data-factory.md) | Sugestões para usar o Azure dados fábrica (ADF) no armazém de dados do SQL Azure para desenvolver soluções. |
| 51 | [Utilizar a aprendizagem máquina Azure com armazém de dados SQL](sql-data-warehouse-integrate-azure-machine-learning.md) | Tutorial para utilizar o Azure máquina aprendizagem com armazém de dados do SQL Azure para desenvolver soluções. |
| 52 | [Utilizar a análise da cadeia Azure com armazém de dados SQL](sql-data-warehouse-integrate-azure-stream-analytics.md) | Sugestões para utilizar a análise da cadeia de Azure com armazém de dados do SQL Azure para desenvolver soluções. |
| 53 | [Utilizar o Power BI com armazém de dados SQL](sql-data-warehouse-integrate-power-bi.md) | Sugestões para utilizar o Power BI com armazém de dados do SQL Azure para desenvolver soluções. |
| 54 | [Tirar partido de outros serviços com armazém de dados SQL](sql-data-warehouse-overview-integrate.md) | Ferramentas e parceiros com as soluções que se integram no armazém de dados SQL.  |



## <a name="load"></a>Carregar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 55 | [Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados do SQL Azure (Azure dados fábrica)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Saiba como carregar os dados com a fábrica de dados do Azure |
| 56 | [Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Saiba como utilizar PolyBase para carregar os dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL. Carrega algumas tabelas a partir de dados públicos num esquema de armazém de dados de venda a retalho Contoso. |
| 57 | [Carregar os dados do SQL Server para armazém de dados do SQL Azure (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Utiliza bcp para exportar dados do SQL Server para ficheiros simples, AZCopy para importar dados para o armazenamento de Blobs do Azure e PolyBase para ingerir esta última os dados para armazém de dados do SQL Azure. |
| 58 | [Carregar os dados do SQL Server para armazém de dados do SQL Azure (ficheiros simples)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Para um tamanho de dados pequenas utiliza bcp para exportar dados do SQL Server para ficheiros simples e importar os dados diretamente para armazém de dados do SQL Azure. |
| 59 | [Carregar os dados do SQL Server para Azure SQL dados armazém (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Mostra-lhe como criar um pacote SQL Server Integration Services (SSIS) para mover dados de uma grande variedade de origens de dados para armazém de dados SQL. |
| 60 | [Carregar os dados com PolyBase no armazém de dados SQL](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Utiliza bcp para exportar dados do SQL Server para ficheiros simples, AZCopy para importar dados para o armazenamento de Blobs do Azure e PolyBase para ingerir esta última os dados para armazém de dados do SQL Azure. |
| 61 | [Guia para utilizar PolyBase no armazém de dados SQL](sql-data-warehouse-load-polybase-guide.md) | Directrizes e recomendações para utilizar PolyBase em cenários armazém de dados SQL. |
| 62 | [Carregar os dados de exemplo para armazém de dados SQL](sql-data-warehouse-load-sample-databases.md) | Carregar os dados de exemplo para armazém de dados SQL |
| 63 | [Carregar os dados com bcp](sql-data-warehouse-load-with-bcp.md) | Saiba que bcp é e como utilizá-lo para cenários de armazenamento de dados. |
| 64 | [Carregar os dados para armazém de dados do SQL Azure](sql-data-warehouse-overview-load.md) | Obter informações sobre os cenários comuns para dados carregar para armazém de dados SQL. Estas incluem utilizando PolyBase, armazenamento de Blobs do Azure, ficheiros simples e envio de disco. Também pode utilizar as ferramentas de terceiros. |



## <a name="migrate"></a>Migrar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 65 | [Migrar o seu código SQL para armazém de dados SQL](sql-data-warehouse-migrate-code.md) | Sugestões para migrar o seu código SQL para armazém de dados do SQL Azure para desenvolver soluções. |
| 66 | [Migrar os seus dados](sql-data-warehouse-migrate-data.md) | Sugestões para migrar os seus dados para o armazém de dados do SQL Azure para desenvolver soluções. |
| 67 | [Utilitário de migração de armazém de dados (pré-visualização)](sql-data-warehouse-migrate-migration-utility.md) | Migre para o armazém de dados SQL. |
| 68 | [Migrar o seu esquema para armazém de dados SQL](sql-data-warehouse-migrate-schema.md) | Sugestões para migrar o seu esquema para armazém de dados do SQL Azure para desenvolver soluções. |
| 69 | [Migrar a sua solução para armazém de dados SQL](sql-data-warehouse-overview-migrate.md) | Sugestões de migração para trazer a solução a plataforma armazém de dados do SQL Azure. |



## <a name="partners"></a>Parceiros

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 70 | [Parceiros de informações da empresa de negócios armazém de dados SQL](sql-data-warehouse-partner-business-intelligence.md) | Listas de parceiros de informações da empresa de negócios de terceiros com as soluções que suportam armazém de dados SQL. |
| 71 | [Parceiros de integração de dados do armazém de dados SQL](sql-data-warehouse-partner-data-integration.md) | Listas de parceiros de terceiros com soluções de integração de dados que suportam armazém de dados do SQL Azure. |
| 72 | [Parceiros de gestão de dados do armazém de dados SQL](sql-data-warehouse-partner-data-management.md) | Listas de parceiros de gestão de dados de terceiros com as soluções que suportam armazém de dados SQL. |



## <a name="reference"></a>Referência

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 73 | [Tópicos de referência para armazém de dados SQL](sql-data-warehouse-overview-reference.md) | Conteúdo disponíveis links de referência para armazém de dados SQL. |
| 74 | [Os cmdlets do PowerShell e os REST APIs para armazém de dados SQL](sql-data-warehouse-reference-powershell-cmdlets.md) | Localize os cmdlets do PowerShell superiores para armazém de dados SQL Azure incluindo como interromper e retomar uma base de dados. |
| 75 | [Elementos de idioma](sql-data-warehouse-reference-tsql-language-elements.md) | Lista de ligações para conteúdos de referência para os elementos de idioma Transact-SQL utilizada para armazém de dados SQL. |
| 76 | [Tópicos de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Ligações para conteúdos de referência para os tópicos Transact-SQL utilizada pelo armazém de dados SQL. |
| 77 | [Vistas do sistema](sql-data-warehouse-reference-tsql-system-views.md) | Ligações ao sistema de vistas conteúdo para armazém de dados SQL. |



## <a name="security"></a>Segurança

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 78 | [Suportam de clientes de nível inferior armazém de dados do SQL - para auditoria e dinâmicos dados Masking](sql-data-warehouse-auditing-downlevel-clients.md) | Saiba mais sobre o suporte de clientes do armazém de dados SQL nível inferior para auditoria de dados |
| 79 | [Auditoria no armazém de dados do Azure SQL](sql-data-warehouse-auditing-overview.md) | Introdução ao auditoria no armazém de dados do SQL Azure |
| 80 | [Começar com transparente dados encriptação (TDE) no armazém de dados SQL](sql-data-warehouse-encryption-tde.md) | Encriptação de dados transparente (TDE) no armazém de dados SQL |
| 81 | [Começar com transparente dados encriptação (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Encriptação de dados transparente (TDE) no armazém de dados SQL (T SQL) |
| 82 | [Proteger uma base de dados no armazém de dados SQL](sql-data-warehouse-overview-manage-security.md) | Sugestões para proteger uma base de dados no armazém de dados do SQL Azure para desenvolver soluções. |



## <a name="miscellaneous"></a>Diversas

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 83 | [Instalar o Visual Studio 2015 e SSDT para armazém de dados SQL](sql-data-warehouse-install-visual-studio.md) | Instalar o Visual Studio e ferramentas de desenvolvimento do SQL Server (SSDT) para armazém de dados do Azure SQL |
| 84 | [Migração para Premium armazenamento detalhes](sql-data-warehouse-migrate-to-premium-storage.md) | Instruções para migrar uma armazém de dados SQL existente para o armazenamento de premium |
| 85 | [Introdução ao deteção ameaça](sql-data-warehouse-security-threat-detection.md) | Como começar com a ameaça detecção |
| 86 | [Limites de capacidade de armazenamento de dados SQL](sql-data-warehouse-service-capacity-limits.md) | Valores máximos para ligações, bases de dados, tabelas e consultas para armazém de dados SQL. |
| 87 | [Resolução de problemas armazém de dados do Azure SQL](sql-data-warehouse-troubleshoot.md) | Resolução de problemas armazém de dados do Azure SQL. |

