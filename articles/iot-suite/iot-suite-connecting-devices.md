<properties
   pageTitle="Ligue um dispositivo com C no Windows | Microsoft Azure"
   description="Descreve como ligar um dispositivo à Azure IoT Suite pré-configurado remoto monitorização solução com uma aplicação escrita C em execução no Windows."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Ligar o seu dispositivo para a solução pré-configurado monitorização remota (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Criar uma solução de amostra C no Windows

Os passos seguintes mostram-lhe como utilizar o Visual Studio para criar uma aplicação de cliente escrita C que comunica com a solução monitorização remota pré-configuradas automaticamente.

Criar um projeto starter no Visual Studio 2015 e adicione os pacotes de NuGet IoT concentrador dispositivo cliente:

1. No Visual Studio 2015, crie uma aplicação de consola do C utilizando o modelo de Visual C++ **Win32 consola de aplicação** . Nome do projeto **RMDevice**.

2. Na página **Definições de aplicações** no **Assistente da aplicação Win32**, certifique-se de que a **aplicação de consola** está selecionada e, desmarque **Precompiled cabeçalho** e **verifica do ciclo de vida de desenvolvimento da segurança (SDL)**.

3. No **Explorador de soluções**, elimine os ficheiros stdafx.h, targetver.h e stdafx.cpp.

4. No **Explorador de soluções**, mudar o nome de ficheiro RMDevice.cpp para RMDevice.c.

5. No **Explorador de soluções**, com o botão direito no projeto **RMDevice** e, em seguida, clique em **Gerir NuGet pacotes**. Clique em **Procurar**, em seguida, procure e instale seguintes pacotes de NuGet no projeto:

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

6. No **Explorador de soluções**, com o botão direito no projeto **RMDevice** e, em seguida, clique em **Propriedades** para abrir a caixa de diálogo de **Páginas de propriedades** do projeto. Para obter mais detalhes, consulte o artigo [Propriedades do projeto C++ Visual definição][lnk-c-project-properties]. 

7. Clique na pasta **Ligador** , em seguida, clique em página de propriedades de **entrada** .

8. Adicione **crypt32.lib** para a propriedade **Dependências adicionais** . Clique em **OK** e, em seguida, em **OK** novamente para guardar o projeto valores de propriedade.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Especificar o comportamento do dispositivo IoT concentrador

As bibliotecas do cliente IoT concentrador utilizam um modelo para especificar o formato das mensagens que envia o dispositivo para IoT concentrador e os comandos que recebe a partir do IoT Hub.

1. No Visual Studio, abra o ficheiro RMDevice.c. Substituir os existentes `#include` declarações com o seguinte código:

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Adicione as declarações de variáveis seguintes após a `#include` declarações. Substitua os valores de marcador de posição [Id do dispositivo] e [chave do dispositivo] com valores para o seu dispositivo a partir do dashboard de monitorização solução remoto. Utilize o nome do anfitrião concentrador IoT do dashboard para substituir [nome do IoTHub]. Por exemplo, se o nome de anfitrião de concentrador IoT **contoso.azure devices.net**, substitua [nome do IoTHub] **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Adicione o seguinte código para definir o modelo que permite que o dispositivo comunicar com IoT concentrador. Este modelo Especifica que o dispositivo envia temperatura, temperatura externa, humidade e um id de dispositivo como telemetria. O dispositivo também envia metadados sobre o dispositivo para IoT Hub, incluindo uma lista de comandos que suporta o dispositivo. Este dispositivo responde aos comandos **SetTemperature** e **SetHumidity**:

    ```
    // Define the Model
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

## <a name="implement-the-behavior-of-the-device"></a>Implementar o comportamento do dispositivo

Agora adicione código que implementa o comportamento definido no modelo.

1. Adicione as seguintes funções executar quando o dispositivo recebe os comandos **SetTemperature** e **SetHumidity** a partir do IoT Hub:

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

2. Adicione a função seguinte que envia uma mensagem a IoT concentrador:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Adicione a função seguinte que cria o a biblioteca de serialização no SDK:

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Adicione a função seguinte para ligar a IoT concentrador, enviar e receber mensagens e desligar do concentrador. Repare que a forma como o dispositivo envia metadados sobre si mesmo, incluindo os comandos suporta a IoT concentrador quando liga-se. Este metadados ativa a solução atualizar o estado do dispositivo para **Executar** no dashboard de:

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
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

5. Substitua a função **principal** com o seguinte código para invocar a função **remote_monitoring_run** :

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. Clique em **Criar** e, em seguida, **Criar solução** para criar a aplicação de dispositivo.

7. No **Explorador de soluções**, com o botão direito do projecto **RMDevice** , clique em **Depurar**e, em seguida, clique em **Iniciar nova instância** para executar o exemplo. Consola do apresenta as mensagens, tal como a aplicação envia telemetria exemplo a IoT concentrador e recebe comandos.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx