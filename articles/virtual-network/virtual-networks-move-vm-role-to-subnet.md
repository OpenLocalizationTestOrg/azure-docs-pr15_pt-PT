<properties 
   pageTitle="Como mover uma instância VM ou função para sub-rede diferente"
   description="Saiba como mover VMs e instâncias de função para uma sub-rede diferente"
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

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Como mover uma instância VM ou função para sub-rede diferente

Pode utilizar o PowerShell para mover o seu VMs de uma sub-rede para outro na mesma rede virtual (VNet). Instâncias de função podem ser movidas pela CSCFG de edição, em vez de utilizar o PowerShell.

>[AZURE.NOTE] Este artigo contém informações que são relativo Azure apenas implementações clássicas.

Por que motivo mover VMs para outra sub-rede? Migração de sub-rede é útil quando a mais antiga sub-rede é demasiado pequena e não pode ser expandida devido existentes VMs em execução nessa sub-rede. Nesse caso, pode criar uma nova, maior sub-rede e migrar as VMs para a nova sub-rede, em seguida, após a migração estiver concluída, pode eliminar a antiga sub-rede vazia.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Como mover uma VM para outra sub-rede

Para mover uma VM, execute o cmdlet do PowerShell AzureSubnet conjunto, ao utilizar o exemplo abaixo como um modelo. No exemplo abaixo, podemos está a mover TestVM a partir da sua sub-rede apresentar, à sub-rede 2. Certifique-se de que a editar o exemplo para refletir o seu ambiente. Tenha em atenção que sempre que executar o cmdlet AzureVM atualização como parte de um procedimento, irá reiniciar a VM como parte do processo de atualização.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Se tiver especificado um interno privado endereço IP estático para sua VM, terá de limpar essa definição antes de poder mover a VM para uma nova sub-rede. Nesse caso, utilize o seguinte:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover uma instância de função para outra sub-rede

Para mover uma instância de função, edite o ficheiro CSCFG. No exemplo abaixo, podemos está a mover "Role0" na rede virtual *VNETName* da sua sub-rede apresentar à *sub-rede 2*. Uma vez que já foi implementada a instância de função, irá alterar apenas o nome de sub-rede = sub-rede-2. Certifique-se de que a editar o exemplo para refletir o seu ambiente.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
