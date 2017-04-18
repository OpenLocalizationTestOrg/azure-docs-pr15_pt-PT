<properties
   pageTitle="Secreta cofre chave com o Gestor de recursos modelo | Microsoft Azure"
   description="Mostra como transmitir uma palavra-passe a partir de uma chave cofre como um parâmetro durante a implementação."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Passar valores seguros durante a implementação

Quando precisar de transmitir um valor seguro (como uma palavra-passe) como um parâmetro durante a implementação, pode armazenar esse valor como uma palavra-passe num [Cofre de chave do Azure](./key-vault/key-vault-whatis.md) e fazer referência o valor de outros modelos de Gestor de recursos. Incluir apenas uma referência para o segredo no seu modelo para que o segredo nunca é exposto e não é necessário introduzir manualmente o valor para o segredo sempre que implementar os recursos. Pode especificar quais os utilizadores ou principais de serviço podem aceder a palavra-passe.  

## <a name="deploy-a-key-vault-and-secret"></a>Implementar uma chave cofre e a palavra-passe

Para criar cofre chave que pode ser referenciado a partir de outros modelos de Gestor de recursos, tem de definir a propriedade **enabledForTemplateDeployment** como **true**e tem de conceder acesso ao utilizador ou principal de serviço que executará de implementação que referencia o segredo.

Para obter informações sobre como implementar uma chave cofre e a palavra-passe, consulte o artigo [esquemas de secreta de Cofre de tecla](resource-manager-template-keyvault-secret.md)e [esquema do Cofre de chave](resource-manager-template-keyvault.md) .

## <a name="reference-a-secret-with-static-id"></a>Referência de uma palavra-passe com o id estático

Referenciar o segredo a partir de um ficheiro de parâmetros que transmite valores para o modelo. Referenciar o segredo passando o identificador de recursos do cofre chave e o nome do segredo. Neste exemplo, o segredo cofre chave tem de existir, e estiver a utilizar um valor estático para a mesma id do recurso.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Um ficheiro inteiro parâmetro poderá ser semelhante:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

O parâmetro que aceita o segredo deve ser um **securestring**. O exemplo seguinte mostra as secções relevantes de um modelo que implementa o SQL server que necessita de uma palavra-passe de administrador.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Referência de uma palavra-passe com o id dinâmico

A secção anterior mostrava como passar um id do recurso estático para o segredo chave cofre. No entanto, no alguns cenários, é necessário fazer referência a uma chave cofre palavra-passe que varia com base no processo de implementação atual. Nesse caso, não consegue disco rígido código o id do recurso no ficheiro de parâmetros. Infelizmente, dinamicamente não consegue gerar o id do recurso no ficheiro parâmetros porque expressões de modelo não são permitidos no ficheiro de parâmetros.

Para gerar dinamicamente o id do recurso para uma palavra-passe cofre chave, terá de mover o recurso que necessita do segredo para um modelo aninhado. No seu modelo principal, adicionar o modelo aninhado e passar um parâmetro que contém o id do recurso gerados dinamicamente.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Próximos passos

- Para obter informações gerais sobre cofres chaves, consulte o artigo [Introdução ao Azure chave cofre](./key-vault/key-vault-get-started.md).
- Para obter informações sobre como utilizar uma chave cofre com uma Máquina Virtual, consulte o artigo [Considerações de segurança para o Gestor de recursos do Azure](best-practices-resource-manager-security.md).
- Para obter exemplos concluídos referenciar segredos chave, consulte o artigo [exemplos de chave do cofre](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

