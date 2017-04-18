<properties
    pageTitle="Azure monitorização REST API guiadas | Microsoft Azure"
    description="Como autenticar pedidos para e utilizar a API REST monitorização do Azure."
    authors="mcollier, rboucher"
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
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure monitorização REST API guiadas
Este artigo mostra-lhe como efetuar autenticação para que o seu código pode utilizar a [Referência da API do resto do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

A API do Monitor Azure torna através de programação obter as definições métrica disponíveis predefinido (o tipo de métrica como o tempo de CPU, pedidos, etc.), granularidade e métricas valores. Assim que obtidos, os dados podem ser guardados num arquivo de dados separado como base de dados do SQL Azure, DocumentDB ou Azure dados Lake. A partir desse local análise adicional pode ser executada, conforme necessário.

Para além de trabalhar com vários pontos de dados métrica, tal como este artigo demonstra, a API do Monitor torna possível a lista de regras de alertas, ver registos de atividade e muito mais. Para uma lista completa de operações disponíveis, consulte a [Referência da API do resto do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor pedidos de autenticação

O primeiro passo é autenticar o pedido.

Todas as tarefas executadas em relação a API do Monitor Azure utilizam o modelo de autenticação de Gestor de recursos do Azure. Por conseguinte, todos os pedidos de têm de ser autenticados com o Azure Active Directory (Azure AD). Uma abordagem para autenticar a aplicação de cliente é para criar um Azure AD principal do serviço e obter o token de autenticação (JWT). O script de exemplo seguinte demonstra a criação de um serviço do Azure AD principal através do PowerShell. Para uma guia passo a mais detalhada passo, consulte a documentação sobre a [utilização do Azure o PowerShell para criar um serviço principal para aceder a recursos](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). Também é possível [criar um princípio de serviço através do portal do Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API do Azure monitores, a aplicação de cliente deve utilizar o capital de serviço anteriormente criada para autenticar. O exemplo seguinte PowerShell script mostra uma abordagem, utilizando a [Biblioteca de autenticação do Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) para o ajudar a autenticação de JWT token. O token JWT é transmitido como parte de um parâmetro de autorização de HTTP nos pedidos para o Monitor REST API do Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Assim que o passo de configuração de autenticação estiver concluído, consultas, em seguida, podem ser executadas contra o Azure Monitor REST API. Existem duas consultas úteis:

1. As definições de métricas de um recurso de lista

2. Obter os valores métricos


## <a name="retrieve-metric-definitions"></a>Obter definições métricas
>[AZURE.NOTE] Para obter definições métricas utilizando o Azure Monitor REST API, utilize "2016-03-01" como a versão da API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Para uma aplicação de lógica Azure, as definições de métricas apareceriam semelhantes ao seguinte captura de ecrã:

![ALT "JSON vista de resposta definição métrica."](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Para mais informações, consulte a documentação [as definições de métricas de um recurso no Azure Monitor REST API da lista](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Obter valores métricas
Assim que as definições de métricas disponíveis são conhecidas, em seguida, é possível obter os valores de métricos relacionados. Utilize nome 'value' a métrica (não o ' localizedValue') para qualquer pedidos de filtragem (por exemplo, obter os pontos de dados métrica 'CpuTime' e 'Os pedidos de'). Se forem especificados sem filtros, é devolvida a métrica predefinida.

>[AZURE.NOTE] Para obter valores métricas utilizando o Azure Monitor REST API, utilize "2016-06-01" como a versão da API.

**Método**: obter

**Pedir URI**: https://management.azure.com/subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/_{recurso-fornecedor-espaço de nomes}_/_{tipo de recurso}_/_{nome do recurso}_/providers/microsoft.insights/metrics?$filter=_{filtro}_& versão api =_{apiVersion}_

Por exemplo, para obter os pontos de dados de métricas de RunsSucceeded para o intervalo de tempo determinado e para grão um tempo de 1 hora, o pedido seria da seguinte forma:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

O resultado apareceriam semelhante ao exemplo seguinte captura de ecrã:

![ALT "Resposta JSON que mostra o valor métrico média tempo de resposta"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Para obter vários pontos de dados ou de agregação, adicione os nomes de definição métrica e tipos de agregação para o filtro, conforme visto no exemplo seguinte:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Utilizar ARMClient
Uma alternativa a através do PowerShell (como mostrado acima), é utilizar [ARMClient](https://github.com/projectkudu/ARMClient) no seu computador Windows. ARMClient trata a autenticação do Azure AD (e token JWT resultante) automaticamente. Utilização de ARMClient para obtenção de dados de métricos de destaque os seguintes passos:

1. Instale o [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/ARMClient).

2. Na janela de terminal, escreva _armclient.exe início de sessão_. Pede-lhe iniciar sessão no Azure.

3. Tipo de _armclient obter [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Tipo de _armclient obter [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![ALT "Utilizar ARMClient para trabalhar com a Azure monitorização REST API"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Obter o ID do recurso
Utilizar a API REST realmente pode ajudar a compreender as definições de métricas disponíveis, granularidade e valores relacionados. Que informações são úteis quando utilizar a [Biblioteca de gestão de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para o código anterior, o ID do recurso para utilizar é o caminho completo para o recurso Azure pretendido. Por exemplo, para consultar contra uma aplicação Web do Azure, o ID do recurso seria:

*/subscriptions/{Subscription-ID}/resourceGroups/{Resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista seguinte contém alguns exemplos de formatos de ID do recurso para vários recursos Azure:

* **Concentrador IoT** – /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.Devices/IotHubs/_{nome da concentrador iot}_

* **Agrupamento de SQL flexível** - /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.Sql/servers/_{agrupamento db}_/elasticpools/_{nome de agrupamento de sql}_

* **Base de dados do SQL (v12)** - /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.Sql/servers/_{nome do servidor}_/databases/_{nome da base de dados}_

* **Serviço Bus** - /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.ServiceBus/_{espaço de nomes}_/_{nome do servicebus}_

* **Conjuntos de escala VM** - /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{nome do vm}_

* **VMs** - /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.Compute/virtualMachines/_{nome do vm}_

* **Evento concentradores** - /subscriptions/_{id da subscrição}_/resourceGroups/_{nome de grupo de recursos}_/providers/Microsoft.EventHub/namespaces/_{eventhub-espaço de nomes}_


Existem abordagens alternativas para obter o ID do recurso, incluindo através do Explorador de recursos do Azure, visualizar o recurso pretendido no portal do Azure e através do PowerShell ou o clip do Azure.

### <a name="azure-resource-explorer"></a>Recurso Azure Explorer
Para localizar o ID do recurso para um recurso pretendido, uma abordagem útil é utilizar a ferramenta de [Azure recurso Explorer](https://resources.azure.com) . Navegue para o recurso pretendido e, em seguida, veja o ID apresentado, tal como a captura de ecrã seguinte:

![ALT "Recurso Azure o Explorador"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure
Também pode ser obtido o ID do recurso a partir do portal Azure. Para fazê-lo, navegue para o recurso pretendido e, em seguida, clique em propriedades. O ID do recurso é apresentado no pá propriedades, conforme visto na captura de ecrã seguinte:

![ALT "ID do recurso apresentado na pá propriedades no portal do Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
O ID do recurso pode ser obtido utilizando os cmdlets do Azure PowerShell também. Por exemplo, para obter o ID do recurso para uma aplicação Web do Azure, execute o cmdlet Get-AzureRmWebApp, tal como a captura de ecrã seguinte:

![ALT "ID do recurso obtida através do PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>Clip Azure
Para obter o ID do recurso utilizando o clip do Azure, executar o comando 'Web App azure Mostrar', especificando o ' – json' opção, tal como apresentado na captura de ecrã seguinte:

![ALT "ID do recurso obtida através do PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Obter dados de registo de atividade
Para além de trabalhar com definições métricas e valores relacionados, também é possível obter informações interessantes adicionais relacionados com recursos Azure. Por exemplo, é possível dados do [registo de atividade](https://msdn.microsoft.com/library/azure/dn931934.aspx) de consulta. O exemplo seguinte demonstra a utilizar o Azure Monitor REST API para dados de registo de atividade de consulta dentro de um intervalo de datas específico para uma subscrição do Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Próximos passos
* Rever a [Descrição geral da monitorização](monitoring-overview.md).
* Ver as [métricas suportados com o Azure Monitor](monitoring-supported-metrics.md).
* Reveja a [Referência da API do resto do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Reveja a [biblioteca de gestão Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
