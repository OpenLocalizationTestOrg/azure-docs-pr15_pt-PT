<properties
 pageTitle="Azure soluções para Internet das coisas | Microsoft Azure"
 description="Uma descrição geral das IoT no Azure, incluindo uma arquitetura de solução de exemplo e como se relaciona com Azure IoT concentrador, SDK do dispositivo e soluções pré-configurado"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Próximos passos

Azure IoT concentrador é um serviço Azure que permite seguro e fiáveis bidirecional comunicações entre a sua aplicação novamente final e milhões de dispositivos. Permite-o aplicação back-end para:

- Receba telemetria à escala de seus dispositivos.
- Encaminhar os dados de seus dispositivos para um processador de eventos da cadeia.
- Receba carregamentos de ficheiros a partir de dispositivos.
- Envie comandos de dispositivo de nuvem para dispositivos específicos.

Pode utilizar IoT concentrador para implementar o seu próprio back-end da solução. Além disso, IoT concentrador inclui um registo de identidade do dispositivo utilizado para aprovisionar dispositivos, as respetivas credenciais de segurança e dos seus direitos para ligar para o concentrador. Para saber mais sobre IoT concentrador, consulte o artigo [o que é o Centro de IoT?] [lnk-iot-hub].

Para saber como o concentrador de IoT Azure permite baseadas em normas gestão de dispositivos IoT para o utilizador gerir remotamente, configurar e atualizar os seus dispositivos, consulte o artigo [Descrição geral do Azure IoT concentrador de tipos de gestão de dispositivos][lnk-device-management].

Para implementar aplicações de cliente numa grande variedade de sistemas operativos e plataformas de hardware do dispositivo, pode utilizar o dispositivo de IoT SDK. O dispositivo de IoT SDK incluir bibliotecas que facilitam o envio de telemetria a um concentrador de IoT e a receber na nuvem-a-dispositivo comandos. Quando utiliza os SDK, pode escolher a partir de várias protocolos de rede para comunicar com IoT concentrador. Para obter mais informações, consulte as [informações acerca do dispositivo SDK][lnk-device-sdks].

Para começar a escrever algum código e executar alguns exemplos, consulte o artigo [Introdução ao centro de IoT] [ lnk-getstarted] tutorial.

Também poderá estar interessado no [Conjunto de aplicações do Azure IoT][lnk-iot-suite], que é uma coleção de soluções pré-configurado. IoT conjunto de aplicações permite-lhe começar rapidamente e dimensionar IoT projetos para abordam cenários comuns de IoT – como monitorização remota, gestão de elementos e manutenção do aspeto do Office.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md