<properties
    pageTitle="Alta densidade alojamento na aplicação de serviço de Azure | Microsoft Azure"
    description="Alta densidade alojamento na aplicação de serviço do Azure"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Alta densidade alojamento na aplicação de serviço do Azure

Ao utilizar a aplicação de serviço, a aplicação é desacoplada a partir da capacidade atribuída pelo dois conceitos:

- **a aplicação:** Representa a aplicação e a respectiva configuração runtime. Por exemplo, inclui a versão do .NET runtime deve ser carregado, as definições da aplicação, etc.

- **o plano de serviço de aplicação:** Define as características da capacidade, conjunto de funcionalidades disponíveis e Localidade da aplicação. Por exemplo, poderão ser características grande máquina de (quatro núcleos), quatro instâncias, funcionalidades Premium do Leste dos EUA.

Uma aplicação sempre está associada a um plano de serviço de aplicação, mas um plano de serviço de aplicação pode fornecer a capacidade de um ou mais aplicações.

Como resultado, a plataforma fornece a flexibilidade de isolar uma única aplicação ou ter várias aplicações partilhar recursos através da partilha de um plano de serviço de aplicação.

No entanto, quando várias aplicações partilham um plano de serviço de aplicação, uma instância nessa aplicação é executado em todas as instâncias desse plano de serviço de aplicação.

## <a name="per-app-scaling"></a>Por aplicação dimensionamento
*Por aplicação dimensionamento* é uma funcionalidade que pode ser ativada no nível de plano de serviço de aplicação e, em seguida, é utilizada por aplicação.

Por aplicação dimensionamento escalas uma aplicação separadamente a partir do plano de serviço de aplicação que aloja-lo. Desta forma, um plano de serviço de aplicação pode ser configurado para fornecer 10 instâncias, mas pode ser definida uma aplicação para dimensionar para apenas 5-las.

O modelo de Gestor de recursos do Azure seguinte cria um plano de serviço de aplicação que está dimensionado saída para 10 instâncias e uma aplicação que está configurada para utilizar por aplicação dimensionamento e dimensionar apenas 5 instâncias.

O plano de serviço de aplicação é a definição da propriedade **de sites dimensionamento** como VERDADEIRO ( `"perSiteScaling": true`). A aplicação está a definir o **número de trabalhadores** a utilizar a 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Configuração recomendada para alta densidade alojamento

Por aplicação dimensionamento é uma funcionalidade que está ativada em ambientes de serviço de aplicação e regiões Azure públicos. No entanto, a estratégia recomendada é utilizar ambientes de serviço de aplicação para tirar partido das suas funcionalidades avançadas e os conjuntos de maiores capacidade.  

Siga estes passos para configurar alta densidade de alojamento para as suas aplicações:

1. Configurar o ambiente do serviço de aplicação e selecione um conjunto de trabalho é dedicado o cenário de alojamento de alta densidade.

1. Crie um único plano de serviço de aplicação e dimensioná-lo para utilizar a capacidade disponível no agrupamento de trabalho.

1. Defina o sinalizador de dimensionamento por site como verdadeiro no plano de serviço de aplicação.

1. Novos sites são criados e atribuídos para esse plano de serviço de aplicação com a propriedade **numberOfWorkers** definida como **1**. Utilizando esta configuração origina a densidade mais alta possível neste conjunto de trabalho.

1. O número de trabalhadores pode ser configurado de forma independente por site para conceder recursos adicionais, conforme necessário. Por exemplo, um site de utilização de alta poderá definir **numberOfWorkers** a **3** para ter mais de processamento de capacidade para essa aplicação, enquanto baixa utilizar sites seriam definido **numberOfWorkers** como **1**.
