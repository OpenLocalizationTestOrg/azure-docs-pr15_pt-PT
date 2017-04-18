<properties
    pageTitle="Utilizar o PowerShell para criar e configurar uma área de trabalho de análise de registo | Microsoft Azure"
    description="Inicie sessão Analytics utiliza dados a partir de servidores no seu no local ou na nuvem infraestrutura. É possível recolher dados de computador a partir do armazenamento Azure quando gerado pelo diagnósticos do Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>Gerir a análise de registo através do PowerShell

Pode utilizar o [registo Analytics os cmdlets do PowerShell] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) para efetuar várias funções no registo de análise a partir de uma linha de comandos ou como parte de um script.  Exemplos das tarefas que pode realizar com PowerShell incluem:

+ Criar uma área de trabalho
+ Adicionar ou remover uma solução
+ Importar e exportar procuras guardadas
+ Criar um grupo de computador
+ Ativar o conjunto de registos do IIS de computadores com o Windows agent instalado
+ Recolher contadores de desempenho de computadores Linux e Windows
+ Recolher eventos do sistema em computadores Linux 
+ Recolher eventos de registos dos eventos do Windows
+ Recolher registos de eventos personalizados
+ Adicionar o agente de análise de registo para uma máquina virtual Azure
+ Configurar a análise de registo para indexar dados recolhidos utilizando os diagnósticos do Azure


Este artigo fornece dois exemplos de código que ilustram algumas das funções que pode executar a partir do PowerShell.  Pode referir-se para a [registo Analytics PowerShell referência do cmdlet] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) para outras funções.

> [AZURE.NOTE] Análise de registo era anteriormente chamado informações operacionais avançados, que é o motivo pelo qual é o nome utilizado nos cmdlets.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o PowerShell com a área de trabalho de análise de registo, tem de ter:

+ Uma subscrição do Azure, e 
+ A área de trabalho de análise de registo do Azure ligados à sua subscrição do Azure.

Se tiver criado uma área de trabalho OMS, mas não está ainda associada-lo para uma subscrição do Azure pode criar a ligação:

+ No portal do Azure
+ No portal do OMS ou 
+ Utilizar os cmdlets Get-AzureRmOperationalInsightsLinkTargets e AzureRmOperationalInsightsWorkspace novo.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Criar e configurar uma área de trabalho de análise de registo

O seguinte exemplo de script ilustra como:

1.  Criar uma área de trabalho
2.  As soluções disponíveis de lista
3.  Adicionar soluções para a área de trabalho
4.  Pesquisas de importação guardada
5.  Pesquisas de exportação guardada
6.  Criar um grupo de computador
7.  Ativar o conjunto de registos do IIS de computadores com o Windows agent instalado
8.  Recolher contadores disco lógico de desempenho de computadores Linux (% utilizado Inodes; Megabytes livres; % Utilizada espaço; Transferência de disco/seg; Disco lê/seg; Disco escritas/seg)
9.  Recolher eventos do sistema de Linux computadores
10. Recolher eventos de erro e de aviso a partir do registo de eventos a aplicação a partir de computadores com o Windows
11. Recolher contador de desempenho de memória disponível MB de computadores com o Windows
12. Recolher um registo personalizado 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configurar a análise de registo para indexar diagnósticos do Azure 

Para agentless monitorização de recursos Azure, os recursos têm de ter diagnósticos Azure ativado e configurado para escrever a uma conta de armazenamento. Análise de registo, em seguida, pode ser configurado para recolher os registos de conta de armazenamento. Recursos de que precisa para executar a configuração anterior por inclui:

+ Serviços em nuvem clássico (funções web e trabalhador)
+ Clusters ferro serviço
+ Grupos de segurança de rede
+ Chave cofres e 
+ Gateways de aplicação

Também pode utilizar o PowerShell para configurar uma área de trabalho de análise de registo numa subscrição do Azure recolher registos de subscrições Azure diferentes.

Mostra o exemplo seguinte como:

1.  Listar os contas de armazenamento existentes e as localizações que registo Analytics vai indexar dados a partir de
2.  Criar uma configuração de ler a partir de uma conta de armazenamento
3.  Actualizar a configuração do recentemente criada indexar dados a partir de localizações adicionais
4.  Eliminar a configuração recentemente criada

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Próximos passos

- [Os cmdlets do PowerShell de análise de registo de rever](http://msdn.microsoft.com/library/mt188224.aspx) para obter informações adicionais sobre como utilizar o PowerShell para a configuração de análise de registo.

