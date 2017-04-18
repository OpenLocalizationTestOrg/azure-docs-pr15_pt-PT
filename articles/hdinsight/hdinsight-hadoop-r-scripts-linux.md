<properties
    pageTitle="Instalar R baseado em Linux HDInsight | Microsoft Azure"
    description="Saiba como instalar e utilizar R para personalizar clusters Hadoop baseado em Linux."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar R no HDInsight Hadoop clusters

Pode instalar o R em qualquer tipo de cluster no Hadoop no HDInsight utilizando a personalização de cluster de **Acção de Script** . Isto permite cientistas de dados e os analistas utilizar R para implementar a arquitetura de programação MapReduce/FIO poderosa processar grandes quantidades de dados em clusters Hadoop que são implementadas no HDInsight.

> [AZURE.IMPORTANT] A [camada premium](https://azure.microsoft.com/pricing/details/hdinsight/) oferta para HDInsight inclui R servidor como parte do seu cluster HDInsight. Isto permite que os scripts de R utilizar MapReduce e motores para executar cálculos distribuídos. Para mais informações, consulte o artigo [começar a utilizar o servidor de R na HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>O que é R?

O <a href="http://www.r-project.org/" target="_blank">Project R para computação estatísticas de</a> é um abrir origem de idioma e ambiente para computação de estatística. R fornece centenas de funções de estatística compilação-in e as suas próprias linguagem de programação que combina aspetos de programação funcional e orientada para objectos. Fornece também capacidades gráficas extensas. R é o ambiente de programação preferido para mais profissional estaticistas e cientistas numa grande variedade de campos.

Scripts de R podem ser executados em Hadoop clusters de HDInsight que foram personalizados utilizando Script ação quando criada para instalar o ambiente de R. R é compatível com o armazenamento de Blobs do Azure (WASB) para que possam ser processados dados armazenados aí utilizar R num HDInsight.

## <a name="what-the-script-does"></a>O que faz o script

A ação de script que utilizou para instalar o R no seu cluster HDInsight instala seguintes pacotes de Ubuntu, que disponibiliza uma instalação de R básica:

* [base de r](http://packages.ubuntu.com/precise/r-base): pacote de Base GNU R
* [r-base-Dev Center](http://packages.ubuntu.com/precise/r-base-dev): pacotes Auxilliary GNU R

Os seguintes pacotes RHadoop também são instalados, que fornecer integração com MapReduce e HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite que os programadores R utilizar Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite que os programadores R utilizar Hadoop HDFS (WASB para HDInsight)

Para além disso, são instalados os pacotes de R seguintes:

| Pacote R | O que fornece |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Um nível baixo R para Java interface. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integração R e C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Objetos serializar/serialização R para JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funções para operações de valor bit a bit no vectores de número inteiro. |
| [Resumo](https://cran.r-project.org/web/packages/digest/index.html) | Crie Hash criptografia digestão de objetos de R. |
| [funcional](https://cran.r-project.org/web/packages/functional/index.html) | Caril, compor e outras funções superior ordem |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Flexível reestruturar e agregar dados. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Wrappers simples e consistentes para operações de cadeia comuns. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Ferramentas para dividir, aplicar e combinar dados. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Ferramentas para mover as estatísticas de janela, GIF, Base64, ROC AUC, etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Aproximação de correspondência de cadeia e distância funções de cadeia. |

## <a name="install-r-using-script-actions"></a>Instalar R utilizar ações de Script

A seguinte ação de script é utilizada para instalar R num HDInsight cluster. https://hdiconfigactions.blob.Core.Windows.NET/linuxrconfigactionv01/r-Installer-v01.SH
    
Esta secção fornece instruções sobre como utilizar o script ao criar um novo cluster através do portal Azure. 

> [AZURE.NOTE] Azure PowerShell, o clip do Azure, o HDInsight .NET SDK ou Gestor de recursos do Azure modelos podem também ser utilizados para aplicar acções de script. Também pode aplicar acções de script para já executado clusters. Para mais informações, consulte o artigo [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de aprovisionamento, utilizando os passos em [clusters baseado em aprovisionar Linux HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não concluída de aprovisionamento.

2. No pá **Configuração opcional** , selecione **Ações de Script**e forneça as informações abaixo:

    * __Nome__: introduza um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __HEAD__: selecione esta opção
    * __TRABALHADOR__: selecione esta opção
    * __ZOOKEEPER__: selecione esta opção para instalar no nó Zookeeper.
    * __Parâmetros__: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, utilize o botão **Selecionar** para guardar a configuração. Por fim, utilize o botão **Selecione** na parte inferior da pá a **Configuração opcional** para guardar as informações de configuração opcional.

4. Continue a cluster de aprovisionamento, tal como descrito em [clusters baseado em aprovisionar Linux HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Executar scripts de R

Depois do cluster já terminou de aprovisionamento, utilize os passos seguintes para utilizar R para executar uma operação de MapReduce no cluster.

1. Ligar ao cluster HDInsight utilizando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como utilizar SSH com HDInsight, consulte o seguinte:

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. A partir do `username@hn0-CLUSTERNAME:~$` pedido, introduza o seguinte comando para iniciar uma sessão de R interativa:

        R

3. Introduza o seguinte programa R. Isto gera os números de 1 a 100 e, em seguida, multiplica-los por 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    A primeira linha chamadas rmr2 de biblioteca RHadoop, que é utilizado para operações de MapReduce.

    A segunda linha gera valores 1-100, em seguida, armazena-os para o ficheiro de Hadoop sistema utilizando `to.dfs`.

    A terceira linha cria um processo de MapReduce utilizando a funcionalidade fornecida pela rmr2 e começa a processamento. Deverá ver várias linhas, desloque-se anteriores, tal como o processamento começa.

4. Em seguida, utilize o seguinte para ver o caminho temporário que a saída de MapReduce foi armazenada para:

        print(calc())

    Isto deve ser algo semelhante ao `/tmp/file5f615d870ad2`. Para ver o resultado real, utilize o seguinte:

        print(from.dfs(calc))

    O resultado deve ter este aspeto:

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Para sair R, introduza o seguinte:

        q()


## <a name="next-steps"></a>Próximos passos

- [Instalar e utilizar clusters de matiz no HDInsight](hdinsight-hadoop-hue-linux.md). Matiz é uma web IU torna mais fácil criar, executar e guardar porco e ramo de tarefas, bem como o armazenamento de predefinido utilizado para procurar o HDInsight cluster.

- [Instalar Giraph sobre clusters de HDInsight](hdinsight-hadoop-giraph-install.md). Utilize a personalização de cluster para instalar Giraph HDInsight Hadoop clusters. Giraph permite-lhe executar o processamento de graph utilizando Hadoop e podem ser utilizado com Azure HDInsight.

- [Instalar Solr sobre clusters de HDInsight](hdinsight-hadoop-solr-install.md). Utilize a personalização de cluster para instalar Solr HDInsight Hadoop clusters. Solr permite-lhe efetuar operações de procura poderosa nos dados armazenados.

- [Instalar matiz sobre clusters de HDInsight](hdinsight-hadoop-hue-linux.md). Utilize a personalização de cluster para instalar matiz clusters HDInsight Hadoop. Matiz é um conjunto de aplicações Web utilizada para interagir com um cluster de Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
