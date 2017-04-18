<properties
   pageTitle="Disponibilidade e a escala em modelos de Gestor de recursos do Azure | Microsoft Azure"
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

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilidade e a escala em modelos de Gestor de recursos do Azure

Disponibilidade e a escala referir-se a tempo útil e a capacidade para cumprir o pedido. Se uma aplicação tem de ser o 99,9% das vezes,-tem de ter uma arquitetura de que permite que múltiplos em simultâneo cluster recursos. Por exemplo, em vez de ter um site único, uma configuração com um nível superior da disponibilidade inclui várias instâncias do mesmo site, com tecnologia à frente-los de balanceamento. Nesta configuração, uma instância da aplicação pode tomar para baixo para a manutenção, enquanto o restante continuam a funcionar. Escala de outro lado referencia uma capacidade de aplicações para servir o pedido. Com uma carga aplicação desequilibrada, adicionando ou removendo instâncias do conjunto de permite que uma aplicação Dimensionar para cumprir o pedido.

Este documento detalhes como a implementação de exemplo música arquivo estiver configurada para disponibilidade e a escala. Todas as dependências e exclusivas configurações estão realçadas. Para obter a melhor experiência, previamente implemente uma instância da solução a sua subscrição do Azure e funcionam juntamente com o modelo de Gestor de recursos do Azure. O modelo concluído pode ser encontrado aqui – [Implementação de arquivo de música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="availability-set"></a>Conjunto de disponibilidade

Um conjunto de disponibilidade logicamente abranger máquinas virtuais do Azure físicas anfitriões e outros componentes infra-estruturas como power consumíveis e física hardware de rede. Conjuntos de disponibilidade Certifique-se de que durante a manutenção, falha de um dispositivo ou outra vez, não todas as máquinas virtuais sejam efectuadas. Um conjunto de disponibilidade podem ser adicionado a um modelo de Gestor de recursos do Azure utilizando o Visual Studio adicionar novo recurso assistente ou inserir JSON válido para um modelo.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Disponibilidade definir](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Um conjunto de disponibilidade for declarado como uma propriedade de um recurso de Máquina Virtual. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Definir disponibilidade de associação com Máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302).

