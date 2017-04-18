<properties
   pageTitle="Implementar e gerir topologias Apache tempestade no baseado em Linux HDInsight | Microsoft Azure"
   description="Saiba como implementar, monitorizar e gerir topologias Apache tempestade com o Dashboard de tempestade no baseado em Linux HDInsight. Utilize ferramentas de Hadoop para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Implementar e gerir topologias Apache tempestade no baseado em Linux HDInsight

Neste documento, conheça os princípios básicos de gerir e monitorizar topologias tempestade em execução no baseado em Linux tempestade sobre clusters de HDInsight.

> [AZURE.IMPORTANT] Os passos neste artigo requerem uma tempestade baseado em Linux num cluster de HDInsight. Para obter informações sobre como implementar e monitorização topologias no HDInsight baseados no Windows, consulte o artigo [Implementar e gerir topologias Apache tempestade no baseados no Windows HDInsight](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Pré-requisitos

* **Baseado em respostas Linux tempestade num cluster de HDInsight**: consulte o artigo [Introdução ao Apache tempestade no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) para obter passos sobre como criar um cluster de

* **Familiaridade com SSH e SCP**: para obter mais informações sobre como utilizar SSH e SCP com HDInsight, consulte o seguinte:

    * **Clientes Linux, Unix ou OS X**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Os clientes do Windows**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP um cliente**: Este é fornecido com todos os sistemas de Linux, Unix e OS X. Para clientes do Windows, recomendamos que PSCP, que se encontra disponível a partir da [página de transferência betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Iniciar uma topologia de tempestade

### <a name="using-ssh-and-the-storm-command"></a>Utilizar SSH e o comando tempestade

1. Utilize SSH para ligar ao HDInsight cluster. Substituir o **nome de utilizador** o nome do seu início de sessão SSH. Substitua o **nome de cluster** com o seu nome de cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como utilizar SSH para ligar ao seu cluster HDInsight, consulte os seguintes documentos:
    
    * **Clientes Linux, Unix ou OS X**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Os clientes do Windows**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Isto irá iniciar a topologia de WordCount de exemplo no cluster. Aleatoriamente gerar frases e contar a ocorrência de cada palavra na frases.

    > [AZURE.NOTE] Ao submeter topologia ao cluster, primeiro tem de copiar o ficheiro para caixa que contém o cluster antes de utilizar o `storm` comando. Isto pode ser feito utilizando o `scp` comando a partir do cliente onde o ficheiro existe. Por exemplo,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo WordCount e outros exemplos de starter tempestade, já sejam encontram incluídos no seu cluster no `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Através de programação

Através de programação pode implementar uma topologia para tempestade no HDInsight por comunicar com o serviço de Nimbus alojado no seu cluster. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicação de Java demonstra como implementar e iniciar uma topologia através do serviço de Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Monitorizar e gerir utilizando o comando tempestade

O `storm` utilitário permite-lhe trabalhar com a executar o topologias da linha de comandos. Segue-se uma lista de comandos utilizadas mais frequentemente. Utilizar `storm -h` para obter uma lista completa de comandos.

### <a name="list-topologies"></a>Lista topologias

Utilize o seguinte comando para listar todos os topologias a ser executado:

    storm list
    
Isto irá devolver informações semelhante ao seguinte:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desative e reativar

Desativá-los de uma topologia de interrompe-lo até que seja morto ou reactivar. Utilize o seguinte para desativar e reativar:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Topologias tempestade, uma vez iniciadas, irão continuar a executar o até que seja parado. Para parar de uma topologia, utilize o seguinte comando:

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Redistribuir

Rebalanceamento uma topologia de permite que o sistema de rever paralelismo da topologia. Por exemplo, se tiver redimensionada cluster para adicionar mais notas, rebalanceamento irá permitir que uma topologia tornar a execução utilizar de nós do novos.

> [AZURE.WARNING] Rebalanceamento primeiro uma topologia de desativa a topologia, redistribui trabalhadores uniformemente ao longo do cluster e, em seguida finalmente devolve a topologia ao Estado em que estava antes rebalanceamento ocorreu. Por isso, se a topologia estava ativa,-ficará activo novamente. Se foi desativada-lo, permanecerá desativado.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Monitorizar e gerir utilizando a IU tempestade

IU de tempestade fornece uma interface web para trabalhar com a executar o topologias e está incluída no seu cluster HDInsight. Para ver a IU tempestade, utilize um browser web para abrir __https://CLUSTERNAME.azurehdinsight.net/stormui__, onde o __nome de cluster__ é o nome do seu cluster.

> [AZURE.NOTE] Se pedido para fornecer um nome de utilizador e palavra-passe, introduza o administrador de cluster (admin) e palavra-passe que utilizou quando criar o cluster.


### <a name="main-page"></a>Página principal

Página principal da IU tempestade fornece as seguintes informações:

* **Cluster de resumo**: informações básicas sobre o cluster tempestade.

* **Topologia de resumo**: uma lista de executar o topologias. Utilize as ligações nesta secção para ver mais informações sobre topologias específicas.

* **Autoridade resumo**: informações sobre a autoridade tempestade.

* **Configuração de nimbus**: configuração Nimbus para o cluster.

### <a name="topology-summary"></a>Topologia de resumo

Selecionar uma ligação da secção **topologia de resumo** apresenta as seguintes informações sobre a topologia de:

* **Topologia de resumo**: informações básicas sobre a topologia.

* **Ações de topologia**: ações de gestão que pode efetuar para a topologia.

  * **Ativar**: processamento de currículos de uma topologia desativado.

  * **Desativar**: interrompe uma topologia em execução.

  * **Redistribuir**: ajusta paralelismo da topologia. Deverá redistribuir topologias em execução depois de ter mudado o número de nós no cluster. Esta opção permite-a topologia de modo a ajustar paralelismo para o Adquirente na íntegra para o número maior ou diminuído de nós no cluster.

    Para mais informações, consulte o artigo <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Compreender paralelismo de uma topologia de tempestade</a>.

  * **Eliminar**: termina uma topologia de tempestade após o limite de tempo especificado.

* **Estatísticas de topologia**: estatísticas sobre a topologia. Utilize as ligações na coluna da **janela** para definir o período de tempo para as entradas restantes na página.

* **Spouts**: spouts utilizados da topologia. Utilize as ligações nesta secção para ver mais informações sobre spouts específicos.

* **Bolts**: parafusos utilizados da topologia. Utilize as ligações nesta secção para ver mais informações sobre parafusos específicos.

* **Topologia de configuração**: A configuração da topologia de selecionado.

### <a name="spout-and-bolt-summary"></a>Vareta e resumo de raio

Selecionar uma vareta das secções **Spouts** ou **Bolts** apresenta as seguintes informações sobre o item selecionado:

* **Componente de resumo**: informações básicas sobre a vareta ou raio.

* **Estatística vareta/raio**: estatísticas sobre vareta ou raio. Utilize as ligações na coluna da **janela** para definir o período de tempo para as entradas restantes na página.

* **Estatísticas de entrada** (parafuso apenas): informações sobre as sequências de entrada média consumida pelo raio.

* **Estatísticas de saída**: informações sobre as sequências emitida pelo presente spout ou parafuso.

* **Testamenteiros**: informações sobre as instâncias da vareta ou raio. Selecione a entrada de **porta** para um executor visualizar um registo de informações de diagnóstico produzidas para esta instância específica.

* **Erros**: quaisquer informações de erro para este spout ou parafuso.

## <a name="rest-api"></a>REST API

IU de tempestade baseia-se na parte superior de REST API, para que possa efectuar semelhantes monitorização e gestão funcionalidade ao utilizar a API REST. Pode utilizar a API REST para criar ferramentas personalizadas para gerir e monitorizar topologias tempestade.

Para mais informações, consulte o artigo [Tempestade IU REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). As seguintes informações são específicas a utilizar a API REST com Apache tempestade no HDInsight.

> [AZURE.IMPORTANT] API REST tempestade não está disponível ao público na Internet e tem de ser acedida utilizando um túnel SSH para o nó do cabeça cluster HDInsight. Para obter informações sobre como criar e utilizar um túnel SSH, consulte o artigo [Utilizar SSH túnel para aceder ao web Ambari IU, ResourceManager, JobHistory, NameNode, Oozie e outros web da IU](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Base de URI

O URI base para a API REST em baseado em Linux HDInsight clusters está disponível no nó cabeça na **api/https://HEADNODEFQDN:8744/v1/**; No entanto, o nome de domínio do nó cabeça é gerado durante a criação de cluster e não é estático.

Pode encontrar o nome de domínio completamente qualificado (FQDN) para o nó do cabeça cluster de várias formas diferentes:

* __A partir de um SSH sessão__: Utilize o comando `headnode -f` a partir de uma sessão SSH ao cluster.

* __A partir do Ambari Web__: selecione __Serviços__ situado na parte superior da página, em seguida, selecione __tempestade__. No separador __Resumo__ , selecione __Tempestade IU servidor__. O FQDN do nó que a IU tempestade e REST API está em execução no serão na parte superior da página.

* __A partir do Ambari REST API__: Utilize o comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para obter informações sobre o nó que a IU tempestade e REST API estão em execução no. Substitua a palavra-passe de administrador para o cluster __palavra-passe__ . Substitua o __nome de cluster__ com o nome do cluster. Na resposta, a entrada de "host_name" contém o FQDN do nó.

### <a name="authentication"></a>Autenticação

Pedidos à REST API tem de utilizar **a autenticação básica**, para utilizar o nome de administrador do HDInsight cluster e a palavra-passe.

> [AZURE.NOTE] Porque é enviada autenticação básica utilizando texto simples, deve utilizar **sempre** HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Devolver valores

As informações que são devolvidas a partir da API REST apenas podem ser utilizáveis a partir do cluster ou máquinas virtuais da mesma rede Virtual Azure como cluster. Por exemplo, o nome de domínio completamente qualificado (FQDN) devolvido para servidores Zookeeper não estar acessível a partir da Internet.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como implementar e monitorizar topologias através do Dashboard tempestade, saiba como [topologias com base em desenvolver Java utilizando Maven](hdinsight-storm-develop-java-topology.md).

Para obter uma lista das topologias de exemplo mais, consulte o artigo [topologias de exemplo para tempestade no HDInsight](hdinsight-storm-example-topology.md).
