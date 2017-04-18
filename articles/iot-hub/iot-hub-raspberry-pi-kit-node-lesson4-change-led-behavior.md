<properties
 pageTitle="Opcional secção - alterar ativar e desativar o comportamento do LED | Microsoft Azure"
 description="Personalize as mensagens para alterar o LED e desativar o comportamento."
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

# <a name="42-optional-section-change-the-on-and-off-behavior-of-the-led"></a>4.2 secção opcional: alterar ativar e desativar o comportamento do LED

## <a name="421-what-you-will-do"></a>4.2.1 o que vai fazer

Personalize as mensagens para alterar o LED e desativar o comportamento. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="422-what-you-will-learn"></a>4.2.2 o que vai aprender

Utilize funções Node.js adicionais para alterar o LED e desativar o comportamento.

## <a name="423-what-you-need"></a>4.2.3 o que precisa

Tem foi concluído com êxito [4.1 executar uma aplicação de amostra no seu Pi framboesa para receber nuvem para mensagens de dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="424-add-nodejs-functions"></a>4.2.4 adicionar Node.js funções

1. Abra a aplicação de exemplo no código do Visual Studio, executando os seguintes comandos:

    ```bash
    cd Lesson4
    code .
    ```

2. Abrir o `app.js` ficheiro e, em seguida, adicione as seguintes funções no final:

    ```javascript
    function turnOnLED() {
      wpi.digitalWrite(CONFIG_PIN, 1);
    }

    function turnOffLED() {
      wpi.digitalWrite(CONFIG_PIN, 0);
    }
    ```

    ![Atualizar app.js](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)

3. Adicionar as seguintes condições antes da predefinição um no bloco de parâmetro caso do `receiveMessageCallback` função:

    ```javascript
    case 'on':
      turnOnLED();
      break;
    case 'off':
      turnOffLED();
      break;
    ```

    Agora que configurou a aplicação de exemplo para responder a obter mais instruções através de mensagens. A instrução de "a fazer" ativa o LED e a instrução "desativar" desativa o LED.

4. Abra o ficheiro gulpfile.js e, em seguida, adicione uma nova função antes da função `sendMessage`:

    ```javascript
    var buildCustomMessage = function (messageId) {
      if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
        return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
      } else if (messageId < MAX_MESSAGE_COUNT) {
        return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
      } else {
        return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
      }
    }
    ```

    ![Atualizar gulpfile](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)

5. No `sendMessage` funcionar, substitua a linha `var message = buildMessage(sentMessageCount);` com a nova linha mostrada no seguinte fragmento:

    ```javascript
    var message = buildCustomMessage(sentMessageCount);
    ```

6. Guarde todas as alterações.

### <a name="425-deploy-and-run-the-sample-application"></a>4.2.5 implementar e executar a aplicação de exemplo

Implementar e executar a aplicação de exemplo no seu Pi, executando o seguinte comando:

```bash
gulp
```

Deverá visualizar o LED ativada para dois segundos e, em seguida, desativada para outro dois segundos. A última mensagem "deixar de" impede a aplicação de exemplo a execução.

![activar e desactivar](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Parabéns! Personalizou com êxito as mensagens que são enviadas para o Pi a partir do seu centro IoT.

### <a name="427-summary"></a>4.2.7 resumo

Esta secção opcional demonstrações como personalizar as mensagens para que a aplicação de exemplo, pode controlar ativar e desativar o comportamento do LED de forma diferente.

