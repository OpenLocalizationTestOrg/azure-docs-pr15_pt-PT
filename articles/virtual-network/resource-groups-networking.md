<properties
   pageTitle="Descrição geral do fornecedor de recursos de rede | Microsoft Azure"
   description="Saiba mais sobre o novo fornecedor de recursos de rede no Gestor de recursos do Azure"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Fornecedor de recursos de rede
Uma necessidade de que está por detrás no sucesso do negócio hoje, é a capacidade de criar e gerir aplicações de deverá ter em consideração de rede de grande escala de uma forma ágil, flexível, segura e possa ser repetida. Gestor de recursos Azure (processador) permite-lhe criar aplicações, como um único conjunto de recursos em grupos de recursos. Como recursos são geridos através de vários fornecedores de recurso em processador.

Gestor de recursos do Azure depende de fornecedores de recurso diferente para fornecer acesso aos seus recursos. Existem três fornecedores de recurso principal: armazenamento e cluster de rede. Este documento aborda as características e benefícios do fornecedor de recursos de rede, incluindo:

- **Metadados** – pode adicionar informações para recursos de utilizar etiquetas. Estas marcas podem ser utilizadas para controlar a utilização de recursos ao longo de grupos de recursos e subscrições.
- **Maior controlo da sua rede** - rede recursos são vagamente ao afeta a forma e pode controlá-las de forma mais granular. Isto significa que tiver mais flexibilidade na gestão de recursos de redes.
- **Configuração mais rápida** - porque recursos de rede são vagamente ao afeta a forma, pode criar e orquestrar recursos de rede em paralelo. Este muito tem reduzido hora da configuração.
- O **Controlo de acesso com base da função** - RBAC fornece funções predefinidas, com o âmbito de segurança específico, para além de permitir a criação de funções personalizadas para a gestão de seguro.
- **Mais fácil gestão e implementação** - é mais fácil implementar e gerir aplicações, uma vez que pode pode criar uma pilha de aplicação completo como uma única colecção de recursos num grupo de recursos. E mais rápido para implementar, uma vez que pode implementar fornecendo simplesmente uma carga útil JSON de modelo.
- **Personalização rápida** - pode utilizar modelos declarativa estilo para activar a personalização de forma repetida e rápida de implementações.
- **Personalização de forma repetida** - pode utilizar modelos declarativa estilo para activar a personalização de forma repetida e rápida de implementações.
- **Interfaces gestão** - pode utilizar qualquer uma das seguintes interfaces para gerir os seus recursos:
    - RESTO com base API
    - PowerShell
    - .NET SDK
    - Node.JS SDK
    - Java SDK
    - Clip Azure
    - Portal de pré-visualização
    - PROCESSADOR idioma do modelo

## <a name="network-resources"></a>Recursos de rede
Agora pode gerir recursos de rede de forma independente, em vez de tê-los a todos gerido através de um recurso de cluster única (uma máquina virtual). Este procedimento garante um maior grau de flexibilidade e aprofundar compor uma infraestrutura de escala complexa e grandes num grupo de recursos.

Uma perspectiva conceptual de uma implementação de exemplo que envolvam uma aplicação de várias camadas é apresentada em baixo. Cada recurso que vir, tais como NIC, endereços IP públicos e VMs, pode ser gerido de forma independente.

![Modelo de rede de recursos](./media/resource-groups-networking/Figure2.png)

Todos os recursos contém um conjunto comum de propriedades e defina a respetiva propriedade individual. As propriedades comuns são:

|Propriedade|Descrição|Valores de exemplo|
|---|---|---|
|**nome**|Nome do recurso exclusivo. Cada tipo de recurso tem as suas próprias restrições de nomenclatura.|PIP01, VM01, NIC01|
|**localização**|Azure região onde reside o recurso|westus, eastus|
|**ID**|Identificação exclusivo do URI com base|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Pode verificar as propriedades individuais dos recursos nas secções abaixo.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de gestão
Pode gerir os recursos da redes Azure utilizando interfaces diferentes. Neste documento iremos destacar o cabo dessas interfaces: REST API e modelos.

### <a name="rest-api"></a>REST API
Como mencionado anteriormente, recursos de rede podem ser geridos através de uma variedade de interfaces, incluindo REST API, .NET SDK, Node.JS SDK, Java SDK, PowerShell, clip, Azure Portal e modelos.

