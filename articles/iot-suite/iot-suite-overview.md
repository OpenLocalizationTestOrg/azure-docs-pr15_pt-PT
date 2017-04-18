<properties
    pageTitle="Descrição geral do conjunto de aplicações do Microsoft Azure IoT | Microsoft Azure"
    description="Descrição geral de como o conjunto de aplicações do Azure IoT fornece internet de soluções pré-configurado coisas para recolher, analisar e armazenar dados, fornecer visualizações e integrar com outros sistemas de."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>O que é o conjunto de aplicações do Azure IoT?

Internet Azure dos serviços de coisas (IoT) fornecem uma vasta gama de funcionalidades. Estes serviços de classe enterprise permitem-lhe:

- Recolher dados a partir de dispositivos
- Analisar dados sequências de movimento
- Armazenar e grandes conjuntos de dados da consulta
- Visualizar dados em tempo real e históricos
- Integrar com os sistemas de anterior do office

Para a prestação estas capacidades, pacotes de conjunto de aplicações do Azure IoT em conjunto vários serviços Azure com as extensões personalizadas como *soluções pré-configurado*. Estas soluções pré-configurado são base implementações do padrões de solução IoT comuns que o ajudam a reduzir o tempo de que tomar para entregar a sua IoT soluções. Utilizar os [kits de desenvolvimento de software IoT][lnk-sdks], pode personalizar e expandir estas soluções aos seus requisitos. Também pode utilizar estas soluções como modelos ou exemplos quando está a desenvolver soluções de IoT novas.

O vídeo seguinte fornece uma introdução ao conjunto de aplicações do Azure IoT:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT serviços no conjunto de aplicações do Azure IoT

As soluções pré-configurado, normalmente, utilizam os seguintes serviços:

- Principais ao conjunto de aplicações do Azure IoT são o [Azure IoT concentrador] [ lnk-iot-hub] serviço. Este serviço fornece as capacidades de mensagens do dispositivo para nuvem e na nuvem para o dispositivo e age como o gateway para a nuvem e outros serviços IoT Suite chaves. O serviço permite-lhe receber mensagens dos dispositivos de escala e enviar comandos para os seus dispositivos.

- [Azure a análise da cadeia] [ lnk-asa] fornece uma análise de dados em movimento. Conjunto de aplicações do IoT tira partido deste serviço para processar telemetria recebida, executar a agregação e detetar eventos. As soluções pré-configurado também utilizam a análise da cadeia para processar informativas mensagens que contêm dados, tais como metadados ou comando respostas a partir de dispositivos. As soluções utilizam a análise da cadeia para processar as mensagens a partir do seu dispositivos e transmitir a essas mensagens a outros serviços.

- [Armazenamento Azure] [ lnk-azure-storage] e [Azure DocumentDB] [ lnk-document-db] fornecem as capacidades de armazenamento de dados. As soluções pré-configurado utilizam armazenamento de BLOBs para armazenar telemetria e para o tornar disponível para uma análise. As soluções utilizam DocumentDB para armazenar metadados de dispositivo e ativar as capacidades de gestão de dispositivo as soluções.

- [Azure Web Apps] [ lnk-web-apps] e o [Microsoft Power BI] [ lnk-power-bi] fornecem as capacidades de visualização de dados. A flexibilidade do Power BI permite-lhe criar rapidamente a suas própria dashboards interativos que utilizem dados IoT conjunto de aplicações.

Para obter uma descrição geral da arquitetura de uma solução IoT típica, consulte o artigo [do Microsoft Azure e Internet de coisas (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Soluções pré-configurado

Conjunto de aplicações do IoT inclui pré-configurado soluções que permitem-lhe rapidamente começar com e para explorar os cenários IoT comuns, tais como *monitorização remota* e *Manutenção do aspeto do Office*, que torna possível a conjunto de aplicações do Azure IoT. Pode implementar estas soluções à sua subscrição do Azure e, em seguida, execute um cenário de IoT completo e de fim para fim.

## <a name="next-steps"></a>Próximos passos

Agora que tem uma descrição geral do que pode fazer IoT conjunto de aplicações e quais são os componentes principais, pode obter mais informações sobre as soluções pré-configurado no IoT conjunto de aplicações, consulte o artigo [quais são as IoT Azure soluções pré-configuradas?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
