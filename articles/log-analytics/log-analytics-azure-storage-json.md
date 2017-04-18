<properties
    pageTitle="Analisar os registos de diagnóstico Azure utilizando a análise de registo | Microsoft Azure"
    description="Análise de registo pode ler os registos a partir do Azure serviços que escrever Azure registos de diagnóstico para blob armazenamento no formato JSON."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analisar os registos de diagnóstico Azure utilizando a análise de registo

Análise de registo pode recolher registos para os seguintes serviços Azure escreverem [Azure registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) ao armazenamento blob do formato JSON:

+ Automatização (pré-visualização)
+ Chave cofre (pré-visualização)
+ Gateway de aplicação (pré-visualização)
+ Grupo de segurança de rede (pré-visualização)

As secções seguintes guiá-lo através de utilizar o PowerShell para:

+ Configurar a análise de registo para recolher os registos de armazenamento para cada recurso  
+ Ativar a solução de análise de registo para o serviço do Azure

Antes da análise de registo podem recolher dados para estes recursos, tem de estar ativados diagnósticos do Azure. Utilizar o `Set-AzureRmDiagnosticSetting` cmdlet para ativar os registos.

Consulte os seguintes artigos para obter mais informações sobre como ativar o registo de diagnóstico:

+ [Cofre chave](../key-vault/key-vault-logging.md)
+ [Gateway de aplicação](../application-gateway/application-gateway-diagnostics.md)
+ [Grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)

Esta documentação também inclui detalhes sobre:

+ Resolução de problemas de recolha de dados
+ Parar a recolha de dados

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar a análise de registo para recolher registos de diagnóstico Azure

Recolher registos para estes serviços e ativar a solução visualizar os registos de início é executada a utilização de scripts de PowerShell.