A API Rest está em conformidade com a especificação do protocolo HTTP 1.1. A estrutura URI geral da API do é apresentada em baixo:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

E os parâmetros de chavetas representam os seguintes elementos:

- **id da subscrição** - o id da subscrição Azure.
- **recurso-fornecedor-espaço de nomes** - espaço de nomes para o fornecedor a ser utilizado. O valor para o fornecedor de recursos de rede é *Microsoft.Network*.
- **nome de região** - o nome do Azure região

Os seguintes métodos HTTP são suportados quando efetuar chamadas à REST API:

- **Colocar** - utilizados para criar um recurso de um determinado tipo, modificar uma propriedade de recursos ou alterar uma associação entre recursos.
- **Obter** - utilizados para obter informações de um recurso aprovisionada.
- **Eliminar** - utilizado para eliminar um recurso existente.

O pedido e a resposta em conformidade com um formato de carga útil JSON. Para obter mais detalhes, consulte o artigo [Azure APIs de gestão de recursos](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>PROCESSADOR idioma do modelo
Para além de gestão de recursos imperatively (através do APIs ou SDK), também pode utilizar um estilo de programação declarativa para criar e gerir os recursos de rede, utilizando o idioma de modelo de processador.

Baixo –, é fornecida uma representação de exemplo de um modelo

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

O modelo é principalmente uma descrição de JSON dos recursos e os valores de instância inseridos através de parâmetros. O exemplo abaixo pode ser utilizado para criar uma rede virtual com 2 sub-redes.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Tem a opção fornecer os valores de parâmetro manualmente ao utilizar um modelo ou pode utilizar um ficheiro de parâmetro. O exemplo abaixo apresenta um conjunto possível de valores de parâmetros para ser utilizado com o modelo acima:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Principais vantagens do utilizar modelos são:

- Pode criar uma infraestrutura complexa num grupo de recursos num estilo declarativa. Orchestration da criação de recursos, incluindo a gestão de dependência, é processada pelo processador.
- A infraestrutura de pode ser criada de forma a forma repetida através de diferentes regiões e dentro de uma região alterando simplesmente parâmetros.
- O estilo declarativa leva a mais curto tempo de antecedência na criação de modelos e gradual saída a infraestrutura de.

Para obter modelos de exemplo, consulte o artigo [Guia de introdução Azure modelos](https://github.com/Azure/azure-quickstart-templates).

Para mais informações sobre a linguagem de modelo de processador, consulte o artigo [Azure Gestor de recursos do modelo de idiomas](../resource-group-authoring-templates.md).

O modelo de exemplo acima utiliza a rede virtual e recursos de sub-rede. Existem outros recursos de rede que pode utilizar conforme indicado abaixo:

### <a name="using-a-template"></a>Utilizar um modelo

Pode implementar os serviços para Azure a partir de um modelo ao através do PowerShell, AzureCLI, ou ao efetuar um clique para implementar a partir de GitHub. Para implementar os serviços a partir de um modelo no GitHub, execute os seguintes passos:

1. Abra o ficheiro de template3 do GitHub. Por exemplo, abra [da rede Virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Clique em **implementar para Azure**e, em seguida, iniciar sessão no portal do Azure com as suas credenciais.
3. Verifique se o modelo e, em seguida, clique em **Guardar**.
4. Clique em **Editar parâmetros** e selecione uma localização como, *EUA Ocidental*, para vnet e sub-redes.
5. Se for necessário, altere os parâmetros **ADDRESSPREFIX** e **SUBNETPREFIX** e, em seguida, clique em **OK**.
6. Clique em **selecionar um grupo de recursos** e, em seguida, clique em que pretende adicionar a vnet e sub-redes para o grupo de recursos. Em alternativa, pode criar um novo grupo de recursos ao clicar em **ou criar novos**.
3. Clique em **Criar**. Repare no mosaico da apresentação de **implementação do modelo de aprovisionamento**. Depois de concluir o processo de implementação, irá ver um ecrã semelhante a uma abaixo.

![Implementação do modelo de exemplo](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Próximos passos

[Idioma do modelo do Gestor de recursos do Azure](../resource-group-authoring-templates.md)

[Azure à rede – modelos utilizados frequentemente](https://github.com/Azure/azure-quickstart-templates)

[Calcular o fornecedor de recursos](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)
