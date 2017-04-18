<properties
    pageTitle="Introdução ao evento concentradores no C | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores; enviar eventos por C e recebê-los no Java utilizando o anfitrião de processador de eventos."
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
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um sistema de ingestão altamente dimensionáveis que pode incorporação milhões de eventos por segundo, ativar uma aplicação processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Assim que recolhidos concentradores evento, pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte o artigo [Descrição geral de evento concentradores][].

Neste tutorial vai aprender para ingerir esta última mensagens a um concentrador de evento utilizar uma aplicação de consola no C e para obtê-las, paralelas ao utilizar a biblioteca c# [Anfitrião de processador de eventos][] .

Para poder concluir este tutorial terá o seguinte procedimento:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos irá assumir a pilha de gcc num [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04. Instruções para outros ambientes serão fornecidas ligações externas.

+ Microsoft Visual Studio Express para Windows

+ Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Versão de avaliação gratuita do Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  Execute o projecto **Recetor** .

    ![][21]

2.  Executar o programa do **remetente** e repare os eventos que são apresentados na janela do destinatário.

    ![][24]

## <a name="next-steps"></a>Próximos passos

Agora que já tiver criado uma aplicação de trabalho que cria um concentrador de evento e envia e recebe dados, pode mover aos seguintes cenários:

- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .
- [Descrição geral de concentradores do evento][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Anfitrião de processador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
