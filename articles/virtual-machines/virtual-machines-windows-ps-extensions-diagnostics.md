<properties
    pageTitle="Utilizar o PowerShell para activar diagnósticos do Azure numa máquina virtual a executar o Windows | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Saiba como utilizar o PowerShell para activar diagnósticos do Azure numa máquina virtual a executar o Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Utilizar o PowerShell para activar diagnósticos do Azure numa máquina virtual a executar o Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnósticos do Azure são a capacidade de Azure ativa a coleção de diagnóstico dados numa aplicação implementada. Pode utilizar a extensão de diagnóstico para recolher dados de diagnóstico como registos da aplicação ou contadores de desempenho a partir de uma máquina de virtual (VM) Azure que está a executar o Windows. Este artigo descreve como utilizar o Windows PowerShell para ativar a extensão de diagnóstico para uma VM. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para deste artigo.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Activar a extensão de diagnóstico, se utilizar o modelo de implementação do Gestor de recursos

Pode ativar a extensão de diagnóstico enquanto cria uma VM do Windows através do modelo de implementação do Gestor de recursos do Azure adicionando a configuração de extensão para o modelo de Gestor de recursos. Consulte o artigo [criar uma máquina de virtual do Windows com a monitorização e diagnósticos de utilizando o modelo de Gestor de recursos do Azure](virtual-machines-windows-extensions-diagnostics-template.md).

Para ativar a extensão de diagnóstico sobre um VM existente que foi criado através do modelo de implementação do Gestor de recursos, pode utilizar o cmdlet do PowerShell [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) conforme apresentado abaixo.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* é o caminho para o ficheiro que contém a configuração de diagnóstico em XML, tal como descrito no [exemplo](#sample-diagnostics-configuration) abaixo.  

Se o ficheiro de configuração de diagnósticos Especifica um elemento **StorageAccount** com um nome de conta de armazenamento, em seguida, o script *Conjunto AzureRMVMDiagnosticsExtension* configurar automaticamente a extensão de diagnóstico para enviar dados de diagnóstico para essa conta de armazenamento. Para que isto funcione, a conta de armazenamento tem de estar na mesma subscrição como a VM.

Se não **StorageAccount** foi especificado na configuração de diagnóstico, em seguida, é necessário transmitir no parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, o cmdlet sempre irá utilizar a conta de armazenamento especificado no parâmetro e não o que é especificado no ficheiro de configuração de diagnóstico.

Se estiver a conta de armazenamento de diagnóstico numa subscrição diferente da VM, em seguida, tem de passar explicitamente nos parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessária quando a conta de armazenamento de diagnóstico está na mesma subscrição, tal como o cmdlet pode consultar automaticamente e definir o valor de chave quando ativar a extensão de diagnóstico. No entanto, se estiver a conta de armazenamento de diagnóstico numa subscrição diferente, o cmdlet poderá não conseguir obter a chave automaticamente e precisa de especificar explicitamente a tecla através do parâmetro *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Assim que a extensão de diagnóstico está ativada numa VM, pode obter as definições atuais utilizando o cmdlet [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) .

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

O cmdlet devolve *PublicSettings*, que contém a configuração de XML num formato codificado em Base64. Para ler XML, tem de descodificá-lo.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

O cmdlet [Remover AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) pode ser utilizado para remover a extensão de diagnósticos da VM.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Activar a extensão de diagnóstico, se utilizar o modelo clássico de implementação

Pode utilizar o cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) para activar uma extensão de diagnóstico sobre uma VM criados por si através do modelo de implementação clássica. O exemplo seguinte mostra como criar uma nova VM através do modelo de implementação clássica com a extensão de diagnóstico ativada.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Para ativar a extensão de diagnóstico numa VM existente que foi criado através do modelo clássico de implementação, utilize o cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) para obter a configuração de VM. Em seguida, atualize a configuração do VM para incluir a extensão de diagnósticos utilizando o cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) . Por fim, aplicam-se a configuração atualizada para a VM utilizando [AzureVM de atualização](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Configuração de diagnósticos de exemplo

O XML seguinte pode ser utilizado para a configuração do público diagnósticos com os scripts acima. Esta configuração de exemplo irá transferir vários contadores de desempenho para a conta de armazenamento de diagnóstico, juntamente com os erros da aplicação, segurança e canais de sistema nos registos de eventos do Windows e qualquer erros partir dos registos de infraestrutura de diagnóstico.

A configuração tem de ser atualizadas para incluir o seguinte:

- O atributo *resourceID* do elemento **métricas** tem de ser atualizadas com o ID do recurso para a VM.
    - O ID do recurso pode ser criado utilizando o seguinte padrão de: "/resourceGroups//subscrições / {*ID da subscrição para a subscrição com a VM*} {*o nome de resourcegroup para a VM*} / providers/Microsoft.Compute/virtualMachines/ {*o nome VM*}".
    - Por exemplo, se o ID da subscrição para a subscrição que está a ser executado a VM é **11111111-1111-1111-1111-111111111111**, o nome do grupo de recursos para o grupo de recursos é **MyResourceGroup**e o nome do VM é **MyWindowsVM**, em seguida, o valor para *resourceID* seria:

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Para obter mais informações sobre como métricas são geradas com base na configuração de contadores e métricas de desempenho, consulte [Diagnósticos do Azure tabela métricas de armazenamento](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- O elemento **StorageAccount** tem de ser atualizados com o nome da conta de armazenamento de diagnóstico.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Próximos passos
- Para obter orientações adicionais sobre como utilizar a funcionalidade diagnósticos do Azure e outras técnicas para resolver os problemas, consulte o artigo [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Esquema de configurações de diagnóstico](https://msdn.microsoft.com/library/azure/mt634524.aspx) explica as várias opções de configurações de XML para a extensão de diagnóstico.
