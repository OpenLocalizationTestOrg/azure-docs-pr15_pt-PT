<properties
    pageTitle="Utilizar um dispositivo real com o SDK do Gateway IoT | Microsoft Azure"
    description="Azure IoT Gateway SDK instruções passo a passo utilizar um dispositivo de Texas instrumentos SensorTag para enviar dados para IoT concentrador através de um gateway a executar o um módulo de calcular Intel Edison"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-real-device-using-linux"></a>Azure IoT Gateway SDK (beta) – enviar mensagens de dispositivo para nuvem com um dispositivo real com Linux

Este tutorial da [amostra de energia baixa Bluetooth] [ lnk-ble-samplecode] mostra-lhe como utilizar o [Azure IoT Gateway SDK] [ lnk-sdk] para reencaminhar telemetria de dispositivo para nuvem a IoT concentrador a partir de um dispositivo físico e como encaminhar comandos a partir do IoT Hub para um dispositivo físico.

Este tutorial abrange:

* **Arquitetura**: informações de arquitecturais importantes sobre a amostra de energia baixa Bluetooth.

* **Criar e executar**: os passos necessários para criar e executar o exemplo.

## <a name="architecture"></a>Arquitetura

Tutorial mostra-lhe como criar e executar um IoT Gateway num módulo de calcular de Edison para Intel que é executada Linux. O gateway é construído utilizando o SDK do Gateway IoT. O exemplo utiliza um dispositivo Texas instrumentos SensorTag Bluetooth baixa energia (tabela) para recolher dados de temperatura.

Quando executa o gateway-lo:

- Liga-se para um dispositivo SensorTag utilizando o protocolo de energia de baixa Bluetooth (tabela).
- Liga-se a concentrador IoT utilizando o protocolo HTTP.
- Reencaminha telemetria dispositivo SensorTag a IoT concentrador.
- Encaminha os comandos a partir do IoT Hub ao dispositivo SensorTag.

O gateway contém os módulos seguintes:

- Um *módulo de tabela* que interfaces com um dispositivo de tabela para receber dados de temperatura a partir dos comandos de dispositivo e enviar para o dispositivo.
- Uma *Tabela nuvem módulo do dispositivo* que converte as mensagens JSON chegar a partir da nuvem para tabela as instruções para o *módulo de tabela*.
- Um *módulo de registo* que regista todas as mensagens de gateway.
- Um *módulo de mapeamento de identidade* que converte entre tabela dispositivo MAC endereços e identidades do dispositivo Azure IoT concentrador.
- Um *módulo de IoT concentrador* que carrega dados de telemetria a um concentrador de IoT e recebe comandos do dispositivo de um concentrador de IoT.
- Um *módulo de impressora de tabela* que interpreta telemetria a partir do dispositivo de tabela e imprime formatado dados consola para ativar a resolução de problemas e depuração.

### <a name="how-data-flows-through-the-gateway"></a>Como os fluxos de dados através do Gateway

O diagrama de bloco seguinte ilustra pipeline de fluxo de dados de telemetria carregamento:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Os passos que um item de telemetria leva em viagem a partir de um dispositivo de tabela a IoT concentrador são:

1. O dispositivo de tabela gera uma amostra de temperatura e envia-o ao longo do Bluetooth no módulo da tabela no gateway.
2. O módulo tabela recebe a amostra e publica-lo para o corretor juntamente com o endereço de MAC do dispositivo.
3. Módulo de mapeamento de identidade levantar o auscultador esta mensagem e utiliza uma tabela interna para traduzir o endereço de MAC do dispositivo para uma identidade de dispositivo IoT concentrador (um ID do dispositivo e chave do dispositivo). Em seguida, publica uma nova mensagem que contém os dados de exemplo de temperatura, o endereço de MAC de dispositivo, o ID do dispositivo e a chave do dispositivo.
4. O módulo IoT concentrador recebe esta mensagem nova (gerada pelo módulo de mapeamento de identidade) e publica-lo a IoT concentrador.
5. O módulo do registo regista todas as mensagens a partir do corretor para um ficheiro de disco.

O diagrama de bloco seguinte ilustra o pipeline de fluxo de dados do dispositivo comandos:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. O módulo IoT concentrador consulta periodicamente o concentrador IoT para novas mensagens de comando.
2. Quando o módulo IoT concentrador recebe uma nova mensagem de comando, publica-lo para o corretor.
3. O módulo de mapeamento de identidade escolhe a mensagem de comando e utiliza uma tabela interna para traduzir o ID do dispositivo IoT concentrador para um dispositivo de endereço do MAC. Em seguida, publica uma nova mensagem, que inclui o endereço de MAC do dispositivo de destino no mapa de propriedades da mensagem.
4. Nuvem tabela módulo do dispositivo levantar o auscultador esta mensagem e converte-lo para a instrução de tabela adequada para o módulo de tabela. Em seguida, publica uma nova mensagem.
5. O módulo tabela levantar o auscultador esta mensagem e executa a instrução e/s por comunicar com o dispositivo de tabela.
6. O módulo do registo regista todas as mensagens a partir do corretor para um ficheiro de disco.

