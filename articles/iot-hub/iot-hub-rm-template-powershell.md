<properties
    pageTitle="Criar um concentrador de IoT utilizando um modelo de Gestor de recursos do Azure e PowerShell | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Gestor de recursos do Azure modelos para criar um concentrador de IoT com PowerShell."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>Criar um concentrador de IoT através do PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar o Gestor de recursos do Azure para criar e gerir Azure IoT concentradores através de programação. Este tutorial mostra-lhe como utilizar um modelo de Gestor de recursos do Azure para criar um concentrador de IoT com PowerShell.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos do Azure e clássica](../resource-manager-deployment-model.md).  Este artigo aborda a utilizar o modelo de implementação do Azure o Gestor de recursos.

Para concluir este tutorial precisa do seguinte:

- Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

> [AZURE.TIP] O artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure] [ lnk-powershell-arm] fornece mais informações sobre como utilizar modelos de Gestor de recursos do Azure e de PowerShell scripts para criar Azure recursos. 

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Numa linha de comandos do PowerShell, introduza o seguinte comando para iniciar sessão na sua subscrição do Azure:

```
Login-AzureRmAccount
```

Pode utilizar os seguintes comandos para descobrir onde pode implementar um concentrador de IoT e as versões de API atualmente suportadas:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crie um grupo de recursos para conter o seu centro de IoT utilizando o seguinte comando de uma das localizações suportadas para IoT concentrador. Este exemplo cria um grupo de recursos denominado **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Submeter um modelo de Gestor de recursos do Azure para criar um concentrador de IoT

Utilize um modelo JSON para criar um concentrador de IoT no seu grupo de recursos. Também pode utilizar um modelo de Gestor de recursos do Azure para efetuar alterações a um concentrador de IoT existente.

1. Utilize um editor de texto para criar um modelo de Gestor de recursos do Azure denominado **template.json** com a definição de recursos seguintes para criar um novo concentrador de IoT padrão. Este exemplo adiciona o concentrador IoT na região **Leste dos EUA** , cria o ponto final de evento concentrador compatível com dois grupos do consumidor (**cg1** e **cg2**) e utiliza a versão **2016-02-03** API. Este modelo também espera passar no nome do concentrador de IoT como um parâmetro chamado **hubName**. Para a lista atual de localizações que suportam IoT concentrador ver o [Estado de Azure][lnk-status].

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Guarde o ficheiro de modelo de Gestor de recursos do Azure no seu computador local. Este exemplo assume que guardá-lo numa pasta denominada **c:\templates**.

3. Execute o seguinte comando para implementar o seu novo Centro IoT, prisma o nome do seu centro IoT como um parâmetro. Neste exemplo, o nome do concentrador IoT é **abcmyiothub** (Repare que este nome deve ser exclusiva globalmente para que deve incluir o nome ou as iniciais):

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. O resultado apresenta as teclas para o concentrador de IoT que criou.

5. Pode verificar que a aplicação adicionado o concentrador IoT novo ao visitar o [Azure portal] [ lnk-azure-portal] e ver a lista de recursos, ou ao utilizar o cmdlet do PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Esta aplicação exemplo adiciona um concentrador de IoT padrão S1 para a qual são faturada. Pode eliminar o concentrador IoT através do [Azure portal] [ lnk-azure-portal] ou ao utilizar o cmdlet do PowerShell **Remover AzureRmResource** quando tiver terminado.

## <a name="next-steps"></a>Próximos passos

Agora ter implementado um concentrador de IoT utilizando um modelo de Gestor de recursos do Azure com o PowerShell, poderá querer explorar ainda mais:

- Leia sobre as funcionalidades do [fornecedor de recursos IoT concentrador REST API][lnk-rest-api].
- Leia a [Descrição geral do Gestor de recursos do Azure] [ lnk-azure-rm-overview] para saber mais sobre as funcionalidades do Gestor de recursos do Azure.

Para saber mais sobre como desenvolver IoT concentrador, consulte o seguinte:

- [Introdução ao C SDK][lnk-c-sdk]
- [Concentrador IoT SDK][lnk-sdks]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
