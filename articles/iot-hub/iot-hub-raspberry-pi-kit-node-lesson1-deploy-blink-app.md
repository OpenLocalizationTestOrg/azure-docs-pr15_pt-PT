<properties
 pageTitle="Criar e implementar a aplicação de intermitência | Microsoft Azure"
 description="Clonar a aplicação de Node.js exemplo a partir do Github e gulp para implementar esta aplicação para o seu quadro framboesa Pi 3. Esta aplicação exemplo intermitência LED ligado ao quadro em dois segundos."
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

# <a name="13-create-and-deploy-the-blink-application"></a>1.3 criar e implementar a aplicação de intermitência

## <a name="131-what-you-will-do"></a>1.3.1 o que vai fazer

Clonar a aplicação de Node.js exemplo a partir do Github e utilize a ferramenta de gulp para implementar a aplicação de exemplo para o seu 3 de Pi framboesa. A aplicação de exemplo intermitência LED ligado ao quadro em dois segundos. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="132-what-you-will-learn"></a>1.3.2 o que vai aprender

- Como utilizar o `device-discover-cli` ferramenta para obter informações de funcionamento em rede acerca do seu Pi.
- Como implementar e executar a aplicação de exemplo no seu Pi.
- Como implementar e depurar aplicações em execução remotamente no seu Pi.

## <a name="133-what-you-need"></a>1.3.3 o que precisa

Tem ter concluído com êxito as secções de seguir na Lição 1:

- [Configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
- [Obter as ferramentas de](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="134-obtain-the-ip-address-and-host-name-of-your-pi"></a>1.3.4 obter o nome de anfitrião e endereço IP do seu Pi

Abra uma linha de comandos no Windows ou uma janela de terminal no macOS ou Ubuntu e, em seguida, execute o seguinte comando:

```bash
devdisco list --eth
```

Deverá visualizar um resultado semelhante ao seguinte:

![Deteção de dispositivo](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Tome nota da `IP address` e `hostname` da sua Pi. Tem mais tarde neste informações nesta secção.

> [AZURE.NOTE] Certifique-se de que o seu Pi está ligado à mesma rede que o seu computador. Por exemplo, se o seu computador estiver ligado a uma rede sem fios, enquanto o seu Pi estiver ligado a uma rede com fios, não poderá ver o endereço IP na saída devdisco.

## <a name="135-clone-the-sample-application"></a>1.3.5 clonar a aplicação de exemplo

Para abrir o código de exemplo, siga estes passos:

1. Clonar do repositório de exemplo a partir do Github, executando o seguinte comando:

    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
    ```

2. Abra a aplicação de exemplo no código do Visual Studio, executando os seguintes comandos:

    ```bash
    cd iot-hub-node-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![Estrutura de repo](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

O `app.js` de ficheiros na `app` subpasta é o ficheiro de origem chave que contém o código para controlar o LED.

### <a name="136-install-application-dependencies"></a>1.3.6 instalar o dependências de aplicações

Instale as bibliotecas e outros módulos que necessita para a aplicação de exemplo, executando o seguinte comando:

```bash
npm install
```

## <a name="137-configure-the-device-connection"></a>1.3.7 configurar a ligação do dispositivo

Para configurar a ligação do dispositivo, siga estes passos:

1. O comando gere o ficheiro de configuração de dispositivo, executando o seguinte comando:

    ```bash
    gulp init
    ```

    O ficheiro de configuração `config-raspberrypi.json` contém as credenciais do utilizador que utilizar para iniciar sessão no seu Pi. Para evitar a perda de credenciais de utilizador, o ficheiro de configuração é gerado na subpasta `.iot-hub-getting-started` da pasta principal no seu computador.

2. Abra o ficheiro de configuração de dispositivos no código do Visual Studio, executando o seguinte comando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Substituir o marcador de posição `[device hostname or IP address]` com o endereço IP ou o nome do anfitrião que obtém na secção 1.3.4.

    ![Config.JSON](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

Parabéns! Criou com êxito a primeira aplicação de exemplo para o seu Pi.

## <a name="138-deploy-and-run-the-sample-application"></a>1.3.8 implementar e executar a aplicação de exemplo

### <a name="1381-install-nodejs-and-npm-on-your-pi"></a>1.3.8.1 instalar Node.js e NPM no seu Pi

Instale Node.js e NPM no seu Pi, executando o seguinte comando:

```bash
gulp install-tools
```

Pode demorar dez minutos a concluir a primeira vez executar esta tarefa.

### <a name="1382-deploy-and-run-the-sample-app"></a>1.3.8.2 implementar e executar a aplicação de exemplo

Implementar e executar a aplicação de exemplo executando o seguinte comando:

```bash
gulp deploy && gulp run
```

### <a name="1383-verify-the-app-works"></a>1.3.8.3 Certifique-se de funciona a aplicação

Agora deverá ver o LED no seu Pi intermitência em dois segundos.  Se não vir o LED intermitente, consulte o [Guia de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) de soluções para problemas comuns.
![LED intermitência](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

> [AZURE.NOTE] Utilizar `Ctrl + C` para terminar a aplicação.

## <a name="139-summary"></a>1.3.9 resumo

Que tiver instalado as ferramentas necessárias para trabalhar com o seu Pi e implementado uma aplicação de exemplo para o Pi para intermitência o LED. Agora pode mover para a próxima aula para criar, implementar e executar outra aplicação de exemplo que se liga a sua Pi a Azure IoT concentrador para enviar e receber mensagens.

## <a name="next-steps"></a>Próximos passos

Está agora pronto para começar a lição 2 que começa com [obter as ferramentas de Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