## <a name="prepare-your-hardware"></a>Preparar do hardware

Neste tutorial assume que estiver a utilizar um dispositivo de [Texas instrumentos SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) ligado a um quadro Intel Edison.

### <a name="set-up-the-edison-board"></a>Configurar o quadro Edison

Antes de começar, certifique-se de que se consegue ligar o seu dispositivo Edison à sua rede sem fios. Para configurar o seu dispositivo Edison, tem de ligá-lo a um computador anfitrião. Intel fornece guias para os seguintes sistemas operativos de introdução:

- [Começar a trabalhar com a área de desenvolvimento de Edison Intel num Windows de 64 bits][lnk-setup-win64].
- [Começar a trabalhar com a área de desenvolvimento de Edison Intel num Windows de 32 bits][lnk-setup-win32].
- [Introdução ao quadro Intel Edison desenvolvimento no Mac OS X][lnk-setup-osx].
- [Introdução ao quadro Edison Intel® no Linux][lnk-setup-linux].

Para configurar o seu dispositivo Edison e familiarizar-se com o mesmo, deve concluir todos os passos nestes "Introdução ao", os seus artigos exceto o último passo, "Escolher IDE", que é necessário para o tutorial atual. No final do processo de configuração de Edison tiver:

- Fez Piscar seu Edison com o firmware mais recente.
- Estabelecer uma ligação série a partir do seu anfitrião do Edison.
- Execute o script **configure_edison** para definir uma palavra-passe e ativar a conta na sua Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Activar a conectividade ao dispositivo SensorTag a partir da sua área Edison

Antes de executar o exemplo, tem de verificar se o seu quadro Edison pode ligar para o dispositivo SensorTag.

Primeiro tem de verificar se o seu Edison pode ligar para o dispositivo SensorTag.

1. Desbloquear bluetooth na Edison e verifique se o número da versão está **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Execute o comando **bluetoothctl** . Estão agora uma shell interactivo bluetooth. 

3. Introduza o comando **power na** potência o controlador de bluetooth. Deverá visualizar saída semelhante a:
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Enquanto ainda estiver na shell de bluetooth interativo, introduza o comando **Analisar** para pesquisar dispositivos bluetooth. Deverá visualizar saída semelhante a:
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Torne o dispositivo SensorTag detetável premindo o botão pequenas (o LED verde deve flash). O Edison deve descobrir o dispositivo SensorTag:
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    Neste exemplo, pode ver se o endereço de MAC do dispositivo SensorTag está **A0:E6:F8:B5:F6:00**.

6. Desative a digitalização introduzindo o comando **Digitalizar desativar** .
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Ligar ao seu dispositivo SensorTag utilizando o seu endereço de MAC introduzindo **ligar \<endereço MAC >**. Tenha em atenção que a saída de exemplo abaixo é abreviada:
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Nota: Pode listar as características GATT do dispositivo novamente utilizando o comando de **atributos da lista** .

8. Pode agora desligar a partir do dispositivo utilizando o comando **Desligar** e, em seguida, sair partir da shell de bluetooth utilizando o comando **Sair** :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Agora está pronto para executar o exemplo de tabela Gateway no seu dispositivo Edison.

## <a name="run-the-ble-gateway-sample"></a>Executar a amostra de Gateway de tabela

Para executar a amostra de tabela no seu Edison, é necessário concluir três tarefas:

- Configure dois dispositivos de amostra no seu centro IoT.
- Construa o SDK do Gateway IoT no seu dispositivo Edison.
- Configurar e executar o exemplo de tabela no seu dispositivo Edison.

No momento da escrita, o SDK do Gateway IoT suporta apenas gateways que utilizam módulos de tabela no Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar dois dispositivos de amostra no seu centro IoT

- [Criar um concentrador de IoT] [ lnk-create-hub] na sua subscrição do Azure, terá do nome do seu Centro para concluir este tutorial. Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.
- Adicione um dispositivo chamado **SensorTag_01** para o seu centro IoT e tome nota da sua chave de id e dispositivo. Pode utilizar o [Explorador de dispositivo ou Explorador de iothub] [ lnk-explorer-tools] ferramentas para adicionar este dispositivo para o concentrador de IoT que criou no passo anterior e para obter a sua chave. Serão mapeadas este dispositivo para o dispositivo SensorTag quando configurar o gateway.

### <a name="build-the-iot-gateway-sdk-on-your-edison-device"></a>Criar o SDK do Gateway IoT no seu dispositivo Edison

A versão do **git** na Edsion não suporta submodules. Para transferir a origem completa para o SDK do Gateway IoT para o Edison tem duas opções:

- Opção #1: Clonar o [Azure IoT Gateway SDK] [ lnk-sdk] repositório no seu Edison e, em seguida, clonar manualmente o repositório de cada submodule.
- Opção #2: Clonar o [Azure IoT Gateway SDK] [ lnk-sdk] repositório num ambiente de trabalho dispositivo onde **git** suporta submodules e, em seguida, copie o repositório concluído com submodules para sua Edison.

