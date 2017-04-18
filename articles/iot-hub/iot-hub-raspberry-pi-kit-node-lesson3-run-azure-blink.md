<properties
 pageTitle="Executar a aplicação de exemplo para enviar mensagens de dispositivo para nuvem | Microsoft Azure"
 description="Implementar e execute uma aplicação de exemplo para o seu 3 de Pi framboesa que envia mensagens a IoT concentrador e intermitência o LED."
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

# <a name="32-run-sample-application-to-send-device-to-cloud-messages"></a>3,2 executar a aplicação de exemplo para enviar mensagens de nuvem de dispositivo

## <a name="321-what-you-will-do"></a>3.2.1 o que vai fazer

Implementar e execute uma aplicação de amostra no seu framboesa Pi 3 que envia mensagens para o seu centro IoT. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="322-what-you-will-learn"></a>3.2.2 o que vai aprender

- Como utilizar a ferramenta de gulp para implementar e executar a aplicação de Node.js de amostra no seu Pi.

## <a name="323-what-you-need"></a>3.2.3 o que precisa

- Tem foi concluído com êxito da secção anterior nesta lição: [criar uma aplicação de função Azure e uma conta de armazenamento do Windows Azure para processar e armazenar IoT concentrador mensagens](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

## <a name="324-get-your-iot-hub-and-device-connection-strings"></a>3.2.4 obter os IoT concentrador e dispositivo as cadeias de ligação

A cadeia de ligação de dispositivo é utilizada para ligar a Pi ao seu centro IoT. Cadeia de ligação do centro do IoT é utilizado para ligar o seu centro IoT para a identidade do dispositivo que representa o Pi no centro do IoT.

- Obter a cadeia de ligação de concentrador IoT executando o seguinte comando Azure clip:

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}`é o nome que especificou no lição 2. Utilizar `iot-sample` como o valor de `{resource group name}` se não alterou o valor na lição 2.

- Obter a cadeia de ligação de dispositivo, executando o seguinte comando:

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}`leva-o até o mesmo valor que a que utilizou com o comando anterior. Utilizar `iot-sample` como o valor de `{resource group name}` e utilizar `myraspberrypi` como o valor de `{device id}` se não alterou o valor na lição 2.

## <a name="325-configure-the-device-connection"></a>3.2.5 configurar a ligação do dispositivo

1. Iniciar o ficheiro de configuração executando os seguintes comandos:

    ```bash
    npm install
    gulp init
    ```

2. Abra o ficheiro de configuração do dispositivo `config-raspberrypi.json` no Visual Studio código, ao executar o seguinte comando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

    ![Config.JSON](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)

3. Efetuar as seguintes substituições na `config-raspberrypi.json` ficheiro:

  - Substitua **[nome do anfitrião dispositivo ou o endereço IP]** o endereço IP do dispositivo ou o nome do anfitrião recebeu do `device-discovery-cli` ou com o valor herdadas configurado na Lição 1.
  - Substituir **[cadeia de ligação de dispositivo IoT]** com o `device connection string` que obtidos.
  - Substituir **[cadeia de ligação de concentrador IoT]** com o `iot hub connection string` que obtidos.

Atualizar a `config-raspberrypi.json` de ficheiro, de modo a que pode implementar a aplicação de exemplo do seu computador.

## <a name="326-deploy-and-run-the-sample-application"></a>3.2.6 implementar e executar a aplicação de exemplo

Implementar e executar a aplicação de exemplo no seu Pi, executando o seguinte comando:

```bash
gulp
```

> [AZURE.NOTE] A tarefa de gulp predefinido é executada `install-tools`, `deploy`, e `run` sequencialmente de tarefas. No [Lição 1](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md), executou estas tarefas um após o outro separadamente.

## <a name="327-verify-the-sample-application-works"></a>3.2.7 verificar os trabalhos de aplicação de exemplo

Deverá visualizar o LED que está ligado à sua Pi intermitência em dois segundos. Sempre que o LED intermitência, a aplicação de exemplo envia uma mensagem para o seu centro IoT e verifica a que a mensagem foi enviada com êxito para o seu centro IoT. Além disso, para cada da mensagem recebida pelo concentrador IoT, a mensagem é impressa na janela da consola. A aplicação de exemplo termina automaticamente depois de enviar mensagens de 20.

![](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="328-summary"></a>3.2.8 resumo

Tenha implementado e executar a nova aplicação de exemplo intermitência no seu Pi para enviar mensagens de nuvem de dispositivo para o seu centro IoT. Pode mover para a secção seguinte para monitorizar as suas mensagens, tal como são escritas para a conta de armazenamento do Windows Azure.

## <a name="next-steps"></a>Próximos passos

[3.3 mensagens lidas persiste no armazenamento do Windows Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)