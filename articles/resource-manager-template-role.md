<properties
   pageTitle="Modelo de Gestor de recursos para atribuições de funções | Microsoft Azure"
   description="Mostra o esquema do Gestor de recursos para implementar uma atribuição de funções através de um modelo."
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>Esquema do modelo de atribuições de funções

Atribui um utilizador, grupo ou principal do serviço para uma função de um âmbito especificado.

## <a name="resource-format"></a>Formato de recursos

Para criar uma atribuição de funções, adicione o esquema que se segue à secção de recursos do seu modelo.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valores

As tabelas seguintes descrevem os valores que tem de definir no esquema.

| Nome | Obrigatório | Descrição |
| ---- | -------- | ----------- |
| tipo | Sim    | O tipo de recurso para criar.<br /><br /> Grupo de recursos:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Para o recurso:<br />**{fornecedor-espaço de nomes} / {tipo de recurso} / fornecedores/roleAssignments** |
| apiVersion |Sim | A versão de API para utilizar para criar o recurso.<br /><br /> Utilize **2015-07-01**. | 
| nome | Sim | Um identificador exclusivo globalmente para a nova atribuição de funções. |
| dependsOn | N | Uma matriz separados por vírgulas de um recurso de nomes ou identificadores exclusivos do recurso.<br /><br />Colecção de recursos que depende este atribuição de funções. Se atribuir uma função que confinado a um recurso e recurso está implementado no mesmo livro, inclua esse nome de recurso neste elemento para garantir que o recurso é implementado pela primeira vez. |
| Propriedades | Sim | O objeto de propriedades que identifica o definição de função, o capital e o âmbito. |

### <a name="properties-object"></a>objecto de propriedades

| Nome | Obrigatório | Descrição |
| ---- | -------- | ----------- |
| roleDefinitionId | Sim |  O identificador de uma definição de função existente para ser utilizado a atribuição de funções.<br /><br /> Utilize o seguinte formato:<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Sim | O identificador exclusivo globalmente para um capital existente. Este valor mapas ao dentro do directório de ID e pode apontar para um utilizador, principal do serviço ou grupo de segurança. |
| âmbito | N | O âmbito no qual este atribuição de funções aplica-se ao.<br /><br />Para grupos de recursos, utilize:<br />**/resourceGroups/ /subscriptions/ {id da subscrição} {nome de grupo de recursos}**  <br /><br />Para obter recursos, utilize:<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Como utilizar o recurso de atribuição de funções

Adicionar uma atribuição de funções ao seu modelo quando precisar de adicionar um utilizador, grupo ou principal de serviço a uma função durante a implementação. Atribuições de funções são herdadas do níveis de âmbito, mais elevados para que se já tiver adicionado um principal para uma função ao nível da subscrição, não terá reatribui-lo para o grupo de recursos ou recurso.

Existem muitos valores de identificador que será necessário fornecer ao trabalhar com as atribuições de funções. Pode obter os valores através do PowerShell ou clip de Azure.

### <a name="powershell"></a>PowerShell

O nome da atribuição de funções requer um identificador exclusivo global. Pode gerar um novo identificador para **nome** com:

    $name = [System.Guid]::NewGuid().toString()

Pode obter o identificador para a definição de função com:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Pode obter o identificador para o capital de um dos seguintes comandos.

Para um grupo com o nome **auditores**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para um utilizador com o nome **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para um serviço de capital com o nome **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Clip Azure

Pode obter o identificador para a definição de função com:

    azure role show Reader --json | jq .[].Id -r

Pode obter o identificador para o capital de um dos seguintes comandos.

Para um grupo com o nome **auditores**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Para um utilizador com o nome **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Para um serviço de capital com o nome **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Exemplos

O modelo seguinte recebe um identificador para uma função e um identificador para um utilizador, grupo ou principal de serviço. Atribui a função ao nível do grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



O modelo seguinte cria uma conta de armazenamento e atribui a função de leitor à conta de armazenamento. Os identificadores de dois grupos e a função de leitor foram incluídos no modelo para simplificar a implementação. Esses valores podem ser obtidos no momento da implementação através de script e transmitidos como parâmetros.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modelos de guia de introdução

Os seguintes modelos de mostram como utilizar o recurso de atribuição de funções:

- [Atribuir funções incorporadas ao grupo de recursos](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Atribuir funções incorporadas a VM existente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Atribuir funções incorporadas a vários VMs existentes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a estrutura de modelo, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md).
- Para mais informações sobre o controlo de acesso baseado em funções, consulte o artigo [Baseada em Azure Active Directory funções o controlo de acesso](./active-directory/role-based-access-control-configure.md).
