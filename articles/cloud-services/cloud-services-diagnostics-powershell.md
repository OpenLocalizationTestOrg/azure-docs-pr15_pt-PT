<properties
    pageTitle="Activar diagnósticos no Azure serviços em nuvem através do PowerShell | Microsoft Azure"
    description="Saiba como ativar diagnósticos para serviços em nuvem através do PowerShell"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Activar diagnósticos no Azure serviços em nuvem através do PowerShell

É possível recolher dados diagnóstico como registos da aplicação, etc. a partir de um serviço na nuvem utilizando a extensão do Azure diagnóstico do contador de desempenho. Este artigo descreve como pode ativar a extensão do Azure diagnóstico para um serviço na nuvem através do PowerShell.  Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para deste artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um serviço na nuvem

Esta abordagem de boa para tipo de integração contínua de cenários onde pode ser ativada a extensão diagnóstico como parte de implementação do serviço em nuvem. Quando criar uma nova implementação de serviço na nuvem, pode ativar a extensão diagnóstico ao passagem no parâmetro *ExtensionConfiguration* para o cmdlet [AzureDeployment novo](https://msdn.microsoft.com/library/azure/mt589089.aspx) . O parâmetro *ExtensionConfiguration* leva-o até uma matriz de configurações diagnóstico que podem ser criados utilizando o cmdlet [AzureServiceDiagnosticsExtensionConfig novo](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

O exemplo seguinte mostra como pode ativar diagnósticos para um serviço na nuvem com um WebRole e WorkerRole cada um com uma configuração de diagnósticos diferente.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Se o ficheiro de configuração de diagnósticos Especifica um elemento StorageAccount com um nome de conta de armazenamento, em seguida, o cmdlet novo AzureServiceDiagnosticsExtensionConfig utilizar automaticamente essa conta de armazenamento. Para que isto funcione, a conta de armazenamento tem de estar na mesma subscrição do serviço em nuvem a ser implementada.

A partir do Azure SDK 2.6 em diante publicar os ficheiros de configuração de extensão gerados pela MSBuild destino irá incluir o nome da conta de armazenamento com base na cadeia de configuração de diagnósticos especificada no ficheiro de configuração de serviço (.cscfg). O script abaixo mostra-lhe como analisar os ficheiros de configuração de extensão a partir do destino de publicar e configurar extensão diagnóstico para cada função quando implementar o serviço de nuvem.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio Online utiliza uma abordagem semelhante para deploymnts automatizado dos serviços em nuvem com a extensão diagnóstico. Consulte [Publicar AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) para um exemplo completo.

Se não StorageAccount foi especificado na configuração do diagnóstico, em seguida, é necessário transmitir no parâmetro StorageAccountName para o cmdlet. Se o parâmetro StorageAccountName for especificado, em seguida, o cmdlet utilizar sempre a conta de armazenamento especificado no parâmetro de e não aquele especificado no ficheiro de configuração diagnóstico.

Se estiver a conta de armazenamento de diagnóstico numa subscrição diferente do serviço de nuvem, em seguida, tem de ser efetuada com explicitamente nos parâmetros StorageAccountName e StorageAccountKey o cmdlet. O parâmetro StorageAccountKey não é necessária quando a conta de armazenamento de diagnóstico está na mesma subscrição, tal como o cmdlet pode consultar automaticamente e definir o valor de chave quando ativar a extensão de diagnóstico. No entanto, se estiver a conta de armazenamento de diagnóstico numa subscrição diferente, o cmdlet poderá não conseguir obter a chave automaticamente e precisa de especificar explicitamente a tecla através do parâmetro StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico sobre um serviço de nuvem existente

Pode utilizar o cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) para ativar ou atualizar a configuração de diagnósticos num serviço na nuvem que já está em execução.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração da extensão diagnósticos atual
Utilize o cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) para obter a configuração de diagnósticos atual para um serviço na nuvem.

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
Pode utilizar o cmdlet [AzureServiceDiagnosticsExtension remover](https://msdn.microsoft.com/library/azure/mt589183.aspx) para desativar a opção diagnósticos de um serviço na nuvem.

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Se estiver ativado a extensão de diagnóstico utilizar *Conjunto AzureServiceDiagnosticsExtension* ou o *Novo AzureServiceDiagnosticsExtensionConfig* sem o parâmetro de *função* , em seguida, pode remover a extensão utilizando *Remover AzureServiceDiagnosticsExtension* sem o parâmetro de *função* . Se o parâmetro de *função* foi utilizado quando activar a extensão, em seguida, tem também ser utilizada quando remover a extensão.

Para remover a extensão de diagnósticos de cada função individual:

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Próximos passos

- Para obter orientações adicionais sobre a utilização dos diagnósticos do Azure e outras técnicas para resolver os problemas, consulte o artigo [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](cloud-services-dotnet-diagnostics.md).
- O [Esquema de configuração de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) explica as várias opções de configurações de xml para a extensão de diagnóstico.
- Para saber como pode ativar a extensão de diagnóstico para máquinas virtuais, consulte o artigo [criar uma Máquina Virtual do Windows com a monitorização e diagnósticos de utilizar o modelo de Gestor de recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
