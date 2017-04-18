<properties
 pageTitle="Processar dados sensor de veículo com Apache tempestade no HDInsight | Microsoft Azure"
 description="Saiba como processar veículo sensor dados a partir do evento concentradores com Apache tempestade no HDInsight. Adicionar dados de modelo de DocumentDB e armazenar saída ao armazenamento."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Processar veículo sensor dados a partir do Azure evento concentradores com Apache tempestade no HDInsight

Saiba como processar veículo sensor dados a partir do Azure evento concentradores com Apache tempestade no HDInsight. Este exemplo lê sensor dados a partir do Azure evento concentradores, enriquece os dados ao referenciar dados armazenados num Azure DocumentDB e finalmente armazena os dados para o armazenamento do Windows Azure utilizando o sistema de ficheiro Hadoop (HDFS).

![HDInsight e o diagrama de arquitectura de Internet coisas (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>Descrição geral

Adicionar sensores a veículos permite-lhe prever com base em tendências de dados de histórico de problemas de equipamento, bem como efetuar melhorias ao versões futuras com base na análise de padrão de utilização. Enquanto processamento tradicional em lotes MapReduce pode ser utilizado para esta análise, tem de ser capaz de forma rápida e eficiente carregar os dados a partir de todos os veículos para Hadoop antes de processamento de MapReduce pode ocorrer. Para além disso, pode optar por fazer análise para caminhos críticos falha (temperatura motor, travões, etc.) em tempo real.

Azure evento concentradores foram criados para processar o volume grandes de dados gerados pelo sensores e Apache tempestade no HDInsight podem ser utilizada para carregar e processar os dados antes de os guardar HDFS (cópias ao armazenamento do Windows Azure) para processamento de MapReduce adicionais.

##<a name="solution"></a>Solução

Dados de telemetria para temperatura motor, temperatura e velocidade de veículo são registados por sensores, em seguida, enviadas para o evento concentradores juntamente com veículo identificação número (VIN o carro) e um carimbo de data / hora. A partir daí, uma topologia de tempestade em execução numa tempestade Apache num cluster de HDInsight lê os dados, processa-lo e armazena HDFS.

Durante o processamento, o NIV é utilizado para obter informações sobre o modelo a partir do Azure DocumentDB. Este valor é adicionado ao fluxo de dados antes de está armazenado.

Os componentes utilizados na topologia de tempestade são:

* **EventHubSpout** - lê dados a partir do Azure evento concentradores

* **TypeConversionBolt** - converte a cadeia JSON a partir do evento concentradores numa cadeia de identificação que contém os valores de dados individuais para temperatura motor, temperatura, velocidade, VIN e data/hora

* **DataReferencBolt** - procura o modelo de veículo a partir do DocumentDB utilizando o NIV

* **WasbStoreBolt** - armazena os dados para HDFS (Azure armazenamento)

Um diagrama desta solução é o seguinte:

![topologia tempestade](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Este é um diagrama simplificado e cada componente da solução poderá ter várias instâncias. Por exemplo, várias instâncias de cada componente na topologia são distribuídas por nós tempestade num cluster de HDInsight.

##<a name="implementation"></a>Pós-implementação

Concluído, automatizado solução para este cenário está disponível como parte do repositório [Exemplos de tempestade HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) no GitHub. Para utilizar neste exemplo, siga os passos na [IoTExample ficheiro Leia-me. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Próximos passos

Para obter mais topologias de tempestade de exemplo, consulte o artigo [topologias de exemplo para tempestade no HDInsight](hdinsight-storm-example-topology.md).
