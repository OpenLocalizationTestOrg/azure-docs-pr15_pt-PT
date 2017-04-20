## <a name="typical-output"></a>Saída típica

Segue-se um exemplo do resultado escrito no ficheiro de registo pela amostra Olá mundo. Caracteres de nova linha e separador foram adicionados para legibilidade:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Fragmentos de código

Esta secção aborda algumas partes do código da amostra Olá mundo chaves.

### <a name="gateway-creation"></a>Criação de gateway

O programador tem de escrever o *processo de gateway*. Este programa cria a infra-estrutura interna (o mediador), carrega os módulos e configura tudo a funcionar correctamente. O SDK fornece a função **Gateway_Create_From_JSON** para permitir o arranque do sistema um gateway de um ficheiro JSON. Utilizar a função de **Gateway_Create_From_JSON** tem de passar-o caminho para um ficheiro JSON que especifica os módulos para carregar. 

É possível localizar o código para o processo de gateway na amostra Olá mundo de [main.c] [ lnk-main-c] ficheiro. Para legibilidade, o fragmento abaixo mostra uma versão abreviada do código de processo gateway. Este programa cria um gateway e, em seguida, aguarda que o utilizador premir a tecla **ENTER** antes que lágrimas para baixo o gateway. 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

O ficheiro de definições JSON contém uma lista de módulos para carregar. Cada módulo tem de especificar r:

- **module_name**: um nome exclusivo para o módulo.
- **module_path**: o caminho para a biblioteca que contém o módulo. Para Linux é um ficheiro de .so, no Windows, este é um ficheiro. dll.
- **argumentos**: quaisquer informações de configuração necessita do módulo.

O ficheiro JSON também contém as ligações entre os módulos que serão passados para o broker. Uma hiperligação tem duas propriedades:
- **origem**: nome de módulo a partir de `modules` secção, ou "\*".
- **receptor**: nome de módulo a partir de `modules` secção

Cada hiperligação define uma rota da mensagem e a direcção. Mensagens do módulo de `source` devem ser entregues no módulo `sink`. O `source` pode estar definido para "\*", que indica que as mensagens de qualquer módulo serão recebidas por `sink`.

O exemplo seguinte mostra o ficheiro de definições de JSON utilizado para configurar o exemplo Olá mundo em Linux. Cada mensagem produzida pelo módulo de `hello_world` será consumida pelo módulo de `logger`. Se um módulo requer que um argumento depende da concepção do módulo. Neste exemplo, o módulo de registo tem um argumento que é o caminho para o ficheiro de saída e o módulo Olá mundo não tem quaisquer argumentos:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Publicação de mensagem Olá mundo módulo

Pode encontrar o código utilizado pelo módulo de "Olá mundo" para publicar mensagens no ['hello_world.c'] [ lnk-helloworld-c] ficheiro. O fragmento abaixo mostra uma versão alterada com comentários adicionais e algum código removido para legibilidade de processamento de erros:

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Processamento de mensagens Hello World módulo

O módulo de Hello World nunca necessita de processar quaisquer mensagens de outros módulos de publicar o broker. Isto faz com que a aplicação de chamada de retorno de mensagem no módulo Olá mundo uma função de operação não funcional.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Mensagem de módulo do registo de publicação e processamento

O módulo de registo recebe mensagens a partir do Mediador e escreve-as para um ficheiro. Nunca publica quaisquer mensagens. Por conseguinte, o código do módulo de registo nunca chama a função de **Broker_Publish** .

A função **Logger_Recieve** a [logger.c] [ lnk-logger-c] o ficheiro é a chamada de retorno invoca o broker para entregar mensagens no módulo de registo. O fragmento abaixo mostra uma versão alterada com comentários adicionais e algum código removido para legibilidade de processamento de erros:

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como utilizar o SDK do Gateway IoT, consulte o seguinte:

- [IoT Gateway SDK – enviar mensagens de nuvem de dispositivo com um dispositivo simulado a utilizar o Linux][lnk-gateway-simulated].
- [Azure IoT Gateway SDK] [ lnk-gateway-sdk] em GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md