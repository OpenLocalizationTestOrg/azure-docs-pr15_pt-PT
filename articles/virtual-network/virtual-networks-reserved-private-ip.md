<properties 
   pageTitle="Como configurar um interno privado endereço IP estático"
   description="Noções sobre IPs interno estático (diminuições) e como geri-los"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Como configurar um endereço IP de privado interno estático através do PowerShell (clássico)
Na maioria dos casos, não é necessário especificar um endereço IP estático interno para a sua máquina virtual. VMs numa rede virtual irão receber automaticamente um endereço IP interno a partir de um intervalo que especificar. Mas em certos casos, especificar um endereço IP estático para uma determinada VM sentido. Por exemplo, se o seu VM é aceder ao executar o DNS ou será um controlador de domínio. Um endereço IP estático interno permanece VM mesmo através de um Estado de parar/deprovision. 

> [AZURE.IMPORTANT] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos e clássica](../resource-manager-deployment-model.md). Este artigo aborda a utilizar o modelo de implementação clássica. A Microsoft recomenda a maioria das novas implementações utilização de [modelo de implementação do Gestor de recursos](virtual-networks-static-private-ip-arm-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Como verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *10.0.0.7* está disponível num vnet denominada *TestVnet*, execute o seguinte comando PowerShell e verifique se o valor para *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] Se quiser experimentar o comando acima no seguir ambiente seguros as diretrizes no artigo [criar uma rede Virtual](virtual-networks-create-vnet-classic-portal.md) para criar um vnet *TestVnet* o nome e certifique-se de que utiliza o espaço de endereços *10.0.0.0/8* .

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Como especificar um endereço IP estático interno quando criar uma VM
O script do PowerShell cria um novo serviço de nuvem denominado *TestService*, em seguida, obtém uma imagem a partir do Azure, em seguida, cria uma VM denominada *TestVM* no novo serviço em nuvem utilizando a imagem obtida, define a VM para ser numa sub-rede denominada *sub-rede 1*e define *10.0.0.7* como um endereço IP estático interno para a VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Como obter estáticos internos IP informações para uma VM
Para ver as informações de IP internas estáticas para a VM criada com o script acima, execute o seguinte comando PowerShell e observar os valores para o *endereço IP*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Como remover um endereço IP estático interno de uma VM
Para remover o IP estático interno adicionado à VM no script acima, execute o seguinte comando do PowerShell:
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Como adicionar um endereço IP estático interno para uma VM existente
Para adicionar um estático interno IP da VM criadas utilizando o script acima, execução ele seguinte comando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>Próximos passos

[IP reservado](virtual-networks-reserved-public-ip.md)

[Endereço IP público de nível da instância (ILPIP)](virtual-networks-instance-level-public-ip.md)

[IP reservado REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
