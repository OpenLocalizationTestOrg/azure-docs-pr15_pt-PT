<properties
   pageTitle="Utilizar Hadoop porco no HDInsight | Microsoft Azure"
   description="Saiba como utilizar porco com Hadoop no HDInsight."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utilizar porco com Hadoop no HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Porco Apache](http://pig.apache.org/) é uma plataforma na criação de programas para Hadoop ao utilizar um idioma procedimento conhecido como *porco Latim*. Porco é uma alternativa a Java para criar soluções *MapReduce* e é incluído no Azure HDInsight.

Neste artigo, irá obter informações sobre como pode utilizar porco com HDInsight.

##<a id="why"></a>Porquê utilizar porco?

Ao utilizar apenas um mapa e uma função de reduzir um dos desafios de processamento de dados utilizando MapReduce no Hadoop está a implementar lógica de processamento. Para processamento complexo, muitas vezes tiver que dividir processamento em várias operações de MapReduce estão ligados em conjunto para obter o resultado pretendido.

Em vez de forçar para utilizar apenas o mapa e reduzir funções, porco permite-lhe definir processamento como uma série de transformações de os fluxos de dados através de para produzir o resultado pretendido.

O idioma de porco Latim permite-lhe descrever o fluxo de dados a partir da entrada observou, através de um ou mais transformações, para produzir o resultado pretendido. Porco Latim programas siga este padrão geral:

- **Carga**: ler os dados de manipulação do sistema de ficheiros
- **Transformar**: manipular os dados
- **Informações de estado ou loja**: Exportar dados para o ecrã ou armazená-lo para processamento

Porco Latim também suporta a funções definidas pelo utilizador (UDF), que lhe permite invocar componentes externos que implementam lógica que é difícil de modelo no Latim porco.

Para mais informações sobre porco Latim, consulte o artigo [porco Latim referência Manual 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e [2 de Manual de referência de porco Latim](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obter um exemplo de utilização de UDFs com porco, consulte os seguintes documentos:

* [Utilizar DataFu com porco no HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu aqui uma coleção de UDFs útil mantidas por Apache

* [Utilizar Python com porco e ramo no HDInsight](hdinsight-python.md)

* [Utilizar c# com ramo e porco no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Sobre os dados de exemplo

Este exemplo utiliza um ficheiro de exemplo *log4j* , que é armazenado na **/example/data/sample.log** no seu contentor de armazenamento de Blobs. Consiste em cada registo dentro do ficheiro de uma linha de campos que contém uma `[LOG LEVEL]` campo a apresentar o tipo e gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de registo é o erro.

> [AZURE.NOTE] Pode também gerar um ficheiro de log4j utilizando a ferramenta de [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) registo e, em seguida, carregar esse ficheiro para o seu blob. Consulte [Carregar dados ao HDInsight](hdinsight-upload-data.md) para obter instruções. Para mais informações sobre como são utilizados blobs no armazenamento Azure HDInsight, consulte o artigo [Utilizar armazenamento de Blobs do Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md).

Os dados de exemplo estão armazenados armazenamento de Blobs do Azure HDInsight utiliza como o sistema de ficheiro predefinido para Hadoop clusters. HDInsight pode aceder a ficheiros armazenados no blobs ao utilizar o prefixo **wasb** . Por exemplo, para aceder ao ficheiro de sample.log, teria de utilizar a seguinte sintaxe:

    wasbs:///example/data/sample.log

Porque WASB é o armazenamento de predefinido de HDInsight, também pode aceder o ficheiro utilizando **/example/data/sample.log** a partir do porco Latim.

> [AZURE.NOTE] A sintaxe, **wasbs: / / /**, é utilizado para aceder a ficheiros armazenados no contentor de armazenamento predefinido para o seu cluster HDInsight. Se tiver especificado contas de armazenamento adicional quando aprovisionado o seu cluster e que pretende aceder a ficheiros armazenados nestas contas, pode aceder aos dados especificando o contentor nome e armazenamento endereço de conta, por exemplo: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Sobre a tarefa de exemplo

A seguinte tarefa porco Latim carrega o ficheiro de **sample.log** a partir do armazenamento predefinida para o seu cluster HDInsight. Em seguida, executa uma série de transformações que resultam uma contagem do número de vezes cada nível de registo ocorreu nos dados de entrada. Os resultados são copiados para STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

A imagem seguinte mostra uma explicação detalhada do que faz cada transformação aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

##<a id="run"></a>Executar a tarefa de porco Latim

HDInsight pode executar tarefas porco Latim utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual o método é ideal para si, em seguida, siga a hiperligação para obter instruções sobre.

| **Utilize esta opção** se pretender que...                                   | … .an **interativos** shell | ... processamento **batch** | … .with este **sistema operativo de cluster** | … .from este **sistema operativo do cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Windows, Mac OS X, Unix ou Linux        |
| [Laço](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows, Mac OS X, Unix ou Linux        |
| [.NET SDK para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows (por agora)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows                                  |
| [Ambiente de trabalho remoto](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Tarefas em execução porco no Azure HDInsight utilizando no local SQL Server Integration Services

Também pode utilizar o SQL Server Integration Services (SSIS) para executar uma tarefa porco. O Azure Feature Pack para SSIS fornece os seguintes componentes trabalhar com tarefas de porco no HDInsight.


- [Azure HDInsight porco tarefa][pigtask]
- [Gestor de ligação de subscrição do Azure][connectionmanager]


Saiba mais sobre o Azure Feature Pack para SSIS [aqui][ssispack].


##<a id="nextsteps"></a>Próximos passos

Agora que aprendeu como utilizar porco com HDInsight, utilize as ligações seguintes para explorar outras formas de trabalhar com Azure HDInsight.

* [Carregar dados ao HDInsight][hdinsight-upload-data]
* [Utilizar ramo com HDInsight][hdinsight-use-hive]
* [Utilizar Sqoop com HDInsight](hdinsight-use-sqoop.md)
* [Utilizar Oozie com HDInsight](hdinsight-use-oozie.md)
* [Utilizar tarefas de MapReduce com HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
