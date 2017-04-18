<properties
   pageTitle="Ligação MapReduce e SSH com Hadoop no HDInsight | Microsoft Azure"
   description="Saiba como utilizar SSH para executar tarefas de MapReduce utilizando Hadoop no HDInsight."
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

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Utilizar MapReduce com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Este artigo vai aprender a utilizar Shell seguro (SSH) para ligar a um Hadoop num cluster de HDInsight e, em seguida, submeter tarefas de MapReduce através dos comandos Hadoop.

> [AZURE.NOTE] Se já estiver familiarizado com a utilização de servidores baseado em Linux Hadoop, mas estiver familiarizado HDInsight, consulte o artigo [sugestões baseado em Linux HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um cluster de baseado em Linux HDInsight (Hadoop no HDInsight)

* Um cliente SSH. Sistemas operativos Linux, Unix e Mac deve vêm com um cliente SSH. Utilizadores do Windows tem de transferir um cliente, como [betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Ligar-se a SSH

Ligar para o nome de domínio completamente qualificado (FQDN) do seu cluster HDInsight utilizando o comando SSH. O FQDN será o nome que lhe atribuiu cluster, seguido de **. azurehdinsight.net**. Por exemplo, o seguinte procedimento seria ligar a um cluster denominado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se lhe fornecidos uma chave de certificado para SSH autenticação** quando criou o cluster HDInsight, poderá ter especificar a localização da chave privada no sistema de cliente, por exemplo:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se lhe fornecidos uma palavra-passe para autenticação SSH** quando criou o cluster HDInsight, terá de fornecer a palavra-passe quando lhe for pedido.

Para mais informações sobre como utilizar SSH com HDInsight, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>Betumes (clientes Windows)

Windows não fornece um cliente SSH incorporado. Recomendamos que utilize **betumes**, que podem ser transferidos das [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para mais informações sobre como utilizar betumes, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Utilize os comandos Hadoop

1. Depois de estar ligado ao HDInsight cluster, utilize o seguinte comando **Hadoop** para iniciar uma tarefa MapReduce:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Esta ação inicia a classe de **wordcount** , que está contida num ficheiro **hadoop-mapreduce-examples.jar** . Como entrada, utiliza o documento **wasbs://example/data/gutenberg/davinci.txt** e saída está guardada na **wasbs: / / / exemplo/dados/WordCountOutput**.

    > [AZURE.NOTE] Para mais informações sobre esta tarefa MapReduce e os dados de exemplo, consulte o artigo [Utilizar MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. A tarefa emite detalhes como processa e devolve informações semelhante ao seguinte quando a tarefa estiver concluída:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando a tarefa estiver concluída, utilize o seguinte comando para listar os ficheiros de saída que estão armazenados na **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Isto deve mostrar dois ficheiros, **_SUCCESS** e **Peça-r-00000**. O ficheiro de **Peça-r-00000** contém a exportação para esta tarefa.

    > [AZURE.NOTE] Algumas tarefas MapReduce podem dividir os resultados ao longo de vários ficheiros de **Peça-r-# # #** . Em caso afirmativo, utilize o # # # sufixo para indicar a ordem dos ficheiros.

4. Para ver os resultados, utilize o seguinte comando:

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Isto apresenta uma lista das palavras que estão contidos no ficheiro de **wasbs://example/data/gutenberg/davinci.txt** e o número de vezes que ocorreu de cada palavra. Segue-se um exemplo dos dados contidos no ficheiro:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Resumo

Como pode ver, comandos de Hadoop fornecem uma forma fácil de executar tarefas de MapReduce num HDInsight cluster e, em seguida, ver o resultado da tarefa.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais acerca de tarefas de MapReduce no HDInsight:

* [Utilizar o MapReduce no HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)
