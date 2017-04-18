<properties
   pageTitle="Gestor de recursos do modelo guiadas | Microsoft Azure"
   description="Instruções passo a passo de um modelo de Gestor de recursos aprovisionamento uma arquitetura básica do Azure IaaS."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Tutorial do modelo de Gestor de recursos

Uma das perguntas primeiros ao criar um modelo é "como começar?". Um pode iniciar a partir de um modelo em branco, a estrutura básica descrita no [artigo de modelo de criação](resource-group-authoring-templates.md#template-format), a seguir e adicionar os recursos e parâmetros adequados e variáveis. Uma boa alternativa seria comece por aceder através da [Galeria de guia de introdução](https://github.com/Azure/azure-quickstart-templates) e procure cenários semelhantes ao que está a tentar criar. Pode intercalar vários modelos ou editar uma existente, de acordo com o seu próprio cenário específico. 

Vamos dar uma olhadela numa infraestrutura comuns:

* Duas máquinas virtuais que utilizam a mesma conta de armazenamento, são no mesmo conjunto de disponibilidade e na mesma sub-rede de uma rede virtual.
* Um única imagem e VM o endereço IP para cada máquina virtual.
* Um balanceador de carga com uma regra balanceamento de carga na porta 80

![arquitetura](./media/resource-group-overview/arm_arch.png)

Este tópico orienta-o através dos passos de criar um modelo de Gestor de recursos para esse infraestrutura. O modelo final que criar é baseado num modelo de guia de introdução denominado [2 VMs numa Balanceador de carga e as regras de balanceamento de carga](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

No entanto, que é muito criar ao mesmo tempo, por isso, vamos criar primeiro uma conta de armazenamento e implementá-lo. Depois de ter imposto a criar a conta de armazenamento, irá adicionar os outros recursos e voltar a implementar o modelo para concluir a infraestrutura.

>[AZURE.NOTE] Pode utilizar qualquer tipo de editor quando criar o modelo. O Visual Studio fornece ferramentas que simplificam o desenvolvimento de modelo, mas não necessita de Visual Studio para concluir este tutorial. Para obter um tutorial sobre como utilizar o Visual Studio para criar uma implementação Web App e base de dados SQL, consulte [criar e implementar a grupos de recursos Azure através do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## <a name="create-the-resource-manager-template"></a>Criar o modelo de Gestor de recursos

O modelo é um ficheiro JSON que define todos os recursos que irá implementar. Também permite que definir parâmetros são especificados durante a implementação, variáveis construídos de outros valores e expressões e saídas a partir de implementação. 

Vamos começar com o modelo mais simples:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Guardar este ficheiro como **azuredeploy.json** (tenha em atenção que o modelo pode ter qualquer nome que pretende, basta que a mesma tem de ser um ficheiro de json).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Na secção de **recursos** , adicione um objeto que define a conta de armazenamento, conforme apresentado abaixo. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Poderá estar a pensar onde estas propriedades e valores provêm de. As propriedades **tipo**, **nome**, **apiVersion**e **localização** são elementos padrão que estão disponíveis para todos os tipos de recursos. Pode saber sobre os elementos comuns em [recursos](resource-group-authoring-templates.md#resources). **nome** está definido para um valor de parâmetro passar no durante a implementação e **localização** como a localização utilizada pelo grupo de recursos. Abordaremos como determinar o **tipo** e **apiVersion** nas secções abaixo.

Na secção **Propriedades** contém todas as propriedades que sejam únicas ao tipo de recurso específico. Os valores que especificar nesta secção exatamente corresponder a operação de colocar em REST API para criar esse tipo de recurso. Quando criar uma conta de armazenamento, tem de fornecer um **accountType**. Repare [REST API para criar uma conta de armazenamento](https://msdn.microsoft.com/library/azure/mt163564.aspx) de que a secção de propriedades da operação de resto também contém uma propriedade de **accountType** e os valores permitidos encontram-se documentados. Neste exemplo, o tipo de conta está definido para **Standard_LRS**, mas pode especificar outro valor ou permitir que os utilizadores para passar no tipo de conta, como um parâmetro.

Agora vamos ir novamente para a secção **parâmetros** e veja como pode definir o nome da conta de armazenamento. Pode obter mais informações sobre a utilização de parâmetros na [parâmetros](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Aqui definiu um parâmetro do tipo cadeia onde vai introduzir o nome da conta de armazenamento. O valor para este parâmetro será fornecido durante a implementação do modelo.

## <a name="deploying-the-template"></a>Implementar o modelo
Temos um modelo para criar uma nova conta de armazenamento total. Como se deve lembrar, o modelo foi guardado no ficheiro **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Existem bastante algumas maneiras para implementar um modelo, como pode ver no [artigo de implementação do recurso](resource-group-template-deploy.md). Para implementar o modelo através do Azure PowerShell, utilize:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Em alternativa, para implementar o modelo utilizar Azure clip, utilize:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Agora for o proprietário de uma conta de armazenamento orgulhoso!

São os passos para adicionar todos os recursos necessários para implementar a arquitetura descrita na iniciar deste tutorial. Irá adicionar estes recursos no modelo do mesmo que tenha sido a trabalhar.

## <a name="availability-set"></a>Conjunto de disponibilidade
Depois da definição da conta de armazenamento, adicione um disponibilidade definido para as máquinas virtuais. Neste caso, não existem adicionais propriedades necessárias, para que a sua definição é bastante simple. Consulte o artigo [REST API para criar um conjunto de disponibilidade](https://msdn.microsoft.com/library/azure/mt163607.aspx) para a secção Propriedades completo, no caso que pretende definir as atualização domínio contar e falhas domínio contar valores.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Repare que o **nome** está definido para o valor de uma variável. Para este modelo, o nome do conjunto de disponibilidade é necessário em alguns locais diferentes. Mais facilmente pode manter o seu modelo definindo uma vez por esse valor e utilizá-lo em vários locais.

O valor que especificar para **tipo** contém o fornecedor de recursos e o tipo de recurso. Para conjuntos de disponibilidade, o fornecedor do recurso é **Microsoft.Compute** e o tipo de recurso é **availabilitySets**. Pode obter a lista de fornecedores de recursos disponíveis, executando o seguinte comando PowerShell:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Em alternativa, se estiver a utilizar o Azure clip, pode executar o seguinte comando:
```
    azure provider list
```
Dado que neste tópico que criar com contas de armazenamento, máquinas virtuais e funcionamento em rede virtual, funcionam com:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Para ver os tipos de recurso para um determinado fornecedor, execute o seguinte comando do PowerShell:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Ou, para Azure clip, o seguinte comando devolver os tipos de disponíveis no formato JSON e guardá-lo para um ficheiro.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Deverá ver **availabilitySets** como um dos tipos de dentro **Microsoft.Compute**. O nome completo do tipo de é **Microsoft.Compute/availabilitySets**. Pode determinar o nome do tipo de recurso para qualquer um dos recursos modelo.

## <a name="public-ip"></a>Endereço IP público
Defina um endereço IP público. Novamente, observe a [REST API para os endereços de IP públicos](https://msdn.microsoft.com/library/azure/mt163590.aspx) para as propriedades definir.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

O método de alocação está definido para **dinâmicos** , mas pode defini-lo como o valor que precisa ou defini-lo para aceitar um valor de parâmetro. Activou os utilizadores do seu modelo para transmitir um valor para a etiqueta de nome de domínio.

Agora, vamos ver como determinar a **apiVersion**. O valor que especificar simplesmente corresponde à versão do API REST que pretende utilizar para criar o recurso. Sim, pode procurar na documentação REST API para esse tipo de recurso. Em alternativa, pode executar o seguinte comando do PowerShell para um tipo específico.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Que devolve os seguintes valores:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Para ver as versões de API com Azure clip, execute o mesmo comando **Mostrar fornecedor azure** apresentado anteriormente.

Ao criar um novo modelo, selecione a versão mais recente da API.

## <a name="virtual-network-and-subnet"></a>Rede virtual e de sub-rede
Crie uma rede virtual com uma sub-rede. Observe a [REST API para redes virtuais](https://msdn.microsoft.com/library/azure/mt163661.aspx) para todas as propriedades definir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Balanceador de carga
Agora irá criar um balanceador de carga opostas externos. Uma vez que este Balanceador de carga utiliza o endereço IP público, tem de declarar uma dependência o endereço IP público na secção **dependsOn** . Isto significa que o Balanceador de carga será não é implementado até que o endereço IP público já terminou implementar. Sem definir esta dependência, receberá um erro porque o Gestor de recursos tentará implementar os recursos paralelamente e tentará definir o Balanceador de carga para o endereço IP público que ainda não existe. 

Também irá criar um conjunto de endereços de back-end, algumas regras de entrada NAT para RDP para os VMs e uma regra de balanceamento de carga com uma sonda tcp na porta 80 na definição deste recurso. Dar saída a [REST API para Balanceador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) para todas as propriedades.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interface da rede
Irá criar 2 interfaces de rede, uma para cada VM. Em vez de ter que incluir as entradas duplicadas para interfaces de rede, pode utilizar a [função copyIndex()](resource-group-create-multiple.md) iteramos sobre o ciclo de cópia (designado por nicLoop) e criar números interfaces de rede, tal como foi definido na `numberOfInstances` variáveis. A interface de rede depende da criação da rede virtual e Balanceador de carga. Utiliza a sub-rede definida na criação de rede virtual e o id do Balanceador de carga para configurar o conjunto de endereços do Balanceador de carga e as regras NAT entradas.
Observe a [REST API para interfaces de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) para todas as propriedades.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Máquina virtual
Irá criar máquinas virtuais 2, utilizando a função copyIndex(), como fez no criação das [interfaces de rede](#network-interface).
A criação de VM depende da conta de armazenamento, interface e disponibilidade de um conjunto de rede. Este VM será criado a partir de uma imagem marketplace, tal como foi definido na `storageProfile` propriedade - `imageReference` é utilizado para definir o publisher de imagem, oferta, sku e versão. Por fim, um perfil de diagnóstico está configurado para activar diagnósticos para a VM. 

Para localizar as propriedades relevantes para uma imagem marketplace, siga os artigos [Selecione Linux máquina virtual imagens](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) ou [imagens de máquina virtual do Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Para imagens publicadas pelo **3º outros fornecedores**, terá de especificar com o nome de outra propriedade `plan`. Um exemplo pode ser encontrado na [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) a partir da Galeria de guia de introdução. 

Terminar de definir os recursos para o modelo.

## <a name="parameters"></a>Parâmetros

Na secção de parâmetros, defina os valores que podem ser especificados quando implementar o modelo. Apenas defina parâmetros para valores que pensa que devem ser variado durante a implementação. Pode fornecer um valor predefinido para um parâmetro que é utilizado se um não for fornecido durante a implementação. Também pode definir os valores permitidos conforme apresentado para o parâmetro **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variáveis

Na secção de variáveis, pode definir valores que são utilizados num local mais do que um no seu modelo ou valores que são construídos a partir de outras expressões ou variáveis. Variáveis são utilizadas com frequência para simplificar a sintaxe do modelo.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

O modelo tiver concluído! Pode a comparar o seu modelo do modelo na [Galeria de guia de introdução](https://github.com/Azure/azure-quickstart-templates) em [2 VMs com Balanceador de carga e carregar modelo de regras do Balanceador](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)completo. O modelo poderá ser ligeiramente diferente consoante a utilizar números de versão diferente. 

Pode voltar implementar o modelo através dos comandos mesmo que utilizou quando implementar a conta de armazenamento. Não tem de eliminar a conta de armazenamento antes de implementar novamente, porque o Gestor de recursos serão saltar novamente criar recursos que já existirem e não sofreram alterações.

## <a name="next-steps"></a>Próximos passos

- [Azure Gestor de recursos do modelo Visualizer (ARMViz)](http://armviz.io/#/) é uma ótima ferramenta para visualizar os modelos de processador, tal como poderá ficar demasiado grandes para compreender acabou de ler o ficheiro json.
- Para saber mais sobre a estrutura de um modelo, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md).
- Para saber mais sobre implementar um modelo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md)
