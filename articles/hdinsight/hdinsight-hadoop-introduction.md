 <properties
    pageTitle="Introdução ao Hadoop - o que é Hadoop no HDInsight? | Microsoft Azure"
    description="Obter uma introdução à Hadoop, processamento de dados de grande distribuído e análise e os componentes do ecossistema Hadoop na nuvem no HDInsight."
    keywords="Introdução ao hadoop, o que é hadoop, pilha de tecnologia de hadoop, hadoop ecossistema uma análise de dados grande"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Uma introdução à ecossistema Hadoop no Azure HDInsight

 Este artigo fornece uma introdução à Hadoop no Azure HDInsight, respetivo ecossistema e dados grandes. Saiba mais sobre os componentes Hadoop, terminologia comuns e cenários de análise de dados grande.

## <a name="what-is-hadoop-on-hdinsight"></a>O que é Hadoop no HDInsight?

Hadoop que se refere a um ecossistema de software de abrir origem que é um quadro para distribuído transformação, armazenar e análise grandes dos conjuntos de dados em clusters de computadores. Azure HDInsight disponibiliza os componentes de Hadoop da distribuição **Hortonworks dados plataforma (HDP)** na nuvem e ser implementada e disposições clusters geridos com elevada fiabilidade e disponibilidade.  

