<properties 
    pageTitle="Script do PowerShell para criar um recurso de informações de aplicação" 
    description="Automatize a criação de recursos de informações da aplicação." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>Script do PowerShell para criar um recurso de informações de aplicação

*Informações de aplicação está na pré-visualização.*

Quando pretender monitorizar uma nova aplicação - ou uma nova versão de uma aplicação, com [Informações de aplicação do Visual Studio](https://azure.microsoft.com/services/application-insights/), configurar um novo recurso no Microsoft Azure. Este recurso é onde os dados de telemetria da sua aplicação estão analisados e apresentados. 

Pode automatizar a criação de um novo recurso ao utilizar o PowerShell.

Por exemplo, se estiver a desenvolver uma aplicação do dispositivo móvel, é provável que, em qualquer altura, haverá várias versões publicadas da sua aplicação utilizado pelos seus clientes. Não pretende obter os resultados de telemetria de diferentes versões misturadas. Por isso, irá obter o processo de compilação para criar um novo recurso para cada compilação.

## <a name="script-to-create-an-application-insights-resource"></a>Script para criar um recurso de informações de aplicação

Consulte o artigo especificações o cmdlet relevantes:

* [Novo AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [Novo AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*PowerShell Script*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>O que fazer com o iKey

Cada recurso é identificado pela sua chave de instrumentação (iKey). O iKey é um resultado do script de criação de recursos. Script de compilação deve fornecer que ikey para o SDK de informações da aplicação incorporado na sua aplicação.

Existem duas formas de tornar a iKey disponível para o SDK:
  
* No [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou [códigos de inicialização](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>Consulte também

* [Criar aplicações informações e recursos de teste de web a partir de modelos](app-insights-powershell.md)
* [Configurar o controlo dos diagnósticos do Azure com PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Definir alertas utilizando o PowerShell](app-insights-powershell-alerts.md)

 