<properties 
    pageTitle="Gerir um agrupamento de base de dados flexível (PowerShell) | Microsoft Azure" 
    description="Saiba como utilizar o PowerShell para gerir um agrupamento de base de dados flexível."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Monitorizar e gerir um conjunto de base de dados flexível com PowerShell 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gerir um [conjunto de base de dados flexível](sql-database-elastic-pool.md) utilizando os cmdlets do PowerShell. 

Para códigos de erro comuns, consulte o artigo [códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados](sql-database-develop-error-messages.md).

Podem encontrar valores para conjuntos de dados em [limites de armazenamento e eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Pré-requisitos

* Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
* Agrupamentos de base de dados flexível só estão disponíveis com servidores V12 de base de dados do SQL. Se tiver um servidor V11 de base de dados do SQL, [utilizar o PowerShell para atualizar para o V12 e criar um conjunto de dados](sql-database-upgrade-server-portal.md) num único passo.


## <a name="move-a-database-into-an-elastic-pool"></a>Mover uma base de dados para um conjunto de dados flexível

Pode mover uma base de dados ou desaparecer um conjunto com o [Conjunto AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Alterar definições de desempenho de um conjunto de dados

Quando o desempenho é menor, pode alterar as definições do conjunto de para acomodar crescimento. Utilize o cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Defina o parâmetro - Dtu para eDTUs por agrupamento. Consulte o artigo [limites de armazenamento e eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) para os valores possíveis.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Obter o estado de operações de agrupamento

Criar um conjunto de dados, pode demorar tempo. Para controlar o estado de operações de conjunto de dados, incluindo a criação e atualizações, utilize o cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) .

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Obter o estado de mover uma base de dados flexível para e terminar um conjunto de dados

Mover uma base de dados, pode demorar tempo. Registar um Estado de mover utilizando o cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) .

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Obter dados de utilização de recursos para um conjunto de dados

Métricas que podem ser recuperadas como uma percentagem do limite de agrupamento de recursos:   


| Métrica nome | Descrição |
| :-- | :-- |
| CPU\_por cento | Média de utilização na percentagem do limite do conjunto de calcular. |
| física\_dados\_ler\_por cento | Utilização de e/s média na percentagem com base em relação ao limite do conjunto de. |
| registo\_escrever\_por cento | Média escrever utilização de recursos no percentagem do limite do conjunto de. | 
| DTU\_consumo\_por cento | Utilização de média eDTU na percentagem do limite de eDTU para o agrupamento | 
| armazenamento\_por cento | Utilização de armazenamento em percentagem do limite de armazenamento do conjunto de média. |  
| os trabalhadores\_por cento | Máximos em simultâneo trabalhadores (pedidos) na percentagem com base em relação ao limite do conjunto de. |  
| sessões\_por cento | Máximo de sessões em simultâneo no percentagem com base em relação ao limite do conjunto de. | 
| eDTU_limit | Definição actual de máximo do conjunto flexível DTU para este conjunto flexível durante este intervalo. |
| armazenamento\_limite | Limite de armazenamento do atual máximo do conjunto flexível definição para este conjunto flexível em megabytes durante este intervalo. |
| eDTU\_utilizado | Média eDTUs utilizada pelo conjunto neste intervalo. |
| armazenamento\_utilizado | Armazenamento de média utilizado pelo conjunto neste intervalo em bytes |

**Períodos de granularidade/retenção métricas:**

* Dados serão devolvidos na granularidade 5 minutos.  
* Retenção de dados é de 35 dias.  

Este cmdlet e API limita o número de linhas que pode ser obtido numa chamada para linhas de 1000 (cerca de 3 dias dados no granularidade 5 minutos). Mas este comando pode ser chamado várias vezes com intervalos de tempo diferentes início/fim para obter mais dados 

Para obter as métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Obter dados de utilização de recursos para uma base de dados flexível

Estas APIs são os mesmos como APIs do atuais (V12) utilizadas para monitorizar a utilização de recursos de uma base de dados autónomo, exceto a diferença semântica seguinte. 

Para esta API métricas recuperadas são expresso como uma percentagem da por max eDTUs (ou remate equivalente para a métrica do subjacente como CPU, etc es) definir para esse agrupamento. Por exemplo, 50% de utilização de qualquer uma das seguintes métricas indica que o consumo de recurso específico a 50% do limite de remate de base de dados para esse recurso no conjunto de elemento principal por. 

Para obter as métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Adicionar um alerta para um recurso de conjunto de dados

