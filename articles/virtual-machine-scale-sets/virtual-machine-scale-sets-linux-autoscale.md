<properties
    pageTitle="Conjuntos de escala de Máquina Virtual Autoscale Linux | Microsoft Azure"
    description="Configurar o autoscaling para Linux Máquina Virtual escala conjuntos de utilizar o clip do Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Dimensionar automaticamente Linux máquinas num conjunto de escala de máquina virtual

Conjuntos de escala de máquina virtual facilitam implementar e gerir máquinas virtuais idênticas como um conjunto. Os conjuntos de escala fornecem uma camada altamente dimensionáveis e personalizáveis cluster de hyperscale aplicações e suportam imagens de plataforma do Windows, imagens de plataforma Linux, imagens personalizadas e extensões. Para saber mais, consulte o artigo [Descrição geral de conjuntos de escala de Máquina Virtual](virtual-machine-scale-sets-overview.md).

Este tutorial mostra-lhe como criar um conjunto de escala de máquinas virtuais de Linux utilizar a versão mais recente do Ubuntu Linux. O tutorial também mostra-lhe como Dimensionar automaticamente as máquinas no conjunto. Criar a escala definir e configurar o dimensionamento através da criação de um modelo de Gestor de recursos do Azure e implementá-lo utilizando o clip Azure. Para mais informações sobre modelos, consulte [Gestor de recursos do Azure criação de modelos](../resource-group-authoring-templates.md). Para saber mais sobre a escala automática dos conjuntos de escala, consulte o artigo [escala automática e conjuntos de escala de Máquina Virtual](virtual-machine-scale-sets-autoscale-overview.md).

Neste tutorial, implementar os seguintes recursos e extensões de:

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Para mais informações sobre os recursos de Gestor de recursos, consulte o artigo [Calcular Azure, a rede e a fornecedores de armazenamento no Gestor de recursos do Azure](../virtual-machines/virtual-machines-linux-compare-deployment-models.md).

Antes de começar a com os passos neste tutorial, [instale o clip do Azure](../xplat-cli-install.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Passo 1: Criar um grupo de recursos e uma conta de armazenamento

1. **Inicie sessão no Microsoft Azure** - na interface de comandos (festa, Terminal, comando pede), mude para o Gestor de recursos modo e, em seguida, [Inicie sessão com o seu trabalho ou escola id](../xplat-cli-connect.md#use-the-log-in-method). Siga os pedidos para uma experiência de início de sessão interactivo à sua conta Azure.

        azure config mode arm

        azure login

    > [AZURE.NOTE] Se tiver uma trabalho ou escola ID e não possuem a autenticação de dois fatores activada, utilize `azure login -u` com o ID de iniciar sessão sem uma sessão de interativa. Se não tiver uma trabalho ou escola ID, pode [criar uma trabalho ou escola id a partir da sua conta Microsoft pessoal](../virtual-machines/virtual-machines-linux-create-aad-work-id.md).

2. **Criar um grupo de recursos** – todos os recursos tem de ser implementados para um grupo de recursos. Para este tutorial, dê um nome de grupo de recursos **vmsstest1**.

        azure group create vmsstestrg1 centralus

3. **Implementar uma conta de armazenamento para o novo grupo de recursos** – esta conta de armazenamento é a localização onde armazenou o modelo. Crie uma conta de armazenamento denominada **vmsstestsa**.

        azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa

## <a name="step-2-create-the-template"></a>Passo 2: Criar o modelo
Um modelo de Gestor de recursos do Azure torna para implementar e gerir recursos Azure em conjunto utilizando uma descrição de JSON dos recursos e parâmetros de implementação associado.

1. No seu editor favorito, crie o ficheiro VMSSTemplate.json e adicione a estrutura JSON inicial para suportar o modelo.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Parâmetros não são sempre obrigatórios, mas fornecem uma maneira de valores de entrada quando o modelo é implementado. Adicione estes parâmetros sob o elemento principal de parâmetros que tenha adicionado ao modelo.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Definir um nome para a máquina de virtual separada que é utilizado para aceder as máquinas na escala.
    - Um nome para a conta de armazenamento a localização onde armazenou o modelo.
    - Definir o número de ocorrências de máquinas virtuais para criar inicialmente na escala.
    - Um nome e palavra-passe da conta de administrador nas máquinas virtuais.
    - Definir um prefixo para os recursos que são criados para suportar a escala de nome.

3. Variáveis podem ser utilizadas num modelo para especificar os valores que podem ser alteradas frequentemente ou valores que precisam de ser criado a partir de uma combinação de valores de parâmetros. Adicione estas variáveis no elemento principal de variáveis que tenha adicionado ao modelo.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
        "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

    - Nomes DNS que são utilizados pelas interfaces de rede.
    - Os nomes de endereços IP e prefixos para a rede virtual e sub-redes.
    - Os nomes e os identificadores de rede virtual e carregar balanceador e interfaces de rede.
    - Nomes de conta de armazenamento para as contas associadas máquinas na escala do conjunto.
    - Definições para a extensão de diagnóstico estiver instalado nas máquinas virtuais. Para mais informações sobre a extensão de diagnóstico, consulte o artigo [criar uma Máquina Virtual do Windows com a monitorização e diagnósticos de utilizar o modelo de Gestor de recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Adicione o recurso de conta de armazenamento no elemento principal de recursos que tenha adicionado ao modelo. Este modelo utiliza um ciclo para criar contas de armazenamento de cinco recomendadas onde estão armazenados os discos de sistema operativo e dados de diagnóstico. Este conjunto de contas pode suportar até 100 máquinas virtuais num conjunto de escala, que é o valor máximo atual. Cada conta de armazenamento chama-se com um designador de letra que tenha sido definida nas variáveis combinadas com o sufixo que fornece nos parâmetros para o modelo.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Adicione o recurso de rede virtual. Para mais informações, consulte o artigo [Fornecedor de recursos de rede](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Adicione os recursos de endereço IP públicos que são utilizados pela Balanceador de carga de interface de rede.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Adicione o recurso de Balanceador de carga utilizados pelo conjunto de escala. Para mais informações, consulte o artigo [Azure Gestor de recursos de suporte para Balanceador de carga](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 22
                }
              }
            ]
          }
        },

