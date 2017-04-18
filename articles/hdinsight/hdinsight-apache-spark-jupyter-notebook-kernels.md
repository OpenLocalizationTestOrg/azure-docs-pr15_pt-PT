<properties 
    pageTitle="Disponíveis com Jupyter blocos de notas no HDInsight motores kernels clusters no Linux | Microsoft Azure" 
    description="Saiba mais sobre os kernels de bloco de notas de Jupyter adicionais disponíveis com cluster de motores HDInsight Linux." 
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
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Disponíveis para Jupyter blocos de notas com clusters de motores de Apache no HDInsight Linux kernels

Cluster de motores de Apache HDInsight (Linux) inclui Jupyter blocos de notas que pode utilizar para testar as suas aplicações. Um kernel é um programa que é executado e interpreta o seu código. Motores de HDInsight clusters fornecem duas kernels que pode utilizar com o bloco de notas Jupyter. Estas são:

1. **PySpark** (para aplicações escritas Python)
2. **Motores** (para aplicações escritas Scala)

Neste artigo, irá obter informações sobre como utilizar estes kernels e quais são as vantagens que receber do utilizá-los.

**Pré-requisitos:**

Tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de motores de Apache HDInsight Linux. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>Como posso utilizar os kernels? 

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do startboard, clique no mosaico do seu cluster de motores (se afixados-lo para a startboard). Também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Pá de cluster motores, clique em **Cluster Dashboard**e, em seguida, clique em **Jupyter bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Jupyter para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas com os novos kernels. Clique em **Novo**e, em seguida, clique em **Pyspark** ou **motores**. Deve utilizar a kernel motores para aplicações Scala e PySpark kernel para aplicações Python.

    ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Criar um novo bloco de notas Jupyter") 

3. Isto deve abrir um novo bloco de notas com o kernel que selecionou.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>Por que motivo devo utilizar os kernels PySpark ou motores?

Aqui estão algumas vantagens de utilizar as novas kernels.

1. **Predefinir contextos**. Com a **PySpark** ou kernels **motores** que são fornecidos com Jupyter blocos de notas, não necessita de definir os contextos motores ou ramo explicitamente antes de poder começar a trabalhar com a aplicação que está a desenvolver; estes estão disponíveis para si, por predefinição. Estes contextos são:

    * **sc** - contexto motores
    * **sqlContext** - ramo de contexto


    Sim, não tem de executar declarações semelhante ao seguinte para definir os contextos:

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    Em vez disso, pode utilizar os contextos predefinidos diretamente na sua aplicação.
    
2. **Magics de célula**. O kernel PySpark fornece alguns predefinidas "magics", que são comandos especiais que pode ligar com `%%` (por exemplo, `%%MAGIC` <args>). O comando mágico tem de estar na primeira palavra numa célula de código e permitir várias linhas de conteúdo. O word mágico deve ser na primeira palavra na célula. Adicionar nada antes da magia, até mesmo comentários, vai originar um erro.   Para mais informações sobre magics, consulte o artigo [aqui](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    A tabela abaixo apresenta os magics diferentes disponíveis através do miolo.

  	| Mágico     | Exemplo                         | Descrição  |
  	|-----------|---------------------------------|--------------|
  	| obter ajuda      | `%%help`                            | Gera uma tabela de todos os magics disponíveis com o exemplo e a descrição |
  	| informações      | `%%info`                          | Informações de sessão de resultados para o ponto final de Lívio atual |
  	| configurar | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura os parâmetros para a criação de uma sessão. O sinalizador forçar (-f) é obrigatória se já tiver sido criada uma sessão e a sessão será ignorada e recriada. Observe [POST /sessions de Lívio corpo pedir](https://github.com/cloudera/livy#request-body) para uma lista de parâmetros válidos. Parâmetros têm de ser passados no como uma cadeia JSON e tem de estar ligado a linha seguinte após a magia, conforme apresentado na coluna de exemplo. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Executa uma consulta de ramo contra a sqlContext. Se o `-o` parâmetro é transmitido, o resultado da consulta persiste na % local contexto Python como um dataframe [Pandas](http://pandas.pydata.org/) .   |
  	| local     |     `%%local`<br>`a=1`              | Todos os códigos modulares serão executado localmente. Código tem de ser código Python válido. |
  	| registos      | `%%logs`                        | Exporta os registos de início para a sessão Lívio atual.  |
  	| eliminar    | `%%delete -f -s <session number>` | Elimina uma sessão específica do ponto final Lívio atual. Tenha em atenção que não é possível eliminar a sessão é iniciada para o kernel própria. |
  	| limpeza   | `%%cleanup -f`                    | Elimina todas as sessões do atual ponto final Lívio, incluindo sessão este bloco de notas. O sinalizador -f Forçar é obrigatório.  |

    >[AZURE.NOTE] Além de magics adicionados pelo PySpark kernel, também pode utilizar [incorporados IPython magics](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo `%%sh`. Pode utilizar o `%%sh` mágico executar scripts e bloco de código na headnode cluster. 

3. **Visualização automática**. O kernel **Pyspark** automaticamente apresenta o resultado de consultas ramo e SQL. Tem a opção para escolher entre vários tipos de visualizações, incluindo a tabela, circular, linhas, área, barras.

## <a name="parameters-supported-with-the-sql-magic"></a>Os parâmetros são suportados com o % mágico de sql

A % sql mágico suporta diferentes parâmetros que pode utilizar para controlar o tipo de resultado que recebe quando executar consultas. A tabela seguinte apresenta o resultado.

| Parâmetro     | Exemplo                         | Descrição  |
|-----------|---------------------------------|--------------|
| + o      | `-o <VARIABLE NAME>`                          | Utilize este parâmetro para manter o resultado da consulta, na % contexto de Python local, como um dataframe [Pandas](http://pandas.pydata.org/) . O nome da variável dataframe é o nome da variável que especificar. |
| -q      | `-q`                          | Utilize esta opção para desativar a opção visualizações para a célula. Se não pretende visualizar automaticamente o conteúdo de uma célula e apenas quiser capturá-lo como um dataframe, em seguida, utilizar `-q -o <VARIABLE>`. Se pretende desativar visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL com efeitos de lado, tal como um `CREATE TABLE` declaração), utilize apenas `-q` sem especificar um `-o` argumento. |
| -m       |  `-m <METHOD>`    | Onde o **método** é **tomar** ou em **amostra** (a predefinição é **tomar**). Se o método de **tomar**, o kernel escolhe os elementos a partir da parte superior do conjunto de dados de resultado especificado pelo MAXROWS (descrita posteriormente esta tabela). Se o método de **exemplo**, o kernel aleatoriamente será de exemplo elementos do conjunto de dados de acordo com `-r` parâmetro, descrito a seguir nesta tabela.   |
| -r     |     `-r <FRACTION>`            | Aqui **FRAÇÃO** for um número de vírgula flutuante entre 0,0 e 1,0. Se for o método de exemplo para a consulta SQL `sample`, em seguida, o kernel aleatoriamente amostras a fração especificada dos elementos do conjunto de resultados para que; Por exemplo, se executar uma consulta SQL com os argumentos `-m sample -r 0.01`, em seguida, 1% das linhas resultado será colher aleatoriamente. |
| -n      | `-n <MAXROWS>`                        | **MAXROWS** é um valor de número inteiro. O kernel irá limitar o número de linhas de saída para **MAXROWS**. Se **MAXROWS** for um número negativo como **-1**, em seguida, o número de linhas no conjunto de resultados só estarão limitado. |

**Exemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

A instrução acima faz o seguinte:

* Seleciona todos os registos de **hivesampletable**.
* Uma vez que utilizamos - q, desativa visualização automática.
* Uma vez que utilizamos `-m sample -r 0.1 -n 500` aleatoriamente amostras 10% das linhas na hivesampletable e limites de tamanho do resultado definido para 500 linhas.
* Por fim, uma vez que utilizámos `-o query2` também guarda a saída para um dataframe denominado **consulta2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao utilizar as novas kernels

Independentemente kernel utiliza (PySpark ou motores), deixe os blocos de notas em execução irão consumir os recursos de cluster.  Com estes kernels, porque os contextos são predefinidos, basta sair os blocos de notas não eliminar o contexto e, consequentemente, os recursos de cluster irão continuar a ser utilizado. Aconselhável com miolo PySpark e motores seria utilizar a opção **Fechar e parar** a partir do menu de **ficheiro** do bloco de notas. Isto elimina se o contexto e, em seguida, sai do bloco de notas.    


## <a name="show-me-some-examples"></a>Mostrar-me alguns exemplos

Quando abre um bloco de notas Jupyter, verá duas pastas disponíveis no nível de raiz.

* A pasta **PySpark** tem os blocos de notas de exemplo que utilizam o kernel **Python** novo.
* A pasta **Scala** tem os blocos de notas de exemplo que utilizam o kernel **motores** novo.

Pode abrir o bloco de notas **00 - [leia-ME primeiro] motores o mágico Kernel funcionalidades** a partir da pasta **PySpark** ou **motores** para saber mais sobre os diferentes magics disponíveis. Também pode utilizar os outros exemplo blocos de notas disponíveis em duas pastas para saber como alcançar cenários diferentes utilizando Jupyter blocos de notas com clusters de motores de HDInsight.

## <a name="where-are-the-notebooks-stored"></a>Onde estão armazenados os blocos de notas?

Jupyter blocos de notas são guardados na conta de armazenamento associada ao cluster na pasta **/HdiNotebooks** .  Blocos de notas, ficheiros de texto e pastas que cria a partir de dentro Jupyter será acessíveis a partir do WASB.  Por exemplo, se utilizar Jupyter para criar uma pasta de **pasta** e um bloco de notas **myfolder/mynotebook.ipynb**, pode aceder desse bloco de notas na `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  O inverso também é verdadeiro, ou seja, se carregar um bloco de notas diretamente para a sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, o bloco de notas também estarão visível a partir do Jupyter.  Blocos de notas irão permanecer na conta de armazenamento, mesmo depois de cluster é eliminado.

A forma como os blocos de notas são guardados na conta de armazenamento é compatível com HDFS. Assim sendo, se SSH num cluster que pode utilizar o ficheiro de comandos de gestão de como o seguinte:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


No caso de existirem problemas de acesso uma conta de armazenamento para o cluster, os blocos de notas também são guardados na headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Browser suportado
Blocos de notas Jupyter executar contra HDInsight motores clusters só são suportados no Google Chrome.

## <a name="feedback"></a>Comentários

Os novos kernels estão em evolução fase e irão amadurecer ao longo do tempo. Isto poderia significa que podem ser alteradas APIs tal como estes kernels amadurecer. Agradecemos eventuais comentários que tiver ao utilizar estes kernels novos. Isto vai ser muito útil no modelar a versão final destes kernels. Pode deixar os seus comentários/comentários na secção de **comentários** na parte inferior deste artigo.


## <a name="seealso"></a>Consulte também


* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

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

* [Utilizar os pacotes externos com Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e ligar a um cluster de motores de HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)
