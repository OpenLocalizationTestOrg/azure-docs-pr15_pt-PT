<properties
   pageTitle="Azure gateway de protocolo IoT | Microsoft Azure"
   description="Descreve como utilizar o gateway de protocolo Azure IoT para expandir as capacidades e suporte do protocolo de Azure IoT concentrador."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Suporte protocolos adicionais para IoT concentrador

Azure IoT concentrador suporta vierem comunicação através dos protocolos MQTT, AMQP e HTTP. Em alguns casos, dispositivos ou gateways campo poderão não conseguir utilizar um destes protocolos padrão em irão necessitar adaptação protocolo. Nestes casos, pode utilizar um gateway personalizado. Um gateway personalizado pode ativar a adaptação de protocolo para os pontos finais de IoT concentrador por pontes o tráfego para e a partir do IoT Hub. Pode utilizar o [gateway de protocolo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para permitir a adaptação de protocolo para IoT concentrador.

## <a name="azure-iot-protocol-gateway"></a>Azure gateway de protocolo IoT

O gateway de protocolo Azure IoT é um quadro para adaptação protocolo que foi concebido para alta escala, comunicação de dispositivo bidireccional com IoT concentrador. O gateway protocolo é um componente de pass-through aceita ligações de dispositivos ao longo de um protocolo específico. -Preenche o tráfego para IoT concentrador sobre AMQP 1.0. O gateway de protocolo Azure IoT está disponível como um projeto de origem de abrir o software para fornecer flexibilidade para adicionar suporte para uma variedade de protocolos e versões de protocolo.

Pode implementar o gateway protocolo no Azure de uma forma altamente dimensionável utilizando funções de trabalho de serviços em nuvem Azure. Além disso, o gateway protocolo pode ser implementado em ambientes no local, como os gateways de campo.

O gateway de protocolo Azure IoT inclui uma placa de protocolo MQTT permite-lhe personalizar o comportamento de protocolo MQTT, se necessário. Uma vez que IoT concentrador fornece suporte incorporado para o protocolo de v3.1.1 MQTT, deverá apenas considerar a utilização da placa de protocolo MQTT se tiver uma necessidade de personalizações de protocolo ou requisitos específicos para funcionalidades adicionais.

O adaptador MQTT também demonstra o modelo para criar adaptadores de protocolo para outros protocolos de programação. Além disso, o modelo de programação de gateway do Azure IoT protocolo permite-lhe Plug-in componentes personalizados para processamento especializado como autenticação personalizada, transformações de mensagem, compressão/descompressão ou encriptação/desencriptar de tráfego entre dispositivos e IoT concentrador.

Para flexibilidade, o gateway protocolo e a implementação de MQTT são fornecidos num projeto de software de abrir origem. Esta opção permite-lhe personalizar a implementação, conforme necessário.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o gateway de protocolo Azure IoT e como utilizar e implementá-lo como parte da sua solução IoT, consulte o artigo:

* [Azure IoT protocolo gateway no repositório na GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia do Programador de gateway de protocolo do Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre como planear a sua implementação IoT concentrador, consulte:

- [Comparar com concentradores de evento][lnk-compare]
- [Dimensionamento, HA e DR][lnk-scaling]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
