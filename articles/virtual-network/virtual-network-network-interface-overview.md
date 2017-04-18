<properties 
   pageTitle="Interfaces de rede | Microsoft Azure"
   description="Saiba mais sobre interfaces de rede Azure no Gestor de recursos do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>Interfaces de rede

Uma interface de rede (NIC) é a interligação entre uma Máquina Virtual (VM) e a rede de software subjacente. Este artigo explica o que uma interface de rede é e como são utilizadas no modelo de implementação de Gestor de recursos do Azure.

A Microsoft recomenda a implementar o novos recursos utilizando o modelo de implementação do Gestor de recursos, mas também pode implementar VMs com a conectividade da rede no modelo de implementação [clássica](virtual-network-ip-addresses-overview-classic.md) . Se estiver familiarizado com o modelo clássico, existem diferenças importantes no redes VM no modelo de implementação de Gestor de recursos. Saiba mais sobre as diferenças ao ler o artigo [Máquina Virtual rede - clássica](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

No Azure, uma interface de rede:

1. É um recurso que pode ser criado, eliminado, e tem as suas próprias definições configuráveis.
2. Deve estar ligada a uma sub-rede no Azure rede Virtual (VNet) quando é criado. Se não estiver familiarizado com VNets, saiba mais sobre os mesmos lendo o artigo [Descrição geral de rede Virtual](virtual-networks-overview.md) . NIC deve estar ligada a um VNet que existe na mesma [localização](https://azure.microsoft.com/regions) de Azure e [subscrição](../azure-glossary-cloud-terminology.md#subscription) como NIC. Após a criação de uma imagem, pode alterar a sub-rede que está ligado ao, mas não é possível alterar VNet que está ligado ao.
3. Tem atribuído um nome para o mesmo que não pode ser alterado após a NIC é criada. O nome têm de ser exclusivo dentro de um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups)do Azure, mas não tem de ser exclusivo a subscrição, a localização do Azure que é criada ou VNet que está ligado ao. NIC várias normalmente, é criada dentro de uma subscrição do Azure. É recomendado que elaborar Convenção de nomenclatura que faz a gestão de várias NIC facilita mais do que utilizar nomes predefinidos. Consulte o artigo [recomendado convenções de nomenclatura para recursos Azure](../guidance/guidance-naming-conventions.md) para obter sugestões.
4. Pode ser anexado uma VM, mas só pode ser anexado a uma única VM que existe na mesma localização como NIC.
5. Tem um endereço de MAC, que é mantido com NIC para, desde que permanece anexado a uma VM. O endereço MAC é persistente se a VM for reiniciada (a partir do sistema operativo) ou parado (retirar a atribuição) e começar a utilizar o Portal do Azure, Azure PowerShell ou a Interface de comandos do Azure. Se tem desligado a partir de uma VM e anexado a uma VM diferente, o NIC recebe um endereço de MAC diferente. Se o NIC é eliminado, o endereço MAC está atribuído a outros NIC.
6. Deve ter uma primária **privado** estático ou dinâmico endereço IP *IPv4* atribuído à mesma.
8. Poderá ter um recurso de endereço IP público associados à mesma.
9. Suporta acelerada de redes com Virtualização de e/s de raiz único (SR IOV) para tamanhos VM específicos a executar o versões específicas do sistema operativo do Microsoft Windows Server. Para saber mais sobre esta funcionalidade de pré-visualização, leia o artigo [Accelerated funcionamento em rede para uma máquina virtual](virtual-network-accelerated-networking-powershell.md) .
10. Pode receber o tráfego não destinado a endereços IP privados atribuídos à mesma se reencaminhamento IP estiver ativado para NIC. Se uma VM estiver a executar software de firewall por exemplo, encaminha os pacotes não destinados a suas própria endereços IP. A VM ainda tem de executar software capaz de encaminhar ou reencaminhar o tráfego, mas para fazê-lo, reencaminhamento IP tem de estar ativado para um NIC.
11. Muitas vezes é criada no mesmo grupo de recursos, como a VM-é anexado às ou o mesmo VNet que que está ligado ao, apesar de-não é necessário para ser.

## <a name="vms-with-multiple-network-interfaces"></a>VMs com várias interfaces de rede

NIC vários pode ser anexado a uma VM, mas quando fazê-lo, tenha em atenção o seguinte:  

- O tamanho da memória virtual tem de suportar NIC múltiplos. Para saber mais sobre quais os tamanhos VM que suportam NIC várias, leia os artigos [tamanhos do Windows Server VM](../virtual-machines/virtual-machines-windows-sizes.md) ou [Linux VM tamanhos](../virtual-machines/virtual-machines-linux-sizes.md) .   
- A VM tem de ser criada com, pelo menos, duas NIC. Se a VM é criada com apenas uma imagem, mesmo se o tamanho da memória virtual suporta mais do que um, não é possível anexar NIC adicional para a VM após ter sido criada. Desde que a VM foi criada com, pelo menos, duas NIC, pode anexar NIC adicional para a VM após ter sido criada, desde que o tamanho da memória virtual suporta mais de duas NIC.  
- Pode desanexar NIC secundário (NIC principal não é possível destacar) a partir de uma VM se a VM tem pelo menos três NIC anexada. Não é possível desanexar NIC se a VM tem dois ou menos NIC anexado ao mesmo.  
- A ordem da NIC a partir de dentro da VM será aleatória e também pode ser alteradas ao longo do Azure infraestrutura atualizações. No entanto, os endereços IP e o correspondente ethernet MAC endereços, permanecerá igual. Por exemplo, suponha que o sistema operativo identifica Azure NIC1 como Eth1. Eth1 tem o endereço IP 10.1.0.100 e endereço de MAC 00-0D-3A-B0-39-0D. Depois de uma infraestrutura Azure atualizar e reiniciar o computador, o sistema operativo podem agora identificar Azure NIC1 como Eth2, mas os endereços IP e MAC serão os mesmos como estavam quando o sistema operativo identificada Azure NIC1 como Eth1. Quando for um reinício iniciados por cliente, a ordem NIC permanecerá igual no interior do sistema operativo.  
- Se a VM é um membro de um [conjunto de disponibilidade](../azure-glossary-cloud-terminology.md#availability-set), todos os VMs dentro do conjunto de disponibilidade tem de ter uma única NIC ou NIC múltiplos. Se o VMs tiverem várias NIC, o número que cada tenham não é necessário que seja o mesmo, desde que cada VM tem, pelo menos, duas NIC.

## <a name="next-steps"></a>Próximos passos

- Saiba como criar uma VM com uma única NIC lendo o artigo [criar uma VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Saiba como criar uma VM com vários NIC lendo o artigo [Implementar uma VM com vários NIC](virtual-network-deploy-multinic-arm-ps.md) .
- Saiba como criar uma NIC com vários configurações de IP lendo o artigo [endereços IP múltiplos para máquinas virtuais Azure](virtual-network-multiple-ip-addresses-powershell.md) .
