<properties
    pageTitle="Azure dispositivo IoT SDK para C - serializador | Microsoft Azure"
    description="Saiba mais sobre como utilizar a biblioteca de serializador no dispositivo Azure IoT SDK para C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-serializer"></a>Dispositivo, Microsoft Azure IoT SDK para C – mais informações sobre serializador

O [primeiro artigo](iot-hub-device-sdk-c-intro.md) nesta série introduzida o **Azure IoT dispositivo SDK para C**. O seguinte artigo fornecida uma descrição mais detalhada da [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Este artigo conclui cobertura do SDK ao fornecer uma descrição mais detalhada do componente de restante: a biblioteca de **serializador** .

O artigo introdutório descrita como utilizar a biblioteca de **serializador** para enviar eventos para e receber mensagens a partir do IoT Hub. Neste artigo, Expandimos o debate ao fornecer uma explicação mais completa de como os seus dados com a linguagem de macro **serializador** do modelo. O artigo também inclui mais detalhes sobre como a biblioteca serializes mensagens (e em alguns casos como pode controlar o comportamento de serialização). Recomendamos também irá descrevem alguns parâmetros pode modificar que determinam o tamanho dos modelos de que criar.

Por fim, o artigo revisits alguns tópicos abrangidos nos artigos anterior, tal como a mensagem e processamento de propriedade. Como podemos ficará a saber, essas funcionalidades funcionam da mesma forma utilizando a biblioteca de **serializador** , tal como com a biblioteca de **IoTHubClient** .

Tudo o que é descrito neste artigo é baseado os exemplos SDK **serializador** . Se pretender segui-los, consulte o artigo o **simplesample\_amqp** e **simplesample\_http** aplicações incluídas no dispositivo Azure IoT SDK para C.

Pode localizar o **Azure IoT dispositivo SDK para C** no [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub repositório e ver os detalhes da API na [referência da C API](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-modeling-language"></a>O idioma de modelação

O [artigo introdutório](iot-hub-device-sdk-c-intro.md) nesta série introduzido o **Azure IoT dispositivo SDK para C** modelação de idioma através do exemplo fornecido na **simplesample\_amqp** aplicação:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Como pode ver, o idioma de modelação é baseado C macros. Começar a sua definição com sempre **inicial\_espaço de nomes** e terminam sempre com **fim\_espaço de nomes**. É comuns para atribuir um nome do espaço de nomes para a sua empresa ou, tal como no exemplo, o projeto que está a trabalhar.

O que vai dentro do espaço de nomes são as definições de modelo. Neste caso, não existe um único modelo para uma anemometer. Mais uma vez, o modelo pode ser com o nome nada, mas normalmente isto chama-se para o dispositivo ou tipo de dados que pretende trocar com IoT concentrador.  

Modelos de contenham uma definição de eventos, pode penetração a IoT concentrador (os *dados*), assim como as mensagens que possa receber a partir do IoT Hub (as *ações*). Como pode ver do exemplo, eventos têm um tipo de e um nome; ações de ter um nome e parâmetros opcionais (cada uma com um tipo de).

O que não é demonstrado neste exemplo são tipos de dados adicionais que são suportados pelo SDK. Vamos abordar já a seguir.

> [AZURE.NOTE] Concentrador IoT refere-se aos dados de que um dispositivo envia-lhe como *eventos*, enquanto o idioma de modelação se refere à mesma como *dados* (definido utilizando **WITH_DATA**). Da mesma forma, IoT concentrador refere-se aos dados que enviar para dispositivos como *mensagens*, enquanto o idioma de modelação se refere à mesma como *ações* (definido utilizando **WITH_ACTION**). Tenha em atenção que estes termos podem ser utilizados alternadamente neste artigo.

### <a name="supported-data-types"></a>Tipos de dados suportados

São suportados os seguintes tipos de dados em modelos criados com a biblioteca **serializador** :

| Tipo                    | Descrição                            |
|-------------------------|----------------------------------------|
| dupla                  | duplo precisão número de vírgula flutuante |
| Int                     | número inteiro de 32 bits                         |
| flutuante                   | número de vírgula flutuante precisão único |
| longo                    | número inteiro longo                           |
| int8\_t                 | número inteiro de 8 bits                          |
| Int16\_t                | número inteiro de 16 bits                         |
| Int32\_t                | número inteiro de 32 bits                         |
| Int64\_t                | número inteiro de 64 bits                         |
| Booleano                    | Booleano                                |
| ASCII\_CARÁCT\_ptr        | Cadeia de caracteres ASCII                           |
| EDM\_data\_tempo\_desfasamento | desvio de tempo de data                       |
| EDM\_GUID               | GUID                                   |
| EDM\_binário             | em binário                                 |
| DECLARAR\_estrutura         | tipo de dados complexa                      |

Vamos começar com o último tipo de dados. O **DECLARE\_estrutura** permite-lhe definir os tipos de dados complexos, que são agrupamentos dos outros tipos de primitivos. Estes agrupamentos permitem-nos definir um modelo de que tem este aspeto:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nosso modelo contém um evento de dados único tipo **TestType**. **TestType** é um tipo de complexo que inclui vários membros constituem demonstram tipos primitivos suportados pelo **serializador** modelação de idioma.

Com um modelo da seguinte forma, podemos pode escrever código para enviar dados para IoT concentrador que é apresentada da seguinte forma:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Basicamente, que está a atribuir um valor para todos os membros da estrutura de **teste** e, em seguida, chamar **SendAsync** para enviar o evento de dados de **teste** para a nuvem. **SendAsync** é uma função de helper que envia um evento de dados simples a IoT concentrador:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Esta função serializes o evento de dados determinado e envia-o para concentrador IoT utilizando **IoTHubClient\_SendEventAsync**. Este é o mesmo código de outros fabricantes referido artigos anterior (**SendAsync** contém a lógica para uma função conveniente).

Uma função helper utilizada no código anterior é **GetDateTimeOffset**. Esta função transforma o período de tempo especificado um valor de tipo de **EDM\_data\_tempo\_desfasamento**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Se executar este código, a seguinte mensagem é enviada a IoT concentrador:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Note que a serialização é JSON, qual é o formato gerado pela biblioteca **serializador** . Tenha em atenção que cada membro do objeto JSON série corresponde aos membros do **TestType** definido no nosso modelo. Os valores também corresponder exatamente aos que são utilizados no código. No entanto, tenha em atenção que os dados binários são codificado em base64: "AQID" é o base64 codificação de {0x01, 0x02, 0x03}.

Este exemplo demonstra a vantagem de utilizar a biblioteca de **serializador** – permite--nos enviar JSON na nuvem, sem ter de explicitamente lidar com serialização na nossa aplicação. Só temos de se preocupar está a definir os valores dos eventos dados no nosso modelo e, em seguida, chamar APIs simples para enviar os eventos na nuvem.

Com esta informação, podemos pode definir modelos que incluem o intervalo de tipos de dados suportadas, incluindo tipos complexos (Recomendamos poderia mesmo incluir tipos complexos dentro de outros tipos de complexos). No entanto, ele serializado JSON gerado por um ponto de importante mostra o exemplo acima. Determina *como* podemos enviar dados com a biblioteca de **serializador** exatamente como o JSON é constituído. Que ponto específico se encontra o que serão abordados seguinte.

## <a name="more-about-serialization"></a>Mais informações sobre serialização

A secção anterior realça um exemplo de saída gerado pela biblioteca **serializador** . Nesta secção, explicaremos como a biblioteca serializes dados e como pode controlar esse comportamento utilizando a serialização APIs.

Para avançar o debate sobre serialização, podemos irá trabalhar com um novo modelo com base num termóstato. Primeiro, vamos fornecer algumas informações sobre o cenário estamos a tentar endereço.

Pretendemos num termóstato que as medidas temperatura e humidade do modelo. Cada porção de dados vai ser enviadas para a IoT concentrador de tipos de forma diferente. Por predefinição, os ingresses termóstato um evento de temperatura uma vez por cada minutos 2; um evento de humidade é ingressed uma vez a cada 15 minutos. Quando o evento for ingressed, tem de incluir um carimbo de data/hora que mostra o tempo que a temperatura correspondente ou humidade foi medida.

Dada neste cenário, vamos demonstrar duas formas diferentes para modelar os dados e Explicaremos o efeito que tem de modelação na impressão em série.

### <a name="model-1"></a>Modelo de 1

Eis a primeira versão de um modelo que suporta o cenário anterior:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Tenha em atenção que o modelo inclui dois eventos de dados: **temperatura** e **humidade**. Ao contrário dos exemplos anteriores, o tipo de cada evento é uma estrutura definida utilizando **DECLARE\_estrutura**. **TemperatureEvent** inclui uma medida de temperatura e um carimbo de data/hora; **HumidityEvent** contém uma medida de humidade e um carimbo de data/hora. Este modelo dá-numa forma natural para modelar os dados para o cenário descrito acima. Quando enviarmos um evento na nuvem, quer enviaremos uma temperatura/carimbo data/hora ou para um par de humidade/carimbo.

Um evento de temperatura podemos enviar para a nuvem utilizando código como as seguintes:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Vamos irá utilizar valores codificada para temperatura e humidade no código de exemplo, mas imagine que recomendamos realmente estiver a obter estes valores por amostragem sensores correspondentes no termóstato.

O código acima utiliza a auxiliar **GetDateTimeOffset** que foi introduzida anteriormente. Por razões que irão tornar-se limpar posteriores, este código separa explicitamente a tarefa de serializar e enviar o evento. O código anterior serializes o evento de temperatura para um intervalo de tempo. Em seguida, **EnviarMensagem** é uma função de helper (incluído no **simplesample\_amqp**) o evento que envia a IoT concentrador:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Este código é um subconjunto da auxiliar **SendAsync** descrito na secção anterior, para que recomendamos não abordá-la novamente aqui.

Vamos executar o código anterior para enviar o evento de temperatura, este formulário série do evento é enviado a IoT concentrador:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vamos estiver a enviar uma temperatura que é do tipo **TemperatureEvent** e essa estrutura contém um membro de **temperatura** e a **hora** . Isto diretamente é refletido na série de dados.

Da mesma forma, podemos enviar um evento de humidade com este código:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

O formulário de série que é enviado para IoT concentrador é apresentada da seguinte forma:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Novamente, este é conforme esperado.

Com este modelo, pode imagine eventos como adicionais podem ser adicionados facilmente. Definir o mais estruturas utilizando **DECLARE\_estrutura**e incluir o evento correspondente no modelo, usando **com\_dados**.

Agora, vamos modificar o modelo para que inclui os mesmos dados mas com uma estrutura diferente.

### <a name="model-2"></a>Modelo de 2

Considere este modelo alternativo para o apresentado acima:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Neste caso tenha eliminado estamos a **DECLARE\_estrutura** macros e estiver a definir simplesmente os itens de dados a partir do nosso cenário utilizando tipos simples do modelação de idioma.

Apenas para o momento em que vamos ignorar o evento de **tempo** . Com essa anulação, eis o código para penetração **temperatura**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia o seguinte evento série a IoT concentrador:

```
{"Temperature":75}
```

E o código para enviar o evento humidade é apresentada da seguinte forma:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia isto a IoT concentrador:

```
{"Humidity":45}
```

Até ao momento não existem ainda sem surpresas. Agora vamos alterar como utilizamos a macro SERIALIZE.

A macro **SERIALIZE** pode demorar vários eventos de dados como argumentos. Isto permite-nos serializar o evento de **temperatura** e **humidade** em conjunto e enviá-las a IoT concentrador numa chamada:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Poderá adivinhar que o resultado deste código é que os eventos de dados de duas são enviados a IoT concentrador:

[

{"Temperatura": 75},

{"Humidade": 45}

]

Por outras palavras, seria de esperar que este código é igual a enviar **temperatura** e **humidade** separadamente. É apenas uma questão de conveniência para transmitir ambos os eventos para **SERIALIZE** na mesma chamada. No entanto, que não for o caso. Em vez disso, o código acima envia este evento de dados simples a IoT concentrador:

{"Temperatura": "Humidade" 75,: 45}

Pode parecer estranho porque o nosso modelo define **temperatura** e **humidade** como dois eventos *separada* :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais para o ponto, podemos não modelar estes eventos onde a **temperatura** e **humidade** estão na mesma estrutura:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se utilizámos este modelo, seria mais fácil de compreender como **temperatura** e **humidade** seriam enviado na mesma mensagem série. No entanto pode não ser limpar por que é que funciona dessa forma quando passar ambos os eventos de dados para **SERIALIZE** utilizando o modelo de 2.

Este comportamento é mais fácil de compreender se conhecer os pressupostos de que a biblioteca de **serializador** está a fazer. Para analisar este vamos aceda novamente ao nosso modelo:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pense neste modelo nos termos orientada para objectos. Neste caso, está a de modelação um dispositivo físico (um termostato) e que o dispositivo inclui atributos como **temperatura** e **humidade**.

Vamos pode enviar o estado inteiro do nosso modelo com o código como as seguintes:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Assumindo que os valores de temperatura, humidade e a hora são definidas, teria vemos um evento assim enviado a IoT concentrador:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Por vezes, poderá pretender apenas enviar *algumas* propriedades do modelo na nuvem (isto é especialmente verdadeiro se o modelo contiver um grande número de eventos de dados). É útil para enviar apenas um subconjunto de dados eventos, tal como no nosso exemplo anterior:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isto gera exatamente o mesmo evento série como se tinha definido uma **TemperatureEvent** com um membro de **temperatura** e a **hora** , tal como fizemos com o modelo de 1. Neste caso podemos puderam gerar exatamente o mesmo evento série utilizando um modelo diferente (modelo de 2) porque podemos chamado **SERIALIZE** de forma diferente.

O ponto de importante é que se passar vários eventos de dados para **SERIALIZE,** assume que cada evento é uma propriedade num único objeto JSON.

A melhor abordagem depende do que e como ter em consideração o modelo. Se estiver a enviar "eventos" na nuvem e cada evento contém um conjunto de propriedades definido, em seguida, a primeira abordagem faz com que muitas sentido. Nesse caso utilizaria **DECLARE\_estrutura** para definir a estrutura de cada evento e, em seguida, incluí-los no seu modelo com o **com\_dados** macro. Em seguida, envie cada evento como fizemos no primeiro exemplo acima. Nesta abordagem seria apenas ser efetuada com um evento de dados simples para **SERIALIZADOR**.

Se refletir acerca do seu modelo de uma forma orientada para objectos, em seguida, a segunda abordagem poderá suas necessidades. Neste caso, os elementos definido utilizando **com\_dados** são "Propriedades" do objeto. Passar o subconjunto de eventos para **SERIALIZE** que goste, dependendo da quantidade de estado do seu "do objeto" que pretende enviar na nuvem.

Nether abordagem é errada ou para a direita. Basta cuidado de como funciona a biblioteca de **serializador** e escolha a abordagem de modelação que melhor se adequa às suas necessidades.

## <a name="message-handling"></a>Tratamento de mensagens

Até ao momento este artigo tem apenas abordado enviar eventos para IoT concentrador e não endereçada a receber mensagens. O motivo para isto é que o que precisamos de saber sobre a receber mensagens em grande medida tem sido abrangido um [artigo anterior](iot-hub-device-sdk-c-intro.md). Recuperar a partir desse artigo que processar mensagens através do registo de uma função de chamada de retorno da mensagem:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, a escrever a função de chamada de retorno é chamada quando for recebida uma mensagem:

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

Esta implementação do **IoTHubMessage** chamadas a função específica para cada ação no seu modelo. Por exemplo, se o seu modelo define esta ação:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Tem de definir uma função com esta assinatura:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** denomina-em seguida, quando essa mensagem é enviada para o seu dispositivo.

Ainda estamos ainda não explicado é o aspeto a versão de série da mensagem. Por outras palavras, se pretender enviar uma mensagem de **SetAirResistance** para o seu dispositivo, o que é que o aspeto?

Se estiver a enviar uma mensagem para um dispositivo, deverá fazê-lo através do serviço de Azure IoT SDK. Ainda precisa de saber que cadeia para enviar para invocar uma acção específica. O formato geral para enviar uma mensagem é apresentada da seguinte forma:

```
{"Name" : "", "Parameters" : "" }
```

Estiver a enviar um objeto JSON série com duas propriedades: **nome** é o nome da ação (mensagem) e **parâmetros** contém os parâmetros dessa ação.

Por exemplo, para invocar **SetAirResistance** pode enviar esta mensagem para um dispositivo:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome de ação tem de corresponder exatamente uma ação definida no seu modelo. Os nomes de parâmetro tem de corresponder também. Tenha em atenção sensibilidade a maiúsculas e minúsculas. **Nome** e **parâmetros** são sempre em maiúsculas. Certifique-se corresponder a maiúsculas e minúsculas do seu nome de ação e parâmetros no seu modelo. Neste exemplo, o nome de ação é "SetAirResistance" e não "setairresistance".

Esta secção descritas tudo o que precisa saber quando eventos enviar e receber mensagens com a biblioteca de **serializador** . Antes de passar, vamos abordar algumas parâmetros pode configurar que controlam como grandes encontra o seu modelo.

## <a name="macro-configuration"></a>Configuração de macro

Se estiver a utilizar a biblioteca de **serializador** uma parte do SDK ter em consideração importante encontra-se na biblioteca do azure-c-partilhado-utility.
Se tiver clonar do repositório de Azure-iot-SDK a partir do GitHub através da opção – recursiva, irá encontrar esta biblioteca utilitário partilhada aqui:

```
.\\c\\azure-c-shared-utility
```

Se não tiver clonar a biblioteca, pode encontrá-lo [aqui](https://github.com/Azure/azure-c-shared-utility).

Dentro de biblioteca utilitário partilhada, irá encontrar na seguinte pasta:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta pasta contém uma solução do Visual Studio denominada **macro\_utilitários\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

O programa nesta solução gera a **macro\_utils.h** ficheiro. Existe uma macro predefinido\_utils.h ficheiro incluído com o SDK. Esta solução permite-lhe modificar alguns parâmetros e, em seguida, recriar o ficheiro de cabeçalho com base nos seguintes parâmetros.

Os parâmetros de chave duas se preocupem com são **nArithmetic** e **nMacroParameters** são definidos nestas duas linhas que se encontram na macro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Estes valores são os parâmetros de predefinidos incluídos com o SDK. Cada parâmetro tem o seguinte significado:

-   Controlos de nMacroParameters – quantos parâmetros pode ter uma DECLARE\_definição de macro do modelo.

-   nArithmetic – controla o número total de membros permitidos num modelo.

O motivo que estes parâmetros são importantes é porque estes controlam como grandes seu modelo pode ser. Por exemplo, considere esta definição de modelo:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Tal como mencionado anteriormente, **DECLARE\_modelo** é apenas uma macro C. Os nomes do modelo e o **com\_dados** declaração (ainda outra macro) é parâmetros de **DECLARE\_modelo**. **nMacroParameters** define quantos parâmetros podem ser incluídos no **DECLARE\_modelo**. Eficazmente, isto define quantos dados evento ação declarações e pode ter. Como tal, com o limite predefinido de 124 Isto significa que pode definir um modelo com uma combinação de cerca de 60 ações e eventos de dados. Se tentar exceder este limite, receberá erros de compilador que têm um aspeto semelhantes ao seguinte:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

O parâmetro **nArithmetic** é mais informações sobre o funcionamento interno da linguagem de macros do que a aplicação.  Controla o número total de membros, que pode ter no seu modelo, incluindo **DECLARE_STRUCT** macros. Se começar a ver erros de compilador como estas, em seguida, que deverá tentar aumentando **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Se quiser alterar estes parâmetros, modifique os valores na macro\_utils.tt de ficheiros, recompilar a macro\_utilitários\_h\_generator.sln solução e execute o programa compilado. Ao fazê-lo, uma nova macro\_utils.h ficheiro é gerado e colocado na. \\comuns\\diretório inc.

Para que possa utilizar a nova versão do macro\_utils.h, remover o pacote de NuGet **serializador** da sua solução e no seu lugar incluir o projeto do Visual Studio **serializador** . Permite que o seu código compilar contra o código de origem da biblioteca de serializador. Isto inclui a macro atualizada\_utils.h. Se pretender efetuar este procedimento para **simplesample\_amqp**, comece por remover o pacote de NuGet para a biblioteca de serializador da solução:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Em seguida, adicione este projeto à sua solução do Visual Studio:

> . \\c\\serializador\\construir\\windows\\serializer.vcxproj

Quando terminar, a solução deverá ter o aspeto da seguinte forma:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Agora quando compilar a solução, a macro atualizada\_utils.h é incluída no seu binário.

Tenha em atenção que estes valores suficientemente altas crescentes podem exceder os limites de compilador. Para este ponto, o **nMacroParameters** é o parâmetro com o qual se preocupem principal. A especificação de C99 Especifica que um mínimo de 127 parâmetros são permitidos numa definição de macro. O compilador Microsoft segue a especificação de exatamente (e tem um limite de 127), pelo que não poderá aumentar **nMacroParameters** para além das predefinido. Outros elaboração poderá permitem-lhe para fazê-lo (por exemplo, o compilador GNU suporta um limite superior).

Até ao momento podemos tenha abrangido praticamente tudo o que precisa de saber sobre como escrever código com a biblioteca de **serializador** . Antes da celebração, vamos revisitar alguns tópicos de anterior artigos que poderá estar a pensar sobre.

## <a name="the-lower-level-apis"></a>As API de níveis inferiores

A aplicação de exemplo no qual este artigo aborda está **simplesample\_amqp**. Este exemplo utiliza a complicados (a não-"tudo") APIs para eventos de enviar e receber mensagens. Se utilizar estas APIs, é executado um tópico de fundo que leva-o até o cuidado de eventos de enviar e receber mensagens. No entanto, pode utilizar as API (TA) de níveis inferiores para eliminar este tópico de fundo e tomar explícito controlo sobre quando eventos de enviar ou receber mensagens a partir da nuvem.

Como é descrito um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md), existe um conjunto de funções que consiste em das APIs complicadas:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

Estas APIs são demonstradas nos **simplesample\_amqp**.

Também existe um análogo conjunto de API de níveis inferiores.

-   IoTHubClient\_TA\_CreateFromConnectionString

-   IoTHubClient\_TA\_SendEventAsync

-   IoTHubClient\_TA\_SetMessageCallback

-   IoTHubClient\_TA\_Destroy

Note que a API de níveis inferiores funcionam exatamente da mesma forma, tal como descrito nos artigos anterior. Pode utilizar o primeiro conjunto de APIs se pretender que um tópico de fundo para processar eventos enviar e receber mensagens. Utilize o segundo conjunto de APIs se pretender explícito controlo sobre quando enviar e receber dados a partir do IoT Hub. Qualquer um dos conjuntos de trabalho APIs quatros bem com a biblioteca de **serializador** .

Para um exemplo de como as API de níveis inferiores são utilizadas com a biblioteca de **serializador** , consulte o artigo o **simplesample\_http** aplicação.

## <a name="additional-topics"></a>Tópicos adicionais

Alguns outros tópicos mencionar novamente são propriedade processamento, utilizando as credenciais do dispositivo alternativo e opções de configuração. Estes são todos os tópicos abrangidos um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md). O ponto principal é todas estas funcionalidades funcionam da mesma forma com a biblioteca de **serializador** tal como com a biblioteca de **IoTHubClient** . Por exemplo, se pretender anexar propriedades a um evento a partir do seu modelo, utilize **IoTHubMessage\_propriedades** e **mapear**\_**AddorUpdate**, da mesma forma que é descrito anteriormente:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Se o evento foi gerado a partir da biblioteca de **serializador** ou criado manualmente utilizando a biblioteca de **IoTHubClient** não importa.

Para utilizar as credenciais de dispositivo alternativo **IoTHubClient\_TA\_criar** funciona apenas, assim como **IoTHubClient\_CreateFromConnectionString** para imputar uma **IOTHUB\_cliente\_PROCESSAR**.

Por fim, se estiver a utilizar a biblioteca de **serializador** , pode definir opções de configuração com **IoTHubClient\_TA\_SetOption** tal como fez ao utilizar a biblioteca de **IoTHubClient** .

Uma funcionalidade que seja exclusiva à biblioteca **serializador** são a inicialização APIs. Antes de poder começar a trabalhar com a biblioteca, tem de chamar **serializador\_inicialização**:

```
serializer_init(NULL);
```

Isto é feito antes de ligar para **IoTHubClient\_CreateFromConnectionString**.

Da mesma forma, quando tiver terminado a última chamada faremos em que trabalha com a biblioteca, é **serializador\_deinit**:

```
serializer_deinit();
```

Caso contrário, todas as outras funcionalidades listadas em cima funcionam da mesma na biblioteca de **serializador** tal como na biblioteca de **IoTHubClient** . Para mais informações sobre qualquer um destes tópicos, consulte o [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) nesta série.

## <a name="next-steps"></a>Próximos passos

Este artigo descreve em detalhe os aspectos exclusivos da biblioteca de **serializador** contidos no **dispositivo Azure IoT SDK para C**. Com as informações fornecidas que deve ter uma boa compreensão sobre como utilizar modelos para eventos de enviar e receber mensagens a partir do IoT Hub.

Isto também conclui a série de três partes sobre como desenvolver aplicações com o **dispositivo Azure IoT SDK para C**. Isto deve ser informações suficientes para não só a começar mas dar-lhe um conhecimento aprofundado sobre como funcionam as APIs. Para obter informações adicionais, existem alguns exemplos SDK não abrangido aqui. Caso contrário, a [documentação SDK](https://github.com/Azure/azure-iot-sdks) é um bom recurso para obter informações adicionais.


Para saber mais sobre como desenvolver IoT concentrador, consulte o [SDK do concentrador de IoT][lnk-sdks].

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
