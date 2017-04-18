<properties
   pageTitle="Ligadas modelos com o Gestor de recursos | Microsoft Azure"
   description="Descreve como utilizar modelos ligados num modelo de Gestor de recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especifique um ficheiro de parâmetro e URLs dinamicamente criados."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>Utilizar modelos ligados com o Gestor de recursos do Azure

A partir de dentro de um modelo de Gestor de recursos do Azure, pode ligar a outro modelo, que permite-lhe decomposição a sua implementação para um conjunto de destino, modelos finalidade específicos. Tal como acontece com permitem uma aplicação para várias categorias de código, decomposição fornece benefícios em termos de testes, reutilizar e legibilidade.  

Pode passar parâmetros a partir de um modelo principal a um modelo ligado e essas parâmetros diretamente podem mapear para parâmetros ou variáveis expostas pelo modelo de chamada. O modelo ligado também pode ser efetuada com uma variável de saída para o modelo de origem, permitindo-um intercâmbio de dados bidirecional entre modelos.

## <a name="linking-to-a-template"></a>Ligar a um modelo

Criar uma ligação entre dois modelos ao adicionar um recurso de implementação dentro do modelo principal que aponta para o modelo ligado. Defina a propriedade **templateLink** para o URI do modelo ligado. Pode fornecer valores de parâmetros para o modelo ligado ao especificar os valores diretamente no seu modelo ou ao ligar a um ficheiro de parâmetro. O exemplo seguinte utiliza a propriedade de **parâmetros** para especificar um valor de parâmetro diretamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

O serviço do Gestor de recurso tem de conseguir aceder ao modelo de ligadas. Não é possível especificar um ficheiro local ou de um ficheiro que só está disponível na sua rede local para o modelo ligado. Apenas pode fornecer um valor URI que inclui **http** ou **https**. Uma das opções é colocar o seu modelo ligado numa conta de armazenamento e utilize o URI para esse item, tal como mostrado no seguinte exemplo.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Apesar do modelo ligado tem de estar disponível externamente, não é necessário ser ficará disponível ao público. Pode adicionar o seu modelo a uma conta de armazenamento privado que esteja acessível para apenas o proprietário da conta de armazenamento. Em seguida, criar um assinatura (SA) token de acesso partilhado de ativar o acesso durante a implementação. Adicione esse token de SA para o URI para o modelo ligado. Para obter passos sobre como configurar um modelo numa conta de armazenamento e gerar um token de SA, consulte o artigo [recursos de implementar modelos de Gestor de recursos e Azure PowerShell](resource-group-template-deploy.md) ou [recursos de implementar modelos de Gestor de recursos e clip do Azure](resource-group-template-deploy-cli.md). 

O exemplo seguinte mostra um modelo de elemento principal com ligações para outro modelo. O modelo ligado é acedido com um token de SA que lhe é transmitido no como um parâmetro.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Apesar do token é transmitido no como uma cadeia segura, o URI do modelo ligado, incluindo o token de SA, tem sessão iniciado nas operações de implementação para esse grupo de recursos. Para limitar a exposição, defina uma expiração para o token.

## <a name="linking-to-a-parameter-file"></a>Ligar a um ficheiro de parâmetro

O exemplo seguinte utiliza a propriedade **parametersLink** para ligar a um ficheiro de parâmetro.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

O valor de URI para o ficheiro ligado parâmetro não pode ser um ficheiro local e tem de incluir **http** ou **https**. Também pode ser limitado ao livro acesso através de um token de SA o ficheiro de parâmetro.

## <a name="using-variables-to-link-templates"></a>Utilizar variáveis para ligar a modelos

Os exemplos anteriores mostrava valores de URL codificada para as ligações de modelo. Esta abordagem pode funcionar para um modelo simple mas não funciona bem quando trabalhar com um conjunto maior de modelos modulares. Em vez disso, pode criar uma variável estática que armazena um URL base para o modelo principal e, em seguida, criar dinamicamente URLs para os modelos de ligadas a partir desse URL base. A vantagem desta abordagem é facilmente pode mover ou bifurcam o modelo de uma vez que apenas precisar de alterar a variável estática no modelo principal. O modelo principal transmite a URIs em todo o modelo decomposto correto.

O exemplo seguinte mostra como utilizar um URL base para criar duas URLs para os modelos ligados (**sharedTemplateUrl** e **vmTemplate**). 

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

Também pode utilizar [deployment()](resource-group-template-functions.md#deployment) para obter o URL de base para o modelo atual e utilizá-la para obter o URL para outros modelos na mesma localização. Esta abordagem é útil se a localização do modelo for alterada (talvez devido ao controlo de versões) ou se pretender evitar definitivamente codificação de URL no ficheiro de modelo. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>Condicionalmente ligar a modelos

Pode ligar a modelos diferentes por passagem de um valor de parâmetro é utilizado para construir o URI do modelo ligado. Esta abordagem funciona bem quando precisa de especificar durante a implementação que ligadas modelo para utilizar. Por exemplo, pode especificar um modelo para utilizar para uma conta existente do armazenamento e outro modelo para utilizar para uma nova conta de armazenamento.

O exemplo seguinte mostra um parâmetro para um nome de conta de armazenamento e um parâmetro para especificar se a conta de armazenamento é novo ou existente.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Criar uma variável para o modelo URI que inclui o valor do parâmetro novo ou existente.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Fornecer esse valor variável para o recurso de implementação.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

O URI resolve a um modelo com o nome **existingStorageAccount.json** ou **newStorageAccount.json**. Crie modelos para esses URIs.

O exemplo seguinte mostra o modelo de **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

O exemplo seguinte mostra o modelo de **newStorageAccount.json** . Tenha em atenção que, como o armazenamento existente, o objeto de conta de armazenamento do modelo de conta é devolvido no saídas de. O modelo mestra funciona com o modelo ligado.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Exemplo concluído

Os modelos de exemplo seguintes apresentam uma disposição simplificada dos modelos ligadas para ilustrar vários dos conceitos neste artigo. Parte do princípio de que modelos foram adicionados ao mesmo contentor numa conta de armazenamento com acesso público desativado. O modelo ligado transmite um valor para o modelo na secção **exporta** principal.

O ficheiro **parent.json** consiste em:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

O ficheiro **helloworld.json** consiste em:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
PowerShell, que obter um token para o contentor e implementar modelos com:

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

No Azure clip, obter um token para o contentor e implementar os modelos com o código seguinte. Atualmente, tem de fornecer um nome para a implementação ao utilizar um modelo de URI que inclui um token de SA.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Lhe for pedido para fornecer o token de SA como um parâmetro. Tem de preceda o token com **?**.

## <a name="next-steps"></a>Próximos passos
- Para saber mais sobre o que define a sequência de implementação para os recursos, consulte o artigo [definir dependências em modelos de Gestor de recursos do Azure](resource-group-define-dependencies.md)
- Para saber como definir um recurso mas criar várias instâncias do mesmo, consulte o artigo [criar várias instâncias de recursos no Gestor de recursos do Azure](resource-group-create-multiple.md)
