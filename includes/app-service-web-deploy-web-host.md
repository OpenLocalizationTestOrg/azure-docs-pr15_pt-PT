### <a name="app-service-plan"></a>Plano de serviço de aplicação

Cria o plano de serviço de alojamento do web app. Fornecer o nome do plano através do parâmetro **hostingPlanName** . A localização do plano é a mesma localização utilizada para o grupo de recursos. O tamanho de camadas e trabalhador preços estão especificados nos parâmetros **sku** e **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

