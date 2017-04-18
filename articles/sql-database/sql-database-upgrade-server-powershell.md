<properties
    pageTitle="Atualização para o V12 de base de dados do SQL Azure através do PowerShell | Microsoft Azure"
    description="Explica como atualizar para V12 de base de dados do SQL Azure, incluindo como atualizar as bases de dados Web e empresas e como atualizar um servidor de V11 migrar as suas bases de dados diretamente para um agrupamento de base de dados flexível através do PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Atualização para o V12 de base de dados do SQL Azure através do PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de dados do SQL é a versão mais recente, por isso atualizar para o V12 de base de dados do SQL é recomendado.
V12 de base de dados do SQL tem várias [vantagens em relação à versão anterior](sql-database-v12-whats-new.md) incluindo:

- Maior compatibilidade com o SQL Server.
- Desempenho melhorado premium e novos níveis de desempenho.
- [Agrupamentos de base de dados flexível](sql-database-elastic-pool.md).

Este artigo fornece instruções para atualizar os servidores de SQL V11 de base de dados existentes e bases de dados para o V12 de base de dados do SQL.

Durante o processo de atualizar para o V12, actualizar quaisquer bases de dados Web e empresas para uma nova camada de serviço para que as indicações para atualizar as bases de dados Web e empresas são incluídas.

Além disso, migrar para um [conjunto de base de dados flexível](sql-database-elastic-pool.md) pode ser mais custo eficaz que atualizar para níveis de desempenho individuais (preços camadas) para bases de dados simples. Conjuntos de dados também simplificam a gestão de base de dados porque apenas necessárias para gerir as definições de desempenho para o conjunto em vez de gerir separadamente os níveis de desempenho das bases de dados individuais. Se tiver bases de dados em múltiplos servidores, considere a movê-las no mesmo servidor e tirar partido de colocá-los num conjunto de dados.

Pode seguir os passos neste artigo facilmente Migrar bases de dados de servidores V11 diretamente na agrupamentos de base de dados flexível.

Tenha em atenção que as bases de dados irão permanecer online e continuar a trabalhar em toda a operação de atualização. No momento da transição real para o novo nível de desempenho temporário largando das ligações à base de dados pode ocorrer durante uma duração muito pequena que é normalmente cerca de 90 segundos mas podem ser quanto 5 minutos. Se a aplicação tiver [breves falhas de processamento fontes de ligação](sql-database-connectivity-issues.md) , é suficiente para proteger contra ligações interrompidas no final da atualização.

Atualizar para o SQL V12 de base de dados não pode ser anulada. Após uma atualização no servidor não pode ser revertido para V11.

Após atualizar para V12, [recomendações de camada de serviço](sql-database-service-tier-advisor.md) e [recomendações de agrupamento flexível](sql-database-elastic-pool-create-portal.md) não imediatamente estará disponíveis até que o serviço tenha tempo para avaliar a sua das cargas de trabalho no novo servidor. Histórico de recomendação V11 server não se aplica aos servidores V12 para que não é retida.  

## <a name="prepare-to-upgrade"></a>Preparar a atualizar

