<properties 
    pageTitle="Instalar Jupyter bloco de notas no seu computador e ligá-lo a um cluster de motores de HDInsight | Microsoft Azure" 
    description="Saiba mais sobre como instalar Jupyter bloco de notas localmente no seu computador e ligá-lo a um cluster de motores de Apache no Azure HDInsight." 
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
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Instalar Jupyter bloco de notas no seu computador e ligar a Apache motores cluster no HDInsight Linux

Este artigo vai aprender instalar o bloco de notas Jupyter, com a PySpark personalizado (por Python) e kernels motores (para Scala) com magia motores e ligar o bloco de notas a um cluster de HDInsight. Podem existir um número de razões para instalar o Jupyter no seu computador local e é possível que alguns desafios também. Para obter uma lista das razões e os desafios, consulte a secção [por que motivo devo instalar Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Existem três passos principais envolvidos na instalação do Jupyter e a magia motores no seu computador.

* Instalar o bloco de notas Jupyter
* Instalar os kernels PySpark e motores com a magia motores
* Configurar mágico motores para aceder ao cluster de motores HDInsight

Para mais informações sobre os kernels personalizados e a magia motores disponível para Jupyter blocos de notas com HDInsight cluster, consulte o artigo [Kernels disponíveis para Jupyter blocos de notas com clusters de Apache motores Linux no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos listados aqui não estão para instalar o Jupyter. Estes são para ligar o bloco de notas Jupyter a um cluster de HDInsight quando o bloco de notas estiver instalado.

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de motores de Apache HDInsight Linux. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instale Jupyter bloco de notas no seu computador

Tem de instalar Python antes de poder instalar Jupyter blocos de notas. Python e Jupyter estão disponíveis como parte da [distribuição Ananconda](https://www.continuum.io/downloads). Quando instala Anaconda, que realmente de instalar uma distribuição de Python. Quando Anaconda estiver instalada, adicione a instalação Jupyter ao executar um comando. Esta secção fornece as instruções que devem ser seguidos.

1. Transferir o [instalador do Anaconda](https://www.continuum.io/downloads) para a sua plataforma e executar a configuração. Ao executar o Assistente de configuração, certifique-se de que seleciona a opção para adicionar Anaconda para a variável de PATH.

2. Execute o seguinte comando para instalar Jupyter.

        conda install jupyter

    Para mais informações sobre installting Jupyter, consulte o artigo [Instalar o Jupyter utilizando Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar o kernels e mágico motores

Para obter instruções sobre como instalar a magia motores, kernels PySpark e motores, consulte a [documentação sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurar mágico motores para aceder ao cluster de motores de HDInsight

Nesta secção Configurar a magia de motores instalado anteriormente para ligar a um cluster de motores de Apache tem já tiver criado no Azure HDInsight.

1. As informações de configuração de Jupyter normalmente estão armazenadas no diretório de raiz de utilizadores. Para localizar o seu diretório no qualquer plataforma do SO, escreva os seguintes comandos.

    Comece a shell de Python. Na janela de comandos, escreva o seguinte:

        python

    No Python shell, introduza o seguinte comando para descobrir o diretório.

        import os
        print(os.path.expanduser('~'))

2. Navegue para o diretório e criar uma pasta denominada **.sparkmagic** se ainda não existir.

3. Dentro da pasta, crie um ficheiro denominado **config.json** e adicione o seguinte fragmento de JSON dentro da mesma.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Substitua **{USERNAME}**, **{CLUSTERDNSNAME}**e **{BASE64ENCODEDPASSWORD}** com valores adequados. Pode utilizar um número de utilitários na sua linguagem de programação favorita ou online para gerar uma palavra-passe de codificação base64 para a sua palavra-passe actualy. Um simple fragmento de Python para executar a partir da sua linha de comandos seria:

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Inicie o Jupyter. Utilize o seguinte comando a partir da linha de comandos.

        jupyter notebook

6. Certifique-se de que consegue ligar ao cluster utilizando o bloco de notas Jupyter e que pode utilizar a magia motores disponível com as kernels. Execute os passos seguintes.

    1. Crie um novo bloco de notas. A partir do lado direito, clique em **Novo**. Deverá ver kernel predefinido **Python2** e as duas novas kernels que instalar, **PySpark** e **motores**.

        ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Criar um novo bloco de notas Jupyter")

    
        Clique em **PySpark**.


    2. Execute o seguinte fragmento de código.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Se pode obter o resultado com êxito, a sua ligação ao HDInsight cluster é verificada.

    >[AZURE.TIP] Se pretende atualizar a configuração do bloco de notas para ligar a um cluster diferente, atualize o config.json com o novo conjunto de valores, conforme mostrado no passo 3 acima. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que motivo devo instalar Jupyter no meu computador?

Pode ser um número de razões pelas quais poderá pretender instalar Jupyter no seu computador e, em seguida, ligue-a um cluster de motores num HDInsight.

* Apesar de Jupyter blocos de notas já sejam encontram disponíveis no cluster motores no Azure HDInsight, instalar Jupyter no seu computador fornece que a opção para criar os seus blocos de notas localmente, teste a sua aplicação relativamente a um cluster em execução e, em seguida, carregue os blocos de notas ao cluster. Para carregar os blocos de notas ao cluster, pode carregá-las utilizando Jupyter bloco de notas que está a ser executado ou ao cluster ou guardá-las para a pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para obter mais informações sobre como os blocos de notas são armazenados no cluster, consulte o artigo [onde são Jupyter blocos de notas armazenados](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, pode ligar a clusters motores diferentes, com base no seu requisito de aplicação.
* Pode utilizar o GitHub para implementar um sistema de controlo de origem e têm controlo de versão para os blocos de notas. Também pode fazer com um ambiente de colaboração onde vários utilizadores podem trabalhar com o mesmo bloco de notas.
* Pode trabalhar com blocos de notas localmente sem ter um cluster de. Só precisa de um cluster para testar os seus blocos de notas contra, não para gerir manualmente blocos de notas ou um ambiente de desenvolvimento.
* Pode ser mais fácil de configurar o seu próprio ambiente de desenvolvimento local que configurar a instalação de Jupyter no cluster.  Pode tirar partido de todo o software que já a instalou localmente sem configurar um ou mais clusters remotos.

>[AZURE.WARNING] Com Jupyter instalado no seu computador local, vários utilizadores podem executar mesmo bloco de notas no mesmo cluster motores ao mesmo tempo. Situação, são criados múltiplas Lívio sessões. Se ocorrer um problema e pretende que depurar, será uma tarefa complexa para controlar qual a sessão Lívio pertence a qual o utilizador.




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

* [Kernels disponíveis para o bloco de notas Jupyter num cluster de motores para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizar os pacotes externos com Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)
