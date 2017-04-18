<properties
   pageTitle="Implementar várias instâncias de recursos | Microsoft Azure"
   description="Utilize a operação de cópia e matrizes num modelo de Gestor de recursos do Azure para iteramos várias vezes quando implementar recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Criar várias instâncias de recursos no Gestor de recursos do Azure

Este tópico mostra-lhe como iteramos no seu modelo de Gestor de recursos do Azure para criar várias instâncias de um recurso.

## <a name="copy-copyindex-and-length"></a>copiar, copyIndex e comprimento

Dentro do recurso para criar várias vezes, pode definir um objeto de **cópia** que especifica o número de vezes a iteração. A cópia leva-o até o seguinte formato:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Pode aceder ao valor atual de iteração com a função **copyIndex()** , tal como é mostrado abaixo dentro da função concat.

    [concat('examplecopy-', copyIndex())]

Quando criar vários recursos a partir de uma matriz de valores, pode utilizar a função de **duração** para especificar a contagem. Forneça a matriz como o parâmetro para a função de comprimento.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Utilize o valor de índice remissivo no nome

Pode utilizar a cópia operação criar várias instâncias de um recurso que são exclusivamente com nome com base no índice incrementar os. Por exemplo, poderá pretender adicionar um número exclusivo para o fim de cada nome de recurso que é implementado. Para implementar três web sites com o nome:

- examplecopy 0
- examplecopy-1
- examplecopy-2.

Utilize o modelo seguinte:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Deslocamento valor de índice

Notará no exemplo anterior direcionada o valor de índice remissivo a partir do zero para 2. Para compensar o valor de índice remissivo, pode passar um valor na função **copyIndex()** , tal como **copyIndex(1)**. O número de iterações para efetuar ainda está especificado no elemento de cópia, mas o valor de copyIndex é desfasamento pelo valor especificado. Sim, o mesmo modelo como no exemplo anterior, mas utilizar especificando **copyIndex(1)** seria implementar três web sites com o nome:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Utilize a funcionalidade copiar com matriz
   
A operação de cópia é particularmente útil quando trabalhar com matrizes porque que pode fazer iteração cada elemento na matriz. Para implementar três web sites com o nome:

- examplecopy Contoso
- examplecopy Fabrikam
- examplecopy Coho

Utilize o modelo seguinte:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Obviamente, defina a contagem de cópia para um valor que não seja o comprimento da matriz. Por exemplo, poderia criar uma matriz com muitos valores e, em seguida, passar um valor de parâmetro especifica quantos os elementos de matriz para implementar. Nesse caso, defina a contagem de cópia conforme mostrado no exemplo primeiro. 

## <a name="depending-on-resources-in-a-loop"></a>Dependendo de recursos num ciclo

Pode especificar que um recurso ser implementada após a outro recurso utilizando o elemento **dependsOn** . Quando precisar de implementar um recurso que depende da coleção de recursos num ciclo, pode utilizar fornecer o nome do ciclo cópia no elemento de **dependsOn** . O exemplo seguinte mostra como implementar 3 contas de armazenamento antes de implementar a Máquina Virtual. Não é apresentada a definição de Máquina Virtual completa. Repare que o elemento de cópia tem o **nome** definido para **storagecopy** o elemento **dependsOn** para as máquinas virtuais também está definido para **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>Ciclo num recurso aninhado

Não é possível utilizar um ciclo de cópia de um recurso aninhado. Se necessitar de criar várias instâncias de um recurso que normalmente definir como aninhadas dentro de outro recurso, em vez disso, tem de criar o recurso como um recurso de nível superior e definir a relação com o recurso de elemento principal através das propriedades do **nome** e **tipo** .

Por exemplo, imaginemos que normalmente define um conjunto de dados como um recurso aninhado dentro de uma fábrica de dados.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Para criar várias instâncias de conjuntos de dados, que precisa para alterar o modelo, conforme apresentado abaixo. Tipo de aviso completamente qualificado total e o nome inclui o nome de fábrica do mesmo de dados.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Criar várias instâncias quando copiar não irá funcionar

Só pode utilizar **cópia** em tipos de recursos e não em propriedades dentro de um tipo de recurso. Isto poderá criar problemas por si quando pretender criar várias instâncias de algo que faz parte de um recurso. Um cenário comum é criar vários discos de dados para uma Máquina Virtual. Não pode utilizar **cópia** com os discos de dados, uma vez que **dataDisks** é uma propriedade na máquina Virtual, não o seu próprio tipo de recurso. Em vez disso, criar uma matriz com tantas discos de dados à medida que vai precisar de e passar do número real de discos de dados para criar. Na definição do máquina virtual, utilize a função de **tomar** para obter apenas o número de elementos que realmente quer da matriz.

Um exemplo completo deste padrão é mostrar no modelo de [criar uma VM com uma seleção de discos dados dinâmica](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

As secções relevantes do modelo de implementação são apresentadas abaixo. Muitas o modelo foi removida para realçar as secções envolvidos na dinamicamente criação de um número de discos de dados. Repare que o parâmetro **numDataDisks** que permite-lhe passar o número de discos para criar. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Próximos passos
- Se quiser saber mais sobre as secções de um modelo, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](./resource-group-authoring-templates.md).
- Para obter todas as funções que pode utilizar um modelo, consulte [Funções de modelo de Gestor de recursos do Azure](./resource-group-template-functions.md).
- Para saber como implementar o modelo, consulte o artigo [Implementar uma aplicação com Azure Gestor de recursos do modelo](resource-group-template-deploy.md).
