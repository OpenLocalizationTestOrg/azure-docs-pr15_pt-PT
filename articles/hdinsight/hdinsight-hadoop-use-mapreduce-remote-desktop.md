<properties
   pageTitle="MapReduce e ambiente de trabalho remoto com Hadoop no HDInsight | Microsoft Azure"
   description="Saiba como utilizar o ambiente de trabalho remoto para ligar a Hadoop no HDInsight e executar tarefas MapReduce."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Utilizar MapReduce no Hadoop no HDInsight com o ambiente de trabalho remoto

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, irá obter informações sobre como ligar a um Hadoop num cluster de HDInsight utilizando o ambiente de trabalho remoto e, em seguida, executar tarefas MapReduce utilizando o comando Hadoop.

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um cluster de HDInsight baseados no Windows (Hadoop no HDInsight)

* Um computador cliente a executar o Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Ligar-se ao ambiente de trabalho remoto

Ativar o ambiente de trabalho remoto para HDInsight cluster, em seguida, ligar para o mesmo ao seguir as instruções em [ligar ao clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Utilize o comando Hadoop

Quando estiver ligado ao ambiente de trabalho para o cluster HDInsight, utilize os passos seguintes para executar uma tarefa de MapReduce utilizando o comando Hadoop:

1. A partir do ambiente de trabalho HDInsight, comece a **linha de comandos Hadoop**. Esta ação abre uma nova linha de comandos na **c:\apps\dist\hadoop-&lt;número da versão >** diretório.

    > [AZURE.NOTE] O número da versão alterada à medida Hadoop é atualizado. A variável de ambiente de **HADOOP_HOME** pode ser utilizada para localizar o caminho. Por exemplo, `cd %HADOOP_HOME%` altera diretórios para o directório de Hadoop, sem que seja necessário saber o número de versão.

2. Para utilizar o comando **Hadoop** para executar uma tarefa de MapReduce de exemplo, utilize o seguinte comando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Esta ação inicia a classe de **wordcount** , que está contida no ficheiro **hadoop-mapreduce-examples.jar** no diretório atual. Como entrada, utiliza o documento **wasbs://example/data/gutenberg/davinci.txt** e saída está guardada na: **wasbs: / / / exemplo/dados/WordCountOutput**.

    > [AZURE.NOTE] Para mais informações sobre esta tarefa MapReduce e os dados de exemplo, consulte o artigo <a href="hdinsight-use-mapreduce.md">Utilizar MapReduce no HDInsight Hadoop</a>.

2. A tarefa emite detalhes como esta é processada e devolve informações semelhante ao seguinte quando a tarefa está concluída:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando a tarefa estiver concluída, utilize o seguinte comando para listar os ficheiros de saída armazenados na **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Isto deve mostrar dois ficheiros, **_SUCCESS** e **Peça-r-00000**. O ficheiro de **Peça-r-00000** contém a exportação para esta tarefa.

    > [AZURE.NOTE] Algumas tarefas MapReduce podem dividir os resultados ao longo de vários ficheiros de **Peça-r-# # #** . Em caso afirmativo, utilize o # # # sufixo para indicar a ordem dos ficheiros.

4. Para ver os resultados, utilize o seguinte comando:

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Isto apresenta uma lista das palavras que estão contidas na ficheiro **wasbs://example/data/gutenberg/davinci.txt** , juntamente com o número de vezes que ocorreu de cada palavra. Segue-se um exemplo dos dados contidos no ficheiro:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Resumo

Como pode ver, o comando Hadoop fornece uma forma fácil de executar tarefas de MapReduce num HDInsight cluster e, em seguida, ver o resultado da tarefa.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais acerca de tarefas de MapReduce no HDInsight:

* [Utilizar o MapReduce no HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)
