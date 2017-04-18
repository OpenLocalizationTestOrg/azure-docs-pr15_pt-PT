<properties
    pageTitle="Processamento de estado no Gestor de recursos modelos | Microsoft Azure"
    description="Mostra recomendado abordagens para utilizar objetos complexos para partilhar dados de estado com modelos do Gestor de recursos do Azure e modelos ligados"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Partilha de estado no Gestor de recursos do Azure modelos

Este tópico mostra-lhe melhores práticas para gerir e partilhar estado dentro de modelos. Os parâmetros e variáveis mostradas neste tópico são exemplos do tipo de objetos que pode definir de forma conveniente organizar aos requisitos da implementação. A partir nestes exemplos, pode implementar a suas própria objetos com valores de propriedade adequadas para o seu ambiente.

Este tópico faz parte de um documento técnico maior. Para ler o papel completo, transfira o [mundo classe Gestor modelos considerações sobre os recursos e prova práticas](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## <a name="provide-standard-configuration-settings"></a>Fornecer as definições de configuração padrão

Em vez de oferecer um modelo que fornece flexibilidade total e os variações inúmeros, um padrão comuns consiste em fornecer uma seleção de configurações conhecidas. Na verdade, os utilizadores possam selecionar tamanhos de t-shirt padrão, tais como sandbox, pequenas, médio e grande. Outros exemplos de tamanhos de t-shirt são ofertas de produto, tal como Comunidade edition ou enterprise edition. Em outros casos, poderá estar configurações de carga de trabalho específicas de uma tecnologia –, tais como reduzir o mapa ou sem sql.

Com objetos complexos, pode criar variáveis que contêm conjuntos de dados, por vezes conhecidos como "as matrizes de propriedades" e utilizar esses dados para orientar a declaração de recursos no seu modelo. Esta abordagem fornece boas, conhecidas configurações de vários tamanhos são pré-configuradas automaticamente para clientes. Sem configurações conhecidas, os utilizadores do modelo tem determinar cluster de dimensionamento na sua própria, factor em restrições de recursos de plataforma e efetuar cálculos matemáticos para identificar a criação de partições resultante de contas de armazenamento e outros recursos (devido a restrições cluster de tamanho e de recurso). Para além de tornar uma melhor experiência do cliente, alguns configurações conhecidas são mais fáceis de suporte e pode ajudar a entregar um nível superior da densidade.

O exemplo seguinte mostra como definir variáveis que contêm objetos complexos para que representa a coleções de dados. As coleções de definem valores que são usados para máquina virtual tamanho, as definições de rede, definições do sistema operativo e definições de disponibilidade.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Repare que a variável de **tshirtSize** concatena o tamanho da t-shirt fornecido através de um parâmetro (**pequeno**, **médio**, **grande**) para o texto **tshirtSize**. Utilize esta variável para obter a variável de objecto complexa associado para esse tamanho t-shirt.

Em seguida, pode fazer referência a estas variáveis mais tarde no modelo. A capacidade de referência variáveis com o nome e respectivas propriedades simplifica a sintaxe de modelo e torna mais fácil de compreender contexto. O exemplo seguinte define um recurso para implementar ao utilizar os objetos previamente apresentados para definir valores. Por exemplo, o tamanho da memória virtual está definido por obter o valor de `variables('tshirtSize').vmSize` enquanto o valor para o tamanho do disco é obtido a partir do `variables('tshirtSize').diskSize`. Além disso, o URI para um modelo ligado está definido com o valor para `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Pass Estado-membro a um modelo

Partilhar o estado para um modelo através de parâmetros que fornecem diretamente durante a implementação.

A tabela seguinte lista parâmetros utilizados mais frequentemente em modelos.

Nome | Valor | Descrição
---- | ----- | -----------
localização    | Cadeia de uma lista das regiões Azure restrita   | A localização onde os recursos são implementados.
storageAccountNamePrefix    | Cadeia    | Nome exclusivo de DNS para a conta de armazenamento onde são colocados discos a VM
NomeDomínio  | Cadeia    | Nome de domínio da jumpbox acessível publicamente VM no formato: **{NomeDomínio}. { localização}.cloudapp.com** por exemplo: **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Cadeia    | Nome de utilizador para os VMs
adminPassword   | Cadeia    | Palavra-passe para os VMs
tshirtSize  | Cadeia de uma lista de restrita oferecidos tamanhos t-shirt   | O tamanho de unidade de escala com nome à disposição. Por exemplo, "Pequena", "médio", "Grande"
virtualNetworkName  | Cadeia    | Nome da rede virtual que o consumidor pretende utilizar.
enableJumpbox   | Cadeia de uma lista restrita (ativado/desactivado) | Parâmetro que identifica se pretende ativar uma jumpbox para o ambiente. Valores de: "ativado", "desativado"

O parâmetro **tshirtSize** utilizado na secção anterior é definido como:

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Passar de estado para modelos ligados

Ao ligar a modelos ligados, muitas vezes utilizar uma mistura de estático e gerado variáveis.

### <a name="static-variables"></a>Variáveis estáticas

Variáveis estáticas frequentemente são utilizadas para fornecer base valores, tais como URLs, que são utilizados através de um modelo.

No seguinte excerto do modelo, `templateBaseUrl` Especifica a localização de raiz para o modelo no GitHub. A linha seguinte cria uma nova variável `sharedTemplateUrl` que concatena o URL de base com o nome do modelo de recursos partilhados conhecido. Abaixo nessa linha, uma variável de objeto complexa é utilizada para armazenar um tamanho de t-shirt, onde o URL de base é concatenado para a localização do modelo de configuração conhecido e armazenado na `vmTemplate` propriedade.

A vantagem desta abordagem é que se alterar a localização do modelo, apenas precisar de alterar a variável estática num único local, transmite-la em toda os modelos ligados.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Variáveis geradas

Para além de variáveis estáticas, diversas variáveis são geradas dinamicamente. Esta secção identifica alguns dos tipos comuns de variáveis gerados.

#### <a name="tshirtsize"></a>tshirtSize

Estiver familiarizado com esta variável gerado dos exemplos acima.

#### <a name="networksettings"></a>networkSettings

Numa capacidade, capacidade ou modelo de ponto a ponto âmbito solução, os modelos ligados normalmente criam recursos que existam numa rede. Uma abordagem simples é utilizar um objeto complexo para armazenar as definições de rede e transmiti-los para modelos ligados.

Um exemplo de comunicar as definições de rede pode ser visto abaixo.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Recursos criados no ligadas modelos são colocados frequentemente num conjunto de disponibilidade. No exemplo seguinte, o nome do conjunto de disponibilidade é especificado e também o domínio de falhas e o domínio de atualização contam o número de utilizar.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Se precisar de vários conjuntos de disponibilidade (por exemplo, uma para os nós principais) e outro para nós de dados, pode utilizar um nome como um prefixo, especifique a vários conjuntos de disponibilidade ou siga o modelo mostrado anteriormente para a criação de uma variável para um tamanho de t-shirt específico.

#### <a name="storagesettings"></a>storageSettings

Detalhes de armazenamento frequentemente são partilhadas com modelos ligados. No exemplo abaixo, um objeto de *storageSettings* fornece detalhes sobre os nomes de conta e o contentor de armazenamento.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Com modelos ligados, poderá ter de passar definições do sistema operativo para vários tipos de nós nos tipos de configuração conhecido diferente. Um objeto complexo é uma maneira fácil para armazenar e partilhar informações do sistema operativo e também facilita a várias opções de sistema operativo de suporte para implementação.

O exemplo seguinte mostra um objeto para *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

Uma variável gerada, *machineSettings* é um objeto de complexo que contém uma mistura de variáveis de core para criar uma VM. As variáveis incluem o nome de utilizador do administrador e palavra-passe, um prefixo para os nomes VM e uma referência de imagem do sistema operativo.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Tenha em atenção que *osImageReference* obtém os valores a partir da variável de *osSettings* definida no modelo de principal. Isto significa que pode alterar facilmente o sistema operativo para uma VM — totalmente ou com base na preferência do consumidor modelo.

#### <a name="vmscripts"></a>vmScripts

O objeto *vmScripts* contém detalhes sobre os scripts de transferir e executar numa instância VM, incluindo fora e dentro de referências. Fora da referências incluem a infraestrutura. Referências de interior incluem o software instalado instalado e a configuração.

Utilize a propriedade *scriptsToDownload* para listar os scripts para transferir para a VM. Este objeto também contém referências a argumentos da linha de comandos para diferentes tipos de ações. Estas ações incluem executar a instalação predefinida para cada nó individual, uma instalação que é executada depois de todos os nós são implementados e quaisquer scripts adicionais que poderão ser específicas para um determinado livro.

Este exemplo é a partir de um modelo utilizado para implementar MongoDB, solicitar uma mediador para a prestação elevada disponibilidade. Foi adicionado a *arbiterNodeInstallCommand* para *vmScripts* para instalar o mediador.

A secção de variáveis é onde encontrar as variáveis que definem o texto específico para executar o script com os valores inicial maiúscula.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>Devolver estado a partir de um modelo

Não só pode ser efetuada com dados para um modelo, também pode partilhar dados para o modelo de chamada. Na secção **resultados** de um modelo ligada, pode fornecer pares valor/chave que podem ser consumidos pelo modelo de origem.

O exemplo seguinte mostra como passar o endereço IP privado gerado num modelo ligadas.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Dentro do modelo principal, pode utilizar esses dados com a seguinte sintaxe:

    "[reference('master-node').outputs.masterip.value]"

Pode utilizar esta expressão na secção de resultados de ou na secção de recursos do modelo principal. Não pode utilizar a expressão na secção variáveis, uma vez que depende o estado de execução. Para devolver este valor a partir do modelo principal, utilize:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Para obter um exemplo de utilização de secção de resultados de um modelo ligada para devolver discos de dados para uma máquina virtual, consulte o artigo [criar vários discos de dados para uma Máquina Virtual](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definir as definições de autenticação para máquina virtual

Pode utilizar o mesmo padrão mostrado anteriormente para as definições de configuração para especificar as definições de autenticação para uma máquina virtual. Criar um parâmetro para passagem no tipo de autenticação.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Adicionar variáveis para os diferentes tipos de autenticação e uma variável para armazenar qual o tipo é utilizada para esta implementação baseada no valor do parâmetro.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Quando definir a máquina virtual, defina o **osProfile** para a variável que criou.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Próximos passos
- Para saber mais sobre as secções do modelo, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](resource-group-authoring-templates.md)
- Para ver as funções que estão disponíveis dentro de um modelo, consulte [Funções de modelo de Gestor de recursos do Azure](resource-group-template-functions.md)

