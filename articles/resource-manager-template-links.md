<properties
   pageTitle="Modelo de Gestor de recursos para recursos de ligação | Microsoft Azure"
   description="Mostra o esquema do Gestor de recursos para implementar ligações entre recursos relacionados através de um modelo."
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Esquema de modelo de ligações de recursos

Cria uma ligação entre dois recursos. A ligação é aplicada a um recurso conhecido como o recurso de origem. O segundo recurso na ligação é conhecido como o recurso de destino.

## <a name="schema-format"></a>Formato de esquema

Para criar uma ligação, adicione o esquema que se segue à secção de recursos do seu modelo.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valores

As tabelas seguintes descrevem os valores que tem de definir no esquema.

| Nome | Valor |
| ---- | ---- |
| tipo | Enumeração<br />Obrigatório<br />**{espaço de nomes} / {escreva} / fornecedores/ligações**<br /><br />O tipo de recurso para criar. {Espaço de nomes de} e valores de {tipo} referir-se para o tipo de espaço de nomes e de recursos do fornecedor do recurso de origem. |
| apiVersion | Enumeração<br />Obrigatório<br />**2015-01-01**<br /><br />A versão de API para utilizar para criar o recurso. |  
| nome | Cadeia<br />Obrigatório<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> até 64 carateres e não pode conter <>, % &,?, ou quaisquer carateres de controlo.<br /><br />Um valor que especifica tanto o nome do recurso de origem e um nome para a ligação. |
| dependsOn | Matriz<br />Opcional<br />Uma lista separados por vírgulas de um recurso de nomes ou identificadores exclusivos do recurso.<br /><br />Colecção de recursos que depende desta ligação. Se os recursos que está a ligar são implementados no mesmo livro, inclua os nomes de recursos neste elemento para se certificar de que são implementadas pela primeira vez. | 
| Propriedades | Objecto<br />Obrigatório<br />[objecto de propriedades](#properties)<br /><br />Um objeto que identifica o recurso a ligação e notas sobre a ligação. |  

<a id="properties" />
### <a name="properties-object"></a>objecto de propriedades

| Nome | Valor |
| ------- | ---- |
| targetId | Cadeia<br />Obrigatório<br />**{id do recurso}**<br /><br />O identificador do recurso de destino para ligar a. |
| notas | Cadeia<br />Opcional<br />até 512 carateres<br /><br />Descrição do bloqueio. |


## <a name="how-to-use-the-link-resource"></a>Como utilizar o recurso de ligação

Aplicar uma ligação entre dois de recursos quando os recursos têm uma dependência que persistir após a implementação. Por exemplo, uma aplicação pode ligar a uma base de dados num grupo de recursos diferentes. Pode definir essa dependência através da criação de uma ligação à base de dados a partir da aplicação. Ligações permitem-lhe a relação entre dois recursos do documento. Mais tarde, o utilizador ou alguém na sua organização pode consultar um recurso para ligações descobrir como o recurso funciona com outros recursos.

Todos os recursos ligados tem de pertencer à mesma subscrição. Cada recurso pode ser associado a 50 outros recursos. Se qualquer um dos recursos ligados são eliminou ou moveu, o proprietário da ligação tem limpar a ligação restante.

Para trabalhar com ligações através do resto, consulte o artigo [Recursos ligadas](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilize o seguinte comando Azure PowerShell para ver todas as ligações na sua subscrição. Pode fornecer outros parâmetros para limitar os resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Exemplos

O exemplo seguinte se aplica um cadeado só de leitura para uma aplicação web.

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
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modelos de guia de introdução

Os seguintes modelos de guia de introdução implementar recursos com uma ligação.

- [Alertar para fila com a aplicação de lógica](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Alerta a margem com a aplicação de lógica](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Aprovisionar uma aplicação de API com um gateway existente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Aprovisionar uma aplicação de API com um novo gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Criar uma aplicação de aplicação de lógica mais API através de um modelo](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Aplicação de lógica que envia uma mensagem de texto quando é acionada um alerta](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a estrutura de modelo, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md).
