<properties
    pageTitle="Todos os tópicos do serviço de base de dados SQL | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço Azure com o nome da base de dados de SQL que existem no http://azure.microsoft.com/documentation/articles/, título e descrição."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Todos os tópicos do serviço de base de dados do Azure SQL

Este tópico indica cada tópico que se aplica diretamente para o serviço de **Base de dados SQL** do Azure. Pode procurar esta página Web para palavras-chave utilizando **Ctrl + F**, para localizar os tópicos de interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Daxko/CSI utilizado Azure para acelerar o ciclo de desenvolvimento e para melhorar os serviços de cliente e o desempenho](sql-database-implementation-daxko.md) | Saiba mais sobre como o Daxko/CSI utiliza base de dados SQL para acelerar o ciclo de desenvolvimento e para melhorar os serviços de cliente e o desempenho |
| 2 | [Azure dá alcance global GEP e eficiência maior](sql-database-implementation-gep.md) | Saiba mais sobre como GEP utiliza a base de dados SQL para alcançar mais globais clientes e alcançar eficiência maior |
| 3 | [Com o Azure, SnelStart tem expandido rapidamente os serviços de negócio a uma taxa de 1.000 novas Azure SQL bases de dados por mês](sql-database-implementation-snelstart.md) | Saiba mais sobre como SnelStart utiliza a base de dados SQL para expandido rapidamente os seus serviços de negócio a uma taxa de 1.000 novas Azure SQL bases de dados por mês |
| 4 | [Umbraco utiliza a base de dados do SQL Azure para rapidamente aprovisionar escala de serviços de e para milhares de inquilinos na nuvem](sql-database-implementation-umbraco.md) | Saiba mais sobre como Umbraco utiliza a base de dados SQL para aprovisionar rapidamente e serviços de escala para milhares de inquilinos na nuvem |
| 5 | [Gerir a base de dados do Azure SQL com PowerShell](sql-database-manage-powershell.md) | Gestão de base de dados SQL Azure com PowerShell. |
| 6 | [Suporte SSMS para Azure AD MFA com base de dados SQL e armazém de dados SQL](sql-database-ssms-mfa-authentication.md) | Utilize a autenticação estimadas são aplicadas Multi com SSMS para base de dados SQL e armazém de dados SQL. |
| 7 | [Explicar unidades da transação da base de dados (DTUs) e flexível da base de dados as unidades da transação (eDTUs)](sql-database-what-is-a-dtu.md) | Noções sobre que Azure base de dados SQL unidade da transação é. |


## <a name="updated-articles-sql-database"></a>Artigos atualizados, base de dados SQL

Esta secção apresenta artigos que foram atualizados recentemente, onde a atualização foi grande ou significativo. Para cada artigo actualizado, é apresentado um fragmento aproximado do texto markdown adicionados. Os artigos foram atualizados no intervalo de datas do **2016-08-22** **2016-10 -**05.

