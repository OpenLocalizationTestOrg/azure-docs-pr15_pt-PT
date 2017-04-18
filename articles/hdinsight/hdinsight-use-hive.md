<properties
    pageTitle="Saiba o que é ramo e como utilizar HiveQL | Microsoft Azure"
    description="Saiba mais sobre Apache Hive e como utilizá-la com Hadoop no HDInsight. Escolha como executar a sua tarefa ramo e utilize HiveQL para analisar um ficheiro do exemplo Apache log4j."
    keywords="hiveql, o que é ramo"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Utilizar ramo e HiveQL com Hadoop no HDInsight para analisar um ficheiro do exemplo Apache log4j

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Neste tutorial, irá aprender a utilizar o Apache Hive no Hadoop no HDInsight e escolha como pretende executar a sua tarefa ramo. Também irá aprender sobre HiveQL e como analisar um ficheiro do exemplo Apache log4j.

##<a id="why"></a>O que é ramo e por que motivo utilizá-lo?
[Ramo de Apache](http://hive.apache.org/) é um sistema de armazém de dados para Hadoop, que permite aos resumo de dados, consultar e análise dos dados utilizando HiveQL (um idioma de consulta semelhante ao SQL). Ramo pode ser utilizado para forma interativa explorar os seus dados ou para criar o lote reutilizável processar tarefas.

Ramo permite-lhe estrutura de projeto nos dados em grande medida não estruturados. Depois de definir a estrutura, pode utilizar Hive para consultar esses dados sem dados de conhecimento de Java ou MapReduce. **HiveQL** (o idioma de consulta ramo) permite-lhe escrever consultas com instruções que são semelhantes às T-SQL.

Ramo compreende como trabalhar com dados estruturados e semiestruturados, como ficheiros de texto onde os campos são delimitados por carateres específicos. Ramo também suporta personalizado **serializador/deserializers (SerDe)** para dados complexos ou irregular estruturados. Para mais informações, consulte o artigo [como utilizar uma SerDe JSON personalizado com HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Funções definidas pelo utilizador (UDF)

Também pode ser prolongado ramo através **das funções definidas pelo utilizador (UDF)**. Um UDF permite-lhe implementar funcionalidade ou lógica que não está modelada facilmente no HiveQL. Para obter um exemplo de utilização de UDFs com ramo, consulte o seguinte:

* [Utilizar um utilizador Java definidos função com ramo](hdinsight-hadoop-hive-java-udf.md)

* [Utilizar Python com ramo e porco no HDInsight](hdinsight-python.md)

* [Utilizar c# com ramo e porco no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar um UDF Hive personalizado ao HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Exemplo de Hive UDF personalizado para converter os formatos de data/hora carimbo de data/hora ramo](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Ramo de tabelas externos de vs tabelas interno

Existem algumas coisas que precisa de saber sobre a tabela interna ramo e tabela externa:

- O comando **Criar tabela** cria uma tabela interna. O ficheiro de dados têm de estar localizado no contentor predefinido.
- O comando **Criar tabela** move o ficheiro de dados para /hive/Armazém/<TableName> pasta.
- O comando **Criar a tabela externa** cria uma tabela externa. O ficheiro de dados pode estar fora o contentor predefinido.
- O comando **Criar a tabela externa** não mover o ficheiro de dados.
- O comando **Criar a tabela externa** não lhe permitir todas as pastas na localização. Este é o motivo por que motivo o tutorial faz uma cópia do ficheiro sample.log.

Para obter mais informações, consulte o artigo [HDInsight: Hive internas e externas tabelas aqui][cindygross-hive-tables].


##<a id="data"></a>Sobre os dados de exemplo, um ficheiro de log4j Apache

Este exemplo utiliza um ficheiro de exemplo *log4j* , que é armazenado na **/example/data/sample.log** no seu contentor de armazenamento de Blobs. Consiste em cada registo dentro do ficheiro de uma linha de campos que contém uma `[LOG LEVEL]` campo a apresentar o tipo e gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de registo é o erro.

> [AZURE.NOTE] Pode também gerar um ficheiro de log4j utilizando a ferramenta de [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) registo e, em seguida, carregar esse ficheiro para o contentor blob. Consulte [Carregar dados ao HDInsight](hdinsight-upload-data.md) para obter instruções. Para mais informações sobre como o armazenamento de Blobs do Azure é utilizado pelos HDInsight, consulte o artigo [Utilizar armazenamento de Blobs do Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md).

Os dados de exemplo estão armazenados armazenamento de Blobs do Azure HDInsight utiliza como o sistema de ficheiro predefinido. HDInsight pode aceder a ficheiros armazenados no blobs ao utilizar o prefixo **wasb** . Por exemplo, para aceder ao ficheiro de sample.log, teria de utilizar a seguinte sintaxe:

    wasbs:///example/data/sample.log

Porque o armazenamento de Blobs do Azure for o armazenamento de predefinido de HDInsight, também pode aceder o ficheiro utilizando **/example/data/sample.log** a partir do HiveQL.

> [AZURE.NOTE] A sintaxe, **wasbs: / / /**, é utilizado para aceder a ficheiros armazenados no contentor de armazenamento predefinido para o seu cluster HDInsight. Se tiver especificado contas de armazenamento adicional quando aprovisionado o seu cluster e que pretende aceder a ficheiros armazenados nestas contas, pode aceder aos dados especificando o contentor nome e armazenamento endereço de conta, por exemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Tarefa de exemplo: colunas do Project para dados delimitados

Seguintes declarações HiveQL irão project colunas para delimitada dados armazenados na **wasbs: / / / / dados de exemplo** diretório:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

No exemplo anterior, as declarações HiveQL executar as seguintes ações:

* __Definir hive.execution.engine=tez;__: define o motor de execução deve utilizar Tez. Utilizar Tez em vez de MapReduce pode fornecer um aumento no desempenho da consulta. Para mais informações sobre Tez, consulte a secção [Utilizar Apache Tez para um desempenho melhorado](#usetez) .

    > [AZURE.NOTE] Esta declaração só é necessário ao utilizar um cluster de baseados no Windows HDInsight; Tez é o motor de execução predefinido para baseado em Linux HDInsight.

* **LARGAR tabela**: elimina a tabela e o ficheiro de dados se a tabela já existe.
* **Criar tabela externa**: cria uma nova tabela **externa** no ramo. Tabelas externas armazenam apenas a definição da tabela Hive; os dados falta na localização original e no formato original.
* **Formato de linha**: indica Hive a forma como os dados estão formatados. Neste caso, os campos no registo de cada são separados por um espaço.
* **Localização de TEXTFILE como ARMAZENADOS**: indica Hive onde estão os dados armazenados (o directório de exemplo de dados) e que este é armazenado como texto. Os dados podem ser num único ficheiro ou distribuir por vários ficheiros no diretório.
* **SELECIONE**: seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[erro]**. Isto deve devolver um valor de **3** , uma vez que existem três linhas que contêm este valor.
* **INPUT__FILE__NAME como '%.log'** - indica Hive que recomendamos só deve devolver dados a partir de ficheiros que terminem em. log. Este processo restringe a pesquisa para o ficheiro de sample.log que contém os dados e mantém o partir de devolver dados a partir de outras exemplo ficheiros de dados que não correspondem ao esquema definido.

> [AZURE.NOTE] Tabelas externas devem ser utilizadas quando pretende os dados subjacentes ser atualizados por uma fonte externa, tal como um processo de carregamento de dados automatizado, ou por outra MapReduce operação e pretende sempre ramo de consultas para utilizar os dados mais recentes.
>
> Largar uma tabela externa é que **não** elimina os dados, elimina apenas a definição da tabela.

Depois de criar a tabela externa, as seguintes declarações são utilizadas para criar uma tabela **interna** .

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Estas instruções executar as seguintes ações:

* **Criar a tabela se não existe**: cria uma tabela, se ainda não existir. Porque não é utilizada a palavra-chave **externa** , este é uma tabela interna, que está armazenada no armazém de ramo de dados e é gerida completamente por ramo.
* **ARMAZENADOS ORC como**: armazena os dados no formato de otimizado linha colunar (ORC). Este é um formato de altamente optimizado e eficaz para armazenar dados ramo.
* SUBSTITUIR **INSERT... SELECIONE**: seleciona linhas da tabela que contém **[erro]**e, em seguida, insere os dados na tabela **errorLogs** **log4jLogs** .

> [AZURE.NOTE] Ao contrário de tabelas externas, largar uma tabela interna também elimina os dados subjacentes.

##<a id="usetez"></a>Utilizar Apache Tez para um desempenho melhorado

[Apache Tez](http://tez.apache.org) é uma estrutura que permite que as aplicações intenso de dados, tal como ramo, para ser executado muito mais eficientemente à escala. Na versão mais recente do HDInsight, ramo suporta em execução no Tez. Tez está ativada por predefinição para clusters baseado em Linux HDInsight.

> [AZURE.NOTE] Tez atualmente está desativada por predefinição para clusters HDInsight baseados no Windows e tem de estar ativada. Para tirar partido das Tez, o seguinte valor tem de estar definido para uma consulta de ramo:
>
> ```set hive.execution.engine=tez;```
>
>Isto pode ser apresentado numa base por consulta pelo colocando-o no início da sua consulta. Também pode configurar esta opção para estar ligado por predefinição num cluster ao definir o valor de configuração quando criar o cluster. Pode encontrar mais detalhes em [Clusters de aprovisionamento de HDInsight](hdinsight-provision-clusters.md).

[Ramo de em documentos de estrutura de Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiverem um número de detalhes sobre as configurações de sintonização e opções de implementação.

Para ajudar a depuração de tarefas a executou utilizando Tez, HDInsight fornece web seguinte IU permitem-lhe ver os detalhes de Tez tarefas:

* [Utilizar a IU Tez no HDInsight baseados no Windows](hdinsight-debug-tez-ui.md)

* [Utilizar a vista de Ambari Tez no baseado em Linux HDInsight](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Escolha como pretende executar o processo de HiveQL

HDInsight pode executar tarefas HiveQL utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual o método é ideal para si, em seguida, siga a hiperligação para obter instruções sobre.

| **Utilize esta opção** se pretender que...                                                     | … .an **interativos** shell | ... processamento **batch** | … .with este **sistema operativo de cluster** | … .from este **sistema operativo do cliente** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Vista de ramo](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Qualquer (baseado no browser) |
| [Comando de beeline (a partir de uma sessão SSH)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Windows, Mac OS X, Unix ou Linux        |
| [Ramo de comando (a partir de uma sessão SSH)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Windows, Mac OS X, Unix ou Linux        |
| [Laço](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows, Mac OS X, Unix ou Linux        |
| [Consola de consulta](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | Qualquer (baseado no browser)                            |
| [Ferramentas de HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows                                  |
| [Ambiente de trabalho remoto](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Tarefas em execução ramo no Azure HDInsight utilizando no local SQL Server Integration Services

Também pode utilizar o SQL Server Integration Services (SSIS) para executar uma tarefa de ramo. O Azure Feature Pack para SSIS fornece os seguintes componentes que trabalhar com tarefas de ramo HDInsight.


- [Azure HDInsight ramo tarefa][hivetask]
- [Gestor de ligação de subscrição do Azure][connectionmanager]


Saiba mais sobre o Azure Feature Pack para SSIS [aqui][ssispack].


##<a id="nextsteps"></a>Próximos passos

Agora que aprendeu ramo é e como utilizá-la com Hadoop no HDInsight, utilize as ligações seguintes para explorar outras formas de trabalhar com Azure HDInsight.


- [Carregar dados ao HDInsight][hdinsight-upload-data]
- [Utilizar porco com HDInsight][hdinsight-use-pig]
- [Utilizar Sqoop com HDInsight](hdinsight-use-sqoop.md)
- [Utilizar Oozie com HDInsight](hdinsight-use-oozie.md)
- [Utilizar tarefas de MapReduce com HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
