<properties
    pageTitle="Utilizar matiz com Hadoop no HDInsight Linux clusters | Microsoft Azure"
    description="Saiba como instalar e utilizar matiz com clusters de Hadoop no HDInsight Linux."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar matiz no HDInsight Hadoop clusters

Saiba como instalar matiz no HDInsight Linux clusters e utilizar o túnel para encaminhar os pedidos para matiz.

## <a name="what-is-hue"></a>O que é matiz?

Matiz é um conjunto de aplicações Web utilizada para interagir com um cluster de Hadoop. Pode utilizar matiz para procurar o armazenamento associado a um cluster de Hadoop (WASB, no caso de HDInsight clusters), executar tarefas ramo e scripts porco, etc. Os seguintes componentes estão disponíveis com as instalações do matiz num cluster HDInsight Hadoop.

* Editor de ramo cera
* Porco
* Gestor de Metastore
* Oozie
* FileBrowser (que comunica contentor de predefinido WASB)
* Browser de tarefa

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e o Microsoft Support irá ajudar a identificar e resolver problemas relacionados com a estes componentes.
>
> Componentes personalizados recebem faremos suporte para o ajudar a resolver ainda mais o problema. Isto poderá resultar em resolver o problema ou pedir-lhe para participar canais disponíveis para as tecnologias de abrir origem onde se encontram conhecimentos abrangente para essa tecnologia. Por exemplo, existem muitos sites de Comunidade que podem ser utilizados, tal como: [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalar matiz utilizar ações de Script

A ação de script seguintes pode ser utilizada para instalar o matiz num cluster baseado em Linux HDInsight.
https://hdiconfigactions.blob.Core.Windows.NET/linuxhueconfigactionv02/Install-hue-uber-v02.SH
    
Esta secção fornece instruções sobre como utilizar o script quando Aprovisiona o cluster utilizando o Portal do Azure. 

> [AZURE.NOTE] Azure PowerShell, o clip do Azure, o HDInsight .NET SDK ou Gestor de recursos do Azure modelos podem também ser utilizados para aplicar acções de script. Também pode aplicar acções de script para já executado clusters. Para mais informações, consulte o artigo [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de aprovisionamento, utilizando os passos em [clusters de aprovisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não concluída de aprovisionamento.

    > [AZURE.NOTE] Para instalar matiz em HDInsight clusters, o tamanho de headnode recomendada é, pelo menos, A4 (8 núcleos, 14 GB de memória).

2. No pá **Configuração opcional** , selecione **Ações de Script**e forneça as informações, conforme apresentado abaixo:

    ![Parâmetros de acção de script de fornecer para matiz] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Parâmetros de acção de script de fornecer para matiz")

    * __Nome__: introduza um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __HEAD__: selecione esta opção
    * __TRABALHADOR__: deixe este em branco.
    * __ZOOKEEPER__: deixe este em branco.
    * __Parâmetros__: deixe este em branco.

3. Na parte inferior das **Ações de Script**, utilize o botão **Selecionar** para guardar a configuração. Por fim, utilize o botão **Selecione** na parte inferior da pá a **Configuração opcional** para guardar as informações de configuração opcional.

4. Continue a cluster de aprovisionamento, tal como descrito em [clusters de aprovisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Utilizar matiz com HDInsight clusters

SSH túnel é a única forma para aceder a matiz num cluster assim que estiver em execução. Túnel através do SSH permite que o tráfego ir diretamente para o headnode do cluster onde matiz está em execução. Depois do cluster já terminou de aprovisionamento, utilize os passos seguintes para utilizar matiz num cluster HDInsight Linux.

1. Utilize as informações em [Utilizar SSH túnel para aceder a Ambari IU da web, ResourceManager, JobHistory, NameNode, Oozie e outros web da IU](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH do seu sistema de cliente para HDInsight cluster e, em seguida, configure o seu browser para utilizar o túnel SSH como um proxy.

2. Depois de ter criado um túnel SSH e configurado o seu browser para o tráfego de proxy através do mesmo, tem de localizar o nome do anfitrião do nó de cabeça principal. Pode fazê-lo através da ligação ao cluster utilizando SSH no Porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` onde __NOMEDOUTILIZADOR__ é o seu nome de utilizador SSH e __nome de cluster__ é o nome do seu cluster.

    Para obter mais informações sobre como utilizar SSH, consulte os seguintes documentos:

    * [Utilizar SSH com baseado em Linux HDInsight a partir de um cliente Linux, Unix ou Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH com baseado em Linux HDInsight a partir de um cliente do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Quando estabelecida, utilize o seguinte comando para obter o nome de domínio completamente qualificado da headnode principal:

        hostname -f

    Isto irá devolver um nome semelhante ao seguinte:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Este é o nome do anfitrião do headnode primária onde se encontra o Web site matiz.

2. Utilize o browser para abrir o portal do matiz em http://HOSTNAME:8888. Substitua o nome do anfitrião com o nome obtido no passo anterior.

    > [AZURE.NOTE] Quando inicia sessão pela primeira vez, ser-lhe-á pedido para criar uma conta que inicie sessão no portal do matiz. As credenciais que especificar aqui serão limitadas ao portal e não relacionados com o administrador ou as credenciais de utilizador SSH que especificou de aprovisionar o cluster.

    ![Inicie sessão no portal de matiz] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Especificar as credenciais para o portal de matiz")

### <a name="run-a-hive-query"></a>Executar uma consulta de ramo

1. A partir do portal de matiz, clique em **Editores de consulta**e, em seguida, clique em **Hive** para abrir o editor de ramo.

    ![Utilizar ramo] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Utilizar ramo")

2. No separador **dar assistência** em **base de dados**, deverá ver **hivesampletable**. Esta é uma tabela de exemplo que é fornecida com todos os clusters de Hadoop no HDInsight. Introduza uma consulta de exemplo no painel direito e ver os resultados no separador **resultados** no painel de abaixo, conforme apresentado na captura de ecrã.

    ![Ramo de executar consulta] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Ramo de executar consulta")

    Também pode utilizar o separador **gráfico** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Procure o armazenamento de cluster

1. A partir do portal de matiz, clique em **Localizador de ficheiro** no canto superior direito da barra de menus.

2. Por predefinição, o browser do ficheiro abre no directório **/user/myuser** . Clique na barra para a direita antes do directório de utilizador no caminho para ir para a raiz do contentor de armazenamento Azure associado ao cluster.

    ![Browser de ficheiro de utilização] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Browser de ficheiro de utilização")

3. Botão direito do rato num ficheiro ou pasta para ver as operações disponíveis. Utilize o botão **carregar** no canto direito para carregar ficheiros para o diretório atual. Utilize o botão **Novo** para criar novos ficheiros ou directórios.

> [AZURE.NOTE] O navegador de ficheiro matiz pode mostrar apenas o conteúdo do contentor predefinido associado ao cluster HDInsight. Qualquer contas/contentores de armazenamento adicional que poderá ter associado ao cluster não será acessíveis utilizando o browser do ficheiro. No entanto, os contentores adicionais associados ao cluster será sempre acessíveis para as tarefas de ramo. Por exemplo, se introduzir o comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` no editor de ramo, pode ver os conteúdos de também contentores adicionais. Este comando **newcontainer** não é o contentor predefinido associado a um cluster.

## <a name="important-considerations"></a>Considerações importantes

1. O script que utilizou para instalar o matiz instala-o apenas na headnode principal do cluster.

2. Durante a instalação, vários serviços de Hadoop (HDFS, FIO, MR2, Oozie) são reiniciados para atualizar a configuração. Depois do script concluir instalação matiz, poderá demorar algum tempo para outros serviços de Hadoop iniciar o. Pode afectar desempenho do matiz inicialmente. Depois de todos os serviços de arranque, matiz irão estar totalmente funcional.

3.  Matiz não reconhece Tez tarefas, que é a predefinição para ramificação do registo atual. Se pretender utilizar MapReduce como o motor de execução de ramo, atualize o script para utilizar o seguinte comando no seu script:

        set hive.execution.engine=mr;

4.  Com Linux clusters, pode ter um cenário onde os seus serviços estão em execução na headnode principal enquanto o Gestor de recursos pode estar em execução no secundária. Como um cenário poderá resultar em erros (mostrados abaixo) ao utilizar matiz para ver os detalhes das tarefas em execução no cluster. No entanto, pode ver os detalhes da tarefa quando a tarefa estiver concluída.

    ![Erro de portal matiz] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Erro de portal matiz")

    Este é devido a um problema conhecido. Como solução, modifique Ambari para que o Gestor de recursos ativa também é executada na headnode principal.

5.  Matiz compreende WebHDFS enquanto HDInsight clusters utilizam o armazenamento do Windows Azure utilizando `wasbs://`. Sim, o script personalizado utilizado com a ação de script instala WebWasb, que é um serviço compatível com o WebHDFS para falar com WASB. Por isso, apesar do portal de matiz diz HDFS em locais (por exemplo, quando move o rato sobre o **Browser do ficheiro**), deve ser interpretada como WASB.


## <a name="next-steps"></a>Próximos passos

- [Instalar Giraph sobre clusters de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilize a personalização de cluster para instalar Giraph HDInsight Hadoop clusters. Giraph permite-lhe executar o processamento de graph utilizando Hadoop e podem ser utilizado com Azure HDInsight.

- [Instalar Solr sobre clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilize a personalização de cluster para instalar Solr HDInsight Hadoop clusters. Solr permite-lhe efetuar operações de procura poderosa nos dados armazenados.

- [Instalar R sobre clusters de HDInsight](hdinsight-hadoop-r-scripts-linux.md). Utilize a personalização de cluster para instalar R clusters HDInsight Hadoop. R é um idioma abrir origem e um ambiente para computação de estatística. Fornece centenas de funções de estatística incorporadas e as suas próprias linguagem de programação que combina aspetos de programação funcional e orientada para objectos. Fornece também capacidades gráficas extensas.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
