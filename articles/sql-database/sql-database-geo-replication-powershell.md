<properties 
    pageTitle="Configurar a replicação de Geo ativa para a base de dados do SQL Azure através do PowerShell | Microsoft Azure" 
    description="Configurar a replicação de Geo ativa para a base de dados do SQL Azure através do PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Configurar a replicação de Geo para base de dados do Azure SQL com PowerShell

> [AZURE.SELECTOR]
- [Descrição geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra-lhe como configurar a replicação do Geo Active para base de dados SQL com o PowerShell.

Para iniciar activação pós-falha através do PowerShell, consulte o artigo [Iniciar planeado ou não planeado activação pós-de uma falha, para a base de dados do SQL Azure com PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Geo-a replicação do Active (secundários legíveis) está agora disponível para todas as bases de dados em todas as camadas de serviço. De Abril de 2017 vai ser foi removido o tipo de secundário que não sejam legíveis e bases de dados que não sejam legíveis existentes serão automaticamente atualizadas para secundários legíveis.



Para configurar a replicação de Geo ativo através do PowerShell, precisa do seguinte:

- Uma subscrição do Azure. 
- Azure base de dados SQL - a base de dados principal ao qual pretende criar uma réplica.
- Azure PowerShell 1.0 ou posterior. Pode transferir e instalar os módulos Azure PowerShell pelos seguintes [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurar as suas credenciais e selecione a sua subscrição

Primeiro tem de estabelecer acesso à sua conta Azure por isso, inicie o PowerShell e, em seguida, execute o cmdlet seguinte. No ecrã de início de sessão, introduza o mesmo e-mail e palavra-passe que utiliza para iniciar sessão portal do Azure.


    Login-AzureRmAccount

Depois de iniciar sessão com êxito verá algumas informações no ecrã que inclui o Id tiver iniciado sessão com e as subscrições Azure que tiver acesso a.


### <a name="select-your-azure-subscription"></a>Selecione a sua subscrição do Azure

Para selecionar a subscrição tem a sua subscrição ID. Pode copiar o Id da subscrição das informações apresentadas no passo anterior, ou se tem múltiplas subscrições e precisa de mais detalhes pode executar o cmdlet **Get-AzureRmSubscription** e copie as informações de subscrição pretendido a partir do conjunto de resultados. O cmdlet seguinte utiliza o Id da subscrição para definir a subscrição atual:

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar com êxito **Selecione AzureRmSubscription** regressa ao PowerShell linha de comandos.


## <a name="add-secondary-database"></a>Adicionar base de dados secundária


Os passos seguintes criam uma nova base de dados secundária numa parceria de replicação Geo.  
  
Para ativar um secundário tem de ser o proprietário da subscrição ou coproprietário. 

Pode utilizar o cmdlet **AzureRmSqlDatabaseSecondary novo** para adicionar uma base de dados secundária num servidor parceiro a uma base de dados local o servidor ao qual está ligado (a base de dados primária). 

Este cmdlet substitui **AzureSqlDatabaseCopy iniciar** com o parâmetro **– IsContinuous** .  Irá saída de um objeto de **AzureRmSqlDatabaseSecondary** que pode ser utilizado pelo outras cmdlets para identificar claramente uma ligação de replicação específico. Este cmdlet irá devolver quando a base de dados secundária é criado e totalmente propagada. Dependendo do tamanho da base de dados poderá demorar minutos para horas.

A base de dados replicada no servidor secundário terão o mesmo nome que a base de dados no servidor principal e, por predefinição, terá o mesmo nível de serviço. A base de dados secundária pode ser legível ou que não sejam legíveis e pode ser uma base de dados única ou uma base de dados flexível. Para mais informações, consulte o artigo [Novo AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) e [Camadas de serviços](sql-database-service-tiers.md).
Depois de secundária é criada e propagada, dados começarão a replicação da base de dados principal para a nova base de dados secundária. Os passos abaixo descrevem como realizar esta tarefa através do PowerShell para criar secundários de não lido e legíveis, quer seja com uma base de dados única ou uma base de dados flexível.

Se a base de dados do parceiro já existe (por exemplo - estatístico como resultado da terminar uma relação de replicação Geo anterior) o comando irá falhar.



### <a name="add-a-non-readable-secondary-single-database"></a>Adicionar um secundário que não sejam legíveis (base de dados único)

O comando seguinte cria um secundário que não sejam legíveis da base de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Adicionar secundário legível (base de dados único)

O comando seguinte cria um secundário legível da base de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Adicionar um secundário que não sejam legíveis (base de dados flexível)

O comando seguinte cria um secundário que não sejam legíveis da base de dados "mydb" no conjunto de base de dados flexível com o nome "ElasticPool1" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Adicionar um secundário legível (base de dados flexível)

O comando seguinte cria um secundário legível da base de dados "mydb" no conjunto de base de dados flexível com o nome "ElasticPool1" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Remover a base de dados secundária

Utilize o cmdlet **AzureRmSqlDatabaseSecondary remover** permanentemente denunciar a parceria de replicação entre uma base de dados secundária e do respectivo principal. Após o termo relação, a base de dados secundária torna-se uma base de dados de leitura e escrita. Se a ligação à base de dados secundária é interrompida o comando é concluída com êxito mas secundária irão tornar-se leitura / escrita depois de conectividade é restaurada. Para mais informações, consulte o artigo [Remover AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) e [Camadas de serviços](sql-database-service-tiers.md).

Este cmdlet substitui parar AzureSqlDatabaseCopy para a replicação. 

Esta remoção é equivalente de uma terminação forçada que remove a ligação de replicação e deixa secundária antiga como uma base de dados autónomo não é totalmente replicada antes de taxas de cessação. Todos os dados de ligação vai ser limpo no antigo principal e antigo secundário, se ou quando estão disponíveis. Este cmdlet irá devolver quando a ligação de replicação é removida. 


Para remover secundário, os utilizadores devem ter acesso de escrita a bases de dados principais e secundários de acordo com RBAC. Consulte o artigo o controlo de acesso baseado em funções para obter detalhes.

O seguinte procedimento remove a ligação de replicação de base de dados denominada "mydb" para o servidor "srv2" do grupo de recursos "rg2". 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Monitorizar o estado de funcionamento e configuração de replicação geo

Tarefas de monitorização incluem a monitorização da configuração do geo replicação e estado de funcionamento de replicação de dados.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) podem ser utilizados para obter as informações sobre as ligações de replicação reencaminhar visíveis na vista de catálogo sys.geo_replication_links.

O comando seguinte obtém o estado da ligação replicação entre a base de dados principal "mydb" e secundário no servidor "srv2" do grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre Geo-replicação do Active, consulte o artigo - [Geo-a replicação do Active](sql-database-geo-replication-overview.md)
- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)

