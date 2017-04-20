> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Este artigo fornece instruções detalhadas do [código de exemplo Olá mundo] [ lnk-helloworld-sample] para ilustrar os componentes fundamentais do [Azure IoT Gateway SDK] [ lnk-gateway-sdk] arquitectura. O exemplo utiliza o SDK de Gateway de concentrador IoT para criar um gateway simple que regista uma mensagem "Olá mundo" para um ficheiro de cinco em cinco segundos.

Este tutorial abrange:

- **Conceitos**: uma visão geral conceitual dos componentes que compõem qualquer gateway criar com o SDK de Gateway IoT.  
- **Arquitectura de exemplo Hello World**: descreve como os conceitos se aplicam à amostra Olá mundo e como os componentes ajustam em conjunto.
- **Como criar o exemplo**: os passos necessários para criar o exemplo.
- **Como executar o exemplo**: os passos necessários para executar o exemplo. 
- **Saída típica**: um exemplo de saída para esperar quando executar o exemplo.
- **Fragmentos de código**: uma colecção de fragmentos de código para mostrar como amostra Olá mundo implementa componentes chave gateway.

## <a name="azure-iot-gateway-sdk-concepts"></a>Conceitos de IoT Gateway SDK Azure

Antes de examinar o código de exemplo ou criar seu próprio gateway do campo utilizando o SDK de Gateway IoT, deverá compreender os conceitos chave que constituem a arquitectura do SDK.

### <a name="modules"></a>Módulos

Criar um gateway com o SDK de Gateway de IoT Azure criando e montagem de *módulos*. Módulos utilizam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, executa alguma acção, opcionalmente transforma-a numa nova mensagem e, em seguida, publica por outros módulos processar. Alguns módulos podem produzir apenas novas mensagens e nunca processar mensagens a receber. Uma cadeia de módulos cria uma tubagem de processamento de dados com cada módulo uma transformação a efectuar nos dados de um ponto em que tubagem.

![Uma cadeia de módulos gateway criada com o SDK de Gateway de IoT Azure][1]
 
O SDK contém o seguinte:

- Módulos previamente escritos que executam funções de gateway comum.
- As interfaces de um programador pode utilizar para escrever módulos personalizados.
- A infra-estrutura necessária para implementar e executar um conjunto de módulos.

O SDK fornece uma camada de abstracção que permite-lhe criar gateways para executar uma variedade de sistemas operativos e plataformas.

![Camada de abstracção do Azure IoT Gateway SDK][2]

### <a name="messages"></a>Mensagens

Apesar de pensar sobre módulos a passagem de mensagens entre si é uma forma conveniente de lo a conceptualizar como funções de um gateway, ele não reflectir com exactidão o que acontece. Módulos de utilizarem um mediador para comunicar entre si, que publicar mensagens broker (bus, pubsub ou qualquer outro padrão de mensagens) e, em seguida, permitir que o broker encaminhar a mensagem para os módulos ligados a ele.

Um módulos utiliza a função de **Broker_Publish** para publicar uma mensagem para o broker. O mediador envia as mensagens a um módulo invocando uma função de chamada de retorno. Uma mensagem é constituído por um conjunto de propriedades de chave/valor e o conteúdo transmitido como um bloco de memória.

![A função de Mediador no Azure IoT Gateway SDK][3]

### <a name="message-routing-and-filtering"></a>Encaminhamento de mensagens e filtragem

Existem duas formas de direccionamento de mensagens para os módulos correctos. Um conjunto de hiperligações poderão ser passado para o broker para que o broker sabe que a origem e o receptor para cada módulo, ou pode filtrar o módulo nas propriedades da mensagem. Um módulo só deve actuar sobre uma mensagem se a mensagem for destinada. As ligações e filtragem de mensagens é o que cria efectivamente uma tubagem de mensagem.

## <a name="hello-world-sample-architecture"></a>Arquitectura de exemplo Hello World

O exemplo Olá mundo ilustra os conceitos descritos na secção anterior. O exemplo Olá mundo implementa um gateway que tenha uma tubagem constituída por dois módulos:

-   O módulo *Olá mundo* cria uma mensagem de cinco em cinco segundos e transmite-o para o módulo de registo.
-   O módulo de *registo* escreve as mensagens que recebe um ficheiro.

![Arquitectura da amostra Olá mundo criada com o SDK de Gateway de IoT Azure][4]

Tal como descrito na secção anterior, o módulo Olá mundo não passa mensagens directamente para o módulo de registo em cinco segundos. Em vez disso, este publica uma mensagem para o Mediador de cinco em cinco segundos.

O módulo de registo recebe a mensagem do broker e actua, escrever o conteúdo da mensagem para um ficheiro.

O módulo de registo só consome mensagens a partir do Mediador, nunca publica novas mensagens para o broker.

![Como o broker encaminha mensagens entre módulos no Azure IoT Gateway SDK][5]

A figura acima mostra a arquitectura da amostra Olá mundo e os caminhos relativos para os ficheiros de origem que implementam diferentes partes da amostra no [repositório de][lnk-gateway-sdk]. Explorar o código ou utilize os fragmentos de código abaixo como guia.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk