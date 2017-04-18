<properties
   pageTitle="Modelo de Gestor de recursos para bloqueios recurso | Microsoft Azure"
   description="Mostra o esquema do Gestor de recursos para implementar bloqueios de recursos através de um modelo."
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

# <a name="resource-locks-template-schema"></a>Esquema de modelo de bloqueios de recursos

Cria um cadeado um recurso e o seu filho recursos.

## <a name="schema-format"></a>Formato de esquema

Para criar um cadeado, adicione o esquema que se segue à secção de recursos do seu modelo.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valores

As tabelas seguintes descrevem os valores que tem de definir no esquema.

| Nome | Obrigatório | Descrição |
| ---- | -------- | ----------- |
| tipo | Sim | O tipo de recurso para criar.<br /><br />Para recursos:<br />**{espaço de nomes} / {escreva} / fornecedores/bloqueios**<br /><br/>Para grupos de recursos:<br />**Microsoft.Authorization/locks** |
| apiVersion | Sim | A versão de API para utilizar para criar o recurso.<br /><br />Utilização:<br />**2015-01-01**<br /><br /> |
| nome | Sim | Um valor que especifica o recurso para bloquear e um nome para o bloqueio. Pode ter até 64 carateres e não pode conter <>, % &,?, ou quaisquer carateres de controlo.<br /><br />Para recursos:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Para grupos de recursos:<br />**{lockname}** |
| dependsOn | N | Uma lista separados por vírgulas de um recurso de nomes ou identificadores exclusivos do recurso.<br /><br />Colecção de recursos, que este bloqueio depende. Se o recurso que está a bloquear for implementado no mesmo livro, inclua esse nome de recurso neste elemento para garantir que o recurso é implementado pela primeira vez. | 
| Propriedades | Sim | Um objeto que identifica o tipo de bloqueio e notas sobre o bloqueio.<br /><br />Consulte o artigo [objecto de propriedades](#properties-object). |  

### <a name="properties-object"></a>objecto de propriedades

| Nome | Obrigatório | Descrição |
| ---- | -------- | ----------- |
| nível   | Sim | O tipo de bloqueio para aplicar ao âmbito.<br /><br />**CannotDelete** - os utilizadores podem modificar recurso mas não eliminá-la.<br />**Só de leitura** - os utilizadores podem ler a partir de um recurso, mas não podem eliminá-la ou execute as ações no mesmo. |
| notas   | N | Descrição do bloqueio. Pode ter até 512 carateres. |


## <a name="how-to-use-the-lock-resource"></a>Como utilizar o recurso de bloqueio

Adicionar este recurso ao seu modelo para impedir que ações especificadas num recurso. O bloqueio aplica-se a todos os utilizadores e grupos.

Para criar ou eliminar bloqueios de gestão, tem de ter acesso a **Microsoft.Authorization/** * ou * *Microsoft.Authorization/locks/* ** ações. Das funções incorporadas, apenas **proprietário** e **utilizador acesso administrador * * são concedidas essas ações. Para obter informações sobre o controlo de acesso baseado em funções, consulte o artigo [O controlo de acesso baseado em funções Azure](./active-directory/role-based-access-control-configure.md).

O bloqueio é aplicado ao recurso especificado e dos recursos de subordinados.

Pode remover um bloqueio com o comando do PowerShell **Remover AzureRmResourceLock** ou com a [operação de eliminação](https://msdn.microsoft.com/library/azure/mt204562.aspx) da API do resto.

## <a name="examples"></a>Exemplos

O exemplo seguinte se aplica um cadeado eliminar não é possível para uma aplicação web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

O exemplo seguinte se aplica um cadeado eliminar não é possível ao grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a estrutura de modelo, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md).
- Para mais informações sobre bloqueios, consulte o artigo [recursos de bloqueio com o Gestor de recursos do Azure](resource-group-lock-resources.md).