| &nbsp; | Artigo | Texto actualizado, fragmento | Actualizada quando |
| --: | :-- | :-- | :-- |
| 8 | [Gerir a base de dados do Azure SQL com PowerShell](sql-database-command-line-tools.md) | Crie um grupo de recursos para os nossos base de dados SQL e recursos Azure relacionados com o cmdlet New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" novo AzureRmResourceGroup-nome $resourceGroupName-localização $resourceGroupLocation* Para obter mais informações, consulte o artigo utilizar o PowerShell Azure com o Gestor de recursos do Azure (... / powershell azure-recurso manager.md). Para obter um script de exemplo, consulte o artigo criar uma base de dados do SQL script PowerShell (sql-base de dados-get-iniciado-powershell.md create-a-sql-database-powershell-script). **Criar um servidor de base de dados SQL** Crie um servidor de base de dados SQL com o cmdlet New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Substitua *servidor1* com o nome para o seu servidor. Nomes de servidor tem de ser exclusivos por todos os servidores de base de dados do Azure SQL. Se o nome do servidor já é redirecionado, obterá um erro. Este comando poderá demorar vários minutos a concluir. O resou | 2016-09-14 |
| 9 | [Copiar uma base de dados do Azure SQL através do PowerShell](sql-database-copy-powershell.md) |   Base de dados origem SQL (os existentes da base de dados para copiar) – $sourceDbName = "DS1" $sourceDbServerName = "servidor1" $sourceDbResourceGroupName = "rg1" SQL da base de dados copiar (a nova BD para ser criada) – $copyDbName = "db1_copy" $copyDbServerName = "Servidor2" $copyDbResourceGroupName = "rg2" copiar uma base de dados no mesmo servidor – novo AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nomedoservidor $sourceDbServerName - DatabaseName $sourceDbName - CopyDatabaseName $copyDbName copiar uma base de dados para um servidor diferentes – novo AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nomedoservidor $sourceDbServerName - DatabaseName $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName copie uma base de dados para um agrupamento de base de dados flexível – $poolName = "pool1" novo AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName - nomedoservidor $sourceDbServerName - DatabaseName $sourceDbName - CopyReso | 2016-09-08 |
| 10 | [Criar um agrupamento de base de dados flexível com c#](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine ("servidor firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console.WriteLine ("Server firewall:" + fwr. FirewallRule.Id);  Console.WriteLine ("flexível conjunto de...");  ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console.WriteLine ("conjunto flexível:" + epr. ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console.WriteLine ("base de dados:" + dbr. Database.Id);  Console.WriteLine ("prima qualquer tecla para continuar...");  Console.ReadKey();  } estático ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [Script do PowerShell para identificar as bases de dados adequados para um agrupamento de base de dados flexível](sql-database-elastic-pool-database-assessment-powershell.md) | Candidatos **para flexível agrupamento de base de dados, podemos excluir o modelo global e quaisquer bases de dados que já estejam a ser um conjunto de dados. Pode adicionar mais bases de dados à lista de excluídos abaixo do conforme necessário** $ListOfDBs = Get AzureRmSqlDatabase - nomedoservidor $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-Object {$_. DatabaseName - notin ("principal") - e $_. CurrentServiceLevelObjectiveName - notin ("ElasticPool")- e $_. CurrentServiceObjectiveName - notin ("ElasticPool")} $outputConnectionString = "origem de dados = $outputServerName; segurança integrada = falso; inicial catálogo = $outputdatabaseName; Id de utilizador = $outputDBUsername; Palavra-passe = $outputDBpassword "$destinationTableName ="resource_stats_output" **criar uma tabela numa base de dados de saída para a coleção de métricas** $sql =" se NOT EXISTS (SELECIONE * a partir do sys.objects object_id onde = OBJECT_ID(N'$($destinationTableName)') e escreva na (N'U')) comece criar tabela $($destinationTableName) (nome_base_dados varchar(128), slo varchar(20), end_time datetime, avg_cpu flutuante, avg_ | 2016-09-29 |
| 12 | [Tutorial de base de dados SQL: criar uma base de dados do SQL em minutos utilizando o portal do Azure](sql-database-get-started.md) |   ! Nova base de dados do sql 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Clique em **Base de dados SQL** para abrir o pá de base de dados SQL. O conteúdo deste pá varia consoante o número das suas subscrições e os objetos existentes (como servidores existentes).  ! Nova base de dados do sql 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Na caixa de texto **nome da base de dados** , forneça um nome para a sua primeira base de dados - tal como "my-base de dados". Uma marca de verificação verde indica que forneça um nome válido.  ! Nova base de dados do sql 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Se tiver múltiplas subscrições, selecione uma subscrição. 6. Em **grupo de recursos**, clique em **Criar novo** e forneça um nome para o primeiro grupo de recursos - tal como "my--grupo de recursos". Uma marca de verificação verde indica que forneça um nome válido.  ! Nova base de dados do sql 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. Em **Selecione origem* | 2016-09-08 |
| 13 | [Experimente a base de dados SQL: Utilizar c# para criar uma base de dados do SQL com a biblioteca de base de dados SQL para .NET](sql-database-get-started-csharp.md) | **Criar um serviço principal para aceder a recursos** O seguinte script do PowerShell cria a aplicação do Active Directory (AD) e o capital de serviço que precisamos de autenticar a nossa aplicação C. O script exporta valores que precisamos para a amostra de C anterior. Para obter informações detalhadas, consulte o artigo utilizar o PowerShell Azure para criar um principal de serviço para aceder a recursos (... / recurso-grupo-autenticar-serviço-principal.md).  Inicie sessão no Azure.  AzureRmAccount adicionar se tem múltiplas subscrições, remova os comentários e definir para a subscrição que pretende trabalhar.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}" conjunto AzureRmContext - SubscriptionId $subscriptionId fornecer estes valores para a nova aplicação AAD.  $appName é o nome a apresentar para a sua aplicação, tem de ser exclusivo no seu diretório.  $uri não precisam de ser um uri real.  $secret é uma palavra-passe que cria.  $appName = "{-nome da aplicação}" $uri = "http://{app-name}" $secret = "{aplicação e palavra-passe}" Criar uma aplicação AAD $azureAdApplication = AzureRmADApp novo | 2016-09-23 |
| 14 | [Gerir bases de dados do SQL Azure através do portal Azure](sql-database-manage-portal.md) | Para ver ou atualizar as definições de base de dados, clique na definição pretendida na pá de base de dados SQL:! Definições de base de dados do SQL (. / media/sql-database-manage-portal/settings.png) **como encontrar um nome de servidor completamente qualificado de bases de dados do SQL?** Para ver o seu nome de servidor de bases de dados, clique em **Descrição geral** no pá a **base de dados SQL** e tenha em atenção o nome do servidor:! Definições de base de dados do SQL (. / media/sql-database-manage-portal/server-name.png) **como gerir regras de firewall para controlar o acesso aos meus do SQL server e a base de dados?** Para ver, criar ou atualizar as regras de firewall, clique em **Definir server firewall** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo configurar uma regra de firewall de nível do servidor de base de dados do Azure SQL utilizando o portal Azure (sql-base de dados-configurar-firewall-settings.md). ! regras de firewall (. / media/sql-database-manage-portal/sql-database-firewall.png) **como alterar a minha SQL da base de dados camada ou desempenho nível de serviço?** Para atualizar o nível de camada ou o desempenho do serviço de uma base de dados do SQL, clique em * * preços camada (s | 2016-09-20 |





## <a name="get-started"></a>Introdução

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 15 | [Constrói inquilinos com várias aplicações com base de dados do Azure SQL com isolamento e eficiência](sql-database-build-multi-tenant-apps.md) | Saiba como base de dados SQL constrói inquilinos com várias aplicações |
| 16 | [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md) | Saiba como se ligar à base de dados do SQL no Azure utilizando o SQL Server Management Studio (SSMS). Em seguida, execute uma consulta de exemplo utilizando Transact-SQL (T SQL). |
| 17 | [Se ligar à base de dados SQL utilizando .NET (c#)](sql-database-develop-dotnet-simple.md) | Utilizar o código de exemplo neste rápida de começar a criar uma aplicação moderna com c# e cópias por uma poderosa base de dados relacional na nuvem com a base de dados do SQL Azure. |
| 18 | [Criar um novo conjunto de base de dados flexível com o portal do Azure](sql-database-elastic-pool-create-portal.md) | Como adicionar um agrupamento de base de dados flexível dimensionáveis à configuração da base de dados do SQL para fácil administração e partilha através de muitas bases de dados de recursos. |
| 19 | [Explorar a tutoriais de base de dados do Azure SQL](sql-database-explore-tutorials.md) | Saiba mais sobre as funcionalidades de base de dados SQL e capacidades |
| 20 | [Tutorial de base de dados SQL: criar uma base de dados do SQL em minutos utilizando o portal do Azure](sql-database-get-started.md) | Saiba como configurar um servidor de base de dados SQL lógico, regra de firewall do servidor, base de dados SQL e dados de exemplo. Além disso, saiba como ligar com ferramentas de cliente, configure os utilizadores e configurar uma regra de firewall de base de dados. |
| 21 | [Base de dados Azure SQL protege e protege](sql-database-helps-secures-and-protects.md) | Saiba como a base de dados SQL a ajuda seguro e proteger |
| 22 | [Base de dados Azure SQL aprende &amp; adapta-se](sql-database-learn-and-adapt.md) | Saiba como base de dados SQL aprende e adapta-se |
| 23 | [Selecione uma opção do SQL Server nuvem: base de dados SQL Azure (PaaS) ou do SQL Server no Azure VMs (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Saber qual a opção do SQL Server nuvem se adequa a aplicação: base de dados SQL Azure (PaaS) ou do SQL Server na nuvem no máquinas virtuais do Azure. |
| 24 | [Escalas de base de dados SQL Azure no momento](sql-database-scale-on-the-fly.md) | Saiba como base de dados SQL escalas no momento |
| 25 | [Explorar início rápido do solução de base de dados Azure SQL](sql-database-solution-quick-starts.md) | Saiba mais sobre soluções de base de dados do Azure SQL |
| 26 | [Base de dados Azure SQL funciona no seu ambiente](sql-database-works-in-your-environment.md) | Saiba como base de dados SQL ajuda a, protege e protege |



## <a name="build-an-app"></a>Criar uma aplicação

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 27 | [Resolução de problemas, diagnosticar e evitar erros de ligação de SQL e erros breves para base de dados SQL](sql-database-connectivity-issues.md) | Saiba como resolver problemas, diagnosticar e impedir que um erro de ligação de SQL ou breves base de dados do SQL Azure.  |
| 28 | [Ligar a uma base de dados SQL com o Visual Studio](sql-database-connect-query.md) | Escreva um programa no c# para da consulta e ligar à base de dados SQL. Informações sobre os endereços IP, as cadeias de ligação, início de sessão seguro e Visual Studio gratuito. |
| 29 | [Portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md) | Ligações de cliente do ADO.NET V12 de base de dados do SQL Azure, por vezes, ignoram o proxy e interagem diretamente com a base de dados. Portas que não seja 1433 tornam-se importantes. |
| 30 | [Códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados](sql-database-develop-error-messages.md) | Saiba mais sobre códigos de erro SQL para as aplicações de cliente da base de dados SQL, tais como os erros comuns de ligação de base de dados, base de dados cópia problemas e erros gerais.  |
| 31 | [Se ligar à base de dados SQL utilizando PHP no Windows](sql-database-develop-php-simple.md) | Apresenta um programa PHP de exemplo que se liga à base de dados do SQL Azure a partir de um cliente do Windows e fornece ligações para os componentes de software necessário necessários pelo cliente. |
| 32 | [Experimente a base de dados SQL: Utilizar c# para criar uma base de dados do SQL com a biblioteca de base de dados SQL para .NET](sql-database-get-started-csharp.md) | Experimente a base de dados SQL para desenvolver aplicações SQL e c# e criar uma base de dados do SQL Azure com c# utilizando a biblioteca de base de dados SQL para .NET. |
| 33 | [Introdução ao funcionalidades JSON base de dados do SQL Azure](sql-database-json-features.md) | Base de dados SQL Azure permite-lhe análise, consulta e formatar dados em notação JavaScript objeto notação (JSON). |
| 34 | [Resolução de problemas de ligação à base de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Passos para identificar e resolver erros comuns de ligação para a base de dados do SQL Azure. |
| 35 | [Erro "Base de dados no servidor não está disponível neste momento" ao ligar a base de dados sql](sql-database-troubleshoot-connection.md) | Resolução de problemas da base de dados no servidor não está atualmente disponível erro quando uma aplicação liga à base de dados SQL. |



## <a name="develop"></a>Desenvolver

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 36 | [Obtenha os valores necessários para autenticar uma aplicação para aceder à base de dados SQL do código](sql-database-client-id-keys.md) | Crie um principal de serviço para aceder a base de dados SQL do código. |
| 37 | [Se ligar à base de dados SQL utilizando Java com JDBC no Windows](sql-database-develop-java-simple.md) | Apresenta um exemplo de código Java que pode utilizar para ligar à base de dados do SQL Azure. O exemplo utiliza JDBC e é executado no computador de cliente do Windows. |
| 38 | [Se ligar à base de dados SQL utilizando Node.js](sql-database-develop-nodejs-simple.md) | Apresenta um exemplo de código Node.js que pode utilizar para ligar à base de dados do SQL Azure. |
| 39 | [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md) | Saiba mais sobre bibliotecas de conectividade disponíveis e as melhores práticas para ligar à base de dados SQL de aplicações. |
| 40 | [Se ligar à base de dados SQL utilizando Python](sql-database-develop-python-simple.md) | Apresenta um exemplo de código Python que pode utilizar para ligar à base de dados do SQL Azure. |
| 41 | [Se ligar à base de dados SQL utilizando Rubi](sql-database-develop-ruby-simple.md) | Dar um exemplo de código Rubi que pode executar para ligar à base de dados do SQL Azure. |
| 42 | [Introdução ao Temporal tabelas na base de dados Azure SQL](sql-database-temporal-tables.md) | Saiba como começar a utilizar tabelas Temporal na base de dados do SQL Azure. |



## <a name="performance-and-scale"></a>Desempenho e a escala

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 43 | [Advisor de base de dados SQL](sql-database-advisor.md) | A classificação de base de dados do SQL Azure fornece recomendações para as bases de dados SQL existente que podem melhorar o desempenho da consulta atual. |
| 44 | [Advisor de base de dados SQL Azure no portal](sql-database-advisor-portal.md) | Pode utilizar a classificação de base de dados do SQL Azure no portal do Azure para rever e implementar recomendações para as bases de dados SQL existente que podem melhorar o desempenho da consulta atual. |
| 45 | [Descrição geral de referência de base de dados SQL Azure](sql-database-benchmark-overview.md) | Este tópico descreve referência de base de dados do SQL Azure utilizados para medir o desempenho da base de dados do SQL Azure. |
| 46 | [Quando deve ser utilizado um agrupamento de base de dados flexível?](sql-database-elastic-pool-guidance.md) | Um agrupamento de base de dados flexível é uma coleção de recursos disponíveis que são partilhados por um grupo de bases de dados flexível. Este artigo fornece orientações para ajudar a adequabilidade do utilizando um agrupamento de base de dados flexível para um grupo de bases de dados. |
| 47 | [Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md) | EXPLICAÇÃO básica da funcionalidade de ferramentas da base de dados flexível da base de dados do SQL Azure, incluindo mais facilmente para executar a aplicação de exemplo. |
| 48 | [Base de dados do SQL perguntas mais frequentes](sql-database-faq.md) | Perguntar respostas aos clientes de perguntas comuns sobre bases de dados na nuvem e base de dados do SQL Azure, sistema de gestão de base de dados relacional da Microsoft (RDBMS) e base de dados como um serviço na nuvem. |
| 49 | [Introdução na memória (pré-visualização) na base de dados SQL](sql-database-in-memory.md) | Tecnologias de SQL na memória melhorar significativamente o desempenho da transaccionais e das cargas de trabalho de análise. Saiba como tirar partido destas tecnologias. |
| 50 | [Utilização de memória OLTP (pré-visualização) para melhorar o desempenho da aplicação na base de dados SQL](sql-database-in-memory-oltp-migration.md) | Adoptem na memória OLTP para melhorar o desempenho transaccional numa base de dados SQL existente. |
| 51 | [Armazenamento de na memória OLTP monitor](sql-database-in-memory-oltp-monitoring.md) | Estimativa e monitor armazenamento incorporado na memória XTP utilizarem, a capacidade de; resolver o erro de capacidade 41823 |
| 52 | [Funcionamento da loja de consulta numa base de dados do Azure SQL](sql-database-operate-query-store.md) | Saiba como trabalhar com o arquivo de consulta numa base de dados do Azure SQL |
| 53 | [Conhecimentos aprofundados de desempenho de base de dados SQL](sql-database-performance.md) | A base de dados do SQL Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas que podem melhorar o desempenho da consulta atual. |
| 54 | [Base de dados SQL Azure e o desempenho para bases de dados simples](sql-database-performance-guidance.md) | Este artigo pode ajudar a determinar qual camada de serviços para escolher para a sua aplicação. Recomenda também formas para a aplicação para obter o máximo partido da base de dados do SQL Azure o gráfico. |
| 55 | [Conhecimentos aprofundados do desempenho de consulta de base de dados do Azure SQL](sql-database-query-performance.md) | Monitorização de desempenho de consulta identifica a maior parte das consultas consumir CPU para uma base de dados do SQL Azure. |
| 56 | [Alterar o serviço camada e desempenho nível (preços camada) de uma base de dados SQL](sql-database-scale-up.md) | Alterar a camada de serviço e nível de desempenho de uma base de dados do Azure SQL mostra como dimensionar a base de dados do SQL para cima ou para baixo. Alterar a camada comparar da base de dados Azure SQL. |
| 57 | [Monitorizar o desempenho da base de dados numa base de dados do Azure SQL](sql-database-single-database-monitor.md) | Saiba mais sobre as opções para a sua base de dados com ferramentas Azure e vistas dinâmicas de gestão de monitorização. |
| 58 | [Sugestões de sintonização de desempenho de base de dados SQL](sql-database-troubleshoot-performance.md) | Sugestões para optimização da base de dados do SQL Azure através de avaliação e melhoria de desempenho. |
| 59 | [Como utilizar lotes para melhorar o desempenho da aplicação de base de dados SQL](sql-database-use-batching-to-improve-performance.md) | O tópico fornece provas esse lotes operações de base de dados imroves significativamente a velocidade e escalabilidade das suas aplicações de base de dados do Azure SQL. Apesar destas técnicas lotes ideal para quaisquer bases de dados do SQL Server, o foco do artigo estiver no Azure. |



## <a name="tools-for-scaling-out"></a>Ferramentas para o dimensionamento saída

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 60 | [Estruturar padrões para multi-inquilino SaaS aplicações e base de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Este artigo aborda os requisitos e comuns padrões de arquitectura de dados da base de dados multi-inquilino aplicações em execução num ambiente nuvem necessitam ter em consideração e as responsabilidades vários associadas com estes padrões. Também explica como base de dados SQL Azure, com as respetivas flexível agrupamentos e flexível ferramentas, ajudar a solucionar estes requisitos de uma forma não compromisso. |
| 61 | [Migrar bases de dados existentes para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md) | Converter bases de dados sharded utilizar ferramentas de base de dados flexível criando um shard Gestor de mapa |
| 62 | [Edifício nuvem dimensionáveis as bases de dados](sql-database-elastic-database-client-library.md) | Criar aplicações de base de dados .NET dimensionáveis com a biblioteca do cliente da base de dados flexível |
| 63 | [Contadores de desempenho para o Gestor de mapa shard](sql-database-elastic-database-perf-counters.md) | ShardMapManager escolares e dados dependentes encaminhamento contadores de desempenho |
| 64 | [Adicionar um shard utilizando ferramentas de base de dados flexível](sql-database-elastic-scale-add-a-shard.md) | Como utilizar flexível escala APIs para adicionar novos shards a um shard definir. |
| 65 | [Implementar um serviço de intercalação de divisão](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Dividir e unir com ferramentas de base de dados flexível |
| 66 | [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) | Como utilizar a classe de ShardMapManager nas aplicações do .NET para dependentes de dados encaminhamento, uma funcionalidade flexível bases de dados para a base de dados do SQL Azure |
| 67 | [Ferramentas de base de dados flexível perguntas mais frequentes](sql-database-elastic-scale-faq.md) | Perguntas mais frequentes sobre a escala de flexível de base de dados do Azure SQL. |
| 68 | [Glossário de ferramentas da base de dados flexível](sql-database-elastic-scale-glossary.md) | Explicação dos termos utilizados para obter ferramentas de base de dados flexível |
| 69 | [Dimensionamento saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md) | Software como um programadores de serviço (SaaS) pode facilmente criar bases de dados flexível, dimensionáveis na nuvem utilizando estas ferramentas |
| 70 | [Credenciais utilizadas para aceder à biblioteca de cliente da base de dados flexível](sql-database-elastic-scale-manage-credentials.md) | Como configurar o nível adequado de credenciais de administrador para só de leitura, para as aplicações de base de dados flexível |
| 71 | [Consultar várias shard](sql-database-elastic-scale-multishard-querying.md) | Execute consultas em várias shards utilizando a biblioteca de cliente flexível da base de dados. |
| 72 | [Mover dados entre bases de dados de saída dimensionada nuvem](sql-database-elastic-scale-overview-split-and-merge.md) | Explica como manipular shards e mover dados através de um serviço alojado personalizada utilizando APIs da base de dados flexível. |
| 73 | [Dimensionar saída bases de dados com o Gestor de mapa shard](sql-database-elastic-scale-shard-map-management.md) | Como utilizar ShardMapManager, a biblioteca do cliente da base de dados flexível |
| 74 | [Configuração de segurança de intercalação de divisão](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurar o x409 certificados para encriptação |
| 75 | [Atualizar uma aplicação para utilizar a biblioteca de cliente flexível da base de dados mais recente](sql-database-elastic-scale-upgrade-client-library.md) | Atualizar aplicações e bibliotecas através do Nuget |
| 76 | [Flexível biblioteca de base de dados do cliente com entidade Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Utilizar a base de dados flexível cliente biblioteca entidade Framework para codificação de bases de dados |
| 77 | [Utilizar a biblioteca do cliente da base de dados flexível com Dapper](sql-database-elastic-scale-working-with-dapper.md) | Utilizar a biblioteca do cliente da base de dados flexível com Dapper. |
| 78 | [Aplicações do inquilinos com várias ferramentas de base de dados flexível e segurança de nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Saiba como utilizar ferramentas de base de dados flexível juntamente com a segurança de nível de linha para criar uma aplicação com uma camada altamente dimensionáveis dados numa base de dados do SQL Azure que suporte inquilinos com várias shards. |



## <a name="elastic-jobs"></a>Tarefas de flexível

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 79 | [Criar e gerir dimensionadas saída bases de dados do SQL Azure com tarefas flexível (pré-visualização)](sql-database-elastic-jobs-create-and-manage.md) | Guiá-lo através da criação e gestão de uma tarefa de base de dados flexível. |
| 80 | [Começar a trabalhar com tarefas de base de dados flexível](sql-database-elastic-jobs-getting-started.md) | como utilizar tarefas de base de dados flexível |
| 81 | [Gerir bases de dados de saída dimensionada nuvem](sql-database-elastic-jobs-overview.md) | Ilustra o serviço de tarefa de base de dados flexível |
| 82 | [Criar e gerir tarefas de base de dados flexível uma base de dados SQL através do PowerShell (pré-visualização)](sql-database-elastic-jobs-powershell.md) | PowerShell utilizado para gerir conjuntos de dados da base de dados do Azure SQL |
| 83 | [Descrição geral de tarefas da base de dados flexível da instalação](sql-database-elastic-jobs-service-installation.md) | Guiá-lo através da instalação da funcionalidade flexível tarefa. |
| 84 | [Desinstalar componentes de tarefas da base de dados flexível](sql-database-elastic-jobs-uninstall.md) | Como desinstalar a ferramenta de tarefas da base de dados flexível |



## <a name="elastic-pools"></a>Conjuntos de dados flexível

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 85 | [O que é um agrupamento de flexível Azure?](sql-database-elastic-pool.md) | Gerir centenas ou milhares de bases de dados utilizando um conjunto de dados. Um preço de um conjunto de unidades de desempenho pode ser distribuído ao longo do conjunto. Deslocar-se de bases de dados ou para fora, irão. |
| 86 | [Criar um agrupamento de base de dados flexível com c#](sql-database-elastic-pool-create-csharp.md) | Utilize técnicas de desenvolvimento c# da base de dados para criar um conjunto de base de dados flexível dimensionáveis base de dados do SQL Azure, para que possa partilhar recursos através de muitas bases de dados. |
| 87 | [Criar um novo conjunto de base de dados flexível com PowerShell](sql-database-elastic-pool-create-powershell.md) | Saiba como utilizar o PowerShell para fora de escala de base de dados do SQL Azure recursos através da criação de um conjunto de base de dados flexível dimensionáveis para gerir a várias bases de dados. |
| 88 | [Script do PowerShell para identificar as bases de dados adequados para um agrupamento de base de dados flexível](sql-database-elastic-pool-database-assessment-powershell.md) | Um agrupamento de base de dados flexível é uma coleção de recursos disponíveis que são partilhados por um grupo de bases de dados flexível. Este artigo fornece um script Powershell para o ajudar a adequabilidade do utilizando um agrupamento de base de dados flexível para um grupo de bases de dados. |
| 89 | [Monitorizar e gerir um conjunto de base de dados flexível com c#](sql-database-elastic-pool-manage-csharp.md) | Utilize técnicas de desenvolvimento c# da base de dados para gerir um agrupamento de base de dados flexível de base de dados do Azure SQL. |
| 90 | [Monitorizar e gerir um conjunto de base de dados flexível com o portal do Azure](sql-database-elastic-pool-manage-portal.md) | Saiba como utilizar o portal do Azure e intelligence incorporada da base de dados de SQL para gerir, monitorizar e à direita de tamanho de um conjunto de base de dados flexível dimensionáveis para otimizar o desempenho da base de dados e gerir o custo. |
| 91 | [Monitorizar e gerir um conjunto de base de dados flexível com PowerShell](sql-database-elastic-pool-manage-powershell.md) | Saiba como utilizar o PowerShell para gerir um agrupamento de base de dados flexível. |
| 92 | [Monitorizar e gerir um conjunto de base de dados flexível com Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Utilize T-SQL para criar uma base de dados do Azure SQL num conjunto de dados flexível. Ou utilize T-SQL para mover a datbase e terminar conjuntos de dados. |
| 93 | [Faturação do conjunto de base de dados flexível e informação de preços](sql-database-elastic-pool-price.md) | Informação de preços específico de agrupamentos de base de dados flexível. |



## <a name="elastic-query"></a>Consulta flexível

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 94 | [Comunicar através de bases de dados de saída dimensionada nuvem (pré-visualização)](sql-database-elastic-query-getting-started.md) | como utilizar cruzada consultas de base de dados da base de dados |
| 95 | [Começar a trabalhar com consultas de base de dados em várias (partições vertical) (pré-visualização)](sql-database-elastic-query-getting-started-vertical.md) | como utilizar a consulta de base de dados flexível com bases de dados na vertical com a partições |
| 96 | [Reportar por toda a bases de dados de saída dimensionada nuvem (pré-visualização)](sql-database-elastic-query-horizontal-partitioning.md) | como configurar consultas flexível sobre a partições horizontais |
| 97 | [Azure SQL base de dados da base de dados flexível descrição geral das consultas (pré-visualização)](sql-database-elastic-query-overview.md) | Descrição geral da funcionalidade flexível consulta |
| 98 | [Consulta em bases de dados na nuvem com diferentes esquemas (pré-visualização)](sql-database-elastic-query-vertical-partitioning.md) | como configurar consultas de base de dados em várias sobre a partições verticais |



## <a name="elastic-transaction"></a>Transação flexível

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 99 | [Transações distribuídas ao longo de bases de dados da nuvem](sql-database-elastic-transactions-overview.md) | Descrição geral das transações de base de dados flexível com base de dados Azure SQL |



## <a name="backup-and-recovery"></a>Recuperação e cópias de segurança

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 100 | [Cópias de segurança da base de dados SQL](sql-database-automated-backups.md) | Saiba mais sobre incorporadas da base de dados do SQL cópias de segurança da base de dados que permitem-lhe a filmar novamente uma base de dados do SQL Azure para um ponto anterior tempo ou copiar uma base de dados para uma nova base de dados numa região geográfica (até 35 dias). |
| 101 | [Descrição geral de continuidade do negócio com base de dados do SQL Azure](sql-database-business-continuity.md) | Saiba como base de dados do SQL Azure suporta continuidade do negócio na nuvem e recuperação de base de dados e ajuda a manter aplicações nuvem críticos em execução. |
| 102 | [Como restaurar uma única tabela a partir de uma cópia de segurança da base de dados do Azure SQL](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Saiba como restaurar uma única tabela a partir da cópia de segurança da base de dados do Azure SQL. |
| 103 | [Estruturar uma aplicação para a recuperação de falhas de nuvem Geo-a replicação do Active a utilizar na base de dados SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Saiba como estruturar soluções de recuperação de falhas de nuvem planeamento de negócio continuidade utilizando a replicação de geo para cópia de segurança de dados de aplicação com base de dados do SQL Azure. |
| 104 | [Restaurar uma base de dados do SQL Azure ou activação pós-falha para um secundário](sql-database-disaster-recovery.md) | Saiba como recuperar uma base de dados a partir de uma falha do Centro de dados regional do ou falha com Azure SQL da base de dados ativo Geo-replicação e capacidades de Geo restaurar. |
| 105 | [Efetuar desagregação de recuperação de falhas](sql-database-disaster-recovery-drills.md) | Saiba a orientação e práticas recomendadas para utilizar a base de dados do SQL Azure para efetuar Brocas de recuperação de falhas que o irão ajudar mantêm o seu missão crítico aplicações empresariais e são e falhas de corrente. |
| 106 | [Estratégias de recuperação de falhas para aplicações utilizando agrupamento flexível de base de dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Saiba como estruturar a solução na nuvem para recuperação de falhas ao selecionar o padrão de activação pós-falha à direita. |
| 107 | [Utilizando a classe de RecoveryManager para corrigir os problemas de mapa shard](sql-database-elastic-database-recovery-manager.md) | Utilizar a classe de RecoveryManager para resolver problemas com o shard mapas |
| 108 | [Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL](sql-database-import.md) | Crie uma base de dados do Azure SQL ao importar um ficheiro BACPAC existente. |
| 109 | [Restaurar uma base de dados do SQL Azure para um ponto anterior no tempo com o Portal do Azure](sql-database-point-in-time-restore-portal.md) | Restaure uma base de dados do SQL Azure para um ponto anterior no tempo. |
| 110 | [Restaurar uma base de dados do SQL Azure para um ponto anterior no tempo com o PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurar uma base de dados do SQL Azure para um ponto anterior no tempo |
| 112 | [Recuperar uma base de dados do Azure SQL com cópias de segurança da base de dados automática](sql-database-recovery-using-backups.md) | Saiba mais sobre restaurar em qualquer altura, que permite-lhe recuperar uma base de dados do SQL Azure um ponto anterior no tempo (até 35 dias). |
| 113 | [Restaurar uma base de dados do Azure SQL eliminada utilizando o Portal do Azure](sql-database-restore-deleted-database-portal.md) | Restaure uma base de dados do Azure SQL eliminado (Azure Portal). |
| 114 | [Restaurar uma base de dados do SQL Azure eliminadas utilizando o PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaure uma base de SQL Azure eliminados (PowerShell). |
| 115 | [Restaurar uma base de dados para um ponto anterior no tempo, restaurar uma base de dados eliminado ou recuperar a partir de uma falha do Centro de dados](sql-database-troubleshoot-backup-and-restore.md) | Saiba como recuperar uma base de dados da nuvem de erros e falhas de utilizar cópias de segurança e réplicas numa base de dados do SQL Azure. |



## <a name="migrate"></a>Migrar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 116 | [Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure base de dados SQL, migração de base de dados, exportar base de dados, Exportar ficheiro BACPAC, sqlpackage |
| 117 | [Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure base de dados SQL, migração de base de dados, exportar base de dados, exportar BACPAC Assistente de ficheiro, exportar aplicação de camadas de dados |
| 118 | [Importar para a base de dados SQL a partir de um ficheiro BACPAC com SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure base de dados SQL, migração de base de dados, importar base de dados, importar ficheiro BACPAC, sqlpackage |
| 119 | [Importar a partir de BACPAC a base de dados SQL utilizando SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Base do Microsoft Azure SQL, base de dados implementar, migração de base de dados, base de dados de importar, exportar base de dados, o Assistente de migração |
| 120 | [Migrar a base de dados do SQL Server para base de dados SQL utilizando implementar base de dados ao Assistente de base de dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure base de dados SQL, migração de base de dados, o Assistente de base de dados do Microsoft Azure |
| 121 | [Migrar a base de dados do SQL Server para base de dados do SQL Azure utilizando a replicação transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure base de dados SQL, migração de base de dados, importar base de dados, replicação transaccional |
| 122 | [Determinar a compatibilidade de base de dados SQL utilizando SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade de base de dados SQL, SqlPackage |
| 123 | [Utilizar o SQL Server Management Studio para compatibilidade determinar a base de dados do SQL antes da migração para a base de dados do SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade de base de dados SQL, exportar dados camada de aplicação Assistente |
| 124 | [Utilizar o Assistente de migração do SQL Azure para problemas de compatibilidade de base de dados SQL Server corrigir antes da migração para a base de dados do SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade, o Assistente de migração do SQL Azure |
| 125 | [Migrar uma base de dados do SQL Server para a base de dados do Azure SQL com ferramentas de dados do SQL Server para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade, o Assistente de migração do SQL Azure, SSDT |
| 126 | [Corrigir problemas de compatibilidade de base de dados do SQL Server utilizando o SQL Server Management Studio antes da migração para a base de dados SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade, o Assistente de migração do SQL Azure |
| 127 | [Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o PowerShell](sql-database-export-powershell.md) | Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o PowerShell |
| 128 | [Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL utilizando o PowerShell](sql-database-import-powershell.md) | Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL utilizando o PowerShell |
| 129 | [Carregar dados a partir de CSV para armazém de dados do SQL Azure (ficheiros simples)](sql-database-load-from-csv-with-bcp.md) | Para um tamanho de dados pequenas utiliza bcp para importar dados para a base de dados do SQL Azure. |
| 130 | [Mover bases de dados entre servidores, entre subscrições e e terminar Azure](sql-database-troubleshoot-moving-data.md) | Passos rápidos para copiar, mover e migrar dados e bases de dados na base de dados do SQL Azure. |



## <a name="move-data-in-and-out"></a>Mover os dados e reduzir

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 131 | [Migração de base de dados do SQL Server para a base de dados do SQL na nuvem](sql-database-cloud-migrate.md) | Saiba mais sobre como no local do SQL Server da base de dados migração para base de dados do SQL Azure na nuvem. Utilize ferramentas de migração de base de dados para testar a compatibilidade antes de migração de base de dados. |
| 132 | [Copiar uma base de dados do Azure SQL](sql-database-copy.md) | Criar uma cópia da base de dados Azure SQL |
| 133 | [Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o Portal do Azure](sql-database-export.md) | Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o Portal do Azure |



## <a name="security"></a>Segurança

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 134 | [Ligar a base de dados SQL ou armazém de dados SQL utilizando a autenticação do Azure Active Directory](sql-database-aad-authentication.md) | Saiba como se ligar à base de dados SQL utilizando a autenticação do Azure Active Directory. |
| 135 | [Sempre encriptadas: Proteger dados sensíveis na base de dados SQL e armazenar o seu chaves de encriptação no arquivo de certificados do Windows](sql-database-always-encrypted.md) | Proteger dados sensíveis na base de dados SQL em minutos. |
| 136 | [Sempre encriptados: Proteger dados sensíveis na base de dados SQL e armazenar das chaves de encriptação no Azure chave Cofre](sql-database-always-encrypted-azure-key-vault.md) | Proteger dados sensíveis na base de dados SQL em minutos. |
| 137 | [Base de dados SQL - suporte de clientes e alterações de ponto final IP de auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Saiba mais sobre o suporte de clientes do nível inferior de base de dados SQL e IP alterações de ponto final para auditoria. |
| 138 | [Configurar as regras da firewall ao nível do servidor de base de dados do Azure SQL utilizando o PowerShell](sql-database-configure-firewall-settings-powershell.md) | Saiba como configurar a firewall para endereços IP que acedem a bases de dados Azure SQL. |
| 139 | [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL utilizar a API REST](sql-database-configure-firewall-settings-rest.md) | Saiba como configurar a firewall para endereços IP que acedem a bases de dados Azure SQL. |
| 140 | [Configurar regras de firewall ao nível do servidor e ao nível da base de dados de base de dados do Azure SQL utilizando o T-SQL](sql-database-configure-firewall-settings-tsql.md) | Saiba como configurar a firewall para endereços IP que acedem a bases de dados Azure SQL. |
| 141 | [Introdução ao SQL da base de dados dinâmicos dados Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md) | Como começar com SQL da base de dados dinâmicos dados Masking no Portal do Azure |
| 142 | [Introdução ao SQL da base de dados dinâmicos dados Masking (Azure clássica Portal)](sql-database-dynamic-data-masking-get-started-portal.md) | Como começar com SQL da base de dados dinâmicos dados Masking no Portal clássica do Azure |
| 143 | [Configurar regras de firewall de base de dados do Azure SQL \- descrição geral](sql-database-firewall-configure.md) | Saiba como configurar uma firewall de base de dados SQL com as regras da firewall ao nível do servidor e ao nível da base de dados para gerir o acesso. |
| 144 | [Tutorial de base de dados SQL: criar contas de utilizador para aceder e gerir uma base de dados de base de dados SQL](sql-database-get-started-security.md) | Saiba como criar contas de utilizador para aceder e gerir uma base de dados. |
| 145 | [Autenticação de base de dados do SQL e autorização: conceder acesso](sql-database-manage-logins.md) | Saiba mais sobre a gestão de segurança da base de dados SQL, especificamente como gerir a segurança do access e o início de sessão da base de dados através da conta principal ao nível do servidor. |
| 146 | [Diretrizes de segurança de base de dados SQL Azure e limitações](sql-database-security-guidelines.md) | Saiba mais sobre as diretrizes de base de dados do Microsoft Azure SQL e limitações relacionadas com a segurança. |
| 147 | [Introdução ao deteção de ameaça de base de dados SQL](sql-database-threat-detection-get-started.md) | Como começar a deteção de ameaça de base de dados do SQL no Portal do Azure |
| 148 | [Como efetuar tarefas administrativas comuns, tais como repor palavra-passe de administrador na base de dados do SQL Azure](sql-database-troubleshoot-permissions.md) | Descreve como executar tarefas administrativas comuns numa base de dados SQL. Por exemplo, a reposição de palavra-passe de administrador, conceder e a remover o acesso. |



## <a name="manage-your-database"></a>Gerir a sua base de dados

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 149 | [Introdução ao auditoria de base de dados SQL](sql-database-auditing-get-started.md) | Introdução ao auditoria de base de dados SQL |
| 150 | [Configurar uma regra de firewall de nível do servidor de base de dados do Azure SQL utilizando o Portal do Azure](sql-database-configure-firewall-settings.md) | Saiba como configurar a firewall para endereços IP aceder Azure SQL server. |
| 151 | [Copiar uma base de dados do SQL Azure através do portal Azure](sql-database-copy-portal.md) | Criar uma cópia da base de dados Azure SQL |
| 152 | [Gerir bases de dados do SQL Azure utilizando a automatização do Azure](sql-database-manage-automation.md) | Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir bases de dados do Azure SQL na escala. |
| 153 | [Descrição geral: ferramentas de gestão para a base de dados SQL](sql-database-manage-overview.md) | Compara as ferramentas e opções para gerir a base de dados do SQL Azure |
| 154 | [Base de dados do SQL Azure utilizando vistas dinâmicas de gestão de monitorização](sql-database-monitoring-with-dmvs.md) | Saiba como detetar e diagnosticar problemas de desempenho comuns utilizando vistas de gestão de dinâmica para monitorizar a base de dados do Microsoft Azure SQL. |
| 155 | [Proteger a sua base de dados SQL](sql-database-security.md) | Saiba mais sobre segurança de base de dados do SQL Azure e o SQL Server, incluindo as diferenças entre a nuvem e do SQL Server no local quando chegar para autenticação, autorização, segurança de ligação, encriptação e conformidade. |



## <a name="extended-events"></a>Eventos expandidos

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 156 | [Código de destino do ficheiro de evento para eventos adicional na base de dados SQL](sql-database-xevent-code-event-file.md) | Fornece PowerShell e Transact-SQL para uma amostra de código de duas fases que demonstrará o destino de ficheiro de eventos num evento adicional na base de dados do SQL Azure. Armazenamento Azure é uma parte obrigatória deste cenário. |
| 157 | [Código de destino de memória intermédia para eventos adicional na base de dados SQL de toque](sql-database-xevent-code-ring-buffer.md) | Fornece uma amostra de código Transact-SQL que fez fácil e rápida através da utilização de destino memória intermédia em anel, base de dados do SQL Azure. |
| 158 | [Eventos adicional na base de dados SQL](sql-database-xevent-db-diff-from-svr.md) | Descreve eventos adicional (XEvents) na base de dados do SQL Azure e como sessões de eventos mudar ligeiramente consoante de sessões de eventos no Microsoft SQL Server. |



## <a name="geo-replication"></a>Geo replicação

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 159 | [Iniciar uma activação pós-falha planeado ou não planeado para base de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md) | Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure através do portal Azure |
| 160 | [Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com PowerShell](sql-database-geo-replication-failover-powershell.md) | Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure através do PowerShell |
| 161 | [Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure utilizando o Transact-SQL |
| 162 | [Descrição geral: Da base de dados ativa Geo-a replicação SQL](sql-database-geo-replication-overview.md) | Replicação do Geo Active permite-lhe 4 réplicas da base de dados em qualquer um dos Azure os centros de dados de configuração. |
| 163 | [Configurar a replicação de Geo para base de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-portal.md) | Configurar a replicação de Geo para base de dados do SQL Azure através do portal Azure |
| 164 | [Configurar a replicação de Geo para base de dados do Azure SQL com PowerShell](sql-database-geo-replication-powershell.md) | Configurar a replicação de Geo ativa para a base de dados do SQL Azure através do PowerShell |
| 165 | [Como gerir segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md) | Este tópico explica considerações de segurança para gerir a segurança depois de restaurar uma base de dados ou uma activação pós-falha. |
| 166 | [Configurar a replicação de Geo para base de dados do Azure SQL com o Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurar a replicação de Geo para base de dados do SQL Azure utilizando o Transact-SQL |
| 167 | [Geo-restaurar uma base de dados do SQL Azure a partir de uma cópia de segurança geo redundantes utilizando o Portal do Azure](sql-database-geo-restore-portal.md) | Geo restaurar uma base de dados do SQL Azure a partir de uma cópia de segurança geo redundantes (Azure Portal). |
| 168 | [Restaurar uma base de dados do SQL Azure a partir de uma cópia de segurança geo redundantes utilizando o PowerShell](sql-database-geo-restore-powershell.md) | Restaurar uma base de dados do SQL Azure para um novo servidor a partir de uma cópia de segurança geo redundantes |



## <a name="upgrade"></a>Atualização

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 169 | [Melhorado desempenho da consulta com compatibilidade 130 de nível de base de dados do SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Passos e ferramentas para determinar qual o nível de compatibilidade é mais adequado para a base de dados na base de dados do SQL Azure ou Microsoft SQL Server |
| 170 | [Atualiza os de ficha técnica em gestão de aplicações na nuvem através da base de dados ativo Geo-a replicação SQL](sql-database-manage-application-rolling-upgrade.md) | Saiba como utilizar a base de dados do Azure SQL geo-replicação para suportar atualiza os onlinehttps da sua aplicação na nuvem. |
| 171 | [Atualização para o V12 de base de dados do SQL Azure através do portal Azure](sql-database-upgrade-server-portal.md) | Explica como atualizar para V12 de base de dados do SQL Azure, incluindo como atualizar as bases de dados Web e empresas e como atualizar um servidor de V11 migrar as suas bases de dados diretamente para um agrupamento de base de dados flexível utilizando o portal do Azure. |
| 172 | [Atualização para o V12 de base de dados do SQL Azure através do PowerShell](sql-database-upgrade-server-powershell.md) | Explica como atualizar para V12 de base de dados do SQL Azure, incluindo como atualizar as bases de dados Web e empresas e como atualizar um servidor de V11 migrar as suas bases de dados diretamente para um agrupamento de base de dados flexível através do PowerShell. |
| 173 | [Planear e preparar-se para atualizar para o V12 de base de dados SQL](sql-database-v12-plan-prepare-upgrade.md) | Descreve as preparações e limitações envolvidos na atualizar para a versão V12 do Azure SQL da base de dados. |
| 174 | [O que há de novo no V12 de base de dados SQL](sql-database-v12-whats-new.md) | Descreve por que motivo serão vantajoso para sistemas de profissionais que estiver a utilizar a base de dados do Azure SQL na nuvem ao atualizar para versão V12 agora. |
| 175 | [FAQ-sol Web e Business Edition](sql-database-web-business-sunset-faq.md) | Descubra quando as bases de dados Web de SQL Azure e empresas irão foi removidas e saiba mais sobre as funcionalidades e das novas camadas de serviço. |



## <a name="tools"></a>Ferramentas

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 176 | [Gerir a base de dados do Azure SQL com PowerShell](sql-database-command-line-tools.md) | Gestão de base de dados SQL Azure com PowerShell. |
| 177 | [Tutorial de base de dados SQL: ligar o Excel para uma base de dados do Azure SQL e criar um relatório](sql-database-connect-excel.md) | Saiba como ligar o Microsoft Excel a base de dados do Azure SQL na nuvem. Importar dados para o Excel para obter informações detalhadas dados e relatórios. |
| 178 | [Criar uma base de dados do SQL e executar tarefas comuns de configuração de base de dados com os cmdlets do PowerShell](sql-database-get-started-powershell.md) | Saiba como criar uma base de dados do SQL com PowerShell. Tarefas comuns de configuração da base de dados podem ser geridas durante os cmdlets do PowerShell. |
| 179 | [Introdução com a sincronização de dados do Azure SQL (pré-visualização)](sql-database-get-started-sql-data-sync.md) | Neste tutorial ajuda-na começar a trabalhar com a sincronização de dados do SQL Azure (pré-visualização). |
| 180 | [Gerir a base de dados do SQL Azure com o SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Saiba como utilizar o SQL Server Management Studio para gerir os servidores de base de dados SQL e bases de dados. |
| 181 | [Gerir bases de dados do SQL Azure através do portal Azure](sql-database-manage-portal.md) | Saiba como utilizar o Portal do Azure para gerir uma base de dados relacional na nuvem através do Portal Azure. |
| 182 | [Alterar o serviço camada e desempenho nível (preços camada) de uma base de dados SQL com PowerShell](sql-database-scale-up-powershell.md) | Alterar a camada de serviço e nível de desempenho de uma base de dados do Azure SQL mostra como dimensionar a base de dados do SQL para cima ou para baixo com o PowerShell. Alterar a camada comparar da base de dados Azure SQL com PowerShell. |
| 183 | [Utilizar o SQL Server Management Studio no Azure RemoteApp para ligar à base de dados SQL](sql-database-ssms-remoteapp.md) | Utilize este tutorial para saber como utilizar o SQL Server Management Studio no Azure RemoteApp para maior segurança e desempenho quando ligar à base de dados SQL |



## <a name="reference"></a>Referência

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 184 | [Bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md) | Apresenta o número de versão mínima para cada controlador que os programas de cliente podem utilizar para ligar à base de dados do SQL Azure ou ao Microsoft SQL Server. Uma ligação é fornecida para informações de versões dos controladores disponibilizadas por Comunidade em vez de Microsoft. |
| 185. | [Limites de recursos base de dados SQL Azure](sql-database-resource-limits.md) | Esta página descreve alguns limites de recursos comuns para a base de dados do SQL Azure. |
| 186 | [Diferenças de base de dados de SQL Transact-SQL Azure](sql-database-transact-sql-information.md) | Declarações de Transact-SQL que são menos de totalmente suportadas na base de dados do SQL Azure |



## <a name="miscellaneous"></a>Diversas

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 187 | [Copiar uma base de dados do Azure SQL através do PowerShell](sql-database-copy-powershell.md) | Criar cópia de uma base de dados do Azure SQL através do PowerShell |
| 188 | [Copiar uma base de dados do Azure SQL utilizando o Transact-SQL](sql-database-copy-transact-sql.md) | Criar cópia de uma base de dados do Azure SQL utilizando o Transact-SQL |
| 189 | [Limitações de geral de base de dados do Azure SQL e diretrizes](sql-database-general-limitations.md) | Esta página descreve algumas limitações gerais para a base de dados do SQL Azure, bem como as áreas de interoperabilidade e suporte. |
| 190 | [Recomendações de camada preços de base de dados SQL](sql-database-service-tier-advisor.md) | Quando alterar preços camadas no portal do Azure, preços recomendações camada são desde que recomendar a camada que melhor se adequada para executar a carga de trabalho do Azure SQL base de dados existente. Comparar camadas descrevem o nível de camadas e o desempenho do serviço de uma base de dados do SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Extras

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Artigos relacionados a partir de outros serviços do Azure

- [Agregar a sua aplicação dos serviços de aplicação do Azure no Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Ferramentas de documentação

- [Atualizações anunciadas para base de dados do SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Procurar todos os tipos de documentação do Microsoft Azure, com filtros](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Gráficos de caminho de formação

- [Gráficos de caminho de formação para todos os serviços do Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Caminho de formação: Saiba a base de dados Azure SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Caminho de formação: Escala de flexível de base de dados SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


