<properties
   pageTitle="Utilizar a shell de ramo no HDInsight (Hadoop) | Microsoft Azure"
   description="Saiba como utilizar a shell de ramo com um cluster de baseado em Linux HDInsight. Irá obter informações sobre como ligar ao cluster HDInsight utilizando SSh, em seguida, utilizar a Shell Hive para forma interativa executar consultas."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Utilizar ramo com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este artigo vai aprender a utilizar Shell seguro (SSH) para ligar a um Hadoop num cluster de Azure HDInsight e, em seguida, submeter consultas ramo de forma interativa utilizando a interface de linha de comandos do ramo (CLI).

> [AZURE.IMPORTANT] Enquanto o comando ramo está disponível em clusters baseado em Linux HDInsight, deverá tomar em consideração de utilizar Beeline. Beeline é um cliente mais recente para trabalhar com ramo e está incluído no seu cluster HDInsight. Para mais informações sobre como utilizar esta, consulte o artigo [Utilizar Hive com Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um Hadoop baseado em Linux num cluster de HDInsight.

* Um cliente SSH. Sistema operativo Mac, Unix e Linux devem vêm com um cliente SSH. Utilizadores do Windows tem de transferir um cliente, como [betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Ligar-se a SSH

Ligar para o nome de domínio completamente qualificado (FQDN) do seu cluster HDInsight utilizando o comando SSH. O FQDN será o nome que lhe atribuiu cluster, em seguida, **. azurehdinsight.net**. Por exemplo, o seguinte procedimento seria ligar a um cluster denominado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se lhe fornecidos uma chave de certificado para SSH autenticação** quando criou o cluster HDInsight, poderá ter especificar a localização da chave privada no sistema de cliente:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se lhe fornecidos uma palavra-passe para autenticação SSH** quando criou o cluster HDInsight, terá de fornecer a palavra-passe quando lhe for pedido.

Para mais informações sobre como utilizar SSH com HDInsight, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Betumes (clientes baseados no Windows)

Windows não fornece um cliente SSH incorporado. Recomendamos que utilize **betumes**, que podem ser transferidos das [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para mais informações sobre como utilizar betumes, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Utilize o comando de ramo

2. Quando estiver ligado, inicie o clip Hive utilizando o seguinte comando:

        hive

3. Utilizar o clip, introduza as seguintes instruções para criar uma nova tabela denominada **log4jLogs** ao utilizar os dados de exemplo:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instruções executar as seguintes ações:

    * **LARGAR tabela** - elimina a tabela e o ficheiro de dados, caso já existe na tabela.
    * **Criar tabela externa** - cria uma nova tabela «externa» no ramo. Tabelas externas armazenam apenas a definição da tabela no ramo. Os dados falta na localização original.
    * **Formato de linha** - indica Hive a forma como os dados estão formatados. Neste caso, os campos no registo de cada são separados por um espaço.
    * **Localização de TEXTFILE como ARMAZENADOS** - indica Hive onde estão os dados armazenados (o directório de exemplo de dados) e que a mesma está armazenada como texto.
    * **SELECIONE** - seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Isto deve devolver um valor de **3** como existem três linhas que contêm este valor.
    * **INPUT__FILE__NAME como '%.log'** - indica Hive que recomendamos só deve devolver dados a partir de ficheiros que terminem em. log. Este processo restringe a pesquisa para o ficheiro de sample.log que contém os dados e mantém o partir de devolver dados a partir de outras exemplo ficheiros de dados que não correspondem ao esquema definido.

    > [AZURE.NOTE] Tabelas externas devem ser utilizadas quando pretende os dados subjacentes ser atualizados por uma fonte externa, tal como um processo de carregamento de dados automatizado, ou por outra MapReduce operação, mas pretende sempre ramo de consultas para utilizar os dados mais recentes.
    >
    > Largar uma tabela externa é que **não** eliminar os dados, apenas a definição da tabela.

4. Utilize as seguintes instruções para criar uma nova tabela «interna» denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instruções executar as seguintes ações:

    * **Criar a tabela se não existe** - cria uma tabela, se ainda não existir. Uma vez que não é utilizada a palavra-chave **externa** , este é uma tabela interna, que está armazenada no armazém de ramo de dados e é gerida completamente por ramo.
    * **ARMAZENADOS ORC como** - armazena os dados no formato de otimizado linha colunar (ORC). Este é um formato de altamente optimizado e eficaz para armazenar dados ramo.
    * SUBSTITUIR **INSERT... SELECIONE** - seleciona linhas da tabela **log4jLogs** que contêm **[erro]**, em seguida, insere os dados na tabela **errorLogs** .

    Para verificar que foram guardadas apenas as linhas que contêm **[erro]** na coluna t4 à tabela **errorLogs** , utilize a seguinte instrução para devolver todas as linhas de **errorLogs**:

        SELECT * from errorLogs;

    Deve ser devolvido três linhas de dados, todos os que contenham **[erro]** na coluna t4.

    > [AZURE.NOTE] Ao contrário de tabelas externas, largar uma tabela interna irá eliminar dados subjacentes.

##<a id="summary"></a>Resumo

Como pode ver, o comando de ramo fornece uma forma fácil de forma interativa executar consultas de ramo num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre ramo no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar Tez com ramo, consulte os seguintes documentos para as informações de depuração:

* [Utilizar a IU Tez no HDInsight baseados no Windows](hdinsight-debug-tez-ui.md)

* [Utilizar a vista de Ambari Tez no baseado em Linux HDInsight](hdinsight-debug-ambari-tez-view.md)

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

