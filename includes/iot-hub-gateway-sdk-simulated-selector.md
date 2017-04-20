> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

Este tutorial da [nuvem dispositivo simulada carregar exemplo] mostra como utilizar o [SDK de Gateway de IoT Azure] [ lnk-sdk] para enviar telemetria nuvem de dispositivo para IoT concentrador de dispositivos ' Simulada '.

Este tutorial abrange:

1. **Arquitectura**: informações arquitecturais importantes sobre a amostra simuladas carregar de nuvem de dispositivo.

2. **Criar e executar**: os passos necessários para criar e executar a amostra.

## <a name="architecture"></a>Arquitectura

O exemplo de nuvem dispositivo simulada carregar mostra como utilizar o SDK para criar um gateway que envia telemetria a partir de dispositivos ' Simulada ' a um concentrador IoT. Os dispositivos simulados não consegue ligar directamente ao concentrador IoT porque:

- Os dispositivos não utilizam um protocolo de comunicações compreendido IoT concentrador.
- Os dispositivos não são suficientemente hábil para não se esqueça da identidade que lhes foi atribuída pelo IoT concentrador.

O gateway resolve estes problemas para os dispositivos simulados das seguintes formas:

- O gateway compreende o protocolo utilizado pelos dispositivos simulados, recebe telemetria nuvem de dispositivo de dispositivos e reencaminha as mensagens para o concentrador IoT utilizando um protocolo, entende-se pelo concentrador.
- O gateway armazena identidades IoT concentrador em nome de dispositivos simulados e funciona como um proxy quando os dispositivos simulados enviem mensagens para IoT concentrador.

O diagrama seguinte mostra os principais componentes da amostra, incluindo os módulos de gateway:

![][1]


> [AZURE.NOTE] Os módulos não conseguem passar mensagens directamente entre si. Os módulos publicar mensagens de um mediador interna que envia as mensagens para os outros módulos utilizando um mecanismo de subscrição, tal como é mostrado no diagrama abaixo. Para mais informações, consulte a [começar a trabalhar com o SDK do Gateway IoT][lnk-gw-getstarted].

### <a name="protocol-ingestion-module"></a>Módulo do protocolo de ingestão

Este módulo é o ponto de partida para obter dados a partir de dispositivos, através do gateway e, em nuvem. No exemplo, o módulo efectua quatro tarefas:

1.  Cria dados de temperatura simulada.
    
    Nota: Se estiver a utilizar dispositivos reais, o módulo poderia ler dados dos dispositivos físicos.

2.  Coloca os dados de temperatura simulada para o conteúdo de uma mensagem.

3.  Adiciona uma propriedade com um endereço MAC falsa para a mensagem que contém os dados de temperatura simulada.

4.  Torna a mensagem disponível para o módulo seguinte na cadeia.

> [AZURE.NOTE] O módulo chamado **ingestão X do protocolo** no diagrama acima é chamado **simuladas dispositivo** no código fonte.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; módulo IoT concentrador ID

Este módulo verifica a existência de mensagens que incluem uma propriedade que contém o endereço MAC, adicionado pelo módulo de ingestão de protocolo, do dispositivo simulado. Se o módulo de encontrar destas propriedades, adiciona outra propriedade com uma chave de dispositivo do concentrador IoT à mensagem e, em seguida, disponibiliza a mensagem para o módulo seguinte na cadeia. Trata-se como amostra associa identidades de dispositivo do concentrador IoT dispositivos simulados. O programador configura o mapeamento entre endereços MAC e identidades de concentrador IoT manualmente como parte da configuração do módulo. 

> [AZURE.NOTE]  Este exemplo utiliza um endereço MAC como um identificador de dispositivo exclusivos e correlaciona-lo com uma identidade de dispositivo do concentrador IoT. No entanto, pode escrever o seu próprio módulo que utiliza um identificador exclusivo diferente. Por exemplo, poderá ter dispositivos com dados de telemetria com um nome de dispositivo exclusivos incorporado que pode utilizar para determinar a identidade de dispositivo do concentrador IoT ou números de série exclusivos.

### <a name="iot-hub-communication-module"></a>Módulo de comunicação IoT concentrador

Este módulo demora mensagens com um concentrador IoT identidade do dispositivo atribuída pelo módulo do anterior e envia o conteúdo da mensagem ao concentrador IoT utilizando HTTP. HTTP é uma das três protocolos compreendidos IoT concentrador.

Em vez de abrir uma ligação para cada dispositivo simulada IoT concentrador, este módulo é aberta uma única ligação de HTTP do gateway para a distribuidora IoT e multiplexes ligações a partir de todos os dispositivos simuladas pela ligação. Isto permite que um único gateway ligar a muitos mais dispositivos, expressa ou simulados, ao que seria possível se aberta uma ligação exclusiva para cada dispositivo.

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Amostra de carregamento do dispositivo nuvem simulada]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md