```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
A disponibilidade definir conforme visto a partir do portal Azure. Cada máquina virtual e detalhes sobre a configuração encontram detalhadas mais.

![Conjunto de disponibilidade](./media/virtual-machines-windows-dotnet-core/ase-win.png)

Para obter informações aprofundadas nos conjuntos de disponibilidade, consulte o artigo [Gerir disponibilidade das máquinas virtuais](./virtual-machines-windows-manage-availability.md). 

## <a name="network-load-balancer"></a>Balanceador de carga de rede

Considerando que um conjunto de disponibilidade fornece tolerância a falhas aplicação, um balanceador de carga disponibiliza várias instâncias da aplicação no endereço de uma única rede. Várias instâncias de uma aplicação do podem ser alojadas em muitas máquinas virtuais, cada um deles ligado a um balanceador de carga. Tal como a aplicação é acedida, Balanceador de carga encaminha o pedido de entrada ao longo dos membros anexados. Um balanceador de carga podem ser adicionado utilizando o Visual Studio adicionar novo recurso assistente, ou inserindo corretamente formatada recurso JSON para o modelo de Gestor de recursos do Azure.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Balanceador de carga de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Uma vez que a aplicação de exemplo é exposta à internet com um endereço IP público, este endereço está associado com o Balanceador de carga. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [associação do Balanceador de carga de rede com o endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

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

A partir do portal do Azure, a descrição geral Balanceador de carga de rede mostra a associação com o endereço IP público.

![Balanceador de carga de rede](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>Regra de Balanceador de carga

Ao utilizar um balanceador de carga, regras são configuradas que controlam como de balanceamento de tráfego entre os recursos pretendidos. Com a aplicação da loja de música de exemplo, o tráfego chega à porta 80 do endereço IP público e está distribuído pela porta 80 de todas as máquinas virtuais. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Regra Balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Uma vista da regra de Balanceador de carga de rede a partir do portal.

![Regra de Balanceador de carga de rede](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>Sonda do Balanceador de carga

Também tem o Balanceador de carga monitorizar a cada máquina virtual para que os pedidos são processados apenas para executar sistemas. Esta monitorização ocorre o mais pela pesquisa constante de uma porta predefinida. A implementação de arquivo de música está configurada para sonda porta 80 em todas as máquinas virtuais incluídas. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Sonda Balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

A sonda de Balanceador de carga vista a partir do portal Azure.

![Sonda de Balanceador de carga de rede](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>NAT regras de entrada

Ao utilizar um balanceador de carga, regras tem de colocar para um local que fornecem acesso desequilibrado não carregar cada máquina Virtual. Por exemplo, quando criar uma ligação RDP com cada máquina virtual, este tráfego não deve ser balanceamento de carga, preferir um caminho previamente determinado deve ser configurado. Caminhos previamente determinados são configurados utilizando um recurso de regra de NAT de entrada. Utilizar este recurso, comunicação de entrada pode ser mapeada para máquinas virtuais individuais. 

Com a aplicação da loja de música, uma porta começando na 5000 é mapeada para porta 3389 em cada máquina Virtual para acesso RDP. O `copyindex()` função é utilizada para incrementar a porta de entrada, assim que a segunda máquina de Virtual recebe uma porta de recepção de 5001, o terceiro 5002 e assim sucessivamente.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Regras de NAT entrada](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Um exemplo de entrada regra NAT conforme visto no portal do Azure. É criada uma regra de RDP NAT para cada máquina virtual na implementação.

![Regra NAT de entrada](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

Para obter informações detalhadas sobre o Balanceador de carga de rede do Azure, consulte o artigo [balanceamento de carga para serviços de infraestrutura Azure](./virtual-machines-windows-load-balance.md).

## <a name="deploy-multiple-vms"></a>Implementar VMs múltiplos

Por fim, para um conjunto de disponibilidade ou Balanceador de carga para funcionar de forma eficaz, várias máquinas virtuais necessários. Podem ser implementadas VMs múltiplos utilizando a função de cópia de modelo de Gestor de recursos do Azure. Utilizar a função copiar, não é necessário definir um número finito de máquinas virtuais, preferir este valor pode ser dinamicamente fornecido no momento da implementação. A função Copiar consome o número de instâncias seja criado e alças implementar o número inicial de máquinas virtuais e recursos associados.

O modelo de exemplo do arquivo de música, um parâmetro é definido que utiliza na contagem de instâncias. Este número é utilizado em todo o modelo quando criar máquinas virtuais e recursos relacionados.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

Num recurso Máquina Virtual, o ciclo de cópia é dado um nome e o número de parâmetro de instâncias utilizado para controlar o número de cópias resultantes.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Máquina Virtual cópia função](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290). 


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

Pode ser acedida a iteração atual da função cópia com o `copyIndex()` função. Pode ser utilizado o valor da função de índice remissivo cópia para atribuir um nome máquinas virtuais e outros recursos. Por exemplo, se são implementadas duas instâncias de uma máquina virtual, precisam nomes diferentes. O `copyIndex()` função pode ser utilizada como parte do nome do máquina virtual para criar um nome exclusivo. Um exemplo do `copyindex()` função utilizada para fins de nomenclatura pode ser visualizada no recurso de Máquina Virtual. Aqui, o nome do computador é uma concatenação da `vmName` parâmetro bem como a `copyIndex()` função. 

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [Função de índice de cópia](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309). 


```none
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

O `copyIndex` função é utilizada várias vezes no modelo de exemplo do arquivo de música. Recursos e funções utilizando `copyIndex` incluir algo específico para uma única ocorrência de máquina virtual tal e a interface de rede, as regras do Balanceador de carga, e qualquer depende funções. 

Para mais informações sobre a função copiar, consulte o artigo [criar várias instâncias de recursos no Gestor de recursos do Azure](../resource-group-create-multiple.md).

## <a name="next-step"></a>Passo seguinte

<hr>

[Passo 4 - implementação da aplicação com modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)