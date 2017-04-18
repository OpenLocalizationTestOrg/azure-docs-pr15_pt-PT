<properties
    pageTitle="O que é o Azure evento concentradores? | Microsoft Azure"
    description="Descrição geral e a descrição do Azure evento concentradores"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# <a name="what-is-azure-event-hubs"></a>O que é o Azure evento concentradores?

Azure concentradores de evento é um serviço de penetração altamente dimensionáveis dados que pode ingerir esta última milhões de eventos por segundo para que possa processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Evento concentradores age como "porta da frente" para uma pipeline de evento, e assim que os dados recolhidos a um concentrador de evento, podem ser transformado e armazenadas utilizando qualquer fornecedor de análise em tempo real ou adaptadores de lotes/armazenamento. Evento concentradores decouples produção de uma sequência de eventos do consumo desses eventos, para que os consumidores de evento possam aceder os eventos na sua própria agenda. Para obter mais informações e detalhes técnicos, consulte o artigo [Descrição geral de evento concentradores](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Capacidades de concentradores de evento

Evento concentradores é um evento serviço que fornece o evento e processamento de telemetria na escala grandes, com baixa latência e fiabilidade alta de processamento. Este serviço é especialmente útil para:

- Instrumentação de aplicação
- Experiência de utilizador ou o processamento de fluxo de trabalho
- Cenários de Internet coisas (IoT)

Algumas outras capacidades de evento concentradores chaves incluem o comportamento de controlo no aplicações móveis, tráfego informações a partir de web farms, captura no jogo evento na consola jogos ou telemetria recolhidas a partir do Máquinas industriais ou veículos ligados.

## <a name="next-steps"></a>Próximos passos

Para obter informações detalhadas sobre concentradores de evento, consulte os tópicos seguintes.

- [Descrição geral de concentradores do evento](event-hubs-overview.md)
- [Guia de programação de concentradores de evento](event-hubs-programming-guide.md)
- [Evento concentradores FAQ de disponibilidade e suporte](event-hubs-availability-and-support-faq.md)
- Começar com um [evento concentradores tutorial][]
- Uma [aplicação de exemplo que utiliza o evento concentradores][] de concluída

[Tutorial do evento concentradores]: event-hubs-csharp-ephcs-getstarted.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
