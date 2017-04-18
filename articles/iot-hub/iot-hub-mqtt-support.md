<properties
 pageTitle="Suporte de IoT concentrador MQTT | Microsoft Azure"
 description="Descrição do MQTT de suporte no nível de concentrador IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>Suporte de IoT concentrador MQTT

Concentrador IoT permite que dispositivos comunicar com os pontos finais de dispositivo IoT concentrador utilizando o [MQTT v3.1.1] [ lnk-mqtt-org] protocolo no porta 8883 ou MQTT v3.1.1 através de protocolo WebSocket na porta 443. Concentrador IoT requer todas as comunicações de dispositivo para ser protegidos utilizando TLS/SSL (por conseguinte, IoT concentrador não suporta ligações não seguras através da porta 1883).

## <a name="connecting-to-iot-hub"></a>Ligar a IoT concentrador

Um dispositivo pode utilizar o protocolo MQTT para ligar a um concentrador de IoT utilizando as bibliotecas no [SDK do Microsoft Azure IoT] [ lnk-device-sdks] ou utilizando o MQTT protocolo diretamente.

## <a name="using-the-device-client-sdks"></a>Utilizar o cliente de dispositivo SDK

[Cliente do dispositivo SDK] [ lnk-device-sdks] que suporte o protocolo MQTT estão disponíveis para Java, Node.js, C, c# e Python. O cliente de dispositivo SDK utilizar a cadeia de ligação IoT concentrador padrão para estabelecer uma ligação a um concentrador de IoT. Para utilizar o protocolo MQTT, o parâmetro de protocolo de cliente tem de ser definido para **MQTT**. Por predefinição, o cliente de dispositivo SDK ligar a um concentrador de IoT com o **CleanSession** sinalizador definido como **0** e utilizar **QoS 1** para troca de mensagens com o concentrador IoT.

Quando um dispositivo estiver ligado a um concentrador de IoT, o cliente de dispositivo SDK fornecem métodos que permitem o dispositivo enviar mensagens para e receber mensagens a partir de um concentrador de IoT.

A tabela seguinte contém ligações para os exemplos de código para cada idioma suportado e especifica o parâmetro para utilizar para estabelecer uma ligação a concentrador IoT utilizando o protocolo MQTT.

| Idioma                   | Parâmetro de protocolo        |
| -------------------------- | ------------------------- |
| [NODE.js][lnk-sample-node] | Azure-iot dispositivo mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar uma aplicação do dispositivo do AMQP para MQTT
Se estiver a utilizar o [cliente de dispositivo SDK][lnk-device-sdks], mudar do utilizando AMQP para MQTT necessita de alterar o parâmetro de protocolo na inicialização cliente, tal como indicado acima.

Ao fazê-lo, certifique-se verificar se os seguintes itens:

* AMQP devolve erros para várias condições, enquanto MQTT termina a ligação. Como resultado seu exceção processamento lógica poderá implicar algumas alterações.
* MQTT não suporta operações de *Rejeitar* quando receber [mensagens de C2D][lnk-messaging]. Se o seu back-end tem de recebe uma resposta a partir da aplicação de dispositivo, considere utilizar [métodos diretos][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Utilizar o protocolo MQTT diretamente

Se um dispositivo não é possível utilizar o cliente de dispositivo SDK, ainda pode ligar para os pontos finais dispositivo público utilizando o protocolo MQTT. No pacote **Ligar** o dispositivo deve utilizar os seguintes valores:

- Para o campo **ClientId** , utilize o **deviceId**. 
- Para o campo **nome de utilizador** , utilize `{iothubhostname}/{device_id}`, onde {iothubhostname} é o CName completo do concentrador IoT.

    Por exemplo, se o nome do seu centro IoT é **contoso.azure devices.net** e se o nome do seu dispositivo é **MyDevice01**, o campo de **nome de utilizador** completo deve conter `contoso.azure-devices.net/MyDevice01`.

- Para o campo da **palavra-passe** , utilize um token de SA. O formato do token SA é o mesmo que para protocolos HTTP tanto AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Para obter mais informações sobre como gerar SA tokens, consulte a secção dispositivo utilizando o IoT concentrador de [tokens]de segurança[lnk-sas-tokens].
    
    Quando testar, também pode utilizar o [Dispositivo Explorer] [ lnk-device-explorer] ferramenta para gerar rapidamente um token de SA que pode copiar e colar o seu próprio código:
    
    1. Vá para o separador **Gestão** de no Explorador do dispositivo.
    2. Clique em **SA Token** (canto superior direito).
    3. No **SASTokenForm**, selecione o dispositivo no **DeviceID** lista pendente. Defina o **TTL**.
    4. Clique em **Gerar** para criar o token.
    
    O token de SA é gerado tem esta estrutura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    A parte deste token para utilizar como o campo da **palavra-passe** a ligar através do MQTT:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para MQTT ligar e desligar pacotes, IoT concentrador problemas um evento no canal de **Operações de monitorização** com informações adicionais que podem ajudá-lo a resolver problemas de conectividade.

### <a name="sending-messages-to-iot-hub"></a>Enviar mensagens a IoT concentrador

Depois de efetuar uma ligação com êxito, um dispositivo pode enviar mensagens para IoT concentrador utilizando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **Nome de tópico**. O `{property_bag}` elemento permite que o dispositivo enviar mensagens com propriedades adicionais num formato de url codificada. Por exemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Isto `{property_bag}` elemento utiliza a mesma codificação como cadeias de consulta no protocolo HTTP.

Também pode utilizar a aplicação de cliente do dispositivo `devices/{device_id}/messages/events/{property_bag}` como o **será o nome do tópico** para definir *será mensagens* reencaminhadas como uma mensagem de telemetria.

Concentrador IoT não suporta QoS 2 mensagens. Se um cliente de dispositivo publicar uma mensagem com **QoS 2**, IoT concentrador fecha a ligação de rede.
Concentrador IoT não persistirem reter mensagens. Se um dispositivo envia uma mensagem com o sinalizador de **RETER** definido como 1, IoT concentrador adiciona o **x-optar ativamente por participar-reter** propriedade da aplicação para a mensagem. Neste caso, em vez de persistência a mensagem de reter, IoT concentrador transmite-lo para a aplicação de back-end.

### <a name="receiving-messages"></a>Mensagens a receber

Para receber mensagens a partir do IoT Hub, um dispositivo deve subscrever utilizando `devices/{device_id}/messages/devicebound/#` como um **Filtro de tópico**. Carácter universal com múltiplos nível **#** no filtro tópico só é utilizado para permitir que o dispositivo receber propriedades adicionais no nome do tópico. Concentrador IoT não permite a utilização do **#** ou **?** carateres universais para filtrar de tópicos subpastas. Uma vez que IoT concentrador não for um corretor de mensagens de pub sub finalidade geral, apenas suporta os nomes de tópico documentado e filtros de tópico.

Fórum de nota, que o dispositivo não vai receber todas as mensagens a partir do IoT Hub, antes de subscreveu com êxito para o seu dispositivo específico ponto final do, representado pelo `devices/{device_id}/messages/devicebound/#` filtro de tópico. Após ter sido estabelecida subscrição com êxito, o dispositivo vai começar a receber apenas as mensagens na nuvem para o dispositivo que foram enviadas para o mesmo após a hora da subscrição. Se o dispositivo liga-se com o sinalizador de **CleanSession** definido como **0**, será mantida a subscrição através de sessões diferentes. Neste caso, da próxima vez que liga-se com **CleanSession 0** o dispositivo irá receber mensagens pendentes que foram enviadas para o mesmo enquanto estava desligado. Se o dispositivo utiliza sinalizador **CleanSession** definido como **1** , embora, não receberá todas as mensagens a partir do IoT Hub até subscreveu respectivo ponto final do dispositivo.

Concentrador IoT entrega as mensagens com o **Nome do tópico** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` se não existirem quaisquer propriedades de mensagem. `{property_bag}`contém pares de url codificada/valor da chave de propriedades da mensagem. Apenas as propriedades da aplicação e propriedades do sistema pode ser definida utilizador (como **messageId** ou **correlationId**) são incluídas no saco de propriedade. Nomes de propriedades do sistema têm o prefixo **$**, propriedades da aplicação utilizam o nome da propriedade original com sem prefixo.

Quando subscreve um cliente de dispositivo a um tópico com **QoS 2**, IoT concentrador concede máximo QoS nível 1 no pacote **SUBACK** . Após esta ação, IoT concentrador irá entregar mensagens para o dispositivo utilizar QoS 1.

### <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se precisar de personalizar o comportamento de protocolo MQTT no lado na nuvem, devem consultar o [gateway de protocolo Azure IoT] [ lnk-azure-protocol-gateway] que permite-lhe implementar um gateway de protocolo personalizado de alto desempenho interfaces diretamente com IoT concentrador. O gateway de protocolo Azure IoT permite-lhe personalizar o protocolo de dispositivo para acomodar implementações do brownfield MQTT ou outros protocolos personalizados. No entanto, esta abordagem requerem que executar e trabalhar com um gateway protocolo personalizado.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte o artigo [notas MQTT suporte] [ lnk-mqtt-devguide] no guia de programador do Azure IoT concentrador.

Para saber mais sobre o protocolo MQTT, consulte a [documentação de MQTT][lnk-mqtt-docs].

Para saber mais sobre como planear a sua implementação IoT concentrador, consulte:

- [Azure certificada para o catálogo de dispositivo IoT][lnk-devices]
- [Suporte de protocolos adicionais][lnk-protocols]
- [Comparar com concentradores de evento][lnk-compare]
- [Dimensionamento, HA e DR][lnk-scaling]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
