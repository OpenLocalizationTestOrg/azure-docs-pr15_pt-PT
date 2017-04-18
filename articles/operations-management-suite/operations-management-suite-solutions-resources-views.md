<properties
   pageTitle="Vistas no soluções de gestão do conjunto de aplicações de gestão de operações (OMS) | Microsoft Azure"
   description="Soluções de gestão no conjunto de aplicações de gestão de operações (OMS) normalmente irão incluir um ou mais vistas para visualização desses dados.  Este artigo descreve como exportar uma vista criada pelo estruturador de vista e inclua-o numa solução de gestão. "
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Vistas no soluções de gestão do conjunto de aplicações de gestão de operações (OMS) (pré-visualização)

>[AZURE.NOTE]Este é prévia documentação para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeito a alterações.    

[Soluções de gestão no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-solutions.md) normalmente irá incluir um ou mais vistas para visualização desses dados.  Este artigo descreve como exportar uma vista criada pelo [Estruturador de vista](../log-analytics/log-analytics-view-designer.md) e inclua-o numa solução de gestão.  

>[AZURE.NOTE]Os exemplos neste artigo utilizam parâmetros e variáveis que são necessários ou comuns para as soluções de gestão e descrito na [soluções de gestão de criar no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume já está familiarizado a como para [criar uma solução de gestão](operations-management-suite-solutions-creating.md) e a estrutura de um ficheiro de solução.


## <a name="overview"></a>Descrição geral

Para incluir uma vista numa solução de gestão, crie um **recurso** para a mesma no [ficheiro de solução](operations-management-suite-solutions-creating.md).  O JSON que descreve configuração detalhadas a vista é normalmente complexa apesar e não algo que um autor de solução típica seria conseguir criar manualmente.  É o método mais comum criar a vista utilizando o [Estruturador de vista](../log-analytics/log-analytics-view-designer.md), exportá-lo e, em seguida, adicionar a sua configuração detalhada a solução. 

Os passos básicos para adicionar uma vista numa solução são da seguinte forma.  Cada passo é descrito detalhadamente mais nas secções abaixo.

1. Exporte a vista para um ficheiro.
2. Crie o recurso de vista da solução.
3. Adicione os detalhes de vista.

## <a name="export-the-view-to-a-file"></a>Exportar a vista para um ficheiro
Siga as instruções no [Estruturador de vista de análise de registo](../log-analytics/log-analytics-view-designer.md) para exportar uma vista para um ficheiro.  O ficheiro exportado irá estar no formato JSON com os mesmos [elementos como o ficheiro da solução](operations-management-suite-solutions-creating.md#management-solution-files).  

O elemento de **recursos** do ficheiro vista terá um recurso, com um tipo de **Microsoft.OperationalInsights/workspaces** que representa a área de trabalho OMS.  Este elemento terá um subelemento com um tipo de **vistas** que representa a vista e que contém a sua configuração detalhada.  Será copiar os detalhes deste elemento e, em seguida, copie-o para a solução.


## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de vista da solução
Adicione os seguintes recursos de vista para o elemento de **recursos** do seu ficheiro de solução.  Esta opção utiliza variáveis que são descritas abaixo que também tem de adicionar.  Tenha em atenção que as propriedades do **Dashboard** e **OverviewTile** são marcadores de posição que irá substituir com as propriedades do ficheiro exportado vista correspondentes.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Adicione as seguintes variáveis para o elemento de [variáveis](operations-management-suite-solutions-creating.md#variables) do ficheiro solução e substitua os valores para a sua solução.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Tenha em atenção que pode copiar o recurso de vista completa do seu ficheiro exportado vista, mas que precisa para efetuar as seguintes alterações para ela funcionar na sua solução.  

- O **tipo de** para o recurso de vista tem de ser alterado de **vistas** para **Microsoft.OperationalInsights/workspaces**.
- A propriedade **nome** para o recurso de vista tem de ser alterado para incluir o nome da área de trabalho.
- Dependência na área de trabalho tem de ser removido de uma vez que o recurso de área de trabalho não é definido da solução.
- Propriedade **DisplayName** tem de ser adicionada à vista.  O **Id**, **nome**e **DisplayName** tem todas corresponder às.
- Os nomes dos parâmetros devem ser alterados para fazer corresponder o conjunto de parâmetros necessário.
- Variáveis de devem ser definidas na solução e utilizadas nas propriedades adequadas.

## <a name="add-the-view-details"></a>Adicione os detalhes de vista
O recurso de vista no ficheiro de vista exportado irá conter duas elementos no elemento de **Propriedades** com o nome **Dashboard** e **OverviewTile** que contêm a configuração detalhada da vista.  Copie estes dois elementos e os seus conteúdos para o elemento de **Propriedades** do recurso vista no seu ficheiro de solução. 

## <a name="example"></a>Exemplo
Por exemplo, o exemplo seguinte mostra um ficheiro de solução simples com uma vista.  Nas reticências (…) são apresentadas para o conteúdo de **Dashboard** e **OverviewTile** por razões de espaço.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Próximos passos

- Saiba mais detalhes completos de criação de [soluções de gestão](operations-management-suite-solutions-creating.md).
- Inclua [Automatização runbooks na sua solução de gestão](operations-management-suite-solutions-resources-automation.md).