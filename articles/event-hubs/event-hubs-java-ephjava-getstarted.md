<properties
    pageTitle="Introdução ao evento concentradores em Java | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores; eventos com Java a enviar e recebê-los no utilizando o EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="core"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um sistema de ingestão altamente dimensionáveis que pode incorporação milhões de eventos por segundo, ativar uma aplicação processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Assim que recolhidos concentradores evento, pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte o artigo [Descrição geral de evento concentradores][].

Este tutorial mostra como para ingerir esta última mensagens a um concentrador de evento utilizando uma aplicação de consola Java e para obtê-las, paralelas ao utilizar a biblioteca anfitriã de processador de eventos Java.

Para poder concluir este tutorial, terá o seguinte procedimento:

+ Um ambiente de desenvolvimento Java. Para este tutorial, vamos irá assumir [Eclipse](https://www.eclipse.org/).

+ Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Versão de avaliação gratuita do Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  Execute o projecto **Recetor** .

    ![][21]

2.  Execute o projecto do **remetente** .

    ![][22]

## <a name="next-steps"></a>Próximos passos

Agora que já tiver criado uma aplicação de trabalho que cria um concentrador de evento e envia e recebe dados, pode mover aos seguintes cenários:

- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .

Para mais informações, consulte o [Centro de programadores do Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-java-ephjava-getstarted/java-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 