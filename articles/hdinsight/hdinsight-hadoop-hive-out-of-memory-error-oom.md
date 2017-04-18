<properties
    pageTitle="Fora do erro de memória (OOM) - definições de ramo | Microsoft Azure"
    description="Corrigir uma erro de memória esgotada (OOM) a partir de uma consulta de ramo no Hadoop no HDInsight. O cenário de cliente é uma consulta em muitas tabelas grandes."
    keywords="terminar as definições de ramo de erro, OOM, de memória"
    services="hdinsight"
    documentationCenter=""
    authors="rashimg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="rashimg;jgao"/>

# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Corrigir um erro de fora do memória (OOM) com definições de memória ramo no Hadoop no Azure HDInsight

Um dos problemas comuns nominal os nossos clientes está a receber um erro de fora do memória (OOM) quando utilizar Hive. Este artigo descreve um cenário de cliente e as definições de ramo que é recomendável para corrigir o problema.

## <a name="scenario-hive-query-across-large-tables"></a>Cenário: Hive consulta em tabelas grandes

Um cliente executou a consulta abaixo utilizando ramo.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Algumas das nuances desta consulta:

* T1 é um alias de uma tabela grande, tabela1, que tem vários tipos de coluna de cadeia.
* Outras tabelas estão não que grande mas tiver um grande número de colunas.
* Todas as tabelas irão aderir entre si, em alguns casos com várias colunas de tabela1 e outros.

Quando o cliente executou a consulta utilizando ramo no MapReduce num nó 24 A3 cluster, a consulta ficou em cerca de 26 minutos. O cliente apercebeu-se as seguintes mensagens de aviso quando a consulta foi executada com ramo no MapReduce:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Uma vez que a consulta terminar a execução em cerca de 26 minutos, o cliente ignorados estes avisos e em vez disso, ao focar-se em como melhorar a esta ainda mais a desempenho da consulta.

O cliente consultado [otimizar Hive consultas para Hadoop no HDInsight](hdinsight-hadoop-optimize-hive-query.md)e decidido utilizar Tez motor de execução. Assim que a mesma consulta foi executada com a definição de Tez ativada a consulta executou para 15 minutos e, em seguida, emitiu o seguinte erro:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

O cliente decidido, em seguida, utilizar um maior VM (ou seja, D12) pensar uma maior VM teria mais espaço de pilha. Mesmo assim, o cliente prosseguir ver o erro. O cliente de instalações atingido à equipa do HDInsight para obter ajuda na depuração este problema.

## <a name="debug-the-out-of-memory-oom-error"></a>Depurar o erro de fora do memória (OOM)

Os nossos suporte equipas de engenharia em conjunto encontradas um dos problemas a causar o erro Out of memória (OOM) foi e um [problema descrito a JIRA Apache conhecido](https://issues.apache.org/jira/browse/HIVE-8306). A partir de descrição na JIRA:

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Vamos confirmado que esse **hive.auto.convert.join.noconditionaltask** facto foi definida como **Verdadeiro** procurando em ramo site.xml ficheiro:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
    </property>

Basear o aviso e o JIRA, o nosso hipótese foi que mapa participar foi a causa do erro Java pilha espaço OOM. Por isso Pedimos dug mais aprofundada para este problema.

Como é explicado na mensagem no blogue [definições de memória Hadoop fio no HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando Tez motor de execução é utilizado o espaço de pilha utilizado realmente pertence ao contentor de Tez. Ver a imagem abaixo que descrevem a memória de contentor Tez.

![Diagrama de memória Tez contentor: ramo de erro de memória OOM](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Como sugere a mensagem no blogue, as seguintes definições de duas memória definirem a memória contentor para a área de dados dinâmicos: **hive.tez.container.size** e **hive.tez.java.opts**. A partir do nossa experiência, a exceção OOM não significa que o tamanho de contentor é demasiado pequeno. Significa que o tamanho da pilha de Java (hive.tez.java.opts) é demasiado pequeno. Por isso, sempre que vir OOM, pode tentar aumentar **hive.tez.java.opts**. Se for necessário poderá ter de aumentar **hive.tez.container.size**. A definição de **java.opts** deve ser cerca 80% das **container.size**.

> [AZURE.NOTE]  A definição **hive.tez.java.opts** sempre tem de ser mais pequeno do que **hive.tez.container.size**.

Uma vez que uma máquina de D12 tem 28GB de memória, podemos decidido utilizar um tamanho de contentor de 10GB (10240MB) e atribuir 80% java.opts. Isto foi feito na consola do ramo utilizando a definição abaixo:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

A consulta com base nestas definições, com êxito ficou em dez minutos.

## <a name="conclusion-oom-errors-and-container-size"></a>Conclusão: Erros OOM e tamanho de contentor

Obter um erro OOM não seja necessariamente significam que o tamanho de contentor é demasiado pequeno. Em vez disso, deve configurar as definições de memória para que o tamanho da pilha é aumentado e é, pelo menos, 80% do tamanho da memória contentor.