Apache Hadoop foi o projecto abrir origem original grande processamento dos dados. Seguinte foi o desenvolvimento de software relacionados e utilitários considerado como parte da pilha de tecnologia Hadoop, incluindo Apache Hive, Apache HBase, Apache motores e muitas outras pessoas. Consulte o artigo [Descrição geral do ecossistema Hadoop no HDInsight](#overview) para obter detalhes.

## <a name="what-is-big-data"></a>O que são dados grandes?

Dados grandes descreve qualquer grande corpo das informações digitais, do texto num Twitter feed, as informações de sensor de equipamento industrial, para obter informações sobre o cliente de navegação e de compras num Web site. Dados grandes podem ser histórico (ou seja, os dados armazenados) ou em tempo real (o que significa transmitido em sequência diretamente a partir da origem). Estão a ser recolhidos dados grandes no volumes crescentes alguma vez, na velocidades superiores de cada vez e num expandir variedade de formatos.

Para obter dados grandes fornecer informações da empresa acionáveis ou conhecimentos aprofundados, tem de recolher dados relevantes e perguntas à direita. É também pode tornar-se de que os dados são acessíveis, limpo, analisadas e, em seguida, são apresentados de uma forma útil. É onde pode ajudar a análise de dados grande no Hadoop no HDInsight.

## <a name="overview"></a>Descrição geral do ecossistema Hadoop no HDInsight

HDInsight é uma distribuição da nuvem no Microsoft Azure da pilha de tecnologia Apache Hadoop expansão rápida para análise de dados grande. Inclui implementações do Apache motores, HBase, tempestade, porco, ramo, Sqoop, Oozie, Ambari e assim sucessivamente. HDInsight também integra com ferramentas de business intelligence (BI) como Power BI, Excel, SQL Server Analysis Services e o SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, motores, tempestade e clusters personalizados

HDInsight fornece configurações de cluster para Apache Hadoop, motores, HBase ou tempestade. Em alternativa, pode [Personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: fornece armazenamento de dados fiável com [HDFS](#hdfs)e um modelo de programação [MapReduce](#mapreduce) simple para processar e analisar dados em paralelo.

* **<a target="_blank" href="http://spark.apache.org/">Motores de Apache</a>**: um quadro de processamento paralelas que suporta o processamento de na memória para aumentar o desempenho das aplicações de análise de dados grande, gerar funciona para SQL, transmissão de dados e formação de computador. Consulte o artigo [Descrição geral: o que é Apache motores no HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: uma base de dados de NoSQL criada com base em Hadoop que proporciona acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semiestruturados - potencialmente milhares de linhas vezes milhões de colunas. Consulte o artigo [Descrição geral de HBase no HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache tempestade</a>**: um sistema de cálculo distribuído e em tempo real para processar grandes sequências de dados com rapidez. Tempestade é disponibilizado como um cluster de geridos em HDInsight. Consulte o artigo [Analisar dados de sensor em tempo real com tempestade e Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Scripts de personalização de exemplo

Acções de script são scripts que executar durante o aprovisionamento de cluster e podem ser utilizados para instalar componentes adicionais no cluster. Para baseado em Linux clusters, estes são os scripts de festa.

Os seguintes scripts de exemplo são fornecidos pela equipa HDInsight:

* [Matiz](hdinsight-hadoop-hue-linux.md): um conjunto de aplicações web utilizada para interagir com um cluster. Linux clusters apenas.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): Graph processamento para relações de modelos de entre coisas ou pessoas.

* [R](hdinsight-hadoop-r-scripts-linux.md): uma linguagem fonte abrir e um ambiente para computação estatísticos utilizados em aprender máquina.

* [Solr](hdinsight-hadoop-solr-install-linux.md): uma plataforma de pesquisa de escala empresarial que permite que o texto completo pesquisa nos dados.

Para obter informações sobre como desenvolver o seu próprio Script ações, consulte o artigo [desenvolvimento de acção de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>Quais são os componentes de Hadoop e os utilitários?

Os seguintes componentes e utilitários estão incluídos no HDInsight clusters.

* **[Ambari](#ambari)**: Cluster de aprovisionamento, gestão, monitorização e utilitários.

* **[Avro](#avro)** (Microsoft .NET biblioteca para Avro): serialização de dados para o ambiente do Microsoft .NET.

* **[Ramo & HCatalog](#hive)**: linguagem SQL (Structured Query)-como consultar e uma camada de gestão de tabela e armazenamento.

* **[Mahout](#mahout)**: para máquina dimensionável aprendizagem aplicações.

* **[MapReduce](#mapreduce)**: arquitetura de legado para Hadoop distribuído processamento e gestão de recursos. Consulte o artigo [FIO](#yarn), a arquitetura de geração seguinte recurso.

* **[Oozie](#oozie)**: gestão de fluxo de trabalho.

* **[Lisboa](#phoenix)**: camada de base de dados relacional sobre HBase.

* **[Porco](#pig)**: scripting mais simples para MapReduce transformações.

* **[Sqoop](#sqoop)**: dados importar e exportar.

* **[Tez](#tez)**: permite que os processos de com muitos dados executar eficazmente em escala.

* **[FIO](#yarn)**: parte da biblioteca de core Hadoop e próxima geração do quadro de software MapReduce.

* **[ZooKeeper](#zookeeper)**: coordená-lo de processos no sistemas distribuídos.

> [AZURE.NOTE] Para obter informações sobre os componentes específicos e informações sobre a versão, consulte [componentes Hadoop, controlo de versões e ofertas de serviço no HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari é para aprovisionamento, gerir e monitorizar Apache Hadoop clusters. Inclui uma coleção de intuitiva das ferramentas de operador e um conjunto robusto de APIs ocultar a complexidade da Hadoop, simplificar a operação de clusters. HDInsight baseado em Linux clusters prestar tanto a Ambari IU e web API do resto Ambari, enquanto clusters baseados no Windows fornecem um subconjunto de REST API. Vistas de Ambari no HDInsight clusters permitir que as capacidades de IU Plug-in.

Consulte o artigo [Gerir HDInsight clusters utilizando Ambari](hdinsight-hadoop-manage-ambari.md) (apenas Linux), [clusters de Monitor Hadoop HDInsight utilizar a API Ambari](hdinsight-monitor-use-ambari-api.md)e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API referência</a>.

### <a name="avro"></a>Avro (Microsoft .NET biblioteca para Avro)

A biblioteca do Microsoft .NET para Avro implementa o formato de intercâmbio de dados binários compacta Apache Avro para serialização para o ambiente do Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">JavaScript objeto notação (JSON)</a> para definir um esquema de idioma desconhecido que cubra interoperabilidade de idioma, dados de significado serializados de um idioma podem ser lidos num outro. Obter informações detalhadas sobre o formato podem ser encontradas na < um alvo de = href "em branco" _ = "http://avro.apache.org/docs/current/spec.html" > Apache Avro especificação</a>.
O formato de ficheiros de Avro suporta o modelo de programação MapReduce distribuído. Os ficheiros são "divisíveis", o que significa que pode atingir qualquer ponto num ficheiro e comece a ler a partir de um determinado bloco. Para saber como, consulte o artigo [Serialize dados com a biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Da sistema de ficheiros distribuído Hadoop (HDFS) é um sistema de ficheiros distribuído que, com fios e MapReduce é o essencial ecossistema Hadoop. HDFS é o sistema de ficheiros padrão para Hadoop clusters no HDInsight.

### <a name="hive"></a>Ramo & HCatalog

<a target="_blank" href="http://hive.apache.org/">Ramo de Apache</a> é um software de armazém de dados criada com base em Hadoop permite-lhe de consultas e gerir grandes conjuntos de dados no armazenamento distribuído utilizando uma linguagem de SQL gosto denominada HiveQL. Ramo, como porco, é uma abstracção na parte superior de MapReduce. Quando executar, ramo traduz consultas para uma série de tarefas de MapReduce. Ramo de esteja conceptual mais próximo um sistema de gestão de base de dados relacional que porco e, por isso, é adequado para utilização com dados estruturados mais. Para os dados não estruturados, o porco é a melhor escolha. Consulte o artigo [utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gestão de tabela e de armazenamento para Hadoop que apresenta os utilizadores com uma vista de dados relacional. No HCatalog, pode ler e escrever ficheiros em qualquer formato para os quais pode ser escrito um SerDe Hive (serializador desserializador).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca dimensionável dos algoritmos que são executados no Hadoop de aprendizagem automática. Utilizar princípios das estatísticas, aplicações de aprendizagem máquina ensinar sistemas para saber a partir de dados e utilizar as anteriores resultados para determinar o comportamento futuro. Consulte o artigo [recomendações de filme de gerar utilizando Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce é a arquitetura de software legado para Hadoop para escrever publicações de aplicações para o lote processo conjuntos de dados grande em paralelo. Uma tarefa MapReduce divide grandes conjuntos de dados e organiza os dados em pares valor da chave para processamento.

[FIO](#yarn) é a arquitetura recurso seguinte geração Hadoop gestor e a aplicação e é referido como MapReduce 2.0. Executar tarefas MapReduce no FIO.

Para mais informações sobre MapReduce, consulte o artigo <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordená-lo de fluxo de trabalho que faz a gestão dos Hadoop tarefas. -Está integrado com a pilha de Hadoop e suporta tarefas de Hadoop para MapReduce, porco, ramo e Sqoop. Podem também ser utilizado para agendar específica para um sistema, como os programas Java ou scripts da shell de tarefas. Consulte o artigo [utilizar Oozie com Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Lisboa
<a  target="_blank" href="http://phoenix.apache.org/">Lisboa Apache</a> é uma camada de base de dados relacionais através de HBase. Lisboa inclui um controlador JDBC que permite aos utilizadores de consultas e gerir diretamente a tabelas do SQL. Lisboa converte consultas e outras instruções para chamadas de NoSQL API nativas - em vez de utilizar MapReduce - permitindo assim que rápido as aplicações na parte superior NoSQL stores. Consulte o artigo [utilizar Apache Lisboa e de exemplo com HBase clusters](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Porco
<a  target="_blank" href="http://pig.apache.org/">Porco Apache</a> é uma plataforma de alto nível que permite-lhe executar complexas MapReduce transformações no muito grandes conjuntos de dados utilizando uma linguagem de scripts simple denominada porco Latim. Porco converte os scripts porco Latim, de modo que vai executar dentro Hadoop. Pode criar funções definidas pelo utilizador (UDFs) para expandir porco Latim. Consulte o artigo [utilizar porco com Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é ferramenta que transferências em volume dados entre Hadoop e bases de dados relacionais essa SQL ou outros arquivos de dados estruturados, o mais eficientemente. Consulte o artigo [utilizar Sqoop com Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma estrutura de aplicação criada com base em FIO Hadoop que executa complexos, incluindo gráficos geral do processamento de dados. É uma sucessora mais flexível e poderosa ao quadro MapReduce que permite que os processos de com muitos dados, tal como ramo, para executar de forma mais eficiente, em escala. Consulte o artigo ["Utilizar Apache Tez para um desempenho melhorado" no ramo de utilização e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>FIO
Apache FIO é a próxima geração de MapReduce (MapReduce 2.0 ou MRv2) e suporta cenários de processamento de dados para além de processamento com maior escalabilidade e o processamento em tempo real em lotes MapReduce. FIO fornece a gestão de recursos e um quadro de aplicação distribuída. Executar tarefas MapReduce no FIO.

Para saber mais sobre FIO, consulte o artigo <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (FIO)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordenadas processos em grandes sistemas distribuídos através de um espaço de nomes hierárquico partilhado dos registos de dados (znodes). Znodes conter pequenas quantidades de informações de meta necessárias para coordenar processos: Estado, localização, configuração e assim sucessivamente.

## <a name="programming-languages-on-hdinsight"></a>Linguagens de programação no HDInsight

HDInsight clusters – Hadoop, HBase, tempestade e motores clusters de – suportar um número de linguagens de programação, mas algumas não são instaladas por predefinição. Para bibliotecas, módulos ou pacotes não instalados por predefinição, utilizam uma acção de script para instalar o componente. Consulte o artigo [desenvolvimento de ação de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Predefinida com a linguagem de programação

Por predefinição, HDInsight clusters de suporte:

* Java

* Python

Idiomas adicionais podem ser instalados utilizar ações de script: [desenvolvimento de ação de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Idiomas de máquina virtual (JVM) Java

Vários idiomas que não seja Java podem ser executados com uma máquina de virtual Java (JVM); No entanto, executar alguns destes idiomas precisem de componentes adicionais instalados no cluster.

Estes idiomas baseados em JVM são suportados no HDInsight clusters:

* Clojure

* Jython (Python para Java)

* Scala

### <a name="hadoop-specific-languages"></a>Idiomas Hadoop específicas

HDInsight clusters suportam os seguintes idiomas que sejam específicos ecossistema Hadoop:

* Porco Latim para trabalhos de porco

* HiveQL para ramo de tarefas e SparkSQL


## <a name="advantage"></a>Vantagens de Hadoop na nuvem

Como parte do ecossistema Azure na nuvem, Hadoop no HDInsight oferece um número de benefícios, entre elas:

* Clusters de aprovisionamento automático de Hadoop. HDInsight clusters são muito mais fácil criar que configurar manualmente Hadoop clusters. Para obter detalhes, consulte o artigo [clusters de aprovisionar Hadoop HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componentes de Hadoop de estado do ClipArt. Para obter mais detalhes, consulte o artigo [componentes Hadoop, controlo de versões e ofertas de serviço no HDInsight][component-versioning].

* Disponibilidade de alta e fiabilidade dos clusters. Consulte o artigo [disponibilidade e fiabilidade dos clusters de Hadoop HDInsight](hdinsight-high-availability-linux.md) para obter detalhes.

* Armazenamento de dados eficiente e económico com o armazenamento de Blobs do Azure, uma opção de Hadoop compatível. Consulte o artigo [armazenamento de Blobs do Azure de utilização com Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md) para obter detalhes.

* Integração com outros serviços do Azure, incluindo [Web apps](https://azure.microsoft.com/documentation/services/app-service/web/) e [Base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Adicionais VM tipos e tamanhos para executar o HDInsight clusters. Ver [os componentes de Hadoop, controlo de versões e ofertas de serviço no HDInsight] [ component-versioning] para obter detalhes.

* Cluster de dimensionamento. Cluster de dimensionamento permite-lhe alterar o número de nós de um cluster de HDInsight a execução sem ter de eliminar ou criar novamente.

* Suporte de rede virtual. HDInsight clusters podem ser utilizados com a rede Virtual Azure para suportar o isolamento de recursos na nuvem ou cenários de híbridos ligar nuvem recursos com as no seu centro de dados.

* Baixa entrada de custo. Iniciar uma [versão de avaliação gratuita](https://azure.microsoft.com/free/)ou consultar os [Detalhes do preços HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Para obter mais informações sobre as vantagens no Hadoop no HDInsight, consulte a [página de funcionalidades Azure para HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight padrão e HDInsight Premium

HDInsight fornece ofertas de nuvem grande dados em duas categorias, Standard e Premium. HDInsight padrão fornece um cluster de escala empresarial que as organizações podem utilizar para executar as cargas de trabalho de dados grande. HDInsight Premium constrói no que e fornece avançadas analytical e capacidades de segurança para um cluster de HDInsight. Para mais informações, consulte o artigo [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Recursos para obter mais informações sobre análise de dados grande, Hadoop e HDInsight

Construa a nesta Introdução ao Hadoop na nuvem e análise de dados grande com os recursos abaixo.


### <a name="hadoop-documentation-for-hdinsight"></a>Documentação de Hadoop para HDInsight

* [Documentação HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): A página de documentação para Hadoop no Azure HDInsight com ligações para artigos, vídeos e mais recursos.

* [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de guia de aprovisionamento clusters HDInsight Hadoop e executar consultas de ramo de exemplo.

* [Introdução ao motores no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): um tutorial de guia para um cluster de motores de criação e execução de consultas de SQL de motores interativas.

* [Utilizar R servidor no HDInsight](hdinsight-hadoop-r-server-get-started.md): começar a utilizar o servidor de R no HDInsight Premium.

* [Aprovisionar HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md): Saiba como pode aprovisionar um cluster de HDInsight Hadoop através do Azure portal, Azure clip ou Azure PowerShell.


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Saiba mais sobre a biblioteca de software Apache Hadoop, um quadro que permite o processamento de grandes conjuntos de dados distribuído ao longo clusters de computadores.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: Saiba mais sobre a arquitetura e estrutura do Hadoop distribuído sistema de ficheiros, o sistema de armazenamento principal utilizado por aplicações Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: Saiba mais sobre a arquitetura de programação para escrever Hadoop aplicações que rapidamente processam grandes quantidades de dados em paralelo em grandes clusters de nós do cluster.


### <a name="microsoft-business-intelligence"></a>Informações da empresa Microsoft

Familiares (BI) ferramentas de business intelligence - como o Excel, o PowerPivot, o SQL Server Analysis Services e o SQL Server Reporting Services - obter, analisar e reportam dados integrados com o HDInsight utilizando o suplemento Power Query ou controlador de ODBC Hive da Microsoft.

Estas ferramentas de BI podem ajudar na sua análise de dados de grande:

* [Ligar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): Saiba como ligar o Excel para a conta de armazenamento do Windows Azure que armazena os dados associados ao seu cluster HDInsight com o Microsoft Power Query para Excel. Estação de trabalho Windows necessária. Programa Works with cluster baseado no Windows ou Linux.

* [Ligar o Excel ao Hadoop com o controlador de ODBC Hive da Microsoft](hdinsight-connect-excel-hive-odbc-driver.md): Saiba como importar dados do HDInsight com o controlador de ODBC Hive da Microsoft. Estação de trabalho Windows necessária. Programa Works with cluster baseado no Windows ou Linux.

* [Plataforma de nuvem da Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Saiba mais sobre o Power BI para Office 365, transferir a versão de avaliação do SQL Server e configurar o SharePoint Server 2013 e o SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