Pode adicionar regras de alertas para recursos de enviar notificações de correio eletrónico ou cadeias de alertas para [os pontos finais de URL](https://msdn.microsoft.com/library/mt718036.aspx) quando o recurso de acertos um limite de utilização que configurou. Utilize o cmdlet AzureRmMetricAlertRule adicionar. 

> [AZURE.IMPORTANT]Utilização de recursos de monitorização para agrupamentos flexível tem um atraso de pelo menos de 20 minutos. Definir alertas de menos de 30 minutos para flexível conjuntos de dados não é suportada. Todos os alertas definido para agrupamentos flexível com um ponto final (parâmetro chamado "-Tamanhodajanela" na API do PowerShell) de 30 minutos a menor que não podem ser acionou. Certifique-se de que todos os alertas que definir para agrupamentos flexível para utilizam um período (tamanhojanela) de 30 minutos ou mais.

Este exemplo adiciona um alerta para receber notificado quando acede consumo de eDTU um agrupamento acima determinado limiar.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Adicionar alertas para todas as bases de dados num conjunto de dados

Pode adicionar regras de alertas para todas as bases de dados num conjunto de flexível para enviar notificações de correio eletrónico ou um limite de utilização configurado pelo alerta de acertos cadeias de alertas para [os pontos finais de URL](https://msdn.microsoft.com/library/mt718036.aspx) quando um recurso. 

> [AZURE.IMPORTANT] Utilização de recursos de monitorização para agrupamentos flexível tem um atraso de pelo menos de 20 minutos. Definir alertas de menos de 30 minutos para flexível conjuntos de dados não é suportada. Todos os alertas definido para agrupamentos flexível com um ponto final (parâmetro chamado "-Tamanhodajanela" na API do PowerShell) de 30 minutos a menor que não podem ser acionou. Certifique-se de que todos os alertas que definir para agrupamentos flexível para utilizam um período (tamanhojanela) de 30 minutos ou mais.

Este exemplo adiciona um alerta para cada uma das bases de dados num conjunto de dados para obter notificado quando acede ao consumo DTU nessa base de dados acima determinado limiar.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Recolher e monitorizar os dados de utilização de recursos ao longo de múltiplos agrupamentos de uma subscrição

Quando tiver uma subscrição de um grande número de bases de dados, é complexo para monitorizar a cada agrupamento flexível separadamente. Em vez disso, os cmdlets do PowerShell de base de dados SQL e consultas T SQL podem ser combinadas para recolher dados de utilização de recursos a partir de vários conjuntos de dados e suas bases de dados de monitorização e de análise de utilização de recursos. Pode encontrar uma [implementação de exemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) de como um conjunto de powershell scripts no repositório de amostras GitHub SQL Server juntamente com a documentação sobre o que faz e como utilizá-la.

Para utilizar esta implementação de exemplo, siga estes passos listados abaixo.


1. Transferir os [scripts e documentação](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modificar os scripts para o seu ambiente. Especificar um ou mais servidores que são alojados flexível conjuntos de dados.
3. Especificar uma base de dados de telemetria onde as métricas recolhidas estão a ser armazenado. 
4. Personalize o script para especificar a duração de execução dos scripts.

De alto nível, os scripts, faça o seguinte:

*   Enumera todos os servidores de uma subscrição Azure determinada (ou uma lista dos servidores especificada).
*   Executa uma tarefa de fundo para cada servidor. A tarefa é executado num ciclo regulares e recolhe dados de telemetria para todos os conjuntos de dados no servidor. Em seguida, carrega os dados recolhidos para a base de dados de telemetria especificado.
*   Enumera uma lista de bases de dados em cada conjunto para recolher dados de utilização de recursos base de dados. Em seguida, carrega os dados recolhidos para a base de dados de telemetria.

Métricas recolhidas da base de dados de telemetria podem ser analisadas para monitorizar o estado de funcionamento de agrupamentos flexível e as bases de dados no mesmo. O script também instala uma função de valor de tabela predefinida (TVF) da base de dados de telemetria para o ajudar a agregado métricas para uma janela de tempo especificado. Por exemplo, os resultados do TVF podem ser utilizados para mostrar "superiores N flexível conjuntos de dados com a utilização da eDTU máximo numa janela de tempo especificado." Opcionalmente, utilize ferramentas analíticas como o Excel ou do Power BI para consultar e analisar os dados recolhidos.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Exemplo: obter métricas de consumo de recurso para um conjunto de dados e o respetivos bases de dados

Este exemplo obtém as métricas consumo para um determinado conjunto flexível e todas as suas bases de dados. Dados recolhidos são formatados e escritos para um ficheiro formatado. csv. O ficheiro pode ser consultado com o Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Latência de operações de agrupamento flexível

- Alterar o eDTUs mínimo por eDTUs de base de dados ou máximo por bases de dados normalmente conclui em 5 minutos ou menos.
- Alterar eDTUs por agrupamento depende a quantidade de espaço utilizada por todas as bases de dados no conjunto de total. Alterações média 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total utilizada por todas as bases de dados no conjunto de é 200 GB, então a latência esperada para alterar o eDTU agrupamento por agrupamento é 3 horas ou menos.

## <a name="migrate-from-v11-to-v12-servers"></a>Migrar do V11 para os servidores de V12

Os cmdlets do PowerShell estão disponíveis para iniciar, parar ou monitorizar uma atualização para V12 de base de dados do SQL Azure a partir do V11 ou qualquer outra versão do tipo de ficheiro V12.

- [Atualização para o SQL V12 de base de dados através do PowerShell](sql-database-upgrade-server-powershell.md)

Para a documentação de referência sobre estes cmdlets do PowerShell, consulte:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Iniciar AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Parar AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Significa que o cmdlet parar Cancelar, não coloque o cursor. Não existe nenhuma forma de retomar uma atualização, que não seja o iniciar novamente a partir do início. O cmdlet parar limpa e disponibiliza todos os recursos adequados.

## <a name="next-steps"></a>Próximos passos

- [Criar tarefas de flexível](sql-database-elastic-jobs-overview.md) Tarefas de flexível permitem-lhe executar scripts de T-SQL contra qualquer número de bases de dados no conjunto.
- Consulte o artigo [dimensionamento saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md): utilizar ferramentas de base de dados flexível para fora de escala, mover os dados, de consulta ou crie transações.
