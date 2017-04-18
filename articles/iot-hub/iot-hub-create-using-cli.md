<properties
    pageTitle="Criar um concentrador de IoT utilizando Azure clip | Microsoft Azure"
    description="Siga este artigo para criar um concentrador de IoT utilizando a Interface de linha de comandos do Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Criar um concentrador de IoT utilizando o clip do Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar a Interface de comandos do Azure para criar e gerir Azure IoT concentradores através de programação. Este artigo mostra-lhe como utilizar o clip do Azure para criar um concentrador de IoT.

Para concluir este tutorial precisa do seguinte:

- Uma conta do Azure active. Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.
- [Clip Azure 0.10.4] [ lnk-CLI-install] ou posterior. Se já tiver Azure clip pode validar a versão atual na linha de comandos com o seguinte comando:
```
    azure --version
```

> [AZURE.NOTE] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos do Azure e clássica](../resource-manager-deployment-model.md). O clip do Azure tem de estar no modo de Gestor de recursos do Azure:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Configurar a sua conta Azure e subscrição 

1. No início de sessão de linha de comandos ao escrever o seguinte comando
```
    azure login
```
Utilize o browser web sugerido e o código para autenticar.

2. Se tiver múltiplas subscrições Azure, a estabelecer ligação ao Azure irão conceder acesso a todas as subscrições Azure associados com as suas credenciais. Pode ver as subscrições Azure, bem como aquele é a predefinição, utilizando o comando
```
    azure account list 
```

Para definir o contexto de subscrição, sob o qual pretende executar o resto da utilização de comandos

```
    azure account set <subscription name>
```

3. Se não tiver um grupo de recursos pode criar um com nome **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] O artigo [utilizar o clip do Azure para gerir grupos de recursos e recursos Azure] [ lnk-CLI-arm] fornece mais informações sobre como utilizar o clip do Azure para gerir os recursos Azure. 


## <a name="create-an-iot-hub"></a>Criar um concentrador de IoT

Parâmetros necessários:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Para ver todos os parâmetros disponíveis para a criação de pode utilizar o comando de ajuda na linha de comandos
```
    azure iothub create -h 
```
Exemplo rápido:

 Para criar um concentrador de IoT designado **exampleIoTHubName** na de grupo de recursos **exampleResourceGroup** simplesmente execute o seguinte comando
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Este comando do Azure clip cria um concentrador de IoT padrão S1 para a qual são faturada. Pode eliminar o concentrador IoT **exampleIoTHubName** utilizando a seguinte comando 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre como desenvolver IoT concentrador, consulte o seguinte:
- [Concentrador IoT SDK][lnk-sdks]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Utilizar o Portal do Azure para gerir IoT concentrador][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
