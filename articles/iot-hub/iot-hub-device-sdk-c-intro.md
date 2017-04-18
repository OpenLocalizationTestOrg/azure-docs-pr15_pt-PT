<properties
    pageTitle="Com o dispositivo de Azure IoT SDK para C | Microsoft Azure"
    description="Saiba mais sobre e começar a trabalhar com o código de exemplo no dispositivo Azure IoT SDK para C."
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

# <a name="introducing-the-azure-iot-device-sdk-for-c"></a>Apresentamos o dispositivo de Azure IoT SDK para C

O **dispositivo Azure IoT SDK** é um conjunto de bibliotecas concebido para simplificar o processo de eventos enviar e receber mensagens de serviço **Azure IoT concentrador** . Existem diferentes variações de SDK, cada filtragem de uma plataforma específica, mas este artigo descreve o **Azure IoT dispositivo SDK para C**.

O dispositivo de Azure IoT SDK para C escrito ANSI C (C99) para maximizar a portabilidade. Isto torna bem adequado para trabalhar com num número de plataformas e dispositivos, especialmente onde minimizar disco e ambiental memória é uma prioridade.  

Existem uma vasta gama de plataformas no qual o SDK foi testado (consulte o [Azure certificados para o catálogo de dispositivo IoT](https://catalog.azureiotsuite.com/) para obter mais detalhes). Apesar deste artigo inclui instruções da plataforma Windows a execução de código de exemplo, tenha em atenção que o código é descrito neste artigo é exatamente o mesmo intervalo de plataformas suportadas.

Neste artigo qual vai ser introduzido para a arquitetura do dispositivo Azure IoT SDK para C. Vamos demonstrar como iniciar a biblioteca de dispositivo, enviar eventos para IoT concentrador, bem como receber mensagens a partir dos mesmos. As informações neste artigo deverão ser suficientes para começar a utilizar o SDK, mas também fornece ponteiros para obter informações adicionais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura SDK

Pode localizar o **Azure IoT dispositivo SDK para C** no [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub repositório e ver os detalhes da API na [referência da C API](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

A versão mais recente das bibliotecas pode ser encontrada **mestra** ramo deste repositório:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Este repositório contém toda a família do Azure IoT dispositivo SDK. No entanto, este artigo é relativo ao Azure IoT dispositivo SDK *para C* que podem ser encontradas na pasta **c** .

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* A aplicação core do SDK pode ser encontrada na **iothub\_cliente** pasta que contém a execução da camada API mais baixa no SDK: a biblioteca de **IoTHubClient** . A biblioteca **IoTHubClient** contém APIs execução observou mensagens para o envio de mensagens para IoT concentrador, bem como a receber mensagens a partir dos mesmos. Ao utilizar esta biblioteca, é responsáveis pela aplicação serialização mensagem (eventualmente utilizando a amostra de serializador descrita abaixo), mas são processados outros detalhes de comunicar com IoT concentrador por si.
* A pasta **serializador** contém funções auxiliares e exemplos que mostra como serializar dados antes de enviar a Azure IoT concentrador utilizando a biblioteca de cliente. Tenha em atenção que a utilização do serializador não é obrigatória e apenas fornecida como uma comodidade. Se utilizar a biblioteca de **serializador** , comece por definir um modelo que especifica os eventos que pretende enviar IoT concentrador, bem como as mensagens que esperar para receber a partir dos mesmos. Assim que o modelo estiver definido, o SDK fornece uma superfície de API que lhe permite trabalhar facilmente com os eventos e mensagens sem ter de se preocupar detalhes serialização.
A biblioteca depende outras bibliotecas de abrir origem implementam transporte utilizando vários protocolos (MQTT, AMQP).
* A biblioteca **IoTHubClient** depende outras bibliotecas de abrir origem:
   * A biblioteca [Azure C partilhado utilitário](https://github.com/Azure/azure-c-shared-utility) que fornece a funcionalidade comuns para tarefas básicas (como cadeia, manipulação de lista, IO, etc....) necessários ao longo de várias SDK C relacionados com o Azure
   * A biblioteca de [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) que é implementação de lado do cliente do AMQP otimizado para dispositivos de restrição de recursos.
   * Biblioteca de [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) que é uma biblioteca com finalidades genéricas implementar o protocolo MQTT e otimizados para dispositivos de restrição de recursos.

Tudo isto é mais fácil de compreender verificando o código de exemplo. As secções seguintes orientá-lo na algumas das aplicações de exemplo que estão incluídos no SDK. Isto deve dar-lhe uma boa ideia para as diversas capacidades das camadas de arquitecturais do SDK, bem como uma introdução à como funcionam as APIs.

## <a name="before-running-the-samples"></a>Antes de executar as amostras

Antes de poder executar os exemplos no dispositivo Azure IoT SDK para C tem de criar uma instância do serviço na sua subscrição do Azure se já não tiver um e concluir 2 tarefas:
* preparar o seu ambiente de desenvolvimento
* obter as credenciais de dispositivo.

Se necessitar de criar uma instância do Azure IoT concentrador na sua subscrição do Azure, siga as instruções [aqui](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

O [ficheiro Leia-me](https://github.com/Azure/azure-iot-sdks/tree/master/c) incluído com o SDK fornece instruções para preparar o seu ambiente de desenvolvimento e obter credenciais do dispositivo.
As secções seguintes incluem algumas comentários adicionais sobre essas instruções.

### <a name="preparing-your-development-environment"></a>Preparar o seu ambiente de desenvolvimento

Enquanto pacotes são fornecidas para algumas plataformas (como NuGet para Windows ou apt_get para Debian e Ubuntu) e as amostras utilizam estes pacotes quando se encontra disponível, a seguir as instruções de detalhe como criar a biblioteca e as amostras diretamente de formulários o código.

Em primeiro lugar, terá de obter uma cópia do SDK do GitHub e, em seguida, criar a origem. Deve obter uma cópia da origem da partir do ramo **mestra** do [repositório de GitHub](https://github.com/Azure/azure-iot-sdks).

Quando transferir uma cópia da origem de, tem de concluir os passos descritos no artigo SDK ["Preparar o seu ambiente de desenvolvimento"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md).


Seguem-se algumas sugestões para o ajudar a executar o procedimento descrito no guia de preparação:

-   Quando instala o utilitário **CMake** , escolha a opção para adicionar **CMake** ao sistema caminho para **todos os utilizadores** (adicionar ao **utilizador atual** works também):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Antes de abrir a **linha de comandos para programadores para VS2015**, instale as ferramentas de linha de comandos Git. Para instalar estas ferramentas, conclua os passos seguintes:

    1. Inicie o programa de configuração de **2015 do Visual Studio** (ou escolha **Microsoft Visual Studio 2015** a partir de **programas e funcionalidades do** painel de controlo e selecione **Alterar**).
    
    2. Certifique-se a funcionalidade de **Git para Windows** está selecionada no instalador do mas também poderá querer selecionar a opção de **Extensão GitHub para Visual Studio** para fornecer IDE integração:

        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

    3. Conclua o Assistente de configuração para instalar as ferramentas.

    4. Adicione o directório da **Reciclagem** ferramentas Git a variável de ambiente **PATH** do sistema. No Windows, este aspeto o seguinte procedimento:

        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Quando tiver concluído todas as os passos descritos na página ["Preparar o seu ambiente de desenvolvimento"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) , está pronto para compilar as aplicações de exemplo.

### <a name="obtaining-device-credentials"></a>Obter as credenciais de dispositivo

Agora que o seu ambiente de desenvolvimento está configurado, é a seguinte coisa a fazer obter um conjunto de credenciais de dispositivo.  Para um dispositivo possam aceder a um concentrador de IoT, primeiro tem de adicionar o dispositivo para o registo de dispositivo IoT concentrador. Quando adiciona o seu dispositivo irá obter um conjunto de credenciais do dispositivo que terá de para que o dispositivo conseguir ligar a um concentrador de IoT. Estas credenciais em forma de uma **cadeia de ligação de dispositivo**achar que as aplicações de exemplo Abordaremos na secção seguinte.

Existem algumas ferramentas fornecidas no repositório de abrir origem SDK para o ajudar a gerir o concentrador IoT. Uma é uma aplicação denominada Explorer de dispositivo, a segunda opção é que uma node.js com base ferramenta de clip de plataforma cruzada denominada iothub Explorador do Windows. Pode saber mais sobre estas ferramentas [aqui](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

À medida que vamos através da execução os exemplos no Windows neste artigo, estamos a utilizar a ferramenta de dispositivo Explorer. Mas também pode utilizar o Explorador de iothub se preferir ferramentas clip.

A ferramenta de [Dispositivo Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) utiliza as bibliotecas de serviço do Azure IoT para efetuar várias funções no Centro de IoT, incluindo a adição de dispositivos. Se utiliza o Explorador do dispositivo para adicionar um dispositivo, irá obter uma cadeia de ligação correspondente. Precisa de fazer com que a amostra aplicações são executadas nesta cadeia de ligação.

Caso não ainda estiver familiarizado com o processo, o procedimento seguinte descreve como utilizar o Explorador de dispositivo para adicionar um dispositivo e obter uma cadeia de ligação do dispositivo.

Pode encontrar um Windows installer para a ferramenta de dispositivo Explorador no [SDK solte página](https://github.com/Azure/azure-iot-sdks/releases). Mas também pode executar a ferramenta de diretamente a partir do seu código abrir **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** no **Visual Studio 2015** e criação da solução.

Quando executa o programa verá esta interface:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Introduza a **Cadeia de ligação de concentrador IoT** no primeiro campo e clique em **Atualizar**. Este procedimento configura a ferramenta de modo a que pode comunicar com IoT concentrador.

Assim que a cadeia de ligação IoT concentrador está configurada, clique no separador **Gestão de** :

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Este é onde irá gerir os dispositivos registados no seu centro IoT.

Pode criar um dispositivo ao clicar no botão **Criar** . É apresentada uma caixa de diálogo com um conjunto de teclas previamente povoadas (principais e secundários). Tudo o que tem de fazer é introduzir um **ID do dispositivo** e, em seguida, clique em **Criar**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Quando o dispositivo estiver criado, a lista de dispositivos é atualizada com todos os dispositivos registados, incluindo uma que acabou de criar. Se o botão direito do rato seu novo dispositivo, verá este menu:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Se escolher a opção **Copiar cadeia de ligação para o dispositivo selecionado** , a cadeia de ligação para o seu dispositivo é copiada para a área de transferência. Manter uma cópia da cadeia de ligação. Irá precisá-lo quando executar as aplicações de exemplo descritas nas secções seguintes.

Depois de concluir os passos acima, está pronto para começar a executar algum código. Ambos os exemplos de ter uma constante na parte superior do ficheiro de origem principal que permite-lhe introduzir uma cadeia de ligação. Por exemplo, a linha correspondente a partir do **iothub\_cliente\_exemplo\_amqp** aplicação é apresentada da seguinte forma.

```
static const char* connectionString = "[device connection string]";
```

Se pretender segui-los, introduza a cadeia de ligação do dispositivo, recompilar a solução e poderá executar o exemplo.

## <a name="iothubclient"></a>IoTHubClient

Dentro de **iothub\_cliente** pasta no repositório azure-iot-SDK, existe uma pasta de **amostras** que contém uma aplicação denominada **iothub\_cliente\_exemplo\_amqp**.

A versão do Windows da **iothub\_cliente\_exemplo\_ampq** aplicação inclui a solução do Visual Studio seguinte:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Esta solução contém num único projeto. É de notar que não existem quatro NuGet pacotes instalados nesta solução:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.uamqp

Tem sempre o pacote de **Microsoft.Azure.C.SharedUtility** quando está a trabalhar com o SDK. Uma vez que este exemplo se baseia no AMQP, também tem de incluir os pacotes de **Microsoft.Azure.uamqp** e **Microsoft.Azure.IoTHub.AmqpTransport** (existem pacotes equivalentes para o MQTT e HTTP). Uma vez que a amostra utiliza a biblioteca de **IoTHubClient** , tem de incluir o pacote de **Microsoft.Azure.IoTHub.IoTHubClient** na sua solução.

Pode encontrar a aplicação para a aplicação de exemplo na **iothub\_cliente\_exemplo\_amqp.c** ficheiro de origem.

Vamos utilizar esta aplicação de exemplo para o orientar o que necessário para utilizar a biblioteca de **IoTHubClient** .

### <a name="initializing-the-library"></a>A inicialização da biblioteca

> [AZURE.NOTE] Antes de começar a trabalhar com as bibliotecas, poderá ter de efetuar alguns inicialização específico de plataforma. Por exemplo, se planeia utilizar AMQP no Linux tem de iniciar a biblioteca de OpenSSL. Os exemplos no [repositório GitHub](https://github.com/Azure/azure-iot-sdks) chamada de função de utilitário **platform_init** quando o cliente é iniciado e ligar a função **platform_deinit** antes de sair. Estas funções são declaradas no ficheiro de cabeçalho "platform.h". Deverá examinar as definições destas funções para a sua plataforma de destino no [repositório](https://github.com/Azure/azure-iot-sdks) para determinar se precisar de incluir qualquer código de inicialização plataforma no seu cliente.

Para começar a trabalhar com bibliotecas do tem primeiro de atribuir um identificador de cliente IoT concentrador:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Tenha em atenção que recomendamos está a prisma uma cópia da nossa cadeia de ligação de dispositivo para esta função (o que recomendamos obtidos a partir do Explorador de dispositivo). Recomendamos também designar o protocolo que recomendamos pretende utilizar. Este exemplo utiliza AMQP, mas MQTT e HTTP também são uma opção.

Quando tiver um válido **IOTHUB\_cliente\_PROCESSAR**, pode começar a chamar APIs para eventos de enviar e receber mensagens a partir do IoT Hub. Abordaremos já a seguir.

### <a name="sending-events"></a>Enviar eventos

Enviar eventos a IoT concentrador requer que conclua os passos seguintes:

Em primeiro lugar, crie uma mensagem:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Em seguida, envie a mensagem:

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Sempre que enviar uma mensagem, pode especificar uma referência a uma função de chamada de retorno é chamada quando os dados são enviados:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Tenha em atenção a chamada para o **IoTHubMessage\_Destroy** funcionar quando tiver terminado a mensagem. Terá de fazer esta chamada para libertar recursos atribuídos quando criou a mensagem.

### <a name="receiving-messages"></a>Mensagens a receber

Receber uma mensagem é uma operação assíncrona. Em primeiro lugar, registar uma chamada de retorno para ser chamado quando o dispositivo recebe uma mensagem:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O último parâmetro é um ponteiro void para que pretende. No exemplo, é um ponteiro para um número inteiro mas dever-se num ponteiro para uma estrutura de dados mais complexo. Permite que a função de chamada de retorno trabalhar com no estado partilhado com o autor da chamada desta função.

Quando o dispositivo recebe uma mensagem, a função de chamada de retorno registados é chamada:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Tenha em atenção que utiliza o **IoTHubMessage\_GetByteArray** função para obter a mensagem que neste exemplo é uma cadeia.

### <a name="uninitializing-the-library"></a>A anular a inicialização da biblioteca

Quando tiver terminado eventos enviar e receber mensagens, pode terminar a inicialização da biblioteca de IoT. Para fazê-lo, emita a chamada de função seguinte:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Este procedimento liberta os recursos atribuídos anteriormente pela **IoTHubClient\_CreateFromConnectionString** função.

Como pode ver, é fácil eventos de enviar e receber mensagens com a biblioteca de **IoTHubClient** . A biblioteca processa os detalhes de comunicar com IoT Hub, incluindo o protocolo que deseja utilizar (da perspetiva do programador, esta é uma opção de configuração simples).

A biblioteca **IoTHubClient** também fornece precisos sobre como serializar os eventos ao que seu dispositivo envia a IoT concentrador. Em alguns casos, isto é vantajoso, mas noutros casos, este é um detalhe de implementação com as quais que não pretenda que em questão. Se for esse o caso, poderá considerar a utilização de biblioteca de **serializador** , irá descrevemos na secção seguinte.

## <a name="serializer"></a>Serializador

Conceptual a biblioteca de **serializador** encontra-se na parte superior a biblioteca de **IoTHubClient** no SDK. Utiliza a biblioteca de **IoTHubClient** para a comunicação com IoT concentrador subjacente, mas que adiciona capacidades de modelação remover a carga de lidar com serialização de mensagem do programador. Como funciona esta biblioteca é melhor demonstrado por um exemplo.

Dentro de **serializador** pasta no repositório de azure-iot-SDK é uma pasta de **amostras** que contém uma aplicação chamada **simplesample\_amqp**. A versão do Windows deste exemplo inclui a solução do Visual Studio seguinte:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Tal como acontece com o exemplo anterior, deste inclui várias NuGet pacotes:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.IoTHub.Serializer
- Microsoft.Azure.uamqp

Vamos visualizou a maior parte dos seguintes procedimentos no exemplo anterior, mas **Microsoft.Azure.IoTHub.Serializer** há de novo. O que é necessário quando utilizamos a biblioteca de **serializador** .

Pode encontrar a execução da aplicação de exemplo na **simplesample\_amqp.c** ficheiro.

As secções seguintes orientá-lo nas partes principais deste exemplo.

### <a name="initializing-the-library"></a>A inicialização da biblioteca

Para começar a trabalhar com a biblioteca de **serializador** , tem de chamar a inicialização APIs:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

A chamada para o **serializador\_inicialização** função for uma chamada única e é utilizada para iniciar a biblioteca subjacente. Em seguida, ligar para o **IoTHubClient\_CreateFromConnectionString** função, que é a mesma API tal como no exemplo **IoTHubClient** . Esta chamada define a cadeia de ligação do dispositivo (Isto também é onde escolher o protocolo que pretende utilizar). Tenha em atenção que este exemplo utiliza AMQP como o transporte, mas foi utilizaram HTTP.

Por fim, ligar a **criar\_modelo\_instância** função. Note que **WeatherStation** é o espaço de nomes do modelo e **ContosoAnemometer** é o nome do modelo. Assim que a instância de modelo é criada, pode utilizá-lo para começar a enviar eventos e receber mensagens. No entanto, é importante compreender um modelo de que está.

### <a name="defining-the-model"></a>Definir o modelo

Um modelo na biblioteca de **serializador** define os eventos que o dispositivo pode enviar para IoT concentrador e as mensagens, denominadas *ações* no modelação de idioma, que possa receber. Pode definir um modelo de utilizando um conjunto de C macros como o **simplesample\_amqp** aplicação de exemplo:

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

O **começar\_espaço de nomes** e **fim\_espaço de nomes** macros, ambos os tirar o espaço de nomes do modelo como um argumento. É esperado que nada entre estas macros está a definição da sua modelos e as estruturas de dados que utilizam modelos.

Neste exemplo, existe um único modelo denominado **ContosoAnemometer**. Este modelo define dois eventos que o seu dispositivo, pode enviar a IoT concentrador: **DeviceId** e **velocidade do vento**. Também define os três ações (mensagens) que possa receber o seu dispositivo: **TurnFanOn**, **TurnFanOff**e **SetAirResistance**. Cada evento tem um tipo de e cada ação tem um nome (e, opcionalmente, um conjunto de parâmetros).

Os eventos e as ações definidas no modelo de definem uma superfície de API que pode utilizar para enviar eventos para IoT concentrador, assim como responder a mensagens a ser enviadas para o dispositivo. Este é melhor entendido através de um exemplo.

### <a name="sending-events"></a>Enviar eventos

O modelo de define os eventos que pode enviar a IoT concentrador. Neste exemplo, que significa que um dos dois eventos definidos utilizando a macro **WITH_DATA** . Por exemplo, se pretender enviar um evento de **velocidade do vento** a um concentrador de IoT, existem alguns passos envolvidos na fazer isto acontecer. O primeiro é definir os dados que pretende enviar:

```
myWeather->WindSpeed = 15;
```

O modelo definido anteriormente permite-nos para fazê-lo através da definição de um membro de uma **estrutura**. Em seguida, podemos serializar o evento que pretende enviar:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Este código serializes ao evento para um intervalo de tempo (referenciado pelo **destino**). Por fim, enviaremos um evento a IoT concentrador com este código:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Esta é uma função de auxiliar na aplicação de exemplo que envia os nossos evento série a IoT concentrador:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
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
    messageTrackingId++;
}
```

Este código é muito semelhante a podemos mostrado na **iothub\_cliente\_exemplo\_amqp** aplicação, na qual é criada uma mensagem a partir de uma matriz de bytes e, em seguida, utilizada **IoTHubClient\_SendEventAsync** enviá-la a IoT concentrador. Depois de que estamos apenas ter que libertar a alça de mensagem e serializado memória intermédia de dados que são atribuídas anterior.

O segundo para o último parâmetro de **IoTHubClient\_SendEventAsync** é uma referência a uma função de chamada de retorno chama-se quando os dados são enviados com êxito. Eis um exemplo de uma função de chamada de retorno:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para o contexto de utilizador o mesmo ponteiro podemos passados para **IoTHubClient\_SendEventAsync**. Neste caso o contexto é um contador simples, mas pode ser tudo o que quiser.

Há existe para enviar eventos. A única coisa da esquerda para a folha de rosto é como receber mensagens.

### <a name="receiving-messages"></a>Mensagens a receber

Receber uma mensagem funciona da mesma forma para as mensagens de forma trabalha na biblioteca de **IoTHubClient** . Em primeiro lugar, registar uma função de chamada de retorno da mensagem:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, escrever a função de chamada de retorno é chamada quando for recebida uma mensagem:

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

Este código é automático – é o mesmo para qualquer solução. Esta função recebe a mensagem e tratará de encaminhá-lo para a função adequada através da chamada para **executar\_comando**. A função denominada depende neste momento a definição das ações no nosso modelo.

Quando define uma ação no seu modelo, está a necessário para implementar uma função que chama-se quando o seu dispositivo recebe a mensagem correspondente. Por exemplo, se o seu modelo define esta ação:

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

Tenha em atenção que o nome da função corresponde ao nome da ação no modelo e que os parâmetros da função correspondem os parâmetros especificados para a ação. O primeiro parâmetro é sempre necessário e contém um ponteiro para a instância do nosso modelo.

Quando o dispositivo recebe uma mensagem que corresponde a esta assinatura, chama-se a função correspondente. Por conseguinte, para além de ter que incluir o código de texto automático a partir do **IoTHubMessage**, a receber mensagens é apenas uma questão de definir uma função simple para cada ação definida no seu modelo.

### <a name="uninitializing-the-library"></a>A anular a inicialização da biblioteca

Quando tiver terminado dados enviar e receber mensagens, pode terminar a inicialização da biblioteca de IoT:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma destas três funções alinha com as funções de inicialização três descritas anteriormente. Chamar estes APIs assegura que libertar recursos anteriormente atribuídos.

## <a name="next-steps"></a>Próximos passos

Este artigo abrangido os princípios básicos para utilizar as bibliotecas no **dispositivo Azure IoT SDK para C**. -Lhe forneceu informações suficientes para compreender o que está incluído no SDK, sua arquitetura e como começar a trabalhar com os exemplos do Windows. O seguinte artigo continua a descrição do SDK por explicar [mais sobre a biblioteca de IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre como desenvolver IoT concentrador, consulte o [SDK do concentrador de IoT][lnk-sdks].

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]


[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
