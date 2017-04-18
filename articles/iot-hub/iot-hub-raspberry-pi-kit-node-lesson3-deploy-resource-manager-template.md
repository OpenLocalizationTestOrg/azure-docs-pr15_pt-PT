<properties
 pageTitle="Criar uma aplicação de função Azure e a conta de armazenamento do Windows Azure | Microsoft Azure"
 description="A aplicação de função Azure recebe a eventos de concentrador Azure IoT, processa as mensagens a receber e escreve-los ao armazenamento de tabela do Azure."
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

# <a name="31-create-an-azure-function-app-and-azure-storage-account"></a>3.1 criar uma aplicação de função Azure e a conta de armazenamento do Windows Azure

[Funções de Azure](../../articles/azure-functions/functions-overview.md) é uma solução para executar facilmente pequenas partes de código, denominadas "funções", na nuvem. Uma aplicação de função Azure aloja a execução do seu funções no Azure.

## <a name="311-what-will-you-do"></a>3.1.1 o que vai fazer

Utilize um modelo de Gestor de recursos do Azure para criar uma aplicação de função Azure e uma conta de armazenamento do Windows Azure. A aplicação de função Azure recebe a eventos de concentrador Azure IoT, processa as mensagens a receber e escreve-los ao armazenamento de tabela do Azure. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="312-what-will-you-learn"></a>3.1.2 o que vai aprender

- Como utilizar o [Gestor de recursos do Azure](../../articles/azure-resource-manager/resource-group-overview.md) para implementar o Azure recursos.
- Como utilizar uma aplicação de função Azure para processar IoT concentrador mensagens e escrevê-los a uma tabela no armazenamento de tabela do Azure.

## <a name="313-what-do-you-need"></a>3.1.3 o que precisa

- Tem foi concluído com êxito lições anteriores: [Introdução ao seu framboesa Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md) e [criar o seu centro Azure IoT](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="314-open-the-sample-app"></a>3.1.4 abra a aplicação de exemplo

Abra o projeto de exemplo no código do Visual Studio, executando os seguintes comandos:

```bash
cd Lesson3
code .
```

![Estrutura de repo](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

- O `app.js` de ficheiros na `app` subpasta é o ficheiro de origem chave. Este ficheiro de origem contém o código para enviar uma mensagem de 20 horas para o seu centro IoT e intermitência LED para cada mensagem que envia.
- O `arm-template.json` ficheiro é o modelo do Gestor de recursos do Azure que contém uma aplicação de função Azure e uma conta de armazenamento do Windows Azure.
- O `arm-template-param.json` ficheiro é o ficheiro de configuração utilizado pelo modelo Azure o Gestor de recursos.
- O `ReceiveDeviceMessages` subpasta contém o código de Node.js para a função Azure.

## <a name="315-configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>3.1.5 configurar modelos de Gestor de recursos Azure e criar recursos no Azure

Atualizar a `arm-template-param.json` ficheiro no Visual Studio código.

![Azure parâmetros de modelo de Gestor de recursos](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

- Substitua **[o seu nome de IoT concentrador]** **{o meu nome de concentrador}** que especificou na [Lição 2](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
- Substitua **[prefixo cadeia para novos recursos]** qualquer prefixo que pretende. O prefixo assegura que o nome do recurso é globalmente único para evitar conflitos. Não utilize traço ou o número inicial no prefixo.

> [AZURE.NOTE] Não é necessário `azure_storage_connection_string` nesta secção. Mantê-la como está.

Depois de atualizar o `arm-template-param.json` de ficheiros, implementar os recursos para Azure executando o seguinte comando:

```bash
az resource group deployment create --template-file-path arm-template.json --parameters-file-path arm-template-param.json -g iot-sample -n mydeployment
```

Demora cerca de cinco minutos para criar estes recursos. Enquanto a criação de recursos está em curso, pode mover para a secção seguinte.

## <a name="316-summary"></a>3.1.6 resumo

Que criou a sua aplicação de função Azure para processar mensagens do Centro de IoT e uma conta de armazenamento do Windows Azure para armazenar estas mensagens. Pode mover para a secção seguinte para implementar e executar o exemplo para enviar mensagens de dispositivo para nuvem no seu Pi.

## <a name="next-steps"></a>Próximos passos

[3,2 executar a aplicação de exemplo para enviar mensagens de dispositivo para nuvem no seu framboesa Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

