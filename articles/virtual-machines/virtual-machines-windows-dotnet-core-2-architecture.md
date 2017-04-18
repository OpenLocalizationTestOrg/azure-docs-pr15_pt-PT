<properties
   pageTitle="Implementar a calcular recursos com modelos de Gestor de recursos do Azure | Microsoft Azure"
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

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Arquitetura de aplicação com modelos de Gestor de recursos do Azure

Quando desenvolver uma implementação do Azure o Gestor de recursos, requisitos de cluster tem de ser mapeado para recursos Azure e serviços. Se uma aplicação é constituída por vários pontos finais de http, uma base de dados e um serviço de colocação em cache de dados, os recursos Azure anfitrião cada destes componentes tem de ser rationalized. Por exemplo, a aplicação da loja de música de exemplo inclui uma aplicação web que está alojada num computador virtual e uma base de dados do SQL, que está alojado numa base de dados do Azure SQL. 

Este documento apresenta detalhes sobre como os recursos de cluster loja de música estão configurados no modelo de Gestor de recursos do Azure de exemplo. Todas as dependências e exclusivas configurações estão realçadas. Para obter a melhor experiência, previamente implemente uma instância da solução a sua subscrição do Azure e funcionam juntamente com o modelo de Gestor de recursos do Azure. O modelo concluído pode ser encontrado aqui – [Implementação de arquivo de música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="virtual-machine"></a>Máquina virtual

A aplicação de arquivo de música inclui uma aplicação web onde os clientes podem procurar e comprar a música. Apesar de existirem vários serviços Azure que podem alojar aplicações web, neste exemplo, uma Máquina Virtual é utilizada. Utilizando o modelo da loja de música de exemplo, uma máquina virtual é implementada, instalar um servidor web e Web site da loja de música instalado e configurado. Por razões de neste artigo, apenas a implementação de máquina virtual é detalhada. A configuração do servidor web e a aplicação é detalhada um artigo posterior.

Uma máquina virtual podem ser adicionada a um modelo utilizando o Assistente do Visual Studio adicionar novo recurso ou inserindo JSON válido para o modelo de implementação. Quando implementar uma máquina virtual, vários recursos relacionados também são necessários. Se utilizar o Visual Studio para criar o modelo, estes recursos são criados por si. Se manualmente construir o modelo, estes recursos tem de ser inserido e configurado.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [JSON Máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285).

```none
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

Depois de implementado, as propriedades de máquina virtual podem ser vistas no portal do Azure.

![Máquina virtual](./media/virtual-machines-windows-dotnet-core/vm-win.png)

## <a name="storage-account"></a>Conta de armazenamento

Contas de armazenamento tem várias opções de armazenamento e funcionalidades. Para o contexto da máquinas virtuais do Azure, uma conta de armazenamento mantém as unidades de disco rígidas virtuais de máquina virtual e quaisquer discos dados adicionais. O exemplo loja de música inclui uma conta de armazenamento para colocar em espera unidade de disco rígido virtual de cada máquina virtual na implementação. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [A conta de armazenamento](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Uma conta de armazenamento é associar com uma máquina de virtual dentro da declaração de modelo de Gestor de recursos de máquina virtual. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [associação Máquina Virtual e a conta de armazenamento](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Após a implementação, a conta de armazenamento pode ser visualizada no portal do Azure.

![Conta de armazenamento](./media/virtual-machines-windows-dotnet-core/storacct-win.png)

Clicar para o contentor de armazenamento de BLOBs conta, pode ser visto o ficheiro de unidade de disco rígido virtual para cada máquina virtual implementado com o modelo.

![Unidades de disco rígido virtuais](./media/virtual-machines-windows-dotnet-core/vhd-win.png)

Para mais informações sobre o armazenamento do Windows Azure, consulte a [documentação de armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Rede virtual

Se uma máquina virtual exigir redes internas tal como a capacidade de comunicar com outros máquinas virtuais e recursos Azure, uma rede Virtual Azure é necessária.  Uma rede virtual não efetuar a máquina virtual acessível através da internet. A conectividade pública requer um endereço IP público, detalhado mais adiante nesta série.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [subredes e de rede Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
  }
}
```

A partir do portal do Azure, a rede virtual aspeto a imagem seguinte. Repare que todas as máquinas virtuais implementadas com o modelo estão ligadas à rede virtual.

![Rede virtual](./media/virtual-machines-windows-dotnet-core/vnet-win.png)

## <a name="network-interface"></a>Interface da rede

 Uma interface de rede liga-se uma máquina virtual a uma rede virtual, mais especificamente para uma sub-rede que tenha sido definida na rede virtual. 
 
 Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Interface da rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Cada recurso de máquina virtual inclui um perfil de rede. A interface de rede está associada a máquina virtual este perfil.  

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Máquina Virtual Network perfil](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

A partir do portal do Azure, a interface de rede aspeto a imagem seguinte. O endereço IP interno e a associação de máquina virtual podem ser vistos no recurso de interface de rede.

![Interface da rede](./media/virtual-machines-windows-dotnet-core/nic-win.png)

Para mais informações sobre redes virtuais Azure, consulte a [documentação de rede Virtual Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Base de dados Azure SQL

Para além de uma máquina de virtual alojamento Web site da loja de música, uma base de dados do SQL Azure for implementado a base de dados do arquivo de música do anfitrião. A vantagem de utilizar a base de dados do SQL Azure aqui é que não é necessário um segundo conjunto de máquinas virtuais e escala e disponibilidade incorporada no serviço.

Uma base de dados do Azure SQL pode ser adicionado utilizando o Visual Studio adicionar novo recurso de assistente, ou inserindo JSON válido para um modelo. O recurso de SQL Server inclui um nome de utilizador e palavra-passe que é concedida direitos administrativos na instância do SQL. Além disso, é adicionado um recurso de firewall SQL. Por predefinição, aplicações alojadas no Azure são conseguir ligar-se com a instância do SQL. Para permitir que as aplicações externas essa um studio de gestão do SQL Server para ligar para a instância do SQL, a firewall tem de ser configurado. Por razões de demonstração loja de música, a configuração predefinida está bem formatada. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [BD de SQL Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Uma vista da base de dados de MusicStore conforme visto no portal do Azure e do SQL server.

![Do SQL Server](./media/virtual-machines-windows-dotnet-core/sql-win.png)

Para mais informações sobre como implementar a base de dados do SQL Azure, consulte a [documentação de base de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Passo seguinte

<hr>

[Passo 2 - Access e a segurança em modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-3-access-security.md)
