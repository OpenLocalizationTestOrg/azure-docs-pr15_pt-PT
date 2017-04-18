<properties
    pageTitle="Migrar para o Gestor de recursos com PowerShell | Microsoft Azure"
    description="Este artigo explica a migração suportadas plataforma de IaaS recursos a partir do clássico para o Gestor de recursos do Azure através dos comandos do PowerShell do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar IaaS recursos de clássico para o Gestor de recursos do Azure utilizando o PowerShell do Azure

Estes passos mostram-lhe como utilizar comandos do Azure PowerShell para migrar infraestrutura como um serviço (IaaS) recursos a partir do modelo de implementação clássico para o modelo de implementação do Azure o Gestor de recursos. 

Se pretender, também pode migrar os recursos utilizando a [Interface de linha de comandos do Azure (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Para informações gerais sobre cenários de migração suportados, consulte o artigo [plataformas suportadas migração de IaaS recursos a partir do clássico para o Gestor de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager.md). 
- Para orientação detalhada e de instruções de migração, consulte o artigo [técnico vôo picado abrangente no suportadas plataforma a migração de clássico para o Gestor de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## <a name="step-1-plan-for-migration"></a>Passo 1: Planear a migração

Eis alguns procedimentos recomendados que recomendamos como avalie a migrar recursos de IaaS clássica para o Gestor de recursos:

- Leia através das [configurações e funcionalidades suportadas e não suportadas](virtual-machines-windows-migration-classic-resource-manager.md). Se tiver máquinas virtuais que utilizam configurações não suportadas ou funcionalidades, recomendamos que espera para o suporte técnico da funcionalidade/configuração ser anunciada. Em alternativa, se se adequa às suas necessidades, remover essa funcionalidade ou retirar que a configuração para ativar a migração.
-   Se tiver automatizado scripts implementar a infraestrutura e as aplicações hoje, tente criar uma configuração de teste semelhante ao utilizar esses scripts para migração. Em alternativa, pode configurar ambientes de exemplo, utilizando o portal do Azure.

>[AZURE.IMPORTANT]Atualmente, não são suportadas gateways de rede virtual (site-para-site, Azure ExpressRoute, gateway de aplicação, site de ponto) para a migração de clássico para o Gestor de recursos. Para migrar uma rede virtual clássica com um gateway, remova o gateway antes de executar uma operação de consolidação para mover a rede (pode executar o passo preparar sem eliminar o gateway). Depois de concluir a migração, voltar a ligar o gateway no Gestor de recursos do Azure.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Passo 2: Instalar a versão mais recente do PowerShell do Azure

Existem duas opções principais para instalar o Azure PowerShell: [Galeria de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web plataforma Installer (WebPI)](http://aka.ms/webpi-azps). WebPI recebe atualizações mensais. Galeria de PowerShell recebe atualizações de forma contínua. Este artigo é baseado no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Passo 3: Configurar a sua subscrição e inscrever-se para a migração

Em primeiro lugar, inicie uma linha de comandos do PowerShell. Migração, terá de configurar o seu ambiente para ambas as clássica e o Gestor de recursos.

Inicie sessão na sua conta para o modelo de Gestor de recursos.

```powershell
    Login-AzureRmAccount
```

Obter as subscrições disponíveis utilizando o seguinte comando:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Configure a sua subscrição do Azure para a sessão actual. Este exemplo define o nome da subscrição predefinido a **Minha subscrição do Azure**. Substitua o nome de subscrição de exemplo com o seu próprio. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] O registo é um único passo, mas deve fazê-lo uma vez antes de tentar migração. Sem registar, consulte a seguinte mensagem de erro: 

>   *BadRequest: Subscrição não está registada para a migração.* 

Registe-se com o fornecedor de recursos de migração utilizando o seguinte comando:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para o registo concluir. Pode verificar o estado da aprovação utilizando o seguinte comando:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Certifique-se de que está RegistrationState `Registered` antes de continuar. 

Agora, inicie sessão sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Obter as subscrições disponíveis utilizando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Configure a sua subscrição do Azure para a sessão actual. Este exemplo define a subscrição predefinido a **Minha subscrição do Azure**. Substitua o nome de subscrição de exemplo com o seu próprio. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 4: Certifique-se de que tem o suficiente núcleos máquina de Virtual do Gestor de recursos do Azure na região da sua implementação atual ou VNET Azure

Pode utilizar o seguinte comando do PowerShell para verificar se o número atual de núcleos que tiver no Gestor de recursos do Azure. Para saber mais acerca de quotas principais, consulte o artigo [limites e o Gestor de recursos do Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

Este exemplo verifica a disponibilidade na região **Ocidental dos EUA** . Substitua o nome de região de exemplo com o seu próprio. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Passo 5: Executar comandos para migrar os recursos do IaaS

>[AZURE.NOTE] Todas as operações descritas aqui ficam idempotent. Se tiver um problema diferente de uma funcionalidade não suportada ou um erro de configuração, recomendamos que repita preparar, interrupção ou a consolidação de operação. A plataforma tentará, em seguida, a ação novamente.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migrar máquinas virtuais num serviço de nuvem (não está na rede virtual)

Obter a lista de serviços em nuvem utilizando o seguinte comando e, em seguida, escolha o serviço de nuvem que pretende migrar. Se VMs no serviço de nuvem estão numa rede virtual ou se tiverem funções web ou de trabalho, o comando devolver uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obter o nome de implementação do serviço na nuvem. Neste exemplo, o nome do serviço é o **Meu serviço**. Substitua o nome do serviço de exemplo com o seu próprio nome de serviço. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Tem duas opções para escolher a partir de.

* **Opção 1. Migrar os VMs a uma rede virtual criado de plataforma**

    Em primeiro lugar, valide se pode migrar o serviço de nuvem utilizando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando anterior apresenta qualquer avisos e erros que bloqueiam migração. Se a validação é efetuada com êxito, em seguida, pode mover para o passo **preparar** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Opção 2. Migrar a uma rede virtual existente no modelo de implementação de Gestor de recursos**

    Este exemplo define o nome do grupo de recursos para **myResourceGroup**, o nome de rede virtual a **myVirtualNetwork** e o nome de sub-rede a **mySubNet**. Substitua os nomes no exemplo com os nomes dos seus recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Em primeiro lugar, valide se pode migrar o serviço de nuvem utilizando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando anterior apresenta qualquer avisos e erros que bloqueiam migração. Se a validação é efetuada com êxito, pode continuar com o passo preparar seguinte:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Após a operação de preparação ser bem sucedida com qualquer uma das opções anteriores, o estado de migração das VMs de consulta. Certifique-se de que estão na `Prepared` estado.

Este exemplo define o nome VM para **myVM**. Substitua o nome de exemplo com o seu próprio nome VM.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Verifique a configuração para os recursos preparadas utilizando o PowerShell ou o portal do Azure. Se não estiver pronto para migração e voltar para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada agrado, pode avançar e consolidar os recursos utilizando o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migrar máquinas virtuais numa rede virtual

Para migrar uma rede virtual máquinas virtuais, migrar a rede. As máquinas virtuais migrar automaticamente com a rede. Escolha a rede virtual que pretende migrar. 

Este exemplo define o nome de rede virtual para **myVnet**. Substitua o nome de rede virtual de exemplo com o seu próprio. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Se a rede virtual contém web ou funções de trabalho ou VMs com configurações não suportadas, obtém uma mensagem de erro de validação.

Em primeiro lugar, valide se pode migrar a rede virtual utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando anterior apresenta qualquer avisos e erros que bloqueiam migração. Se a validação é efetuada com êxito, pode continuar com o passo preparar seguinte:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas utilizando Azure PowerShell ou o portal do Azure. Se não estiver pronto para migração e voltar para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada agrado, pode avançar e consolidar os recursos utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migrar uma conta de armazenamento

Quando terminar de migrar as máquinas virtuais, recomendamos que migra contas de armazenamento.

Prepare cada conta de armazenamento para migração utilizando o comando seguinte. Neste exemplo, o nome de conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo com o nome da sua conta de armazenamento. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Verifique a configuração da conta de armazenamento preparada utilizando Azure PowerShell ou o portal do Azure. Se não estiver pronto para migração e voltar para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Se a configuração preparada agrado, pode avançar e consolidar os recursos utilizando o seguinte comando:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Próximos passos

- Para mais informações sobre a migração, consulte o artigo [plataformas suportadas migração de IaaS recursos a partir do clássico para o Gestor de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager.md).
- Para migrar recursos de rede adicionais para o Gestor de recursos através do Azure PowerShell, utilize os passos semelhantes com [Mover AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx), [Mover AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)e [Mover AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
- Para abrir origem scripts que pode utilizar para migrar Azure recursos a partir do clássico para o Gestor de recursos, consulte [as ferramentas da Comunidade para migração para migração do Gestor de recursos do Azure](virtual-machines-windows-migration-scripts.md)
