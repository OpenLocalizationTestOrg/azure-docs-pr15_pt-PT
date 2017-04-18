<properties
    pageTitle="Utilizar etiquetas para organizar os recursos do Azure | Microsoft Azure"
    description="Mostra como aplicar etiquetas para organizar os recursos para faturação e gerir."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>Utilizar etiquetas para organizar os recursos do Azure

Gestor de recursos permite-lhe organizar logicamente recursos aplicando etiquetas. As etiquetas consistem pares valor/chave que identifiquem recursos com as propriedades que pode definir. Para marcar recursos como que pertencem à mesma categoria, aplica a mesma etiqueta aos recursos.

Quando visualiza recursos com uma etiqueta específico, consulte recursos a partir de todos os grupos de recursos. Não estão limitados aos apenas os recursos no mesmo grupo de recursos, que lhe permite organizar os recursos de uma forma que seja independente das relações de implementação. Etiquetas podem ser útil quando precisar de recursos de faturação ou gestão de organizar.

Cada etiqueta que adicionar a um recurso ou grupo de recursos é automaticamente adicionada às taxonomia toda a subscrição. Também pode preencher taxonomia para a sua subscrição com nomes de etiqueta e os valores que pretende utilizar como recursos são marcados no futuro.

Cada recurso ou grupo de recursos pode ter um máximo de 15 marcas. O nome da etiqueta está limitado à 512 carateres e o valor de etiqueta é limitado a 256 carateres.

> [AZURE.NOTE] Só pode aplicar etiquetas para recursos que suportam operações de Gestor de recursos. Se tiver criado uma Máquina Virtual, rede Virtual ou armazenamento através do modelo de implementação clássico (tais como através do portal do clássico), não é possível aplicar uma etiqueta para esse recurso. Para suportar etiquetagem, implementar estes recursos através do Gestor de recursos. Todos os outros recursos de suporte de marcação.

## <a name="templates"></a>Modelos

Para marcar um recurso durante a implementação, basta adicionar elemento de **etiquetas** para o recurso que está a implementar e fornecer o nome de etiqueta e um valor. O nome de etiqueta e o valor não necessita previamente existentes na sua subscrição. Pode fornecer até 15 etiquetas para cada recurso.

O exemplo seguinte mostra uma conta de armazenamento com uma etiqueta.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Atualmente, o Gestor de recursos não suporta um objeto para os nomes de etiqueta e valores de processamento. Em vez disso, passar um objeto para os valores de etiqueta, mas ainda tem de especificar os nomes de etiqueta, conforme mostrado no seguinte exemplo.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portal

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Clip Azure

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API

O portal e PowerShell ambos utilizam o [Gestor de recursos REST API](https://msdn.microsoft.com/library/azure/dn848368.aspx) nos bastidores. Se precisar de integrar etiquetagem num ambiente outra, pode obter etiquetas com um GET no id do recurso e actualizar o conjunto de etiquetas com uma chamada de PATCHES.


## <a name="tags-and-billing"></a>Etiquetas e faturação

Para os serviços suportados, pode utilizar etiquetas para agrupar os seus dados de faturaçãohttps. Por exemplo, [máquinas virtuais integrado com o Gestor de recursos do Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) permitem-lhe definir e aplique etiquetas para utilização de faturação para máquinas virtuais de organizar. Se estiver a executar várias VMs para organizações diferentes, pode utilizar os códigos para a utilização do grupo ao centro de custos.  
Também pode utilizar etiquetas para categorizar os custos por runtime ambiente; tal como a utilização de faturação para VMs em execução no ambiente de produção.

Pode obter informações sobre etiquetas através da [utilização de recursos do Azure e RateCard APIs](billing-usage-rate-card-overview.md) ou o ficheiro de valores separados por vírgulas (CSV) de utilização. Transferir o ficheiro de utilização do [portal de contas Azure](https://account.windowsazure.com/) ou [EA portal](https://ea.azure.com). Para mais informações sobre o acesso de programação para as informações de faturação, consulte o artigo [obter informações para o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md). Para operações de REST API, consulte o artigo [Referência da Azure faturação REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando transfere CSV a utilização dos serviços que suportem etiquetas com a faturação, as etiquetas aparecem na coluna **etiquetas** . Para obter mais detalhes, consulte o artigo [entender a minha conta do Microsoft Azure](billing/billing-understand-your-bill.md).

![Consulte o artigo etiquetas no faturação](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Próximos passos

- Pode aplicar restrições e convenções ao longo da sua subscrição com políticas personalizadas. A política que pode definir poderia exigir que todos os recursos tenham um valor para uma etiqueta específico. Para mais informações, consulte o artigo [Utilizar a política para gerir os recursos e controlar o acesso](resource-manager-policy.md).
- Para obter uma introdução à utilização do Azure PowerShell quando implementar recursos, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](./powershell-azure-resource-manager.md).
- Para obter uma introdução à utilização do Azure clip quando implementar recursos, consulte o artigo [utilizar o clip do Azure para Mac, Linux e o Windows com o Azure a gestão de recursos](./xplat-cli-azure-resource-manager.md).
- Para obter uma introdução à utilização do portal do, consulte o artigo [utilizar o portal do Azure para gerir os recursos do Azure](./azure-portal/resource-group-portal.md)  
