> [AZURE.SELECTOR]
- [NODE.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>Introdução

Gémeos de dispositivo os documentos JSON que armazenam informações de estado do dispositivo (meta dados, configurações e condições). Concentrador IoT persiste um duplos dispositivo para cada dispositivo que se liga ao IoT concentrador.

Utilize gémeos dispositivo para:

* Armazenar dados meta dispositivo a partir do seu back-end.
* Comunicar informações de estado atual como funcionalidades disponíveis e condições (por exemplo, o método de conectividade utilizado) da sua aplicação do dispositivo.
* Sincronize o estado dos fluxos de trabalho de execução longa (como atualizações firmware e configuração) entre a aplicação de dispositivo e back-end.
* Metadados do dispositivo, a configuração ou o estado da consulta.

> [AZURE.NOTE] Gémeos dispositivo foram concebidos para sincronização e para consultar as configurações de dispositivo e condições. Utilize [as mensagens de dispositivo para nuvem] [ lnk-d2c] para sequências de eventos de tem a hora marcada (como sequências de telemetria de dados de sensor baseados no tempo) e [métodos de dispositivo de nuvem] [ lnk-methods] para controlo interativo dispositivos, tais como ativar uma ventoinha através de uma aplicação controlado pelo utilizador.

Gémeos dispositivo são armazenados num concentrador de IoT e contêm:

* *etiquetas*, dispositivo metadados apenas acessíveis pelo back-end;
* *propriedades pretendidas*, objetos JSON pode ser modificados pelo back-end e observáveis pela aplicação do dispositivo; e
* *Propriedades denunciadas*, objetos JSON pode ser modificados pela aplicação do dispositivo e legíveis por trás de fim. Propriedades de etiquetas e não podem conter matrizes, mas podem ser aninhados objetos.

![][img-twin]

Para além disso, a aplicação back-end pode consultar gémeos dispositivo com base em todos os dados acima.
Referir-se a [entender gémeos de dispositivo] [ lnk-twins] para obter mais informações sobre gémeos de dispositivo e para o [idioma de consulta IoT concentrador] [ lnk-query] referência para consultar.

> [AZURE.NOTE] Neste momento, são acessíveis apenas a partir de dispositivos que se ligam a concentrador IoT utilizando o protocolo MQTT gémeos dispositivo. Consulte para [suportar MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter a aplicação de dispositivo existente utilizar MQTT.

Este tutorial mostra-lhe como para:

- Crie uma aplicação de back-end que adiciona *etiquetas* para duplos um dispositivo e um dispositivo simulado que relatórios do seu canal de conectividade como um *reportados propriedade* sobre duplos o dispositivo.
- Consulta dispositivos da sua aplicação de back-end através dos filtros em etiquetas e propriedades criadas anteriormente.


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md