Se escolher a opção #2, utilize os seguintes comandos **git** para clonar o SDK do Gateway IoT e todos os seus submodules:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Em seguida, deverá zip do repositório local inteiro para um ficheiro de arquivo única antes de a copiar para o Edison. Pode utilizar um utilitário como **pscp** está incluído no **betumes** para copiar o ficheiro de arquivo para o Edison. Por exemplo:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Quando tiver uma cópia completa do repositório IoT Gateway SDK no seu Edison, podem ser criados utilizando o seguinte comando a partir da pasta que contém o SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurar e executar o exemplo de tabela no seu dispositivo Edison

Para bootstrap e executar a amostra, é necessário configurar a cada módulo que participa no gateway. Esta configuração é fornecida num ficheiro JSON e tem de configurar cinco módulos participantes. Existe um ficheiro JSON de exemplo fornecido no repositório de denominado **gateway_sample.json** que pode utilizar como ponto de partida para criar o seu próprio ficheiro de configuração. Este ficheiro é na pasta **ble_gateway_hl/amostras/src** na cópia local do repositório IoT Gateway SDK.

As secções seguintes descrevem como editar este ficheiro de configuração para o exemplo de tabela e partem do princípio de que o repositório IoT Gateway SDK está na **/home/root/azure-iot-gateway-sdk /** pasta no seu dispositivo Edison. Se o repositório estiver noutro local, deve ajustar os caminhos, consoante adequado:

#### <a name="logger-configuration"></a>Configuração do registo

Assumindo que o repositório de gateway está localizado na pasta **/home/root/azure-iot-gateway-sdk /**, configurar o módulo do registo da seguinte forma:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Configuração do módulo de tabela

A configuração de exemplo para o dispositivo de tabela assume um dispositivo Texas instrumentos SensorTag. Todos os dispositivos padrão de tabela que podem funcionar como um GATT periférico deverá funcionar, mas terá de atualizar as IDs característicos GATT e os dados (para obter instruções de escrita). Adicione o endereço de MAC do seu dispositivo SensorTag: 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>Módulo de IoT concentrador

Adicione o nome do seu centro IoT. O valor de sufixo é normalmente **azure devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuração do módulo mapeamento de identidade

Adicione o endereço MAC do seu dispositivo SensorTag e o dispositivo de Id e a chave do dispositivo **SensorTag_01** que adicionou ao seu centro IoT:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuração de módulo da impressora de tabela

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configuração de encaminhamento

A seguinte configuração garante o seguinte procedimento:
- O **registo do** módulo recebe e registar todas as mensagens.
- O módulo **SensorTag** envia mensagens para o **mapeamento de** e a **Tabela impressora** módulos.
- Módulo de **mapeamento** envia mensagens para o módulo **IoTHub** sejam enviadas para cima para o seu centro IoT.
- O módulo **IoTHub** envia mensagens novamente para o módulo de **mapeamento** .
- Módulo de **mapeamento** envia mensagens novamente para o módulo **SensorTag** .

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Para executar o exemplo executa **ble_gateway_hl** binário passar o caminho para o ficheiro de configuração de JSON. Se tiver utilizado o ficheiro **gateway_sample.json** , o comando para executar este aspeto:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Poderá ter a premir pequeno botão no dispositivo SensorTag para tornar detetável antes de executar a amostra.

Quando executa a amostra, pode utilizar o [Explorador de dispositivo ou Explorador de iothub] [ lnk-explorer-tools] ferramenta para monitorizar as mensagens que reencaminha o gateway dispositivo SensorTag.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens de dispositivo de nuvem

O módulo tabela também suporta instruções envio do Azure IoT concentrador para o dispositivo. Pode utilizar o [Azure IoT concentrador dispositivo Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou o [Explorador do concentrador IoT](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) para enviar mensagens de JSON o módulo de gateway tabela transmite para o dispositivo de tabela. Por exemplo, se estiver a utilizar o dispositivo Texas instrumentos SensorTag, em seguida, pode enviar as seguintes mensagens de JSON para o dispositivo a partir do IoT Hub.

- Repor todos os LEDs e a campainha (desativar)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurar e/s como 'remoto'

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Ativar o LED vermelho

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Ativar o LED verde

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Ativar a campainha

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

O comportamento predefinido para um dispositivo com o protocolo HTTP para ligar a IoT concentrador é consultar cada 25 minutos para um novo comando. Por conseguinte, se enviar vários comandos separados terá de esperar 25 minutos para que o dispositivo para cada comando de receber.

> [AZURE.NOTE] O gateway também verifica a existência de novos comandos sempre que for iniciado, de modo que pode forçá-lo para processar um comando parar e iniciando o gateway.

## <a name="next-steps"></a>Próximos passos

Se pretender obter uma compreensão mais avançada do IoT Gateway SDK e experimentar alguns exemplos de código, visite os seguintes tutoriais do programador e recursos:

- [Azure IoT Gateway SDK][lnk-sdk]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
