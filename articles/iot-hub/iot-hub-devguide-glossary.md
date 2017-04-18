<properties
 pageTitle="Guia do programador - glossário | Microsoft Azure"
 description="Um glossário de termos comuns relativas a IoT concentrador"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="glossary-of-iot-hub-terms"></a>Glossário de termos IoT concentrador de

Este artigo lista alguns dos termos comuns associados IoT concentrador.

## <a name="advanced-message-queueing-protocol-amqp"></a>Avançadas mensagem protocolo de colocação em fila (AMQP)

[AMQP](https://www.amqp.org/) é um dos protocolos mensagens que IoT concentrador suporta para comunicar com dispositivos. Para mais informações sobre os protocolos mensagens que IoT concentrador suporta, consulte o artigo [Enviar e receber mensagens com IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Nuvem dispositivo (C2D)

Normalmente utilizado para fazer referência a mensagens enviadas a partir do IoT Hub para um dispositivo ligado. Muitas vezes, estas mensagens são comandos que indicar o dispositivo para efetuar algumas ação. Para mais informações, consulte o artigo [Enviar e receber mensagens com IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="condition"></a>Condição

Refere-se às informações de estado do dispositivo, como o método de conectividade atualmente em utilização, comunicado por uma aplicação do dispositivo. Dispositivos também podem comunicar suas capacidades. Pode consultar condição e capacidade de utilizar o duplos dispositivo.

## <a name="data-point-message"></a>Mensagem de ponto de dados

Uma mensagem de ponto de dados é uma mensagem de nuvem para o dispositivo que contém os dados de telemetria como velocidade do vento ou a temperatura.

## <a name="desired-properties"></a>Propriedades pretendidas

No contexto de gémeos de dispositivo, as propriedades pretendidas são utilizadas em conjunto com *comunicado propriedades* para sincronizar a configuração do dispositivo ou condição. Propriedades pretendidas só podem ser definidas pela aplicação novamente fim e são observadas pela aplicação do dispositivo. 

## <a name="device-to-cloud-d2c"></a>Dispositivo nuvem (D2C)

Normalmente utilizado para fazer referência a mensagens enviadas a partir de um dispositivo ligado a IoT concentrador. Estas mensagens podem ser mensagens [ponto de dados](#data-point-message) ou [interativos](#interactive-message) . Para mais informações, consulte o artigo [Enviar e receber mensagens com IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="device-identity-registry"></a>Registo de identidade do dispositivo

O [registo de identidade do dispositivo](iot-hub-devguide-identity-registry.md) é o componente incorporado de um concentrador de IoT que armazena informações sobre os dispositivos individuais permitida para ligar a um concentrador.

## <a name="device"></a>Dispositivo

No contexto de IoT, um dispositivo é normalmente um dispositivo autónomo informático pequena, que poderá recolher dados ou controlar outros dispositivos. Por exemplo um dispositivo poderá, um dispositivo de monitorização ambiental ou um controlador para os sistemas de abeberamento e ventilação numa estufa.

## <a name="device-twin"></a>Duplos de dispositivo

Um [duplos dispositivo](iot-hub-devguide-device-twins.md) é uma cópia no Centro de IoT das definições de condição e configuração de um dispositivo física. Pode utilizar um duplos dispositivo para gerir as configurações do dispositivo físico.

## <a name="direct-method"></a>Método directo

Um [método directo](iot-hub-devguide-direct-methods.md) é uma forma que pode acionar um método de executar num dispositivo por invocar uma API no seu centro IoT.

## <a name="event-hub-compatible-endpoint"></a>Ponto final de compatível com o concentrador de evento

Para ler mensagens de dispositivo para nuvem enviadas para o seu centro IoT, pode ligar a um ponto final no seu centro e utilizar qualquer método compatível com o evento concentrador para ler essas mensagens. Os métodos de compatível com o concentrador de evento incluem utilizando o evento concentradores SDK e a análise da cadeia de Azure.

## <a name="field-gateway"></a>Gateway de campo

Um gateway campo permite conectividade para dispositivos que não é possível ligar diretamente a concentrador de IoT e normalmente é implementada localmente com os seus dispositivos. Para obter mais informações, consulte o artigo [o que é o Azure IoT concentrador?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Tarefa

O back-end da solução pode utilizar tarefas para agendar e controlar atividades num conjunto de dispositivos registado com o seu centro IoT. As atividades incluem atualizar as propriedades do dispositivo duplos pretendido, atualizar etiquetas do dispositivo duplos e invocar métodos diretos.

## <a name="protocol-gateway"></a>Gateway protocolo

Um gateway protocolo normalmente é implementado na nuvem e fornece protocolo de serviços de tradução para estabelecer ligação ao IoT concentrador de dispositivos. Para obter mais informações, consulte o artigo [o que é o Azure IoT concentrador?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Mensagem interativa

Uma mensagem interativa é uma mensagem de dispositivo de nuvem accionadores uma ação imediata no aplicação back-end. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser tem sessão iniciada automaticamente no sistema de CRM.

## <a name="iot-hub"></a>Concentrador IoT

Concentrador IoT é um serviço Azure totalmente gerido que permite bidireccional fiável e seguro comunicações entre milhões de IoT dispositivos e solução back-end. Para obter mais informações, consulte o artigo [o que é o Azure IoT concentrador?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>IoT conjunto de aplicações

Conjunto de aplicações do Azure IoT em conjunto pacotes vários serviços Azure com pré-configurado soluções. Estas soluções pré-configurado permitem-lhe começar a trabalhar rapidamente com implementações do fim para fim de cenários IoT comuns. Para obter mais informações, consulte o artigo [o que é o conjunto de aplicações do Azure IoT?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Tarefa

Uma [tarefa](iot-hub-devguide-jobs.md) no Centro de IoT permite-lhe executar operações, tais como um firmware atualização através de vários dispositivos ligados à sua concentrador.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) é um dos protocolos mensagens que IoT concentrador suporta para comunicar com dispositivos. Para mais informações sobre os protocolos mensagens que IoT concentrador suporta, consulte o artigo [Enviar e receber mensagens com IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Propriedades denunciadas

No contexto de gémeos de dispositivo, propriedades denunciadas são utilizadas em conjunto com *as propriedades do pretendido* para sincronizar a configuração do dispositivo ou condição. Propriedades denunciadas só podem ser definidas pela aplicação do dispositivo e podem ser lida e consultadas através da aplicação back-end.

## <a name="tags"></a>Etiquetas

No contexto de devcie gémeos, etiquetas são dispositivo meta-dados armazenados e obtido pela aplicação back-end a forma de um documento JSON. Etiquetas não estão visíveis para aplicações num dispositivo.

## <a name="system-properties"></a>Propriedades do sistema

No contexto de gémeos de dispositivo, propriedades do sistema são só de leitura em incluem informações relativas à utilização de dispositivo, como o último Estado de tempo e de ligação de atividade.