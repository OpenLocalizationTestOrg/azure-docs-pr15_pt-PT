<properties
    pageTitle="Introdução ao evento concentradores no c# | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores; Enviar como eventos no c# e recebê-los no Java utilizando o EventProcessorHost."
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
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um serviço que processa grandes quantidades de dados do evento (telemetria) a partir de dispositivos ligados e de aplicações. Depois de recolher dados para o evento concentradores, pode armazenar os dados utilizando um cluster de armazenamento ou transformá-lo utilizando um fornecedor de análise em tempo real. Esta capacidade de recolha e processamento de evento em grande escala é um componente da chave de arquitecturas de aplicação moderna incluindo Internet de coisas (IoT).

Este tutorial mostra como utilizar o portal de clássico Azure para criar um concentrador de evento. -Lo também mostra-lhe como recolher mensagens a um concentrador de evento utilizando uma aplicação de consola escrita c# e como obtê-las, paralelas ao utilizar a biblioteca anfitriã de processador de eventos Java.

Para concluir este tutorial, terá o seguinte procedimento:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Uma conta do Azure active. <br/>Se não tiver uma, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

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
- [Descrição geral de concentradores do evento][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
