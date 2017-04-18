<properties
 pageTitle="Guia do programador - IoT concentrador SDK | Microsoft Azure"
 description="Azure IoT concentrador programador guia - informações sobre e ligações para dos vários SDK de dispositivo e serviço de Azure IoT concentrador."
 services="iot-hub"
 documentationCenter=""
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

# <a name="iot-hub-sdks"></a>Concentrador IoT SDK

## <a name="iot-hub-device-sdks"></a>Dispositivo IoT concentrador SDK

O dispositivo do Microsoft Azure IoT SDK conter código que facilita a edifício dispositivos e aplicações que ligam e são geridas pelo Azure IoT concentrador services.

O seguinte dispositivo IoT SDK está disponível para transferir do GitHub:

- [Azure dispositivo IoT SDK para C] [ lnk-c-device-sdk] escrito ANSI C (C99) para portabilidade e compatibilidade de plataforma abrangentes.
- [Azure dispositivo IoT SDK para .NET][lnk-dotnet-device-sdk]
- [Azure dispositivo IoT SDK para Java][lnk-java-device-sdk]
- [Azure dispositivo IoT SDK para Node.js][lnk-node-device-sdk]
- [SDK do Microsoft Azure IoT dispositivo para Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Consulte os ficheiros Leia-me nos repositórios GitHub para obter informações sobre como utilizar o idioma e gestores de pacote específico da plataforma para instalar binários e dependências no seu computador de desenvolvimento.

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilidade de hardware e plataformas de SO

Para obter mais informações sobre a compatibilidade SDK com dispositivos de hardware específicos, consulte o artigo [Azure certificados para o catálogo de dispositivo IoT][lnk-certified].

## <a name="iot-hub-service-sdks"></a>Concentrador IoT serviço SDK

Os SDK de serviço do Microsoft Azure IoT contenham código que facilita a criar aplicações que interagem diretamente com IoT concentrador para gerir dispositivos e segurança.

O serviço de IoT SDK seguinte estão disponíveis para transferir do GitHub:

- [Azure service IoT SDK para .NET][lnk-dotnet-service-sdk]
- [Azure service IoT SDK para Node.js][lnk-node-service-sdk]
- [Azure service IoT SDK para Java][lnk-java-service-sdk]

> [AZURE.NOTE] Consulte os ficheiros Leia-me nos repositórios GitHub para obter informações sobre como utilizar o idioma e gestores de pacote específico da plataforma para instalar binários e dependências no seu computador de desenvolvimento.

## <a name="azure-iot-gateway-sdk"></a>Azure IoT Gateway SDK

Este SDK do Azure IoT Gateway contém a infraestrutura e módulos para criar soluções de gateway IoT. É possível expandir o SDK para criar gateways obtidas especialmente para qualquer cenário de fim para fim.

Pode transferir o [Azure IoT Gateway SDK] [ lnk-gateway-sdk] a partir do GitHub.

## <a name="online-api-reference-documentation"></a>Documentação da referência API online

Segue-se uma lista de ligações a documentação online de referência de API para dispositivo Azure IoT, o serviço e bibliotecas de gateway:

- [Internet das coisas (IoT) .NET][lnk-dotnet-ref]
- [Concentrador IoT resto][lnk-rest-ref]
- [Azure dispositivo IoT SDK para C][lnk-c-ref]
- [Azure dispositivo IoT SDK para Java][lnk-java-ref]
- [Azure service IoT SDK para Java][lnk-java-service-ref]
- [Azure dispositivo IoT SDK para Node.js][lnk-node-ref]
- [Azure service IoT SDK para Node.js][lnk-node-service-ref]
- [Azure IoT gateway SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Próximos passos

Outros tópicos de referência neste guia do Programador de IoT concentrador incluem:

- [Pontos finais de IoT concentrador][lnk-devguide-endpoints]
- [Linguagem de consulta para gémeos, métodos e tarefas][lnk-devguide-query]
- [Quotas e limitação][lnk-devguide-quotas]
- [Suporte de IoT concentrador MQTT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md