<properties
    pageTitle="Introdução ao evento concentradores no c# com Apache tempestade | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores; Enviar como eventos no c# e recebê-los num cluster Apache tempestade."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um sistema de ingestão altamente dimensionáveis que pode incorporação milhões de eventos por segundo, ativar uma aplicação processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Assim que recolhidos concentradores evento, pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte o artigo [Descrição geral de evento concentradores].

Neste tutorial vai aprender para ingerir esta última mensagens a um concentrador de evento utilizar uma aplicação de consola no c# e para obtê-las em paralelo utilizando Apache tempestade.

Para poder concluir este tutorial terá o seguinte procedimento:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Ambiente de desenvolvimento de Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos irá assumir [Eclipse](https://www.eclipse.org/)

+ Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Versão de avaliação gratuita do Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  Executar a classe de **LogTopology** a partir do Eclipse, em seguida, aguarde para poder começar destinatários para todas as partições.

2.  Execute o projecto **remetente** , prima **Enter** na janela da consola e ver os eventos que são apresentados na janela do destinatário.

    ![][22]

## <a name="next-steps"></a>Próximos passos

Agora que já tiver criado uma aplicação de trabalho que cria um concentrador de evento e envia e recebe dados, pode mover aos seguintes cenários:

- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 