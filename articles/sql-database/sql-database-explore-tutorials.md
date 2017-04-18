<properties
   pageTitle="Explorar a tutoriais de base de dados do Azure SQL | Microsoft Azure"
   description="Saiba mais sobre as funcionalidades de base de dados SQL e capacidades"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Explorar a tutoriais de base de dados do Azure SQL

As ligações em baixo direcionado para uma descrição geral de cada área da funcionalidade listados e um tutorial passo ao iniciar simple para cada área. Para confinados solução rápido é iniciado que demonstram a utilização da base de dados do SQL numa solução completa com base em cenários reais, consulte o artigo [Azure SQL da base de dados solução rápida é iniciado](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>Utilizar o SQL Server Management Studio

Os seguintes tutoriais, irá obter informações sobre como utilizar o SQL Server Management Studio para administrar e base de dados do Azure SQL da consulta.


> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Tutorial  | Descrição  |
|---|---|---|
| [Ligar à base de dados do SQL Azure com um início de sessão principal ao nível do servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Neste tutorial, saiba como ligar à base de dados do SQL Azure com um início de sessão principal ao nível do servidor.|
| [Ligar à base de dados do SQL Azure como um utilizador](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Neste tutorial, irá Saiba como ligar a uma base de dados do Azure SQL utilizando uma conta de utilizador do nível de base de dados.|
||||

## <a name="elastic-pools"></a>Conjuntos de dados flexível

Os seguintes tutoriais, irá obter informações sobre utilizar [agrupamentos de flexível](sql-database-elastic-pool.md) para gerir os objetivos do desempenho para várias bases de dados que tenham amplamente inesperados e variados padrões de utilização.

| Tutorial  | Descrição  |
|---|---|---|
| [Criar um conjunto de dados flexível](sql-database-elastic-pool-create-portal.md) | Neste tutorial, saiba como criar um conjunto dimensionável de bases de dados Azure SQL. |
| [Monitorizar a uma base de dados flexível](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | Neste tutorial, aprende monitorizar a uma base de dados flexível individual para potenciais problemas. |
| [Adicionar um alerta para um recurso de conjunto de dados](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | Neste tutorial, saiba como adicionar regras para recursos que enviam e-mail para outras pessoas ou cadeias de alertas para os pontos finais de URL quando o recurso de acertos um limite de utilização que configurou. |
| [Mover uma base de dados para um conjunto de dados flexível](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | Neste tutorial, aprende mover uma base de dados para um conjunto de dados flexível. |
| [Mover uma base de dados fora de um conjunto de dados flexível](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | Neste tutorial, saiba como mover uma base de dados fora de um conjunto de dados flexível. |
| [Alterar definições de desempenho de um conjunto de dados](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | Neste tutorial, aprende ajustar os limites de armazenamento de desempenho e para um conjunto de dados. |
||||

## <a name="elastic-database-jobs"></a>Tarefas de base de dados flexível

Os seguintes tutoriais, irá obter informações sobre a utilização de [tarefas da base de dados flexível](sql-database-elastic-jobs-overview.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md) | Neste tutorial, saiba como utilizar as funcionalidades das ferramentas de base de dados flexível utilizando uma aplicação sharded simple. |
| [Começar a trabalhar com tarefas flexível de base de dados do Azure SQL](sql-database-elastic-jobs-getting-started.md)  | Neste tutorial, aprendeu como para como criar e gerir as tarefas que gerir um grupo de bases de dados relacionados.  | 
||||

## <a name="elastic-queries"></a>Consultas flexível

Tutoriais seguintes, vai aprender sobre a execução de [consultas flexível](sql-database-elastic-query-overview.md). 

| Tutorial  | Descrição  |
|---|---|---|
| [Consultar através de uma base de dados (sharded) horizontalmente com a partições)](sql-database-elastic-query-getting-started.md) | Neste tutorial, aprende a criar relatórios a partir de todas as bases de dados numa base de dados (sharded) horizontalmente com a partições através do [query flexível](sql-database-elastic-query-overview.md) |
| [Consultar através de uma base de dados na vertical com a partições)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | Neste tutorial, aprende a criar relatórios a partir de todas as bases de dados de um verticalmente base de dados utilizando a [consulta flexível](sql-database-elastic-query-overview.md) |
| [Migrar uma base de dados existente para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md)| Neste tutorial, aprendeu Dimensionar horizontalmente base de dados (shard) uma SQL Azure. |
||||

## <a name="performance-optimization"></a>Otimização do desempenho

Os seguintes tutoriais, irá obter informações sobre otimizar o [desempenho de bases de dados simples](sql-database-performance-guidance.md). Para otimizar o desempenho do várias bases de dados, consulte o artigo [flexível conjuntos de dados](#elastic-pools).

| Tutorial  | Descrição  |
|---|---|---|
| [Alterar o nível de camadas e o desempenho do serviço da base de dados](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | Neste tutorial, aprende Dimensionar para cima ou dimensionar para baixo o desempenho de uma base de dados do Azure SQL com camadas de serviços. |
| [SQL da base de dados Advisor consulta desempenho conhecimentos aprofundados](sql-database-performance.md#performance-overview) | Neste tutorial, saiba como abrir e utilizar conhecimentos aprofundados Advisor SQL da base de dados consulta desempenho.|
| [Recomendações de desempenho de classificação de base de dados SQL](sql-database-advisor.md#viewing-recommendations) | Neste tutorial, saiba como ver e aplicar as recomendações de desempenho de classificação de base de dados do SQL. |
| [Rever CPU superior consumir consultas](sql-database-query-performance.md#review-top-cpu-consuming-queries)| Neste tutorial, saiba como utilizar o SQL da base de dados Advisor consulta desempenho conhecimentos aprofundados para rever CPU superior consumir consultas.|
| [Ver detalhes de consulta individuais](sql-database-query-performance.md#viewing-individual-query-details)| Neste tutorial, saiba como utilizar o SQL da base de dados Advisor consulta desempenho conhecimentos aprofundados para ver os detalhes de desempenho da consulta individuais.|
||||

## <a name="sql-database-migration-and-archive"></a>Migração de base de dados do SQL e arquivo 

Tutoriais seguintes, vai aprender sobre como [migrar a base de dados do SQL Server existente para a base de dados do SQL Azure](sql-database-cloud-migrate.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Detetar problemas de compatibilidade com ferramentas de dados do SQL Server para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Neste tutorial, saiba como utilizar as ferramentas de dados do SQL Server para Visual Studio para determinar a compatibilidade de base de dados do Azure SQL. |
| [Corrigir problemas de compatibilidade com ferramentas de dados do SQL Server para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Neste tutorial, saiba como utilizar as ferramentas de dados do SQL Server para Visual Studio para corrigir problemas de compatibilidade de base de dados do Azure SQL. |
| [Determinar a compatibilidade de base de dados SQL utilizando SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | Neste tutorial, saiba como utilizar o utilitário da linha de comandos SQLPackage.exe para determinar a compatibilidade de base de dados do Azure SQL.|
| [Determinar a compatibilidade de base de dados SQL utilizando SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Neste tutorial, saiba como utilizar o SQL Server Management Studio para determinar a compatibilidade de base de dados do Azure SQL.|
| [Migrar a base de dados do SQL Server para base de dados SQL utilizando implementar base de dados ao Assistente de base de dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | Neste tutorial, irá obter informações sobre como migrar uma base de dados do SQL Server compatível com a base de dados do SQL Azure com a base de dados implementar ao Assistente de base de dados do Microsoft Azure no SQL Server Management Studio.
| [Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Neste tutorial, irá obter informações sobre como exportar uma base de dados do SQL Server compatível para um ficheiro BACPAC utilizando o Assistente Exportar dados camada de aplicação no SQL Server Management Studio.|
| [Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Neste tutorial, irá Saiba como exportar uma base de dados do SQL Server compatível para um ficheiro BACPAC utilizando o utilitário da linha de comandos SQLPackage.exe.|
| [Importar um ficheiro BACPAC para a base de dados do SQL Azure com SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Neste tutorial vai aprender importar uma base de dados para a base de dados do SQL Azure a partir de um ficheiro BACPAC utilizando o Assistente Exportar dados camada de aplicação no SQL Server Management Studio. |
| [Importar um ficheiro BACPAC para a base de dados do SQL Azure com SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | Neste tutorial vai aprender importar uma base de dados para a base de dados do SQL Azure a partir de um ficheiro BACPAC utilizando o utilitário da linha de comandos SQLPackage. |
| [Importar um ficheiro BACPAC para a base de dados do SQL Azure através do portal Azure](sql-database-import.md) | Neste tutorial vai aprender importar uma base de dados para a base de dados do SQL Azure a partir de um ficheiro BACPAC que está armazenado num BLOBs do Azure utilizando o Portal de Azure.|
| [Importar um ficheiro BACPAC para a base de dados do SQL Azure através do PowerShell](sql-database-import-powershell.md) | Neste tutorial vai aprender importar uma base de dados para a base de dados do SQL Azure a partir de um ficheiro BACPAC através do PowerShell.|
| [Arquivar uma base de dados do Azure SQL através do portal Azure](sql-database-export.md#export-your-database) | Neste tutorial, aprende arquivar uma base de dados do Azure SQL para um ficheiro BACPAC através do portal Azure. |
| [Arquivar uma base de dados do Azure SQL através do PowerShell](sql-database-export-powershell.md) | Neste tutorial, aprende arquivar uma base de dados do Azure SQL para um ficheiro BACPAC através do PowerShell. |
| [Copiar uma base de dados do Azure SQL através do portal Azure](sql-database-copy.md#copy-your-sql-database) | Neste tutorial, saiba como copiar uma base de dados do Azure SQL através do portal Azure. |
| [Copiar uma base de dados do Azure SQL através do PowerShell](sql-database-copy-powershell#copy-your-sql-database) | Neste tutorial, saiba como copiar uma base de dados do Azure SQL através do PowerShell. |
| [Copiar uma base de dados do Azure SQL utilizando o Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | Neste tutorial, saiba como copiar uma base de dados do Azure SQL utilizando o Transact-SQL. |
||||

##<a name="develop"></a>Desenvolver

Os seguintes tutoriais, irá obter informações sobre [Desenvolvimento de base de dados do SQL](sql-database-develop-overview.md) e utilizar [bibliotecas de conectividade](sql-database-libraries.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Se ligar à base de dados SQL utilizando .NET (c#)](sql-database-develop-dotnet-simple.md) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando c#. |
| [Se ligar à base de dados SQL utilizando Java](sql-database-develop-java-simple.md) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando Java. |
| [Se ligar à base de dados SQL utilizando Node.js](sql-database-develop-nodejs-simple.md) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando Node.js. |
| [Se ligar à base de dados SQL utilizando PHP](sql-database-develop-php-simple.md) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando PHP. |
| [Se ligar à base de dados SQL utilizando Python](sql-database-develop-python-simple.md) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando Python. |
| [Se ligar à base de dados SQL utilizando Rubi](sql-database-develop-ruby-simple.md) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando rubi. |
||||
 
## <a name="database-access"></a>Acesso de base de dados

Os seguintes tutoriais, irá obter informações sobre a [criação e gestão de utilizadores e inícios de sessão](sql-database-manage-logins.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Criar uma regra de firewall ao nível do servidor da base de dados do Azure SQL através do portal Azure](sql-database-configure-firewall-settings.md)  | Neste tutorial, saiba como configurar uma firewall de ao nível do servidor de base de dados SQL Azure no portal.  |
| [Criar uma regra de firewall de nível de base de dados através de Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | Neste tutorial, irá obter informações sobre como criar uma regra de firewall de nível de base de dados utilizando o Transact-SQL.|
| [Gerir regras da firewall ao nível do servidor utilizando o Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | Neste tutorial, irá obter informações sobre como gerir uma firewall de nível do servidor de base de dados do Azure SQL utilizando o Transact-SQL.|
| [Gerir regras da firewall ao nível do servidor através do PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | Neste tutorial, irá obter informações sobre como gerir uma firewall de nível do servidor de base de dados do Azure SQL através do PowerShell.|
| [Gerir regras da firewall ao nível do servidor utilizar a API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | Neste tutorial, irá aprender a gerir uma firewall de nível do servidor de base de dados do Azure SQL utilizar a API REPOR.|
| [Ligar à base de dados do SQL Azure com um início de sessão principal ao nível do servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Neste tutorial, saiba como ligar à base de dados do SQL Azure com um início de sessão principal ao nível do servidor.|
| [Conceder acesso de base de dados para um início de sessão] (sql-database-manage-logins.md#granting-database-access-to-a-login() | Neste tutorial, aprende conceder acesso a base de dados para um início de sessão ao nível do servidor.|
| [Criar novo utilizador de base de dados utilizando SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | Neste tutorial, saiba como criar um novo utilizador de base de dados numa base de dados existente utilizando SSMS.|
| [Conceder permissões de proprietário de utilizador de base de dados nova](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | Neste tutorial, aprende conceder permissões de proprietário de utilizador de base de dados existente.|
| [Ligar à base de dados do SQL Azure como um utilizador](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Neste tutorial, aprende a ligar a uma base de dados do Azure SQL utilizando uma conta de utilizador do nível de base de dados.|
||||


## <a name="data-security"></a>Segurança dos dados

Os seguintes tutoriais, irá obter informações sobre como [proteger dados da base de dados do SQL Azure](sql-database-security.md). 

| Tutorial  | Descrição  |
|---|---|---|
| [Ativar a deteção de ameaça para a base de dados através do portal Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | Neste tutorial, saiba como configurar a deteção de ameaça no portal do Azure para a base de dados.|
| [Proteger dados confidenciais uisng sempre encriptados](sql-database-always-encrypted-azure-key-vault.md) | Neste tutorial, irá utilizar o assistente sempre encriptados para proteger dados sensíveis numa base de dados Azure SQL.|
| [Proteger dados senstive utilizar encriptação de dados transparente](https://msdn.microsoft.com/library/dn948096.aspx)| Neste tutorial, saiba como utilizar a encriptação de dados transparente para proteger dados senstive.|
| [Encriptar uma coluna de dados](https://msdn.microsoft.com/library/ms179331.aspx)| Neste tutorial, aprende encriptar uma coluna de dados utilizando o Transact-SQL.|
| [Configurar o máscaras dinâmico de dados](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | Neste tutorial, saiba como configurar o máscaras dinâmico de dados para a base de dados do Azure SQL. |
||||

## <a name="business-continuity-and-query-scale-out"></a>Continuidade de negócios e a escala de opções consulta

Tutoriais seguinte, será Saiba como utilizar [Geo restauro e Geo-a replicação do Active](sql-database-business-continuity.md) para reccover de erros, para continuidade do negócio e para a escala de opções consulta.

| Tutorial  | Descrição  |
|---|---|---|
| [Restaurar uma base de dados do SQL Azure para um ponto de anterior no tempo com o Portal do Azure](sql-database-point-in-time-restore-portal.md)| Neste tutorial, aprende restaurar a base de dados para um ponto anterior no tempo através do portal Azure.|
| [Restaurar uma base de dados do SQL Azure para um ponto anterior no tempo com o PowerShell](sql-database-point-in-time-restore-powershell.md) | Neste tutorial, aprende a restaurar a base de dados para um ponto anterior no tempo através do PowerShell|
| [Restaurar uma base de dados do Azure SQL eliminado utilizando o Portal do Azure](sql-database-restore-deleted-database-portal.md) | Neste tutorial, aprende restaurar uma base de dados eliminado através do portal Azure.|
| [Restaurar uma base de dados do Azure SQL eliminada utilizando o PowerShell](sql-database-restore-deleted-database-powershell.md) | Neste tutorial, aprende restaurar uma base de dados eliminado através do PowerShell.|
| [Configurar a replicação de Geo para base de dados do SQL Azure através do portal Azure](sql-database-geo-replication-portal.md)| Neste tutorial, saiba como configurar Geo-replicação do Active através do portal Azure.|
| [Configurar a replicação de Geo para base de dados do SQL Azure através do PowerShell](sql-database-geo-replication-powershell.md)| Neste tutorial, aprende a configurar a replicação de Geo ativo através do PowerShell.|
| [Configurar a replicação de Geo para base de dados do SQL Azure utilizando o Transact-SQL](sql-database-geo-replication-transact-sql.md)| Neste tutorial, aprende a configurar a replicação de Geo ativo utilizando o Transact-SQL.|
| [Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure através do portal Azure](sql-database-geo-replication-failover-portal.md) | Neste tutorial, aprendeu como activação pós-falha a uma réplica secundária replicadas geo através do portal Azure.|
| [Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure através do PowerShell](sql-database-geo-replication-failover-powershell.md) | Neste tutorial, aprendeu como activação pós-falha a uma réplica secundária replicadas geo através do PowerShell.|
| [Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure utilizando o Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Neste tutorial, aprendeu como activação pós-falha para uma réplica secundária replicadas geo utilizando Transact-SQL.|
||||

## <a name="data-sync"></a>Sincronização de dados

Neste tutorial, irá obter informações acerca da [Sincronização de dados](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial  | Descrição  |
|---|---|---| 
| [Introdução com a sincronização de dados do Azure SQL (pré-visualização)](sql-database-get-started-sql-data-sync.md)  | Neste tutorial, aprendeu os conceitos básicos da sincronização de dados SQL Azure através do Portal clássica Azure. |
||||

## <a name="next-steps"></a>Próximos passos

[Explorar início rápido do solução de base de dados Azure SQL](sql-database-solution-quick-starts.md)