8. Adicione o recurso de interface de rede que é utilizado pela máquina virtual em separado. Porque não acessíveis através de um endereço IP público máquinas num conjunto de escala, uma máquina virtual separada é criada na mesma rede virtual para o access remotamente as máquinas.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Adicione máquina virtual em separado na mesma rede que o conjunto de escala.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "14.04.4-LTS",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
              ]
            }
          }
        },

10. Adicionar que a escala de máquina virtual conjunto de recursos e especificar a extensão de diagnóstico está instalada em todas as máquinas virtuais do conjunto de escala. Muitas das definições para este recurso são semelhantes com o recurso de máquina virtual. As principais diferenças são o elemento de capacidade que especifica o número de máquinas virtuais no conjunto de escala e upgradePolicy que especifica como as atualizações são feitas a máquinas virtuais. O conjunto de escala não é criado até todas as contas de armazenamento são criadas no elemento de dependsOn, conforme especificado.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "Canonical",
                      "offer": "UbuntuServer",
                      "sku": "14.04.4-LTS",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name":"LinuxDiagnostic",
                        "properties": {
                          "publisher":"Microsoft.OSTCExtensions",
                          "type":"LinuxDiagnostic",
                          "typeHandlerVersion":"2.1",
                          "autoUpgradeMinorVersion":false,
                          "settings": {
                            "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint":"https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Adicione o recurso de autoscaleSettings que define como ajusta com base na utilização do processador nos computadores na definir o conjunto de escala.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor\\PercentProcessorTime",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Para este tutorial, estes valores são importantes:

    - **metricName** - este valor é o mesmo que o contador de desempenho definido na variável wadperfcounter. Utilizar essa variável, a extensão de diagnóstico recolhe o contador **Processor\PercentProcessorTime** .
    - **metricResourceUri** - este valor é o identificador de recursos do conjunto de escala de máquina virtual.
    - **timeGrain** – este valor é a granularidade as métricas que são recolhidas. Este modelo, está definido para um minuto.
    - **Estatística** – este valor determina a forma como as métricas são combinadas para acomodar a ação de dimensionamento automática. Os valores possíveis são: média, mínimo, máximo. Este modelo, a utilização da CPU total média das máquinas virtuais é recolhida.
    - **timeWindow** – este valor é o intervalo de tempo no qual os dados da instância são recolhidos. Tem de estar entre 5 minutos e 12 horas.
    - **timeAggregation** – este valor determina como os dados que são recolhidos devem ser combinados ao longo do tempo. O valor predefinido é média. Os valores possíveis são: média, mínimo, máximo, última, Total, contagem.
    - **operador** – este valor é o operador que é utilizado para comparar os dados métricos e o limiar de. Os valores possíveis são: for igual a, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **limiar** – este valor accionadores a ação de escala. Este modelo, máquinas são adicionadas à escala definir quando a utilização da CPU média entre máquinas no conjunto de for cerca de 50%.
    - **direcção** – este valor determina a ação que é disponibilizada quando o valor limite é obtido. Os valores possíveis são aumentar ou diminuir. Este modelo, o número de máquinas virtuais do conjunto de escala é aumentado se o limiar é mais de 50% na janela de tempo definido.
    - **tipo de** – este valor é o tipo de ação que deve ocorrer e tem de estar definida para ChangeCount.
    - **valor** – este valor é o número de máquinas virtuais que são adicionados ou removidos do conjunto de escala. Este valor tem de ser 1 ou maior. O valor predefinido é 1. Este modelo, o número de máquinas na escala definir aumentos pelo 1 quando o limiar de que existe correspondência.
    - **cooldown** – este valor é a quantidade de tempo a aguardar desde a última ação dimensionamento antes da ação seguinte ocorre. Este valor tem de estar entre um minuto e uma semana.

12. Guarde o ficheiro de modelo.    

## <a name="step-3-upload-the-template-to-storage"></a>Passo 3: Carregar um modelo de armazenamento

O modelo pode ser carregado desde que sabe o nome e a chave primária da conta de armazenamento que criou no passo 1.

1. Na sua interface de comandos (festa, terminais, linha de comandos), execute estes comandos para definir as variáveis de ambiente necessárias acesso à conta de armazenamento:

        export AZURE_STORAGE_ACCOUNT={account_name}
        export AZURE_STORAGE_ACCESS_KEY={key}

    Pode obter a chave ao clicar no ícone de chave ao visualizar o recurso de conta de armazenamento no portal do Azure. Ao utilizar uma linha de comandos do Windows, escreva: **Definir** em vez de exportação.

2. Crie o contentor para armazenar o modelo.

        azure storage container create -p Blob templates

3. Carregue o ficheiro de modelo para o novo contentor.

        azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## <a name="step-4-deploy-the-template"></a>Passo 4: Implementar o modelo

Agora que criou o modelo, pode começar a implementar os recursos. Utilize este comando para iniciar o processo:

    azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

Quando prime enter, lhe for pedido para fornecer valores para as variáveis que atribuiu. Forneça estes valores:

    vmName: vmsstestvm1
    vmSSName: vmsstest1
    instanceCount: 5
    adminUserName: vmadmin1
    adminPassword: VMpass1
    resourcePrefix: vmsstest

Deverá demorar cerca de 15 minutos para todos os recursos ser implementada com êxito.

>[AZURE.NOTE]Também pode utilizar a capacidade do portal para implementar os recursos. Utilize esta ligação: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## <a name="step-5-monitor-resources"></a>Passo 5: Recursos de Monitor

Pode obter algumas informações acerca de conjuntos de escala de máquina virtual através destes métodos:

 - Portal do Azure - atualmente pode obter uma quantidade limitada de informações através do portal.
 - [Azure recurso Explorer](https://resources.azure.com/) - esta ferramenta é adequado para a explorar o estado atual do seu conjunto de escala. Siga este caminho e deverá ver a vista de instância do conjunto de escala que criou:

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Clip Azure - Utilize este comando para obter algumas informações:

        azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - Ligar a máquina virtual jumpbox tal como faria com qualquer outro computador e, em seguida, pode ser acedido remotamente as máquinas virtuais na escala do conjunto de para monitorizar processos individuais.

>[AZURE.NOTE]Pode encontrar um REST API completa para obter informações acerca de conjuntos de escala na [Máquina Virtual conjuntos de escala](https://msdn.microsoft.com/library/mt589023.aspx).

## <a name="step-6-remove-the-resources"></a>Passo 6: Remover os recursos

Uma vez que são cobrados para recursos utilizados no Azure, sempre é aconselhável para eliminar os recursos que já não forem necessárias. Não tem de eliminar a cada recurso separadamente a partir de um grupo de recursos. Pode eliminar o grupo de recursos e todos os seus recursos são eliminados automaticamente.

        azure group delete vmsstestrg1

## <a name="next-steps"></a>Próximos passos

- Localizar exemplos de Monitor de Azure funcionalidades no [Monitor do Azure em diferentes plataformas clip rápida iniciar amostras](../monitoring-and-diagnostics/insights-cli-samples.md) de monitorização
- Saiba mais sobre as funcionalidades de notificação do [utilizar autoscale ações para enviar correio eletrónico e webhook as notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Saiba como [os registos de auditoria de utilização para enviar correio eletrónico e webhook as notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Consulte o artigo o modelo [Autoscale um conjunto de escala de VM executar uma aplicação Apache/Ubuntu/PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) que configura uma pilha de luz em exercer a funcionalidade de dimensionamento automática da conjuntos de escala de Máquina Virtual.
