<properties
    pageTitle="Introdução ao evento concentradores no c# | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores com c# e utilizar o EventProcessorHost."
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
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um serviço que processa grandes quantidades de dados do evento (telemetria) a partir de dispositivos ligados e de aplicações. Depois de recolher dados para o evento concentradores, pode armazenar os dados utilizando um cluster de armazenamento ou transformá-lo utilizando um fornecedor de análise em tempo real. Esta capacidade de recolha e processamento de evento em grande escala é um componente da chave de arquitecturas de aplicação moderna incluindo Internet de coisas (IoT).

Este tutorial mostra como utilizar o portal de clássico Azure para criar um concentrador de evento. -Lo também mostra-lhe como recolher mensagens a um concentrador de evento utilizando uma aplicação de consola escrita c# e como obtê-las, paralelas ao utilizar a biblioteca c# [Anfitrião de processador de eventos][] .

Para concluir este tutorial, terá o seguinte procedimento:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Uma conta do Azure active. Se não tiver uma, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. A partir do Visual Studio, abra o projeto **Recetor** que criou anteriormente.

2. Solução **Recetor** , com o botão direito, em seguida, clique em **Adicionar**e, em seguida, clique em **Projeto existente**.
 
3. Localize o ficheiro Sender.csproj existente e, em seguida, faça duplo clique para adicioná-la para a solução.
 
4. Novamente, o botão direito do rato a solução **Recetor** e, em seguida, clique em **Propriedades**. É apresentada a página de propriedades do **destinatário** .

5. Clique em **Projeto de arranque**e, em seguida, clique no botão de **vários projetos de arranque** . Defina a caixa de **ação** para o **destinatário** e **remetente** projetos para **Iniciar**.

    ![][19]

6. Clique em **dependências de projeto**. Na caixa de **projetos** , clique em **remetente**. Na caixa **depende da** , certifique-se de **que auscultador** está selecionada.

    ![][20]

7. Clique em **OK** para fechar a caixa de diálogo **Propriedades** .

1.  Prima F5 para executar o projeto de **Recetor** a partir do Visual Studio, em seguida, aguarde para poder começar destinatários para todas as partições.

    ![][21]

2.  O projeto de **remetente** será executado automaticamente. Prima a tecla **Enter** na janela da consola e ver os eventos que são apresentados na janela do destinatário.

    ![][22]

Prima **Ctrl + C** na janela do **remetente** para terminar a aplicação de remetente, em seguida, prima **Enter** na janela do auscultador para encerrar dessa aplicação.

## <a name="next-steps"></a>Próximos passos

Agora que já tiver criado uma aplicação de trabalho que cria um concentrador de evento e envia e recebe dados, pode mover aos seguintes cenários:

- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .
- [Descrição geral de concentradores do evento][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Anfitrião de processador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