O exemplo abaixo permite que todos os recursos suportados de início de sessão

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Para alguns recursos, é possível executar a configuração anterior a partir do portal Azure utilizando os passos descritos nos [registos de diagnóstico de descrição geral do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar a análise de registo para recolher registos de diagnóstico Azure

Fornecemos um módulo de script do PowerShell que exporta duas cmdlets para ajudá-lo a configurar a análise de registo:

1. `Add-AzureDiagnosticsToLogAnalyticsUI`pede-lhe introduções e é conseguir configurar configurações simples
2. `Add-AzureDiagnosticsToLogAnalytics`leva-o até os recursos para monitorizar como entrada e, em seguida, configura o registo de análise  

### <a name="pre-requisites"></a>Pré-requisitos

1. Azure PowerShell com a versão 1.0.8 ou mais recente dos cmdlets informações operacional.
  - [Como instalar e configurar PowerShell do Azure](../powershell-install-configure.md)
  - Verifique se a sua versão do cmdlets:`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Registo de diagnóstico está configurado para o recurso Azure que pretende monitorizar. Utilizar `Set-AzureRmDiagnosticSetting` ou referir-se para [Utilizar a análise de registo para recolher dados a partir de contas do Azure armazenamento](log-analytics-azure-storage.md) como activar diagnósticos.
3. Uma área de trabalho do [Registo de análise](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. O módulo AzureDiagnosticsAndLogAnalytics PowerShell
  - Transferir o módulo [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) da Galeria de PowerShell

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Opção 1: Executar os Scripts de configuração interativos

Abra o PowerShell e executar:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

São apresentadas de uma lista de selecções disponíveis e atribuído um pedido para faça a sua seleção.
Se lhe pedir para efetue seleções para cada um dos seguintes procedimentos:

+ Tipos de recursos (Azure serviços) para recolher registos a partir do
+ Instâncias de recursos para recolher registos a partir do
+ Área de trabalho de análise de registo para recolher dados

Depois de executar este script, deverá ver registos no registo de análise cerca de 30 minutos depois de novos dados diagnóstico destina-se ao armazenamento. Se registos não estão disponíveis depois desta vez referir-se para a secção Resolução de problemas abaixo.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opção 2: Criar uma lista de recursos e transmiti-los para o cmdlet de configuração

Pode criar uma lista de recursos que tenham diagnósticos Azure ativados e, em seguida, passam os recursos para o cmdlet de configuração.

Pode ver informações adicionais sobre o cmdlet executando `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Para encontrar mais detalhes no OMS [Os cmdlets do PowerShell de análise de registo](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Se o recurso e de área de trabalho estão no diferentes subscrições do Azure, alternar entre eles, conforme necessário utilizar`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Depois de executar este script, deverá ver registos no registo de análise cerca de 30 minutos depois de novos dados diagnóstico destina-se ao armazenamento. Se registos não estão disponíveis depois desta vez referir-se para a secção Resolução de problemas abaixo.  

>[AZURE.NOTE] A configuração não estiver visível no portal do Azure. Pode verificar a configuração utilizando o `Get-AzureRmOperationalInsightsStorageInsight` cmdlet.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Parar a análise de registo de recolher registos de diagnóstico Azure

Para eliminar a configuração de análise de registo de um recurso, utilize o `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Resolução de problemas de configuração para registos de diagnóstico Azure

*Problema*

É apresentada a seguinte mensagem de erro ao configurar um recurso que está a registar ao armazenamento clássico:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Resolução*

Iniciar sessão no API para o modelo de implementação clássica com`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Resolução de problemas de recolha de dados para registos de diagnóstico Azure

Se não estiver a ver dados para o seu recurso Azure no registo de análise, pode utilizar os seguintes passos de resolução de problemas:

+ Verificar a fluir para a conta de armazenamento de dados
+ Verifique se que a solução de análise de registo para o serviço é activada
+ Certifique-se de que a análise de registo está configurada para ler a partir do armazenamento
+ Atualizar a chave de conta de armazenamento

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Certifique-se de dados estão a fluir para a conta de armazenamento

Pode verificar se os dados estão a fluir para a conta de armazenamento de uma ferramenta que permite a navegação em armazenamento Azure (por exemplo Visual Studio) ou através do PowerShell.

Para encontrar a conta de armazenamento o recurso está configurado para iniciar sessão para utilizar o PowerShell seguinte:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

O contentor de armazenamento utilizado pelo diagnósticos do Azure tem um nome com um formato de:  

`insights-logs-<Category>`

Referem-se para [utilizar os cmdlets do armazenamento para verificar se um contentor para os dados recentes](../storage/storage-powershell-guide-full.md) para saber mais sobre como ver os conteúdos da conta de armazenamento.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Verifique se que a solução de análise de registo para o serviço é activada

Se utilizar `Add-AzureDiagnosticsToLogAnalyticsUI`, a solução correta de análise de registo está ativada automaticamente para si.

Para verificar se uma solução está ativada, execute o seguinte PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Se a solução não estiver ativada, pode ativá-lo utilizando o PowerShell seguinte:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Para localizar o nome da solução a ativar para cada tipo de recurso, utilize o PowerShell seguinte (esta variável está disponível depois de ter importado o módulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Certifique-se de que a análise de registo está configurada para ler a partir do armazenamento

Se adicionar recursos adicionais do Azure, tem de diagnósticos do registo para os mesmos e configurar a análise de registo das mesmas.
Para verificar que recursos e contas de armazenamento a análise de registo está configurada para recolher registos para, utilize o PowerShell seguinte:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Atualizar a chave de armazenamento

Se alterar a chave da conta de armazenamento, a configuração de análise de registo também precisa de ser atualizados com a nova chave.
Pode atualizar a configuração de análise de registo com uma nova chave utilizando o PowerShell seguinte:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Para localizar o nome do conhecimentos aprofundados armazenamento, utilize o `Get-AzureRmOperationalInsightsStorageInsight` cmdlet, conforme mostrado nos exemplos anteriores.

## <a name="next-steps"></a>Próximos passos

- [Armazenamento de BLOBs utilizar para o IIS e armazenamento de tabela para eventos](log-analytics-azure-storage-iis-table.md) para ler os registos de início para Azure dos serviços que diagnósticos de escrita para armazenamento de tabela ou os registos do IIS escritos blob armazenamento.
- [Ativar soluções](log-analytics-add-solutions.md) para fornecer visão os dados.
- [Utilizar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.
