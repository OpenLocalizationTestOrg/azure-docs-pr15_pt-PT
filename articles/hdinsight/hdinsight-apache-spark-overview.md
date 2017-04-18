<properties 
    pageTitle="Uma descrição geral dos motores de Apache no HDInsight | Microsoft Azure" 
    description="Introdução ao Apache motores no HDInsight e cenários em que pretende utilizar motores no HDInsight nas suas aplicações." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Descrição geral: Apache motores no HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Motores de Apache</a> é uma paralelamente abrir origem processamento framework que suporta o processamento de na memória para aumentar o desempenho das aplicações analíticos grande dados. Motor de processamento de motores baseia-se para a velocidade, facilidade de utilização e a análise de sofisticadas. Capacidades de cálculo na memória do motores tornam uma boa escolha para algoritmos iterativos nos cálculos máquina de aprendizagem e o gráfico. Motores também são compatível com o armazenamento de Blobs do Azure (WASB) para que os seus dados existentes armazenados no Azure facilmente podem ser processados através de motores.

Quando cria um cluster de motores no HDInsight, criar cluster Azure recursos com motores instalado e configurado. Demora cerca de dez minutos apenas para criar um cluster de motores no HDInsight. Os dados para serem processados são armazenados numa armazenamento de Blobs do Azure. Consulte o artigo [utilizar armazenamento de Blobs do Azure com HDInsight][hdinsight-storage].

![Motores de Apache no Azure HDInsight] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Motores de Apache no Azure HDInsight")


**Pretende começar a trabalhar com os motores de Apache no Azure HDInsight?** Consulte o artigo [Guia de introdução: criar um cluster de motores HDInsight Linux e executar as aplicações de exemplo utilizando Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Para obter uma lista de problemas conhecidos e limitações com a versão atual, consulte o artigo [problemas de motores de Apache no Azure HDInsight (Linux) conhecidos](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>Porquê utilizar motores no Azure HDInsight? 

Azure HDInsight oferece um serviço de motores totalmente gerido. Benefícios da utilização de motores em HDInsight são:

| Funcionalidade                             | Descrição       |
|-------------------------------------|-------------------|
| Facilidade de criar clusters            | Pode criar um novo cluster de motores no HDInsight em minutos utilizando o Portal de gestão do Azure, Azure PowerShell ou o .NET SDK HDInsight. Consulte o artigo [Introdução ao cluster de motores em HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidade de utilização                     | Motores em HDInsight clusters incluem blocos de notas Jupyter pré-configurada. Pode utilizar estes interativo processamento de dados e visualização. Os URLs para o é https://CLUSTERNAME.azurehdinsight.net/jupyter. Substitua o __nome de cluster__ com o nome do seu cluster de motores HDInsight.|
| REST APIs                       | Motores no HDInsight incluem [Lívio](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um API REST com base no servidor de tarefas motores remotamente submeter e monitorizar tarefas em execução. |
| Suporte para o arquivo de Lake de dados do Azure | Motores no HDInsight podem ser configurado para utilizar o arquivo de Lake Azure dados como um armazenamento adicional. Para mais informações no arquivo de Lake de dados, consulte o artigo [Descrição geral do Azure Lake arquivo de dados](../data-lake-store/data-lake-store-overview.md).
| Integração com os serviços do Azure | Motores no HDInsight vem com uma conexão a Azure evento concentradores. Os clientes podem ser criados aplicações transmissão utilizando os controladores de evento, para além de [Kafka](http://kafka.apache.org/), que já se encontra disponível como parte do motores. |
| Suporte para o servidor de R  | Pode configurar um servidor de R num cluster de motores de HDInsight para executar cálculos de R distribuídos com as velocidades prometidas com um cluster de motores. Para mais informações, consulte o artigo [começar a utilizar o servidor de R na HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Integração com o IntelliJ IDEIA  | Pode utilizar o plug-in de HDInsight para IntelliJ para criar e submeter aplicações a um cluster de motores de HDInsight. Para obter mais informações consulte o artigo [Utilizar HDInsight ferramentas Plug-in para IntelliJ IDEIA criar motores aplicações para cluster HDInsight motores Linux](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Consultas em simultâneo              | Motores no HDInsight suporta consultas em simultâneo. Permite que várias consultas a partir de um utilizador ou de várias consultas a partir de vários utilizadores e as aplicações partilhem os mesmos recursos de cluster. |
| Colocação em cache no SSDs                 | Pode escolher a dados de cache na memória ou no SSDs anexados a nós de cluster. Colocação em cache na memória fornece o melhor desempenho de consulta, mas pode ser dispendiosa; colocação em cache no SSDs fornece uma ótima opção para melhorar o desempenho da consulta sem ser necessário para criar um cluster de um tamanho necessário para se ajustar a todo o conjunto de dados na memória.|
| Integração com ferramentas de BI       | Motores para HDInsight fornece conectores para ferramentas de BI como o [Power BI](http://www.powerbi.com/) e o [quadro](http://www.tableau.com/products/desktop) para análise de dados.|
| Bibliotecas de Anaconda pré-carregado        | Motores clusters no HDInsight fornecidos com bibliotecas Anaconda pré-instaladas. Fornece [anaconda](http://docs.continuum.io/anaconda/) perto 200 bibliotecas para formação de máquina, análise de dados, visualização, etc.|
| Escalabilidade                     | Embora possa especificar o número de nós no seu cluster durante a criação, poderá querer aumente ou diminua o cluster para corresponder a carga de trabalho. Todos os HDInsight clusters permitem-lhe alterar o número de nós nos cluster. Além disso, clusters de motores podem ser ignorados sem perda de dados, dado que todos os dados é armazenado no armazenamento de Blobs do Azure. |
| Suporte de 24/7                    | Motores no HDInsight vem com suporte de 24/7 de nível empresarial e um SLA do 99,9% cima-time.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quais são os casos de utilização para motores no HDInsight?

Motores de Apache no HDInsight permite que os seguinte cenários chaves.

### <a name="interactive-data-analysis-and-bi"></a>Análise de dados interativos e BI

[Veja um tutorial](hdinsight-apache-spark-use-bi-tools.md)

Motores de Apache no HDInsight armazena dados em Blobs do Azure. Especialistas de negócios e fabricantes de decisão chave podem analisar e criar relatórios sobre os dados e utilizar o Microsoft Power BI para criar relatórios interativos dos dados analisados. Os analistas podem começar a partir de dados não estruturados semitransparente do/estruturada armazenamento Azure, definir um esquema para os dados utilizando a blocos de notas e, em seguida, criar modelos de dados através do Microsoft Power BI. Motores no HDInsight também suporta a um número de ferramentas de terceiros BI como quadro, Qlikview e SAP Lumira tornando uma plataforma ideal para os analistas de dados, especialistas de negócios e fabricantes de decisão chave.

### <a name="iterative-machine-learning"></a>Formação de máquina iterativo

[Veja um tutorial: prever construir temperaturas uisng dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Veja um tutorial: resultados da inspeção de alimentação de previsão](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Motores de Apache vem com [MLlib](http://spark.apache.org/mllib/), uma biblioteca incorporada na parte superior de motores de aprendizagem automática. Para além disso, os motores no HDInsight também incluem Anaconda, uma distribuição Python com uma variedade de pacotes para aprendizagem máquina. Conjugar isto com um suporte incorporado para Jupyter blocos de notas e tem um ambiente do início da linha para criar aplicações de aprendizagem automática.  

### <a name="streaming-and-real-time-data-analysis"></a>Análise de dados de transmissão e em tempo real

[Veja um tutorial](hdinsight-apache-spark-eventhub-streaming.md)

Análise de dados em tempo real é utilizada para cenários que se situa entre reduzir o tempo para conhecimentos aprofundados de dados através do processamento de dados como cair, a criação de um verdadeiro transmissão solução. Motores no HDInsight oferece um suporte formatado para construção de soluções de análise em tempo real. Enquanto motores já tem conectores para ingerir esta última dados de várias origens como sockets Kafka, Flume, Twitter, ZeroMQ ou TCP, motores no HDInsight adiciona primeira classe suporte para ingesting dados a partir do Azure evento concentradores. Evento concentradores são o serviço de colocação mais amplamente utilizado no Azure. Efetuar ter um suporte out of box para evento concentradores gerar no HDInsight uma plataforma ideal para criar pipeline de análise de tempo real.

##<a name="next-steps"></a>Quais os componentes são incluídos como parte de um cluster de motores?

Motores no HDInsight incluem os seguintes componentes que estão disponíveis em clusters por predefinição.

- [Motores Core](https://spark.apache.org/docs/1.5.1/). Inclui motores Core, motores SQL, motores transmissão APIs, GraphX e MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Lívio](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Jupyter bloco de notas](https://jupyter.org)

Motores no HDInsight também fornece um [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) de conectividade ao clusters de motores HDInsight a partir de ferramentas de BI como o Microsoft Power BI e o quadro.

## <a name="where-do-i-start"></a>Onde começar?

Comece com a criação de um cluster de motores no HDInsight Linux. Consulte o artigo [Guia de introdução: criar um cluster de motores HDInsight Linux e executar as aplicações de exemplo utilizando Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Próximos passos

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para analisar temperatura edifício utilizando dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de registo de Web site utilizando motores no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar as aplicações

* [Criar uma aplicação autónoma Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar tarefas remotamente num cluster de motores utilizando Lívio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para criar e submeter motores Scala aplicações](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizar o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para depurar as aplicações de motores remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocos de notas Zeppelin com um cluster de motores num HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de notas Jupyter num cluster de motores para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizar os pacotes externos com Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e ligar a um cluster de motores de HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
