<properties
   pageTitle="Dependências no Gestor de recursos modelos | Microsoft Azure"
   description="Descreve como configurar um recurso como dependentes no outro recurso durante a implementação para se certificar de recursos são implementados na ordem correta."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Definir dependências em modelos de Gestor de recursos do Azure

Para um dado recurso pode ser outros recursos que tem de existir antes do recurso é implementado. Por exemplo, SQL server tem de existir antes de tentar implementar uma base de dados do SQL. Definir desta relação marcando um recurso como dependente o outro recurso. Normalmente, pode definir uma dependência com o elemento **dependsOn** , mas também pode defini-la através da função de **referência** . 

Gestor de recursos avalia as dependências entre recursos e implementa-los na sua ordem dependente. Quando não estão dependentes uns dos outros recursos, o Gestor de recursos ser implementada-los em paralelo.

## <a name="dependson"></a>dependsOn

No seu modelo, o elemento dependsOn permite-lhe definir um recurso como um dependente de uma ou mais recursos. O valor pode ser uma lista separados por vírgulas de nomes de recursos. 

O exemplo seguinte mostra um conjunto de escala de máquina virtual depende um balanceador de carga, rede virtual e um ciclo que cria várias contas de armazenamento. Outros recursos estes não são apresentados no exemplo seguinte, mas precisam existir noutro local no modelo.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Para definir uma dependência entre e recursos que são criados através de um ciclo de cópia de um recurso, defina o elemento dependsOn ao nome do ciclo. Por exemplo, consulte o artigo [criar várias instâncias de recursos no Gestor de recursos do Azure](resource-group-create-multiple.md).

Enquanto que pode ser inclinado para utilizar dependsOn para mapear relações entre os recursos, é importante compreender por que motivo que está a fazer-uma vez que pode afetar o desempenho da sua implementação. Por exemplo, para documentos como recursos estão interligados, dependsOn não é a abordagem à direita. Não é possível consultar quais os recursos que foram definidos no elemento de dependsOn após a implementação. Ao utilizar dependsOn, pode afeta potencialmente tempo de implementação porque não implementar o Gestor de recursos na paralelos dois recursos que têm uma dependência. Relações entre recursos do documento, em vez disso, utilize a [ligação do recurso](resource-group-link-resources.md).

## <a name="child-resources"></a>Recursos de subordinados

A propriedade de recursos permite-lhe especificar os recursos de subordinados relacionados com o recurso a ser definido. Recursos de subordinados só podem ser definidos cinco níveis de profundidade. É importante ter em atenção que não é criada uma dependência implícita entre um recurso de subordinado e o recurso de elemento principal. Se precisar do recurso de subordinado a ser implementada após o recurso de elemento principal, devem mencionar explicitamente essa dependência com a propriedade dependsOn. 

Cada recurso principal aceita apenas determinados tipos de recursos, como recursos de subordinados. Os tipos de recurso aceite estão especificados no [esquema do modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso principal. O nome do tipo de recurso subordinado inclui o nome do tipo de recurso principal, tais como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são ambos os recursos de subordinados da **Microsoft.Web/sites**.

O exemplo seguinte mostra do SQL server e a base de dados SQL. Repare que é definida uma dependência explícita entre a base de dados SQL e o SQL server, embora a base de dados é subordinado do servidor.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>função de referência

A [função de referência](resource-group-template-functions.md#reference) permite uma expressão ao deriva o valor de outro nome JSON e pares valor ou runtime recursos. Expressões de referência implicitamente declaram que um recurso depende do outro. 

    reference('resourceName').propertyPath

Pode utilizar este elemento ou o elemento dependsOn para especificar dependências, mas não é necessário utilizar ambas para o mesmo recurso dependente. Sempre que possível, utilize uma referência implícita para evitar inadvertidamente adicionando uma dependência desnecessária.

Para saber mais, consulte o artigo [referência de função](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a criação de modelos de Gestor de recursos do Azure, consulte o artigo [modelos de criação](resource-group-authoring-templates.md). 
- Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](resource-group-template-functions.md).

