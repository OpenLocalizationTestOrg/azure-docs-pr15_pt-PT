<properties
 pageTitle="Criar o seu centro IoT e registar a sua framboesa Pi 3 | Microsoft Azure"
 description="Criar um grupo de recursos, criar um concentrador de Azure IoT e registar a sua Pi no centro do Azure IoT utilizando o clip do Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 criar o seu centro IoT e registar a sua framboesa Pi 3

## <a name="221-what-you-will-do"></a>2.2.1 o que vai fazer

- Crie um grupo de recursos.
- Crie o seu centro Azure IoT no grupo de recursos.
- Adicione o seu 3 de Pi framboesa ao concentrador Azure IoT utilizando o clip do Azure.

Quando utiliza o clip do Azure para adicionar o seu Pi ao seu centro IoT, o serviço gera uma chave para sua Pi autenticar com o serviço. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="222-what-you-will-learn"></a>2.2.2 o que vai aprender

- Como utilizar o clip do Azure para criar um concentrador de IoT Azure.
- Como criar uma identidade de dispositivo para o seu Pi no seu centro de IoT Azure.

## <a name="223-what-you-need"></a>2.2.3 o que precisa

- Uma conta do Azure
- Num Mac ou computador Windows com o clip de Azure instalado

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 criar o seu centro Azure IoT

Azure IoT concentrador ajudam-na ligar, monitorizar e gerir milhões de activos IoT. Para criar o seu centro Azure IoT, siga estes passos:

1. Inicie sessão na sua conta Azure executando o seguinte comando:

    ```bash
    az login
    ```

    Todas as subscrições Azure disponíveis são listadas após um início de sessão efetuada com êxito.

2. Predefinir o Azure subscrição que pretende utilizar, executando o seguinte comando:

    ```bash
    az account set -n {subscription id or name}
    ```

    O ID da subscrição ou o nome pode ser encontrado na saída do `az login`.

3. Registe o fornecedor executando o seguinte comando:

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    Tem de registar o fornecedor antes de poder implementar o recurso Azure que fornece o fornecedor.

    > [AZURE.NOTE] Maioria dos fornecedores são registadas automaticamente ao portal do Azure ou o clip de Azure estiver a utilizar, mas não todos. Para mais informações sobre o fornecedor, consulte o artigo [resolver problemas de erros comuns de implementação do Azure com o Gestor de recursos do Azure](../resource-manager-common-deployment-errors.md)

4. Crie um grupo de recursos com o nome do iot amostras na região EUA Ocidental executando o seguinte comando:

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. Crie um concentrador de IoT no grupo de recursos iot amostras, executando o seguinte comando:

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    A edição predefinido do concentrador IoT que criar é **F1**, que é **gratuito**. Para mais informações, consulte o artigo [Azure IoT concentrador preços](https://azure.microsoft.com/pricing/details/iot-hub/).

> [AZURE.NOTE] O nome do seu centro IoT tem de ser exclusivo global.
>
> Pode criar apenas uma edição de **F1** do Azure IoT concentrador com a sua subscrição Azure.

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 registar a sua Pi no seu centro IoT

Cada dispositivo que envia/recebe mensagens de para a sua concentrador Azure IoT tem de estar registado com um ID exclusivo.

Registe o Pi no seu centro Azure IoT por em execução seguinte comando:

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 resumo

Tenha criado um concentrador de Azure IoT e registada seu Pi com uma identidade de dispositivo no seu centro Azure IoT. Está pronto para avançar para a próxima lição para aprender a enviar mensagens a partir do seu Pi para o seu centro IoT.

## <a name="next-steps"></a>Próximos passos

Agora está pronto para iniciar lição 3 que começa com [criar uma aplicação de função Azure e uma conta de armazenamento do Windows Azure para processar e armazenar IoT concentrador mensagens](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).