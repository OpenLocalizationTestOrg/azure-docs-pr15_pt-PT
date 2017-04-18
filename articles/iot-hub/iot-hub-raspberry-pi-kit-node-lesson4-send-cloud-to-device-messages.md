<properties
 pageTitle="Executar a aplicação de exemplo para receber mensagens de dispositivo de nuvem | Microsoft Azure"
 description="A aplicação de exemplo na Lição 4 é executada no seu Pi e monitoriza as mensagens recebidas a partir do seu centro IoT. Uma nova tarefa gulp envia mensagens para o Pi a partir do seu centro IoT para intermitência o LED."
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

# <a name="41-run-the-sample-application-to-receive-cloud-to-device-messages"></a>4.1 executar a aplicação de exemplo para receber mensagens de dispositivo de nuvem

Nesta secção, implementar uma aplicação de amostra no seu framboesa Pi 3. A aplicação de exemplo monitoriza mensagens a receber a partir do seu centro IoT. Também executar uma tarefa gulp no seu computador para enviar mensagens para o seu Pi a partir do seu centro IoT. Ao receber as mensagens, a aplicação de exemplo intermitência o LED. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="411-what-you-will-do"></a>4.1.1 o que vai fazer

- Ligar a aplicação de exemplo para o seu centro IoT.
- Implementar e executar a aplicação de exemplo.
- Envie mensagens a partir do seu centro IoT para o valor de Pi nos intermitência o LED.

## <a name="412-what-you-will-learn"></a>4.1.2 o que vai aprender

- Como monitorizar mensagens a receber a partir do seu centro IoT.
- Como enviar mensagens de dispositivo de nuvem a partir do seu centro IoT para sua Pi. 

## <a name="413-what-do-you-need"></a>4.1.3 o que precisa

- 3 de Pi de framboesa que esteja configurada para utilização. Para saber como configurar o seu Pi, consulte o artigo [Lição 1: começar a trabalhar com o seu dispositivo framboesa Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
- Um concentrador de IoT que é criado na sua subscrição do Azure. Para saber como criar o seu centro de IoT Azure, consulte o artigo [Lição 2: criar o seu centro de IoT Azure](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="414-connect-the-sample-application-to-your-iot-hub"></a>4.1.4 ligar-se a aplicação de exemplo para o seu centro IoT

1. Certifique-se de que está na pasta repo `iot-hub-node-raspberrypi-getting-started`. Abra a aplicação de exemplo no código do Visual Studio, executando os seguintes comandos:

    ```bash
    cd Lesson4
    code .
    ```

    Aviso de `app.js` de ficheiros na `app` subpasta. O `app.js` ficheiro é o ficheiro de origem chave que contém o código para monitorizar a partir do IoT Hub as mensagens a receber. O `blinkLED` função intermitência o LED.

    ![Estrutura de repo](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)

2. Iniciar o ficheiro de configuração com os seguintes comandos:

    ```bash
    npm install
    gulp init
    ```

    Se concluiu lição 3 neste computador, todas as configurações são herdadas para que pode avançar para o passo 4.1.5. Se tiver concluído lição 3 num computador diferente, tem de substituir os marcadores de posição na `config-raspberrypi.json` ficheiro. O `config-raspberrypi.json` ficheiro é na subpasta da sua pasta de raiz.

    ![Configuração](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

- Substitua **[nome do anfitrião dispositivo ou o endereço IP]** endereço IP ou nome do anfitrião que obtém ao executar o comando seu Pi`devdisco list --eth`
- Substitua **[cadeia de ligação de dispositivo IoT]** a cadeia de ligação do dispositivo que obtém ao executar o comando `az iot hub show-connection-string --name {my hub name} --resource-group {resource group name}`.
- Substitua **[cadeia de ligação de concentrador IoT]** IoT concentrador cadeia de ligação que obtém ao executar o comando `az iot device show-connection-string --hub {my hub name} --device-id {device id} --resource-group {resource group name}`.

## <a name="415-deploy-and-run-the-sample-application"></a>4.1.5 implementar e executar a aplicação de exemplo

Implementar e executar a aplicação de exemplo no seu Pi executando os seguintes comandos:
  
```
gulp
```

O comando gulp executa a tarefa de instalar ferramentas pela primeira vez. Em seguida, o ser implementada a aplicação de exemplo para o Pi. Por fim, é executado a aplicação no seu Pi e uma tarefa em separado no seu computador anfitrião para enviar 20 intermitência mensagens para o Pi a partir do seu centro IoT.

Assim que a aplicação de exemplo é executada, desde o início ouvir mensagens a partir do seu centro IoT. Entretanto, a tarefa de gulp envia várias mensagens "intermitência" a partir do seu centro IoT a sua Pi. Para cada da mensagem recebida intermitência, a aplicação de exemplo chama a função de blinkLED para intermitência o LED.

Deverá visualizar o LED intermitência em dois segundos, tal como a tarefa de gulp enviar mensagens de 20 a partir do seu centro IoT para sua Pi. No último registo é uma mensagem "deixar de" que indica a aplicação para parar de executar.

![Gulp](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="416-summary"></a>4.1.6 resumo

Que com êxito enviou mensagens a partir do seu centro IoT a sua Pi para intermitência o LED. Secção seguinte é secção opcional que mostra-lhe como alterar ativar e desativar o comportamento do LED.

## <a name="next-steps"></a>Próximos passos

[Secção opcional: alterar ativar e desativar o comportamento do LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)