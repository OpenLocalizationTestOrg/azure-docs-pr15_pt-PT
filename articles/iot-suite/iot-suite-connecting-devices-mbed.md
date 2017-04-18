<properties
   pageTitle="Ligue um dispositivo com C no mbed | Microsoft Azure"
   description="Descreve como ligar um dispositivo à Azure IoT Suite pré-configurado remoto monitorização solução com uma aplicação escrita C em execução num dispositivo mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Ligar o seu dispositivo para a solução pré-configurado monitorização remota (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Criar e executar a solução de exemplo C

As instruções seguintes descrevem os passos para ligar um [Com capacidade de mbed K64F-FRDM Freescale] [ lnk-mbed-home] dispositivo para a solução de controlo remoto.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Ligar o dispositivo mbed ao seu computador de rede e ambiente de trabalho

1. Ligar o dispositivo mbed à sua rede utilizando um cabo Ethernet. Este passo é necessário porque a aplicação de exemplo requer o acesso à internet.

2. Consulte o artigo [Introdução ao mbed] [ lnk-mbed-getstarted] para ligar o dispositivo de mbed para o computador de secretária.

3. Se o seu ambiente de trabalho PC a executar o Windows, consulte o artigo [Configuração PC] [ lnk-mbed-pcconnect] para configurar o acesso de porta série para o seu dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Criar um projeto mbed e importar o código de exemplo

1. No seu browser, aceda ao [site de programador](https://developer.mbed.org/)mbed.org. Se ainda não iniciou para cima, verá uma opção para criar uma conta (é gratuito). Caso contrário, inicie sessão com as credenciais da conta. Em seguida, clique em **compilador** no canto superior direito da página. Esta ação fornece-lhe para a interface de *área de trabalho* .

2. Certifique-se a plataforma de hardware que estiver a utilizar o aparece no canto superior direito da janela ou clique no ícone no canto direito para selecionar a plataforma de hardware.

3. No menu principal, clique em **Importar** . Em seguida, clique em **clique aqui** para importar a partir de ligação de URL junto ao logótipo do globo mbed.

    ![][6]

4. Na janela de pop-up, introduza uma ligação para o https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ código exemplo, em seguida, clique em **Importar**.

    ![][7]

5. Pode ver na janela de compilador mbed que também importar este projeto importações várias bibliotecas. Alguns são fornecidos e mantidas pela equipa Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), enquanto outras pessoas estão disponíveis no catálogo de bibliotecas de mbed de bibliotecas de terceiros.

    ![][8]

6. Abra o ficheiro remote_monitoring\remote_monitoring.c e localize o código seguinte do ficheiro:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Substituir [Id do dispositivo] e [dispositivo chave], com os seus dados do dispositivo para activar o programa de exemplo ligar ao seu centro IoT. Utilize o nome do anfitrião concentrador IoT para substituir a [nome do IoTHub] e [sufixo IoTHub, ou seja, azure devices.net] marcadores de posição. Por exemplo, o nome de anfitrião de concentrador IoT for **contoso.azure devices.net**, **contoso** se o **hubName** e tudo o que depois é o **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Percorrer o código

Se estiver interessado em como funciona o programa, esta secção descreve algumas partes importantes do código de exemplo. Se pretende apenas a execução de código, avançar para [criar e executa o programa](#buildandrun).

#### <a name="defining-the-model"></a>Definir o modelo

Este exemplo utiliza o [serializador] [ lnk-serializer] biblioteca para definir um modelo que especifica as mensagens que o dispositivo pode enviar a IoT concentrador e receber a partir do IoT Hub. Neste exemplo, o espaço de nomes **Contoso** define um modelo de **termóstato** que especifica os dados de telemetria **temperatura**, **ExternalTemperature**e **humidade** juntamente com metadados como o id do dispositivo, as propriedades do dispositivo e os comandos que o dispositivo responde a:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Relacionada com a definição de modelo são as definições para os comandos de **SetTemperature** e **SetHumidity** que o dispositivo responde a:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>Ligar o modelo para a biblioteca

As funções **EnviarMensagem** e **IoTHubMessage** são o código de texto automático para envio de telemetria do dispositivo e ligar as mensagens a partir do IoT Hub os processadores de comando.

#### <a name="the-remotemonitoringrun-function"></a>A função remote_monitoring_run

Função **principal** o programa invoca a função **remote_monitoring_run** quando a aplicação é iniciada a executar o comportamento do dispositivo, como um cliente de dispositivo IoT concentrador. Esta função **remote_monitoring_run** principalmente consiste em pares aninhadas de funções:

- **plataforma\_inicialização** e **plataforma\_deinit** executar operações de inicialização e encerramento específicos da plataforma.
- **serializador\_inicialização** e **serializador\_deinit** inicializados e não iniciar a biblioteca de serializador.
- **IoTHubClient\_criar** e **IoTHubClient\_Destroy** criar uma alça de cliente, **iotHubClientHandle**, utilizando as credenciais do dispositivo para estabelecer ligação ao seu centro IoT.

Na secção principal da função **remote_monitoring_run** , o programa executa as seguintes operações utilizando a alça de **iotHubClientHandle** :

- Cria uma instância do modelo de termóstato Contoso e configura as chamadas de retorno as mensagem para os dois comandos.
- Envia informações sobre o dispositivo propriamente dito, incluindo os comandos suportar, a sua concentrador IoT utilizando a biblioteca de serializador. Quando o concentrador recebe esta mensagem,-altera o estado de dispositivo no dashboard de **pendentes** **em execução**.
- Inicia um ciclo **enquanto** que envia temperatura, temperatura externa e valores de humidade a IoT concentrador cada segundo.

Para sua referência, eis um modelo de mensagem **DeviceInfo** enviado a IoT concentrador no arranque:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Para sua referência, eis um modelo de mensagem de **telemetria** enviado a IoT concentrador:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Para sua referência, eis uma amostra **que comando** recebido a partir do IoT Hub:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Criar e executar o programa

1. Clique em **Compilar** para construir o programa. Em segurança pode ignorar todos os avisos, mas se a compilação gera erros, corrigir antes de continuar.

2. Se a compilação é efetuada com êxito, o site de compilador mbed gera um ficheiro. bin com o nome do seu projeto e transfere-lo para o seu computador local. Copie o ficheiro. bin para o dispositivo. Guardar o ficheiro. bin para o dispositivo faz com que o dispositivo, reinicie e executar o programa contido no ficheiro. bin. Manualmente pode reiniciar o programa em qualquer altura ao premir o botão Repor no dispositivo mbed.

3. Ligar para o dispositivo com uma aplicação de cliente SSH, tal como betumes. Pode determinar a porta série que utiliza o seu dispositivo, verificando Gestor de dispositivos do Windows.

    ![][11]

4. No betumes, clique no tipo de ligação **em série** . O dispositivo, normalmente, liga à transmissão 9600, por isso introduza 9600 na caixa **velocidade** . Em seguida, clique em **Abrir**.

5. O programa começa em execução. Poderá ter de repor a área (prima CTRL + quebra ou prima o botão de repor a área) se o programa não for iniciado automaticamente quando se liga.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
