<properties
    pageTitle="Utilizar o PowerShell para configuração informações de aplicação num Azure | Microsoft Azure"
    description="Automatize configurar diagnósticos do Azure pipe para informações de aplicação."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Utilizar o PowerShell para configurar a aplicação informações para uma aplicação web do Azure

[Microsoft Azure](https://azure.com) pode ser [configurado para enviar Azure diagnósticos](app-insights-azure-diagnostics.md) para [Informações de aplicação do Visual Studio](app-insights-overview.md). O diagnóstico dizem respeito aos serviços em nuvem Azure e Azure VMs. Estes complementam de telemetria enviadas a partir da aplicação utilizando o SDK de informações da aplicação. Como parte do automatizar o processo de criação de novos recursos no Azure, pode configurar diagnóstico através do PowerShell.

## <a name="azure-template"></a>Modelo do Azure

Se a aplicação web é no Azure e criar os recursos utilizando um modelo de Gestor de recursos do Azure, pode configurar as informações de aplicação adicionando isto para o nó de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-um nome para o recurso de informações de aplicação
* `myWebAppName`-o id da aplicação web


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um serviço na nuvem

O `New-AzureDeployment` cmdlet tem um parâmetro `ExtensionConfiguration`, que assume uma matriz de configurações de diagnóstico. Estes podem ser criados utilizando o `New-AzureServiceDiagnosticsExtensionConfig` cmdlet. Por exemplo:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico sobre um serviço de nuvem existente

Um serviço existente, utilize `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração da extensão diagnósticos atual

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Caso a ativado a extensão de diagnóstico através de `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sem o parâmetro de função, em seguida, pode remover a extensão do utilizando `Remove-AzureServiceDiagnosticsExtension` sem o parâmetro de função. Se o parâmetro de função foi utilizado quando activar a extensão, em seguida, tem também ser utilizada quando remover a extensão.

Para remover a extensão de diagnósticos de cada função individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Consulte também

* [Monitorizar Azure nuvem dos serviços de aplicações com informações de aplicação](app-insights-cloudservices.md)
* [Enviar Azure diagnósticos para informações de aplicação](app-insights-azure-diagnostics.md)
* [Automatizar configurar alertas](app-insights-powershell-alerts.md)

