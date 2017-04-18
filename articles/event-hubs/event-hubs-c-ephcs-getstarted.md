<properties
    pageTitle="Introdução ao evento concentradores C e c# | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores; enviar eventos por C e receber hem no c# utilizando o EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um sistema de ingestão altamente dimensionáveis que pode incorporação milhões de eventos por segundo, ativar uma aplicação processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Assim que recolhidos concentradores evento, pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte o artigo [Descrição geral de concentradores do evento][].

Neste tutorial vai aprender para ingerir esta última mensagens a um concentrador de evento utilizar uma aplicação de consola no C e para obtê-las, paralelas ao utilizar a biblioteca c# [Anfitrião de processador de eventos][] .

Para concluir este tutorial, terá o seguinte procedimento:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos irá assumir a pilha de gcc num [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04. Instruções para outros ambientes serão fornecidas ligações externas.

+ Microsoft Visual Studio Express para Windows

+ Uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  Executar o projeto de **Recetor** a partir do Visual Studio, em seguida, aguarde para poder começar destinatários para todas as partições.

    ![][21]

2.  Executar o programa do **remetente** e ver os eventos que são apresentados na janela do destinatário.

    ![][24]

## <a name="next-steps"></a>Próximos passos

Agora que já tiver criado uma aplicação de trabalho que cria um concentrador de evento e envia e recebe dados, pode mover aos seguintes cenários:

- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .
- [Descrição geral de concentradores do evento][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Anfitrião de processador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
