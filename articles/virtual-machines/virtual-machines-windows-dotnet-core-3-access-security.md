<properties
   pageTitle="Access e a segurança em modelos de Gestor de recursos do Azure | Microsoft Azure" 
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Access e a segurança em modelos de Gestor de recursos do Azure

Aplicações alojadas no Azure provável que precisam de ser access através da internet ou está numa VPN / ligação encaminhar Express com Azure. Com a amostra de aplicação da loja de música, o web site é disponibilizado na internet com um endereço IP público. Com o access estabelecido, devem ser protegidos ligações para a aplicação e acesso aos recursos de máquina virtual próprios. Este segurança do access é fornecida com um grupo de segurança de rede. 

Este documento detalhes como a aplicação de arquivo de música é protegida no modelo de Gestor de recursos do Azure de exemplo. Todas as dependências e exclusivas configurações estão realçadas. Para obter a melhor experiência, previamente implemente uma instância da solução a sua subscrição do Azure e funcionam juntamente com o modelo de Gestor de recursos do Azure. O modelo concluído pode ser encontrado aqui – [Implementação de arquivo de música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).


## <a name="public-ip-address"></a>Endereço IP público

Para fornecer acesso do público para um recurso do Azure, pode ser utilizado um recurso de endereço IP público. Pode ser configurado o endereço IP público com um endereço IP estático ou dinâmico. Se é utilizado um endereço dinâmico, a máquina virtual é e parada desatribuída, os endereços é removido. Quando o computador é iniciado novamente, poderá atribuído um endereço IP público diferente. Para impedir que um endereço IP alterem, pode ser utilizado um endereço IP reservado. 

Um endereço IP público podem ser adicionado a um modelo de Gestor de recursos do Azure utilizando o Visual Studio adicionar novo recurso assistente ou inserindo JSON válido para um modelo. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Um endereço IP público podem ser associado com uma placa de rede Virtual ou um balanceador de carga. Neste exemplo, uma vez que o Web site loja de música é distribuído ao longo máquinas virtuais várias, o endereço IP público é anexado às Balanceador de carga.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [associação do endereço IP público com Balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

O endereço IP público como se vê a partir do portal Azure. Repare que o endereço IP público está associado a um balanceador de carga e não uma máquina de virtual. Balanceadores de carga de rede são detalhados no documento seguinte desta série.

![Endereço IP público](./media/virtual-machines-windows-dotnet-core/pubip-win.png)

Para mais informações sobre os endereços IP Azure públicos, consulte o artigo [endereços IP no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Grupo de segurança de rede

Assim que tiver sido estabelecido acesso aos recursos Azure, este acesso deve ser limitado. Para máquinas virtuais Azure, acesso seguro é feito utilizando um grupo de segurança de rede. Com a amostra de aplicação da loja de música, todo o acesso à máquina virtual é restrito exceto para através de porta 80 para acesso http e porta 3389 para acesso RDP. Um grupo de segurança de rede podem ser adicionado a um modelo de Gestor de recursos do Azure utilizando o Visual Studio adicionar novo recurso assistente ou inserindo JSON válido para um modelo.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Grupo de segurança de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57).

```none
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

Neste exemplo, o grupo de segurança de rede é associar com o objeto de sub-rede declarado no recurso de rede Virtual. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [associação de grupo de segurança de rede com rede Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143).


```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
]
```

Eis o aspeto do grupo de segurança de rede a partir do portal Azure. Repare que pode ser uma NSG associar uma interface sub-rede e / ou de rede. Neste caso, a NSG está associada a uma sub-rede. Nesta configuração, as regras de entrada aplicam a todas as máquinas virtuais ligadas à sub-rede.

![Grupo de segurança de rede](./media/virtual-machines-windows-dotnet-core/nsg-win.png)

Para obter informações aprofundadas sobre os grupos de segurança de rede, consulte o artigo [o que é um grupo de segurança de rede]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Passo seguinte

<hr>

[Passo 3 - disponibilidade e a escala em modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-4-availability-scale.md)
