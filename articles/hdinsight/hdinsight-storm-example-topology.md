<properties
 pageTitle="Topologias Apache tempestade de exemplo no HDInsight | Microsoft Azure"
 description="Uma lista das topologias tempestade de exemplo criadas e testadas com Apache tempestade no HDInsight incluindo topologias c# e Java básicas e trabalhar com concentradores evento."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo tempestade toplogies e componentes para Apache tempestade no HDInsight

Segue-se uma lista dos exemplos criadas e mantidas pela Microsoft para utilização com Apache tempestade no HDInsight. Estes exemplos de folha de rosto uma variedade de tópicos, a partir de criar básicas c# e topologias Java para trabalhar com Azure serviços como o evento concentradores, DocumentDB, do Power BI, base de dados SQL, HBase HDInsight e armazenamento do Windows Azure. Alguns exemplos demonstram também como trabalhar com as tecnologias não Azure ou até mesmo que não sejam da Microsoft, tais como SignalR e Socket.IO

| Descrição                                                                                             | Demonstra                                         | Idioma/Framework         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Escrita arquivo de Lake Azure dados a partir do Apache tempestade](hdinsight-storm-write-data-lake-store.md) | Escrever ao arquivo de Lake de dados do Azure | Java |
| [Evento concentrador Spout e raio de origem](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Origem para o evento concentrador vareta e raio | Java |
| [Desenvolver com base em Java topologias para Apache tempestade no HDInsight][5797064f]                                 | Maven                                                | Java                       |
| [Desenvolver c# topologias para Apache tempestade no HDInsight utilizando o Visual Studio][16fce2d1]                     | HDInsight ferramentas para o Visual Studio                    | C#, Java                   |
| [Criar vários fluxos de dados numa topologia de c# tempestade][ec5a4064]                                         | Vários fluxos                                     | C#                         |
| [Determinar Twitter tópicos tendência com tempestade no HDInsight][3c86c7c8]                                   | Trident                                              | Java, Trident              |
| [Eventos de processo a partir do Azure evento concentradores com tempestade no HDInsight (c#)][844d1d81]                                | Concentradores de evento                                           | C# e Java                |
| [Eventos de processo a partir do Azure evento concentradores com tempestade no HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Concentradores de evento | Java |
| [Utilizar o Power Bi para visualização desses dados a partir de uma topologia de tempestade][94d15238]                              | Power BI                                             | C#                         |
| [Analisar dados de sensor com tempestade e HBase no HDInsight][ab894747]                                     | Evento concentradores, HBase, Socket.IO, dashboard da Web          | C#, Java, JavaScript, HTML |
| [Processar veículo sensor dados a partir do evento concentradores com tempestade no HDInsight][246ee964]                        | Evento concentradores, DocumentDb, Azure armazenamento Blob (WASB)    | C#, Java                   |
| [Extrair, transformação e carga (ETL) a partir do Azure evento concentradores para HBase, utilizar tempestade num HDInsight][b4b68194] | Evento concentradores, HBase                                    | C#                         |
| [Projecto de topologia c# tempestade modelo para trabalhar com os serviços do Azure a partir do tempestade no HDInsight][ce0c02a2]  | Evento concentradores, DocumentDb, SQL base de dados, HBase, SignalR | C#, Java                   |
| [Referências de escalabilidade para ler a partir do Azure evento concentradores com tempestade no HDInsight][d6c540e3]           | Produtividade das mensagens, eventos concentradores, base de dados SQL         | C#, Java                   |
| [Correlacionar eventos utilizar tempestade e HBase num HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Utilizar Python com tempestade no HDInsight](hdinsight-storm-develop-python-topology.md) | Componentes de Python com Java e Clojure com base topologias tempestade | Python |

## <a name="next-steps"></a>Próximos passos

* [Introdução ao Apache tempestade no HDInsight][2b8c3488]

* [Saiba como implementar e gerir topologias tempestade com tempestade no HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Saiba como criar uma tempestade num cluster de HDInsight e utilizar o Dashboard tempestade para implementar topologias de exemplo."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Saiba como implementar e gerir topologias com o Dashboard de tempestade baseada na web e tempestade IU ou as ferramentas de HDInsight para Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Saiba como criar topologias c# tempestade utilizando as ferramentas de HDInsight para Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Saiba como criar topologias tempestade no Java, utilizar Maven, através da criação de uma topologia de wordcount básica."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Demonstra como escrever dados ao Power BI a partir de uma topologia de c#, em seguida, criar um dashboard e um gráfico a partir dos dados."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Demonstra uma topologia de tempestade básica que efetua uma wordcount, implementada no c#. Isto também demonstra como criar sequências de dados de múltiplas dentro de uma topologia de c#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Saiba como ler e escrever dados a partir do Azure evento concentradores com tempestade no HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Saiba como utilizar Apache tempestade no HDInsight para processar sensor dados a partir do Azure evento concentradores, compreendê-lo utilizando D3.js e (opcionalmente), armazene-o para HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Saiba como utilizar Trident para criar uma topologia de tempestade que determina tópicos tendência (com base na hashtags,) no Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Saiba como utilizar uma topologia de tempestade para ler as mensagens a partir do Azure evento concentradores, ler documentos a partir do Azure DocumentDB para fazer referência a dados e guardar dados de armazenamento do Windows Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar débito quando leitura a partir do Azure evento concentradores e guardando a base de dados SQL com Apache tempestade no HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados a partir do Azure concentradores de evento, Agregar e transformar os dados, em seguida, guarde-a para HBase no HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para spouts, parafusos e topologias para interagir com vários serviços Azure como concentradores de evento, DocumentDB e base de dados SQL."
 
