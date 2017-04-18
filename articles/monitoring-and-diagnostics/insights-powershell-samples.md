<properties
    pageTitle="Exemplos de guia de introdução do Monitor PowerShell Azure. | Microsoft Azure"
    description="Utilize o PowerShell para aceder às funcionalidades Azure Monitor como autoscale, alertas, webhooks e procurar registos de atividade."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de guia de introdução do Azure Monitor PowerShell

Este artigo mostra-lhe comandos do PowerShell para o ajudar a aceder às funcionalidades Azure Monitor de exemplo. Azure Monitor permite-lhe a serviços em nuvem AutoScale, máquinas virtuais e Web Apps e enviar as notificações de alerta ou uma chamada URLs web com base em valores de dados de telemetria configurado.

>[AZURE.NOTE] Azure Monitor é o novo nome para o que era denominado "Azure informações" até 25 de Setembro de 2016. No entanto, os espaços de nomes e, consequentemente, os comandos abaixo ainda contenham "informações".

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se ainda não o fez, configure o PowerShell para executar no seu computador. Para mais informações, consulte o artigo [como instalar e configurar o PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Exemplos neste artigo

Os exemplos no artigo mostram como pode utilizar cmdlets do Azure Monitor. Também pode consultar a lista completa de cmdlets do PowerShell de Monitor de Azure na [Cmdlets Azure Monitor (informações)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Iniciar sessão e utilizar a subscrições

Em primeiro lugar, inicie sessão na sua subscrição do Azure.

```PowerShell
Login-AzureRmAccount
```

Isto requer que inicie sessão. Assim que o fizer, sua conta, são apresentados TenantId e predefinido Id da subscrição. Os cmdlets Azure funcionam no contexto da sua subscrição predefinido. Para ver a lista de subscrições que tiver acesso a, utilize o seguinte comando.

```PowerShell
Get-AzureRmSubscription
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Obter registos de auditoria para uma subscrição
Utilizar o `Get-AzureRmLog` cmdlet.  Abaixo estão alguns exemplos comuns.

Obtenha entradas de registo desta data/hora para apresentar:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obter entradas de registo entre um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de registo de um grupo de recursos específicos:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obter entradas de registo a partir de um fornecedor de recurso específico entre um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de registo com um autor específico:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

O comando seguinte obtém os eventos última 1000 a partir do registo de auditoria:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`suporta muitos dos outros parâmetros. Consulte o artigo o `Get-AzureRmLog` referência para obter mais informações.

>[AZURE.NOTE] `Get-AzureRmLog`apenas fornece 15 dias do histórico. Utilizar o parâmetro **- MaxEvents** permite-lhe os eventos N últimos, para além de 15 dias da consulta. Para eventos de acesso a mais de 15 dias, utilize o REST API ou SDK (c# amostra utilizando o SDK). Se não incluir a **hora de início**, o valor predefinido é **hora de fim** menos uma hora. Se não incluir a **hora de fim**, o valor predefinido é a hora atual. Todas as horas estão UTC.

## <a name="retrieve-alerts-history"></a>Obter o histórico de alertas
Para ver todos os eventos de alertas, pode consultar os registos de Gestor de recursos do Azure utilizando os exemplos seguintes.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para visualizar o histórico de uma regra de alerta específica, pode utilizar o `Get-AzureRmAlertHistory` cmdlet, prisma no ID do recurso da regra alerta.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O `Get-AzureRmAlertHistory` cmdlet suporta vários parâmetros. Obter mais informações, consulte o artigo [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Obter informações sobre regras de alertas
Todos os comandos seguintes agir num grupo de recursos com o nome "montest".

Ver todas as propriedades da regra alerta:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Obter todos os alertas, no grupo de recursos:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Obter todas as regras de alertas definido para um recurso de destino. Por exemplo, todas as regras de alertas definem numa VM.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`suporta outros parâmetros. Consulte o artigo [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-alert-rules"></a>Criar regras de alertas
Pode utilizar o `Add-AlertRule` cmdlet para criar, atualizar ou desativar uma regra de alerta.

Pode criar propriedades de e-mail e webhook utilizando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, respetivamente. No cmdlet regra alerta, atribua estas como ações para a propriedade **ações** da regra alerta.

A secção seguinte contém um exemplo que mostra como criar uma regra de alerta com vários parâmetros.

### <a name="alert-rule-on-a-metric"></a>Regra de alerta numa métrica
A tabela seguinte descreve os parâmetros e utilizadas para criar um alerta com uma métrica de valores.


|parâmetro|valor|
|---|---|
|Nome|  simpletestdiskwrite|
|Localização desta regra alerta|   Leste dos EUA|
|ResourceGroup| montest|
|TargetResourceId|  /subscriptions/S1/resourceGroups/montest/Providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName do alerta que é criado|   \PhysicalDisk ( total) \Disk escritas/seg. Consulte o artigo o `Get-MetricDefinitions` cmdlet abaixo sobre como obter os nomes de métricos exatos|
|operador|  GreaterThan|
|Valor de limite (contar/sec para esta métrica)|    1|
|Tamanhodajanela (SS format)|  00:05:00|
|agregação de (estatística de métrica, que utiliza a contagem de média, neste caso)|  Média|
|mensagens de correio eletrónico personalizadas (matriz de cadeia)|'foo@example.com','bar@example.com'|
|enviar e-mail para os proprietários, contribuintes e os leitores|    -SendToServiceOwners|

Criar uma ação de correio eletrónico

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Criar uma ação Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra alerta sobre a métrica do % CPU numa VM clássica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Obter a regra de alerta

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet alerta adicionar também atualiza a regra se já existir uma regra de alerta para as propriedades determinadas. Para desativar uma regra de alerta, inclua o parâmetro **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Alerta em eventos de registo de auditoria

>[AZURE.NOTE] Esta funcionalidade ainda se encontra na pré-visualização.

Neste cenário, irá enviar e-mail quando um Web site com êxito for iniciado na minha subscrição no grupo de recursos *abhingrgtest123*.

Configurar uma regra de e-mail

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurar uma regra de webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra no evento

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Obter a regra de alerta

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

O `Add-AlertRule` cmdlet permite que vários outros parâmetros. Obter mais informações, consulte o artigo [Adicionar AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista das métricas disponíveis para os alertas
Pode utilizar o `Get-AzureRmMetricDefinition` cmdlet para ver a lista de todas as métricas para um recurso específico.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

O exemplo seguinte gera uma tabela com a métrica do nome e a unidade para a mesma.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzureRmMetricDefinition` está disponível em [Obter MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Criar e gerir as definições de AutoScale
Um recurso, tal como uma aplicação Web, VM, serviço na nuvem ou conjunto de escala de VM pode ter apenas uma definição de autoscale configurada para esse efeito.
No entanto, cada definição autoscale pode ter vários perfis. Por exemplo, uma para um perfil de escala com base no desempenho e um segundo para uma agenda com base no perfil. Cada perfil pode ter múltiplas regras configuradas no mesmo. Para mais informações sobre Autoscale, consulte o artigo [como Autoscale uma aplicação](../cloud-services/cloud-services-how-to-scale.md).

Eis os passos, que vamos utilizar:

1. Crie Regra (s).
2. Crie perfis mapear as regras que criou anteriormente para os perfis.
3. Opcional: Crie notificações para autoscale ao configurar as propriedades do webhook e e-mail.
4. Crie uma definição de autoscale com um nome do recurso de destino ao mapear os perfis e notificações que criou nos passos anteriores.

Os exemplos seguintes mostram como pode criar uma definição de Autoscale para uma escala VM configurar para o sistema operativo Windows com base utilizando a métrica de utilização da CPU.

Em primeiro lugar, crie uma regra para escala-out, com um aumento de contagem de instância.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

Em seguida, crie uma regra para escala-in, com uma diminuição de contagem de instância.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Em seguida, crie um perfil para as regras.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a definição de autoscale, incluindo o e-mail e o webhook que criou anteriormente.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a definição de autoscale para adicionar o perfil que criou acima.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para mais informações sobre como gerir as definições de Autoscale, consulte o artigo [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de Autoscale
O exemplo seguinte mostra-lhe como pode ver autoscale recente e eventos de alertas. Utilize a pesquisa de registo de auditoria para visualizar o histórico de Autoscale.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Pode utilizar o `Get-AzureRmAutoScaleHistory` cmdlet para obter AutoScale histórico.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para mais informações, consulte o artigo [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Ver os detalhes de uma definição de autoscale
Pode utilizar o `Get-Autoscalesetting` cmdlet para obter mais informações sobre a definição de autoscale.

O exemplo seguinte mostra os detalhes sobre todas as definições de autoscale no grupo de recursos 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo seguinte mostra os detalhes sobre todas as definições de autoscale no grupo de recursos 'myrg1' e especificamente a definição de autoscale 'MyScaleVMSSSetting' com o nome.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma definição de autoscale
Pode utilizar o `Remove-Autoscalesetting` cmdlet para eliminar uma definição de autoscale.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Gerir perfis de registo para registos de auditoria

Pode criar um *perfil de registo* e exportar dados do seu registos de auditoria para uma conta de armazenamento e pode configurar retenção de dados para o mesmo. Opcionalmente, também pode passe os dados para o seu centro de evento. Tenha em atenção que esta funcionalidade está atualmente em pré-visualização e de só pode criar um perfil de registo por subscrição. Pode utilizar os seguintes cmdlets com a sua subscrição atual para criar e gerir perfis de registo. Também pode escolher uma subscrição em particular. Apesar de PowerShell predefinições para a subscrição atual, pode alterar sempre que a utilização de `Set-AzureRmContext`. Pode configurar os registos de auditoria encaminhar dados para qualquer conta de armazenamento ou evento concentrador dentro desse subscrição. Dados são escritos como ficheiros de BLOBs no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de registo
Para obter os perfis de registo existente, utilize o `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de registo sem retenção de dados

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de registo com retenção de dados

Pode especificar a propriedade **- RetentionInDays** com o número de dias, como um positivo inteiro, onde os dados são guardados.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar registo perfil com retenção e EventHub
Para além de encaminhamento de dados à conta de armazenamento, também pode passe-lo a um concentrador de evento. Note que esta versão de pré-visualização e o armazenamento de configuração da conta é obrigatória mas configuração concentrador de evento é opcional.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar os registos de diagnóstico
Muitos serviços Azure fornecem registos adicionais e telemetria, incluindo os grupos de segurança de rede do Azure, balanceadores de carga de Software, chave cofre, dos serviços de pesquisa do Azure, e Apps lógica e estes podem ser configurados para guardar os dados na sua conta de armazenamento do Windows Azure. Só pode ser executada essa operação num nível de recurso e a conta de armazenamento deve estar presente na mesma região como o recurso de destino onde a definição de diagnóstico está configurada.

### <a name="get-diagnostic-setting"></a>Obter a definição de diagnóstico

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desativar a definição de diagnóstico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Ativar a definição de diagnóstico sem retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Ativar a definição de diagnóstico com retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico com retenção para uma categoria de registo específico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
