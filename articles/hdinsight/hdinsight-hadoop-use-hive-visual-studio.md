<properties
   pageTitle="Ramo de consulta com Hadoop ferramentas para o Visual Studio | Microsoft Azure"
   description="Saiba como utilizar ramo com Hadoop no HDInsight com ferramentas do Visual Studio Hadoop."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Executar consultas de ramo utilizando as ferramentas de HDInsight para Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este artigo vai aprender a submeter ramo consultas a um cluster de HDInsight com as ferramentas de HDInsight para Visual Studio.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que as declarações de HiveQL utilizadas nos exemplos fazer. Para obter informações sobre o HiveQL utilizados neste exemplo, consulte o artigo [Utilizar Hive com Hadoop no HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte.

* Um cluster de Azure HDInsight (Hadoop no HDInsight) (Linux ou baseados no Windows)

* Visual Studio (uma das seguintes versões):

    Visual Studio 2013 Comunidade/Professional/Premium/Ultimate com [atualização 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (Comunidade/Enterprise)

- HDInsight ferramentas para o Visual studio. Consulte o artigo [começar a utilizar o Visual Studio Hadoop ferramentas para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

##<a id="run"></a>Executar consultas de ramo utilizando as ferramentas de HDInsight para Visual Studio

1. Abrir o **Visual Studio** e selecione **Novo** > **projeto** > **HDInsight** > **Ramo de aplicação**. Forneça um nome para este projeto.

2. Abra o ficheiro de **Script.hql** que é criado com este projeto e cole as seguintes declarações de HiveQL:

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
    * **SELECIONE**: selecione uma contagem de todas as linhas onde coluna **t4** contém o valor de **[erro]**. Isto deve devolver um valor de **3** , uma vez que existem três linhas que contêm este valor.
    * **INPUT__FILE__NAME como '%.log'** - indica Hive que recomendamos só deve devolver dados a partir de ficheiros que terminem em. log. Este processo restringe a pesquisa para o ficheiro de sample.log que contém os dados e mantém o partir de devolver dados a partir de outras exemplo ficheiros de dados que não correspondem ao esquema definido.

3. Na barra de ferramentas, selecione o **HDInsight Cluster** que pretende utilizar para esta consulta e, em seguida, selecione **Submeter para WebHCat** para executar as declarações como uma tarefa de ramo utilizando WebHCat. Também pode submeter a tarefa, utilizando o botão __executar através do HiveServer2__ se HiveServer2 estiver disponível na sua versão de cluster. O **Resumo da tarefa Hive** aparece e apresenta informações sobre a tarefa em execução. Utilize a ligação de **Atualizar** para atualizar as informações da tarefa, até que o **Estado da tarefa** muda para **Concluir**.

4. Utilize a ligação de **Saída de tarefa** para ver o resultado desta tarefa. Deve mostrar `[ERROR] 3`, qual é o valor devolvido pela instrução SELECT.

5. Também pode executar consultas de ramo sem criar um projeto. Utilizando o **Explorador do servidor**, expanda **Azure** > **HDInsight**, servidor HDInsight com o botão direito e, em seguida, selecione **escrever uma consulta Hive**.

6. No documento **temp.hql** que aparece, adicione as seguintes instruções de HiveQL:

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instruções executar as seguintes ações:

    * **Criar a tabela se não existe**: cria uma tabela, se ainda não existir. Porque não é utilizada a palavra-chave **externa** , este é uma tabela interna, que está armazenada no armazém de ramo de dados e é gerida completamente por ramo.

        > [AZURE.NOTE] Ao contrário de tabelas **EXTERNOS** , largar uma tabela interna também elimina os dados subjacentes.

    * **ARMAZENADOS ORC como**: armazena os dados no formato de (ORC) colunas linha otimizada. Este é um formato de altamente optimizado e eficaz para armazenar dados ramo.
    * SUBSTITUIR **INSERT... SELECIONE**: seleciona as linhas de **log4jLogs** que contêm **[erro]**, em seguida, insere os dados na tabela **errorLogs** .

7. Na barra de ferramentas, selecione **Submeter** para executar a tarefa. Utilize o **Estado da tarefa** para determinar que a tarefa foi concluída com êxito.

8. Para verificar que a tarefa de concluída e criada uma nova tabela, utilize **O Explorador de servidor** e expanda **Azure** > **HDInsight** > seu cluster HDInsight > **Hive bases de dados** > e **valor predefinido**. Deverá visualizar a tabela de **errorLogs** e a tabela **log4jLogs** .

##<a id="summary"></a>Resumo

Como pode ver, o as ferramentas de HDInsight para Visual Studio fornecem uma forma fácil de executar consultas de ramo num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre ramo no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas de HDInsight para Visual Studio:

* [Introdução ao HDInsight ferramentas para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
