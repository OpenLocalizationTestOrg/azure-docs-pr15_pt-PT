<properties
    pageTitle="Aplicação do Access Hadoop FIO registos através de programação | Microsoft Azure"
    description="Aplicação do Access através de programação inicia sessão um cluster de Hadoop HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Aplicação do Access FIO inicia sessão HDInsight baseados no Windows

Este tópico explica como pode aceder aos registos para as aplicações de FIO (ainda outro recurso Negotiator) que tem terminado num cluster Hadoop no Azure HDInsight

> [AZURE.NOTE] As informações neste documento só se aplica clusters HDInsight baseados no Windows. Para obter informações sobre como aceder ao FIO inicia sessão clusters baseado em Linux HDInsight, consulte o artigo [aplicação do Access FIO inicia sessão baseado em Linux Hadoop no HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Pré-requisitos

- Um cluster de HDInsight baseados no Windows.  Consulte o artigo [clusters de baseados no Windows criar Hadoop HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>Servidor de linha cronológica FIO

O <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de linha cronológica FIO</a> fornece informações genéricas aplicações concluídas, bem como informações de aplicação específica framework através de dois interfaces diferentes. Especificamente:

* Armazenamento e a obtenção de aplicação genérica obter informações sobre HDInsight clusters foi ativado com a versão 3.1.1.374 ou superior.
* O componente de informações da aplicação framework específica da linha cronológica do servidor não está atualmente disponível em HDInsight clusters.


Informações gerais sobre aplicações incluem os seguintes tipos de dados:

* O ID da aplicação, um identificador exclusivo de uma aplicação
* O utilizador que iniciou a aplicação
* Obter informações sobre como tentativas efetuadas para concluir a aplicação
* Os contentores utilizados por qualquer tentativa de aplicação determinado

No seu clusters HDInsight, estas informações serão armazenadas pelo Gestor de recursos do Azure para um arquivo de histórico no contentor predefinido da sua conta de armazenamento do Windows Azure predefinido. Estes dados genéricos sobre aplicações concluídas podem ser obtidos através de um REST API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Registos e FIO aplicações

FIO suporta vários modelos de programação (MapReduce a ser uma delas) por desacoplamento gestão de recursos a partir do agendamento/monitorização de aplicações. Isto é feito através de um global *ResourceManager* (RM), por-trabalhador-nó *NodeManagers* (NMs) e por aplicação *ApplicationMasters* (MGA). O AM por aplicação negocia recursos (CPU, memória, disco, rede) para executar a aplicação com o RM. O RM funciona com o NMs para conceder estes recursos, o que são concedidos como *contentores*. O AM é responsável para controlar o progresso de contentores atribuído à mesma pela RM. Uma aplicação poderá necessitar de muitos contentores dependendo da natureza da aplicação.

Além disso, cada aplicação pode consistir várias *tentativas de aplicação* para poder concluir em presença causa uma falha ou devido a perda de comunicação entre AM e um RM. Por conseguinte, contentores são concedidas a uma tentativa de específica de uma aplicação. Em sentido, um contentor fornece o contexto da unidade básica do trabalho executado por uma aplicação de FIO e todo o trabalho que é concluído dentro do contexto de um contentor é executado no nó trabalho único no qual tenha sido atribuído o contentor. Consulte [FIO conceitos] [ YARN-concepts] para referência ainda mais.

Registos de aplicação (e os registos de contentor associado) são críticos no depurar problemáticas Hadoop aplicações. FIO fornece uma boa estrutura para recolher, Agregar e armazenar registos da aplicação com o [Registo de agregação] [ log-aggregation] funcionalidade. A funcionalidade de registo agregação torna aceder a registos da aplicação mais determinista, tal como agrega registos através de todos os contentores num nó de trabalho e armazena-los como um agregado ficheiro de registo por nó de trabalho no sistema de ficheiros predefinido após a conclusão de uma aplicação. A aplicação poderá utilizar centenas ou milhares de contentores, mas os registos de todos os membros em contentores executar num nó trabalho único irão agregados sempre para um único ficheiro, que resulta num ficheiro de registo por nó trabalhador utilizado pela sua aplicação. Agregação de registo está ativada por predefinição nos HDInsight clusters (versão 3.0 e acima), e podem ser encontrados registos agregados no contentor predefinido do seu cluster na seguinte localização:

    wasbs:///app-logs/<user>/logs/<applicationId>

Nessa localização, o *utilizador* é o nome do utilizador que iniciou a aplicação e *applicationId* é o identificador exclusivo de uma aplicação como atribuído pela RM FIO.

Os registos agregados não são diretamente legíveis, à medida que são escritas um [TFile][T-file], [formato binário] [ binary-format] indexado pelo contentor. FIO fornece ferramentas de clip para estes registos de falha de sistema como texto simples para aplicações ou contentores de interesse. É possível visualizar estes registos tal como texto simples ao executar uma das seguinte FIO comandos diretamente nos nós de cluster (depois de ligar para o mesmo através de RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>FIO ResourceManager IU

O FIO ResourceManager da UI é executada na headnode cluster e pode ser acedido através do dashboard do Azure portal: 

1. Inicie sessão no [Azure portal](https://portal.azure.com/). 
2. No menu à esquerda, clique em **Procurar**, clique em **HDInsight Clusters**, clique num cluster baseados no Windows ao qual pretende aceder os registos de FIO da aplicação.
3. No menu superior, clique em **Dashboard**. Irá ver uma página aberta num browser novo separador chamado **HDInsight consola de consulta**.
4. A partir **Da consola de consulta do HDInsight**, clique em **Fio IU**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
