<properties
    pageTitle="Gerir VMs num conjunto de escala de Máquina Virtual | Microsoft Azure"
    description="Gerir máquinas virtuais numa escala de máquina virtual definir através do Azure PowerShell."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gerir máquinas virtuais num conjunto de escala de máquina virtual

Utilize as tarefas neste artigo para gerir máquinas virtuais no seu conjunto de escala de máquina virtual.

A maior parte das tarefas que envolvam gerir uma máquina virtual num conjunto de escala requer que conhece o ID da instância do computador que pretende gerir. Pode utilizar o [Explorador de recursos do Azure](https://resources.azure.com) para localizar o ID da instância de uma máquina virtual num conjunto de escala. Também é utilizar o Explorador de recursos para verificar o estado das tarefas que pode concluir.

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a sua subscrição e iniciar sessão na sua conta.

## <a name="display-information-about-a-scale-set"></a>Apresentar informações sobre um conjunto de escala

Pode obter informações gerais sobre um conjunto de escala, também conhecida como vista instância. Em alternativa, pode obter informações mais específicas, como informações sobre os recursos no conjunto de escala.

Substitua os valores entre aspas com o nome ou o grupo de recursos e escala definir e, em seguida, execute o comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Devolve algo parecido com:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Substitua os valores entre aspas com o nome do seu conjunto de grupo e a escala do recurso. Substituir *#* com o identificador de instância da máquina virtual que pretende obter informações sobre como e, em seguida, executá-la:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Devolve algo parecido com este exemplo:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Iniciar uma máquina virtual num conjunto de escala

Substitua os valores entre aspas com o nome do seu conjunto de grupo e a escala do recurso. Substituir *#* com o identificador da máquina virtual ao qual pretende iniciar e, em seguida, executá-la:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

No Explorador do recurso, pode Vemos que o estado da instância está **em execução**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Pode começar a todas as máquinas virtuais na escala definir ao não utilizar o parâmetro - InstanceId.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Parar uma máquina virtual num conjunto de escala

Substitua os valores entre aspas com o nome do seu conjunto de grupo e a escala do recurso. Substituir *#* com o identificador da máquina virtual que pretende parar e, em seguida, executá-la:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

No Explorador do recurso, é possível ver o estado da instância é **desatribuído**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Para interromper uma máquina virtual e não retirá-lo, utilize o parâmetro - StayProvisioned. Pode deixar de todas as máquinas virtuais do conjunto ao não utilizar o parâmetro - InstanceId.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Reiniciar uma máquina virtual num conjunto de escala

Substitua o nome do seu grupo de recursos e o conjunto de escala os valores entre aspas. Substituir *#* com o identificador da máquina virtual ao qual pretende reiniciar e, em seguida, executá-la:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Pode reiniciar todas as máquinas virtuais do conjunto ao não utilizar o parâmetro - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Remover uma máquina virtual de um conjunto de escala

Substitua o nome do seu grupo de recursos e o conjunto de escala os valores entre aspas. Substituir *#* com o identificador da máquina virtual que pretende remover e, em seguida, executá-la:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Pode remover o conjunto de escala de máquina virtual ao mesmo tempo ao não utilizar o parâmetro - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de escala

Pode adicionar ou remover máquinas virtuais alterando a capacidade do conjunto. Obter o conjunto de escala que pretende alterar, definir a capacidade ao que pretende que fique e, em seguida, atualize a escala conjunto com a capacidade de nova. Nestes comandos, substitua os valores entre aspas o nome do seu grupo de recursos e o conjunto de escala.

  $vmss = get AzureRmVmss - ResourceGroupName "grupo nome do recurso" - VMScaleSetName "nome do conjunto de escala" $vmss.sku.capacity = 5 atualização AzureRmVmss - ResourceGroupName "grupo nome do recurso"-nome "nome do conjunto de dimensionar" - VirtualMachineScaleSet $vmss 

Se estiver a remover máquinas virtuais do conjunto de escala, as máquinas virtuais com os ids de mais altos são removidas pela primeira vez.
