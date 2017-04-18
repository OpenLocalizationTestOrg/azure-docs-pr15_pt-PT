<properties
   pageTitle="Modelo de Gestor de recursos para uma palavra-passe num cofre chave | Microsoft Azure"
   description="Mostra o esquema do Gestor de recursos para implementar segredos cofre chave através de um modelo."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Esquema do modelo secreta de cofre chave

Cria uma palavra-passe que está armazenada num cofre chave. Este tipo de recurso com frequência é implementado como um recurso de subordinado do [Cofre chave](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Formato de esquema

Para criar uma palavra-passe cofre chave, adicione o esquema que se segue ao seu modelo. O segredo pode ser definido como um recurso subordinado de uma chave cofre ou como recursos de nível superior. Pode defini-la como um recurso de subordinados quando o Cofre chave é implementado no mesmo livro. Terá de definir o segredo como um recurso de nível superior, quando o Cofre chave não está implementado no mesmo livro ou quando tem de criar várias segredos por ciclo o tipo de recurso. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valores

As tabelas seguintes descrevem os valores que tem de definir no esquema.

| Nome | Valor |
| ---- | ---- | 
| tipo | Enumeração<br />Obrigatório<br />**segredos** (quando implementado como um recurso de subordinado do cofre chave) ou<br /> **Microsoft.KeyVault/vaults/secrets** (quando implementado como um recurso de nível superior)<br /><br />O tipo de recurso para criar. |
| apiVersion | Enumeração<br />Obrigatório<br />**2015-06-01** ou **2014-12-19-pré-visualizar**<br /><br />A versão de API para utilizar para criar o recurso. | 
| nome | Cadeia<br />Obrigatório<br />Uma única palavra quando implementado como um recurso de subordinados da chave cofre ou no formato **{nome-Cofre de palavras-chave} / {nome do secreta}** quando implementado como um recurso de nível superior para ser adicionada a um cofre chave existente.<br /><br />O nome do segredo para criar. |
| Propriedades | Objecto<br />Obrigatório<br />[objecto de propriedades](#properties)<br /><br />Um objeto que especifica o valor do segredo para criar. |
| dependsOn | Matriz<br />Opcional<br />Uma lista separados por vírgulas de um recurso de nomes ou identificadores exclusivos do recurso.<br /><br />Colecção de recursos que depende desta ligação. Se o Cofre chave para o segredo é implementado no mesmo livro, inclua o nome do cofre chave neste elemento para se certificar de que é implementado pela primeira vez. |

<a id="properties" />
### <a name="properties-object"></a>objecto de propriedades

| Nome | Valor |
| ---- | ---- | 
| valor | Cadeia<br />Obrigatório<br /><br />O valor secreto para armazenar no chave cofre. Ao passar um valor para esta propriedade, utilize um parâmetro de tipo **securestring**.  |

    
## <a name="examples"></a>Exemplos

O primeiro exemplo implementa uma palavra-passe, como um recurso de subordinado do cofre chave.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

O segundo exemplo implementa o segredo como um recurso de nível superior que está armazenado num cofre chave existente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Próximos passos

- Para obter informações gerais sobre cofres chaves, consulte o artigo [Introdução ao Azure chave cofre](./key-vault/key-vault-get-started.md).
- Para obter um exemplo de referenciar uma palavra-passe cofre chave quando implementar modelos, consulte o artigo [passar valores seguros durante a implementação](resource-manager-keyvault-parameter.md).