- **Atualizar todas as bases de dados Web e empresas**: Utilize o portal ou utilize [o PowerShell para atualizar as bases de dados e o servidor](sql-database-upgrade-server-powershell.md).
- **Rever e suspender Geo replicação**: se a base de dados do Azure SQL estiver configurado para a replicação Geo deve documentar sua configuração atual e [Parar a replicação Geo](sql-database-geo-replication-portal.md#remove-secondary-database). Após a atualização for concluída reconfigure a base de dados para a replicação Geo.
- **Abra as seguintes portas se tiver clientes numa VM Azure**: se o programa cliente liga-se ao V12 de base de dados do SQL enquanto o cliente é executada numa máquina virtual Azure (VM), terá de abrir intervalos de porta 11000 11999 e 14000-14999 na VM. Para obter mais detalhes, consulte [portas para V12 de base de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Pré-requisitos

Para atualizar um servidor para V12 com o PowerShell, tem de ter o PowerShell do Azure mais recente instalada e em execução. Para obter informações detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurar as suas credenciais e selecione a sua subscrição

Para executar os cmdlets do PowerShell contra a sua subscrição do Azure tem de estabelecer acesso à sua conta Azure. Execute o seguinte e será apresentada com um ecrã de início de sessão para introduzir as suas credenciais. Utilize o e-mail e palavra-passe que utiliza para iniciar sessão portal do Azure mesmo.

    Add-AzureRmAccount

Depois de iniciar sessão com êxito deverá ver algumas informações no ecrã que inclui o Id tiver iniciado sessão com e as subscrições Azure que tiver acesso a.

Para selecionar a subscrição que pretende trabalhar com o precisa do Id (**-SubscriptionId**) da subscrição ou a subscrição nome (**-SubscriptionName**). Pode copiá-lo a partir do passo anterior ou, se tiver múltiplas subscrições pode executar o cmdlet **Get-AzureRmSubscription** e copie as informações da subscrição pretendido a partir do conjunto de resultados.

Execute o cmdlet seguinte com as suas informações de subscrição para definir a sua subscrição atual:

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Os seguintes comandos serão executados em relação a subscrição que acabou de selecionar acima.

## <a name="get-recommendations"></a>Obter recomendações

Para obter a recomendação para a atualização de servidor que execute o cmdlet seguinte:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Para mais informações, consulte o artigo [criar um agrupamento de base de dados flexível](sql-database-elastic-pool-create-portal.md) e [base de dados do SQL Azure preços recomendações de camadas](sql-database-service-tier-advisor.md).



## <a name="start-the-upgrade"></a>Iniciar a atualização

Para iniciar a atualização do servidor que execute o cmdlet seguinte:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando executa este processo de atualização do comando será iniciado. Pode personalizar o resultado da recomendação e fornecer a recomendação editada para este cmdlet.


## <a name="upgrade-a-server"></a>Actualizar um servidor


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Mapeamento de atualização de personalizada

Se as recomendações não são adequadas para o seu servidor e caso comercial, em seguida, pode escolher como as bases de dados são atualizados e podem mapear a única ou flexível bases de dados.

Parâmetros ElasticPoolCollection e DatabaseCollection são opcionais:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Monitorizar bases de dados após atualizar para o V12 de base de dados SQL


Depois de atualizar, recomenda-se para monitorizar a base de dados ativamente para garantir que aplicações são executadas no desempenho desejado e a utilização de otimizar conforme necessário.

Além disso, para monitorizar bases de dados individuais, pode monitorizar bases de dados flexível conjuntos de dados [através do portal](sql-database-elastic-pool-manage-portal.md) ou com o [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Dados consumo de recursos:** Para bases de dados Basic, padrão e Premium dados consumo de recursos estão disponíveis através de [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) das DMV da base de dados do utilizador. Fornece das DMV junto à informação sobre o consumo de recurso em tempo real na 15 granularidade segunda para a hora anterior de operação. O consumo de percentagem DTU por um intervalo é calculado como o consumo de percentagem máxima das dimensões CPU, IO e registo. Eis uma consulta para calcular o média consumo de percentagem DTU através de última hora:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informações de monitorização adicionais:

- [Base de dados do SQL azure desempenho seta de quatro pontas única bases de dados](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerações sobre preço e desempenho para um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md).
- [Base de dados do SQL Azure utilizando vistas dinâmicas de gestão de monitorização](sql-database-monitoring-with-dmvs.md)



**Alertas:** Configure o 'Alertas' no portal do Azure para o notifique quando o consumo DTU para uma base de dados atualizado abordagens determinado alto nível. Alertas de base de dados podem ser configuradas no portal do Azure para várias métricas de desempenho como DTU, CPU, IO e registo. Navegue até à sua base de dados e selecione **regras alertas** na pá **Definições** .

Por exemplo, pode configurar um alerta de correio eletrónico "DTU percentagem" se o valor médio de percentagem DTU exceder 75% sobre os últimos 5 minutos. Referir-se a [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) para saber mais sobre como configurar as notificações de alerta.



## <a name="next-steps"></a>Próximos passos

- [Criar um agrupamento de base de dados flexível](sql-database-elastic-pool-create-portal.md) e adicionar algumas ou todas as bases de dados para o conjunto.
- [Alterar o nível de camadas e o desempenho do serviço da base de dados](sql-database-scale-up.md).



## <a name="related-information"></a>Informações relacionadas

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Iniciar AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Parar AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
