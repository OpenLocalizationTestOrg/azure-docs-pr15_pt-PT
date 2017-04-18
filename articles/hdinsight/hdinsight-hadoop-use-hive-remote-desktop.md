<properties
   pageTitle="Utilizar ramo Hadoop e o ambiente de trabalho remoto no HDInsight | Microsoft Azure"
   description="Saiba como se ligar à Hadoop cluster no HDInsight utilizando o ambiente de trabalho remoto e, em seguida, executar consultas de ramo utilizando a Interface de linha de comandos Hive."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Utilizar ramo com Hadoop no HDInsight com o ambiente de trabalho remoto

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, irá obter informações sobre como se ligar a um cluster de HDInsight utilizando o ambiente de trabalho remoto e, em seguida, executar consultas de ramo utilizando o Hive Interface da linha de comandos (CLI).

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que as instruções do HiveQL que são utilizadas nos exemplos fazer. Para obter informações sobre o HiveQL utilizados neste exemplo, consulte o artigo [Utilizar Hive com Hadoop no HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um cluster de HDInsight baseados no Windows (Hadoop no HDInsight)

* Um computador cliente a executar o Windows 10, janela 8 ou Windows 7

##<a id="connect"></a>Ligar-se ao ambiente de trabalho remoto

Ativar o ambiente de trabalho remoto para HDInsight cluster, em seguida, ligar para o mesmo ao seguir as instruções em [ligar ao clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Utilize o comando de ramo

Quando se ter ligado no ambiente de trabalho para o cluster HDInsight, utilize os passos seguintes para trabalhar com ramo:

1. A partir do ambiente de trabalho HDInsight, comece a **linha de comandos Hadoop**.

2. Introduza o seguinte comando para iniciar o clip ramo de registo:

        %hive_home%\bin\hive

    Quando tiver iniciado o clip, irá ver linha de comandos do clip Hive: `hive>`.

3. Utilizar o clip, introduza as seguintes instruções para criar uma nova tabela denominada **log4jLogs** utilizando dados de exemplo:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instruções executar as seguintes ações:

    * **LARGAR tabela**: elimina a tabela e o ficheiro de dados se a tabela já existe.

    * **Criar tabela externa**: cria uma nova tabela «externa» no ramo. Tabelas externas armazenam apenas a definição da tabela Hive (os dados são à esquerda na localização original).

        > [AZURE.NOTE] Tabelas externas devem ser utilizadas quando pretende os dados subjacentes ser atualizados por uma fonte externa (tal como um processo de carregamento de dados automatizado) ou por outra MapReduce operação, mas pretende sempre ramo de consultas para utilizar os dados mais recentes.
        >
        > Largar uma tabela externa é que **não** eliminar os dados, apenas a definição da tabela.

    * **Formato de linha**: indica Hive a forma como os dados estão formatados. Neste caso, os campos no registo de cada são separados por um espaço.

    * **Localização de TEXTFILE como ARMAZENADOS**: indica Hive onde estão os dados armazenados (o directório de exemplo de dados) e que este é armazenado como texto.

    * **SELECIONE**: seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Isto deve devolver um valor de **3** , uma vez que existem três linhas que contêm este valor.

    * **INPUT__FILE__NAME como '%.log'** - indica Hive que recomendamos só deve devolver dados a partir de ficheiros que terminem em. log. Este processo restringe a pesquisa para o ficheiro de sample.log que contém os dados e mantém o partir de devolver dados a partir de outras exemplo ficheiros de dados que não correspondem ao esquema definido.


4. Utilize as seguintes instruções para criar uma nova tabela «interna» denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instruções executar as seguintes ações:

    * **Criar a tabela se não existe**: cria uma tabela, se ainda não existir. Porque não é utilizada a palavra-chave **externa** , este é uma tabela interna, que está armazenada no armazém de ramo de dados e é gerida completamente por ramo.

        > [AZURE.NOTE] Ao contrário de tabelas **EXTERNOS** , largar uma tabela interna também elimina os dados subjacentes.

    * **ARMAZENADOS ORC como**: armazena os dados no formato de (ORC) colunas linha otimizada. Este é um formato de altamente optimizado e eficaz para armazenar dados ramo.

    * SUBSTITUIR **INSERT... SELECIONE**: seleciona as linhas de **log4jLogs** que contêm **[erro]**, em seguida, insere os dados na tabela **errorLogs** .

    Para verificar que foram guardadas apenas as linhas que contêm **[erro]** na coluna t4 à tabela **errorLogs** , utilize a seguinte instrução para devolver todas as linhas de **errorLogs**:

        SELECT * from errorLogs;

    Deve ser devolvido três linhas de dados, todos os que contenham **[erro]** na coluna t4.

##<a id="summary"></a>Resumo

Como pode ver, o o comando ramo fornece uma forma fácil de forma interativa executar consultas de ramo num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre ramo no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar Tez com ramo, consulte os seguintes documentos para as informações de depuração:

* [Utilizar a IU Tez no HDInsight baseados no Windows](hdinsight-debug-tez-ui.md)

* [Utilizar a vista de Ambari Tez no baseado em Linux HDInsight](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

