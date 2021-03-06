<properties
    pageTitle="Introdução ao evento concentradores com C e Apache tempestade | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Azure evento concentradores; enviar eventos por C e recebê-los num cluster Apache tempestade."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="java"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introdução ao evento concentradores

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Evento concentradores é um sistema de ingestão altamente dimensionáveis que pode incorporação milhões de eventos por segundo, ativar uma aplicação processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Assim que recolhidos concentradores evento, pode transformar e armazenar dados utilizando qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte o artigo [Descrição geral de evento concentradores].

Neste tutorial vai aprender para ingerir esta última mensagens a um concentrador de evento utilizar uma aplicação de consola no C e para obtê-las em paralelo utilizando Apache tempestade.

Para concluir este tutorial, terá o seguinte procedimento:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos irá assumir a pilha de gcc num [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04. Instruções para outros ambientes serão fornecidas ligações externas.

+ Ambiente de desenvolvimento de Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos irá assumir [Eclipse](https://www.eclipse.org/).

+ Uma conta do Azure active. Se não tiver uma conta, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  Executar a classe de **LogTopology** a partir do Eclipse, em seguida, aguarde para poder começar destinatários para todas as partições.

2.  Executar o programa do **remetente** e ver os eventos que são apresentados na janela do destinatário.

    ![][23]

> [AZURE.NOTE] Apenas neste tutorial, utiliza tempestade no modo de local para fins de desenvolvimento. Consulte a [Descrição geral de HDInsight tempestade] e a documentação [Apache tempestade] oficial para obter mais informações de implementações tempestade e padrões.

## <a name="next-steps"></a>Próximos passos

Os seguintes recursos estão disponíveis para desenvolver aplicações integrar o evento concentradores e tempestade.

- [Analisar dados de sensor com tempestade e HDInsight][] é um tutorial cenário completo através do evento concentradores, tempestade e HBase para ingerir esta última dados sensor num Hadoop cluster.
- [Transmissão de desenvolver aplicações de processamento de dados com SCP.NET e c# tempestade e HDInsight][] é um tutorial sobre como escrever tubagens tempestade utilizando c#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Descrição geral de concentradores do evento]: event-hubs-overview.md

[Apache tempestade]: https://storm.incubator.apache.org
[Descrição geral de tempestade HDInsight]: ../hdinsight/hdinsight-storm-overview.md/
[Analisar dados de sensor com tempestade e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desenvolver a transmissão aplicações de processamento de dados com SCP.NET e c# tempestade e HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
