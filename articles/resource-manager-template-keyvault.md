<properties
   pageTitle="Modelo de Gestor de recursos para cofre chave | Microsoft Azure"
   description="Mostra o esquema do Gestor de recursos para implementar cofres chaves através de um modelo."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Esquema do modelo de cofre chave

Cria uma chave cofre.

## <a name="schema-format"></a>Formato de esquema

Para criar uma chave cofre, adicione o esquema que se segue à secção de recursos o modelo.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valores

As tabelas seguintes descrevem os valores que tem de definir no esquema.

| Nome | Valor |
| ---- | ---- | 
| tipo | Enumeração<br />Obrigatório<br />**Microsoft.KeyVault/vaults**<br /><br />O tipo de recurso para criar. |
| apiVersion | Enumeração<br />Obrigatório<br />**2015-06-01** ou **2014-12-19-pré-visualizar**<br /><br />A versão de API para utilizar para criar o recurso. | 
| nome | Cadeia<br />Obrigatório<br />Um nome que seja exclusivo ao longo do Azure.<br /><br />O nome do cofre chave para criar. Considere utilizar a função [uniqueString](resource-group-template-functions.md#uniquestring) com convenção de nomenclatura para criar um nome exclusivo, conforme mostrado no exemplo abaixo. |
| localização | Cadeia<br />Obrigatório<br />Uma região válida para cofres chaves. Para determinar regiões válidos, consulte o artigo [suportadas regiões](resource-manager-supported-services.md#supported-regions).<br /><br />Região para alojar o Cofre chave. |
| Propriedades | Objecto<br />Obrigatório<br />[objecto de propriedades](#properties)<br /><br />Um objeto que especifica o tipo de chave cofre para criar. |
| recursos | Matriz<br />Opcional<br />Permitido valores: [recursos secretos do cofre chave](resource-manager-template-keyvault-secret.md)<br /><br />Recursos de subordinados para o Cofre chave. |

<a id="properties" />
### <a name="properties-object"></a>objecto de propriedades

| Nome | Valor |
| ---- | ---- | 
| enabledForDeployment | Booleano<br />Opcional<br />**Verdadeiro** ou **Falso**<br /><br />Especifica se a Cofre está ativado para implementação Máquina Virtual ou serviço ferro. |
| enabledForTemplateDeployment | Booleano<br />Opcional<br />**Verdadeiro** ou **Falso**<br /><br />Especifica se a Cofre está ativado para utilização em implementações do modelo de Gestor de recursos. Para obter mais informações, consulte o artigo [passar valores seguros durante a implementação](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Booleano<br />Opcional<br />**Verdadeiro** ou **Falso**<br /><br />Especifica se a cofre estiver ativado para encriptação de volume. |
| tenantId | Cadeia<br />Obrigatório<br />**Identificador exclusivo global**<br /><br />O identificador de inquilino para a subscrição. Pode recuperá-la com o cmdlet do PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) ou a **conta azure Mostrar** Azure clip comando. |
| accessPolicies | Matriz<br />Obrigatório<br />[objeto de accessPolicies](#accesspolicies)<br /><br />Uma matriz de até 16 objetos que especifique as permissões para o utilizador ou o capital de serviço. |
| SKU | Objecto<br />Obrigatório<br />[objeto de SKU](#sku)<br /><br />O SKU para o Cofre chave. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>objeto de properties.accessPolicies

| Nome | Valor |
| ---- | ---- | 
| tenantId | Cadeia<br />Obrigatório<br />**Identificador exclusivo global**<br /><br />O identificador de inquilino do inquilino Azure Active Directory que contém o **ID de objecto** nesta política de acesso |
| ID de objecto | Cadeia<br />Obrigatório<br />**Identificador exclusivo global**<br /><br />O identificador de objeto do utilizador do Azure Active Directory ou principal de serviço que terá acesso às cofre. Pode obter o valor da [Obter AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou os cmdlets do PowerShell [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) ou os comandos de **utilizador do azure ad** ou **azure ad sp** clip Azure. |
| permissões | Objecto<br />Obrigatório<br />[objeto de permissões](#permissions)<br /><br />As permissões atribuídas em deste cofre ao objeto do Active Directory. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>objeto de properties.accessPolicies.permissions

| Nome | Valor |
| ---- | ---- | 
| teclas | Matriz<br />Obrigatório<br />**todos os**, **cópia de segurança**, **Criar**, **desencriptar**, **Eliminar**, **encriptar**, **obter**, **Importar**, **lista**, **Restaurar**, **início de sessão**, **unwrapkey**, **Atualizar**, **Certifique-se**, **wrapkey**<br /><br />As permissões atribuídas em teclas deste cofre para este objeto do Active Directory. Este valor tem de ser especificado como uma matriz de uma ou mais valores permitidos. |
| segredos | Matriz<br />Obrigatório<br />**todos os**, **Eliminar**, **obter**, **lista**, **Definir**<br /><br />As permissões atribuídas em segredos deste cofre para este objeto do Active Directory. Este valor tem de ser especificado como uma matriz de uma ou mais valores permitidos. |

<a id="sku" />
### <a name="propertiessku-object"></a>objeto de Properties.SKU

| Nome | Valor |
| ---- | ---- | 
| nome | Enumeração<br />Obrigatório<br />**padrão**ou **premium** <br /><br />A camada de serviço da KeyVault para utilizar.  Padrão suporta segredos e chaves protegido por software.  Premium adiciona suporte para protegida HSM teclas. |
| família | Enumeração<br />Obrigatório<br />**A** <br /><br />A família sku para utilizar. |
 
    
## <a name="examples"></a>Exemplos

O exemplo seguinte implementa uma chave cofre e a palavra-passe.

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

## <a name="quickstart-templates"></a>Modelos de guia de introdução

O modelo de guia de introdução seguinte implementa chave cofre.

- [Criar cofre chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Próximos passos

- Para obter informações gerais sobre cofres chaves, consulte o artigo [Introdução ao Azure chave cofre](./key-vault/key-vault-get-started.md).
- Para obter um exemplo de referenciar uma palavra-passe cofre chave quando implementar modelos, consulte o artigo [passar valores seguros durante a implementação](resource-manager-keyvault-parameter.md).

