<properties
   pageTitle="Abra portas para uma VM através do PowerShell | Microsoft Azure"
   description="Saiba como abrir uma porta / crie um ponto final para sua VM do Windows com o modo de implementação do Azure recurso gestor e o Azure PowerShell"
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
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Posso abrir portas e os pontos finais para uma VM no Azure através do PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e ACL regras tem [a versão mais recente do PowerShell Azure instalado](../powershell-install-configure.md). Também pode [executar estes passos através do portal Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Inicie sessão na sua conta Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Exemplo os nomes dos parâmetros incluídos `myResourceGroup`, `myNetworkSecurityGroup`, e `myVnet`.

Crie uma regra. O exemplo seguinte cria uma regra com o nome `myNetworkSecurityGroupRule` para permitir o tráfego TCP na porta 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Em seguida, crie o seu grupo de segurança da rede e atribua a regra HTTP que acabou de criar da seguinte forma. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Agora vamos atribuir o seu grupo de segurança de rede para uma sub-rede. O exemplo seguinte atribui uma rede virtual existente com o nome `myVnet` para a variável de `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe o seu grupo de segurança de rede com a sua sub-rede aceda. O exemplo seguinte associa sub-rede denominada `mySubnet` com o seu grupo de segurança de rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize a sua rede para que as alterações tenham efeito virtual:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre os grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe começar a trabalhar rapidamente com o tráfego a fluir para sua VM. Grupos de segurança de rede fornecem muitas funcionalidades excelentes e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre a [criação de um grupo de segurança de rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Pode definir grupos de segurança de rede e as regras ACL como parte do Gestor de recursos do Azure modelos. Leia mais sobre a [criação de grupos de segurança de rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar de utilizar o reencaminhamento de portas para mapear uma porta externa exclusiva para uma porta interna no seu VM, utilize um balanceador de carga e as regras de tradução de endereços da rede (NAT). Por exemplo, poderá querer para expor TCP porta 8080 externamente e ter o tráfego direcionado para a porta TCP 80 uma VM. Pode obter informações sobre a [criação de um balanceador de carga de acesso à Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximos passos
Neste exemplo, criado uma regra para permitir o tráfego HTTP simples. Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

- [Descrição geral do Gestor de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Descrição geral do Gestor de recursos do Azure para balanceadores de carga](../load-balancer/load-balancer-arm.md)