<properties 
    pageTitle="Ligação de recursos no Gestor de recursos do Azure | Microsoft Azure" 
    description="Crie uma ligação entre recursos relacionados em grupos de recurso diferente no Gestor de recursos do Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Ligação de recursos no Gestor de recursos do Azure

Durante a implementação, pode marcar um recurso como dependentes no outro recurso, mas esse ciclo de vida termina na implementação. Após a implementação, não existe nenhuma relação identificada entre recursos dependentes. Gestor de recursos fornece uma funcionalidade denominada recurso ligar para estabelecer relações persistentes entre recursos.

Com a ligação do recurso, pode documentar relações que aparecem em grupos de recursos. Por exemplo, é comum de ter uma base de dados com os suas próprias ciclo de vida residem no grupo de um recurso e uma aplicação com um ciclo de vida diferentes residem num grupo de recursos diferentes. A aplicação liga-se à base de dados, pelo que pretende marcar uma ligação entre a aplicação e a base de dados. 

Todos os recursos ligados tem de pertencer à mesma subscrição. Cada recurso pode ser associado a 50 outros recursos. A única forma de recursos relacionados de consulta é através da API REST. Se qualquer um dos recursos ligados são eliminou ou moveu, o proprietário da ligação tem limpar a ligação restante. Se **não** avisado quando eliminar um recurso que está associado a outros recursos.

## <a name="linking-in-templates"></a>Ligação nos modelos

Para definir uma ligação num modelo, incluir um tipo de recurso que combina o espaço de nomes do fornecedor de recursos e tipo de recurso com **/providers/links**origem. O nome deve incluir o nome do recurso origem. Forneça o id do recurso do recurso de destino. O exemplo seguinte estabelece uma ligação entre um web site e uma conta de armazenamento.

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


Para obter uma descrição completa do formato de modelo, consulte o artigo [ligações recurso - esquema do modelo](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Ligar-se com a REST API

Para definir uma ligação entre os recursos implementados, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Substitua {id da subscrição} com o seu id da subscrição. Substituir {-grupo de recursos}, {espaço de nomes de fornecedor, {-tipo de recurso} e {-nome do recurso} com os valores que identificam o primeiro recurso na ligação. Substitua o nome da ligação para criar {nome de ligação}. Utilizar 2015-01-01 para a versão da api.

No pedido de incluir um objeto que define o segundo recurso na ligação:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

O elemento de propriedades contém o identificador para o recurso segundo.

Pode consultar ligações na sua subscrição com:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Para obter mais exemplos, incluindo como obter informações sobre ligações, consulte o artigo [Recursos ligadas](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Próximos passos

- Também pode organizar os recursos com etiquetas. Para saber mais sobre recursos etiquetas, consulte o artigo [utilizar etiquetas para organizar os seus recursos](resource-group-using-tags.md).
- Para obter uma descrição de como criar modelos e definir os recursos para ser implementada, consulte o artigo [modelos de criação](resource-group-authoring-templates.md).
