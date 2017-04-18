<properties
   pageTitle="Benefícios de utilizar híbrido Azure janela servidor de | Microsoft Azure"
   description="Saiba como maximizar os benefícios da sua Windows Server Software Assurance para trazer licenças no local para Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Benefício de utilização do Azure híbrido para o Windows Server

Para utilizar o Windows Server com o Software Assurance de clientes, pode trazer o seu no local licenças do Windows Server para Azure e executar o Windows Server VMs no Azure um custo reduzido. A vantagem de utilizar Azure híbrido permite-lhe executar o Windows Server VMs no Azure e obter faturada apenas para a taxa de cluster base. Para mais informações, consulte o artigo o [benefício de utilização do Azure híbrido licenciamento da página](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Este artigo explica como implementar o Windows Server VMs no Azure para utilizar este benefício licenciamento.

> [AZURE.NOTE] Não pode utilizar imagens Azure Marketplace para implementar o Windows Server VMs utilizando a vantagem de utilizar do Azure híbrido. Tem de implementar os VMs utilizar modelos de PowerShell ou Gestor de recursos para registar corretamente o seu VMs como elegíveis para descontos de taxa de cluster base.

## <a name="pre-requisites"></a>Pré-requisitos
Existem algumas pré-requisitos para poder utilizam Azure híbrido utilização benefício para o Windows Server VMs no Azure:

- Tem o módulo Azure PowerShell instalado
- O VHD de servidor do Windows carregou para armazenamento do Windows Azure

### <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
Certifique-se de que ter [instalado e configurado o PowerShell Azure mais recente](../powershell-install-configure.md). Mesmo se passar para implementar o seu VMs utilizar modelos de Gestor de recursos, ainda precisa de Azure PowerShell instalado para carregar o VHD de servidor do Windows (consulte o passo seguinte).

### <a name="upload-a-windows-server-vhd"></a>Carregar um servidor do Windows VHD

Para implementar uma VM de servidor do Windows no Azure, primeiro tem de criar um VHD que contém a sua base compilação do Windows Server. Este VHD deve ser corretamente através do Sysprep preparado antes de iniciar carregue-o para Azure. Pode [Ler mais sobre os requisitos de VHD e processo Sysprep](./virtual-machines-windows-upload-image.md) e [Sysprep suporte para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Criar cópias de segurança a VM antes de executar Sysprep. Após ter preparado seu VHD, carregar o VHD a sua conta de armazenamento do Windows Azure utilizando o `Add-AzureRmVhd` cmdlet da seguinte forma:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint Server e Dynamics também podem utilizar o seu Licenciamento de Software Assurance. Ainda precisa de preparar a imagem do Windows Server ao instalar o seu componentes da aplicação e fornecer as chaves de licença, consoante adequado, em seguida, carregar a imagem do disco para Azure. Reveja a documentação adequada para executar o Sysprep com a aplicação, tal como [Considerações para instalar o SQL Server utilizando o Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [criar uma imagem no SharePoint Server 2016 referência (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

Também pode ler mais sobre o [carregamento de VHD processo Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Este artigo foca-se em implementar o Windows Server VMs. Também pode implementar VMs de cliente do Windows da mesma forma. Os exemplos seguintes, o utilizador substitui `Server` com `Client` corretamente.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Implementar uma VM através do PowerShell guia
Quando implementar o seu VM de servidor do Windows através do PowerShell, têm um parâmetro adicional para `-LicenseType`. Assim que tiver o seu VHD carregado para Azure, cria um novo VM utilizando `New-AzureRmVM` e especifique o tipo de licenciamento da seguinte forma:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Pode [ler instruções mais detalhadas sobre como implementar uma VM no Azure através do PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) abaixo ou ler um guia mais descritivo sobre os diferentes passos para [criar uma VM Windows utilizando o Gestor de recursos e PowerShell](./virtual-machines-windows-ps-create.md).

## <a name="deploy-a-vm-via-resource-manager"></a>Implementar uma VM através do Gestor de recursos
Dentro dos modelos de Gestor de recursos, um parâmetro adicional para `licenseType` pode ser especificado. Pode ler mais sobre a [criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md). Assim que tiver o seu VHD carregado para Azure, edite o modelo de Gestor de recursos para incluir o tipo de licença como parte do fornecedor de cluster e implementar o modelo normal como:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Confirmar o que seu VM está a utilizar o benefício de licenciamento
Depois de ter implementado a VM através de um do PowerShell ou Gestor de recursos do método de implementação, verifique se o tipo de licença com `Get-AzureRmVM` da seguinte forma:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

O resultado é semelhante ao seguinte:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Isto contrasta com a seguinte VM implementada sem o benefício de utilização do Azure híbrido licenciamento, tal como uma VM implementada diretamente a partir da Galeria de Azure:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>Instruções detalhadas sobre o PowerShell

Os seguintes passos detalhados do PowerShell mostram uma implementação total de uma VM. Pode obter mais contexto como para os componentes diferentes a ser criados no [criar uma VM Windows utilizando o Gestor de recursos e PowerShell](./virtual-machines-windows-ps-create.md)e cmdlets reais. Passo pela criação do seu grupo de recursos, conta de armazenamento e uma rede virtual, definir a VM e criar finalmente a VM.
 
Em primeiro lugar, segura obter credenciais, definir uma localização e nome do grupo de recursos:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Crie um endereço IP público:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Defina a sua sub-rede, NIC e VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nome do seu VM e crie um VM config:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Defina o seu sistema operativo:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Adicione o seu NIC para a VM:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Defina a conta de armazenamento para utilizar:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Carregue o seu VHD, convenientemente preparada e anexar a sua VM para utilização:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Por fim, crie a VM e definir o tipo de licenciamento para utilizar o benefício de utilização do Azure híbrido:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Próximos passos

Leia mais informações sobre o [Licenciamento do Azure híbrido utilização benefício](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Saiba mais sobre como [utilizar modelos de Gestor de recursos](../azure-resource-manager/resource-group-overview.md).
