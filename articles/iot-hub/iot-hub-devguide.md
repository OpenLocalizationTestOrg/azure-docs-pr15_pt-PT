<properties
 pageTitle="Tópicos de guia do programador para IoT concentrador | Microsoft Azure"
 description="Guia do programador IoT concentrador Azure que inclui os pontos finais de IoT concentrador, segurança, registo de identidade do dispositivo, gestão de dispositivos e mensagens"
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

# <a name="azure-iot-hub-developer-guide"></a>Guia de programador do Azure IoT concentrador

Azure IoT concentrador é um serviço totalmente gerido que ajuda a ativar fiáveis e seguras as bidirecional comunicações entre milhões de IoT dispositivos e de fim de uma aplicação novamente.

Azure IoT concentrador fornece-lhe:

* Proteger as comunicações utilizando as credenciais de segurança por dispositivo e controlo de acesso.
* Fiável nuvem de dispositivo e na nuvem-a-dispositivo hyper escala envio de mensagens.
* Conectividade de dispositivo fácil com bibliotecas do dispositivo para os idiomas e plataformas mais populares.

Este guia do Programador de IoT concentrador inclui os seguintes artigos:

- [Enviar e receber mensagens com IoT concentrador] [ devguide-messaging] descreve as funcionalidades mensagens (nuvem de dispositivo e na nuvem-a-dispositivo) que expõe IoT concentrador. Este artigo também inclui informações sobre tópicos como formatos de mensagens e os protocolos de comunicações suportados e os números de porta utilizam.
- [Carregar ficheiros a partir de um dispositivo] [ devguide-upload] descreve como pode carregar ficheiros a partir de um dispositivo. Este artigo também inclui informações sobre tópicos como as notificações, pode enviar o processo de uplaod.
- [Gestão de identidades do dispositivo no Centro de IoT] [ devguide-identities] descreve o que armazena de registo de identidade do dispositivo do concentrador de cada IoT informações e como pode aceder e modificá-lo.
- [Controlar o acesso a IoT concentrador] [ devguide-security] descreve o modelo de segurança utilizado para conceder acesso a funcionalidade de IoT concentrador para ambos os dispositivos e na nuvem componentes. Este artigo inclui informações sobre como utilizar tokens e certificados x. 509 e detalhes das permissões que pode conceder.
- [Utilizar gémeos dispositivo para sincronizar distrito e configurações] [ devguide-device-twins] descreve o conceito de *duplos de dispositivo* e a funcionalidade que expõe como sincronizar um dispositivo com o seu duplos. Este artigo inclui informações sobre os dados armazenados num duplos dispositivo.
- [Invocar um método direto num dispositivo] [ devguide-directmethods] descreve o ciclo de vida de um método direto, informações sobre como invocar métodos num dispositivo da sua aplicação de back-end e gerir o método directo no seu dispositivo.
- [Agendar tarefas em múltiplos dispositivos] [ devguide-jobs] descreve como pode agendar tarefas em múltiplos dispositivos. Este artigo descreve como pretende submeter as tarefas que desempenhar tarefas como executar um método direto, atualizar um devcie utilizando um duplos devcie. Também descreve como consultar o estado de uma tarefa.
- [Referência - os pontos finais de IoT concentrador] [ devguide-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão. Também o artigo descreve como pode utilizar um gateway de campo para ativar a alguns dispositivos ligar à sua pontos finais IoT concentrador.
- [Referência - linguagem de consulta para tarefas, métodos e gémeos] [ devguide-query] descreve esse idioma de consulta que permite para recuperar informações a partir do seu centro sobre as suas gémeos de dispositivo e tarefas.
- [Referência - quotas e limitação] [ devguide-quotas] resume as quotas de definir o serviço de IoT concentrador e o comportamento de optimização que pode esperar quando uma quota excedem.
- [Referência - dispositivo e serviço SDK] [ devguide-sdks] listas dos SDK pode utilizar desenvolver aplicações de serviço e dispositivo que interagem com o seu centro IoT. Este artigo inclui ligações para documentação API online.
- [Referência - suporte IoT concentrador MQTT] [ devguide-mqtt] fornece informações detalhadas sobre como o concentrador IoT suporta o protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT incorporado para os SDK e fornece informações sobre como utilizar o protocolo MQTT diretamente.
- [Glossário do] [ devguide-glossary] uma lista de termos relacionados com IoT concentrador comuns.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

