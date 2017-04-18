<properties
 pageTitle="Ler mensagens de persiste no armazenamento do Windows Azure | Microsoft Azure"
 description="Monitorize as mensagens de dispositivo para nuvem, tal como são escritas ao seu armazenamento de tabela do Azure."
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

# <a name="33-read-messages-persisted-in-azure-storage"></a>3.3 mensagens lidas persiste no armazenamento do Windows Azure

## <a name="331-what-will-you-do"></a>3.3.1 o que vai fazer

Monitorize as mensagens de nuvem de dispositivo que são enviadas a partir do seu framboesa Pi 3 para o seu centro IoT como as mensagens são escritas ao seu armazenamento de tabela do Azure. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="332-what-will-you-learn"></a>3.3.2 o que vai aprender

- Como utilizar a tarefa de mensagem lida gulp para ler mensagens persistentes no seu armazenamento de tabela do Azure.

## <a name="333-what-do-you-need"></a>3.3.3 o que precisa

- Tem ter concluído com êxito da secção anterior, [execute a aplicação de exemplo intermitência Azure no seu framboesa Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

## <a name="334-read-new-messages-from-your-storage-account"></a>3.3.4 ler novas mensagens da sua conta de armazenamento

Na secção anterior, executou uma aplicação de amostra no seu Pi. As mensagens de aplicação enviada de exemplo para o seu centro Azure IoT. As mensagens enviadas para o seu centro IoT são armazenadas no seu armazenamento de tabela do Azure através da aplicação de função Azure. Tem a cadeia de ligação de armazenamento do Windows Azure para ler mensagens a partir do seu armazenamento de tabela do Azure.

Para ler mensagens armazenadas no seu armazenamento de tabela do Azure, siga estes passos:

1. Obter a cadeia de ligação ao executar os seguintes comandos:

    ```bash
    az storage account list -g iot-sample --query [].name
    az storage account show-connection-string -g iot-sample -n {storage name}
    ```

    O comando primeira obtém o `storage name` que é utilizado no segundo comando para obter a cadeia de ligação. `iot-sample`é o valor predefinido de `{resource group name}` se não alterou o valor na lição 2.

2. Abra o ficheiro de configuração `config-raspberrypi.json` ficheiro no código do Visual Studio, executando o seguinte comando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Substituir `[Azure Storage connection string]` com a cadeia de ligação que tem no passo 1.
4. Guardar a `config-raspberrypi.json` ficheiro.
5. Enviar mensagens novamente e lê-los a partir do seu armazenamento de tabela do Azure executando o seguinte comando:

    ```bash
    gulp run --read-storage
    ```

    A lógica da leitura a partir do armazenamento de tabela do Azure está a `azure-table.js` ficheiro.

    ![gulp executar-- armazenamento de leitura](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="335-summary"></a>3.3.5 resumo

Tenha ligado a sua Pi a sua concentrador de IoT na nuvem e utilizado a aplicação de exemplo intermitência para enviar mensagens de dispositivo para nuvem com êxito. Também a aplicação de função Azure que utilizou para armazenar as mensagens a receber IoT concentrador ao seu armazenamento de tabela do Azure. Pode mover para a próxima lição sobre como enviar mensagens de dispositivo de nuvem a partir do seu centro IoT seu Pi.

## <a name="next-steps"></a>Próximos passos

[Lição 4: Enviar mensagens de dispositivo de nuvem](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)
