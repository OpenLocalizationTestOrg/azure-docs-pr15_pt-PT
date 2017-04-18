<properties
    pageTitle="Monitorizar Hadoop clusters no HDInsight utilizar a API Ambari | Microsoft Azure"
    description="Utilize as APIs da Ambari Apache para criar, gerir e Hadoop clusters de monitorização. Ferramentas de operador intuitiva e APIs ocultar a complexidade da Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Monitorizar Hadoop clusters no HDInsight utilizar a API Ambari

Saiba como monitorizar HDInsight clusters utilizando Ambari APIs.

> [AZURE.NOTE] As informações neste artigo destinam-se principalmente clusters HDInsight baseados no Windows, que disponibiliza uma versão só de leitura da API do resto do Ambari. Para baseado em Linux clusters, consulte o artigo [Gerir Hadoop clusters utilizando Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>O que é Ambari?

[Apache Ambari] [ ambari-home] é utilizada para aprovisionamento, gerir e Apache Hadoop clusters de monitorização. Inclui uma coleção de intuitiva das ferramentas de operador e um conjunto robusto de APIs ocultar a complexidade da Hadoop, simplificar a operação de clusters. Para mais informações sobre as APIs, consulte o artigo [Referência da API Ambari][ambari-api-reference]. 

HDInsight atualmente suporta apenas a funcionalidade de monitorização Ambari. Ambari API 1.0 é suportado pelo clusters de versão 3.0 e 2.1 HDInsight. Este artigo abrange ao aceder ao Ambari APIs sobre clusters de versão 3.1 e 2.1 HDInsight. A diferença entre os dois principais é que alguns dos componentes foram alterados com a introdução de novas capacidades (como o servidor de histórico de tarefa). 

**Pré-requisitos**

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Opcional) [cURL] [curl]. Para instalá-lo, consulte o artigo [cURL lançamentos e transferências][curl-download].

    >[AZURE.NOTE] Quando utilizar o comando de Laço no Windows, utilizar aspas em vez de único aspas para os valores de opção.

- **Cluster de um Azure HDInsight**. Para obter instruções sobre cluster de aprovisionamento, consulte o artigo [começar a utilizar o HDInsight] [ hdinsight-get-started] ou [aprovisionar HDInsight clusters][hdinsight-provision]. Terá os seguintes dados até o tutorial:

    Propriedade cluster|Nome da variável PowerShell Azure|Valor|Descrição
    ---|---|---|---
    Nome do cluster HDInsight|$clusterName||O nome do seu cluster HDInsight.
    Cluster nome de utilizador|$clusterUsername||Nome de utilizador de cluster especificado quando o cluster foi criado.
    Palavra-passe cluster|$clusterPassword||Palavra-passe de utilizador de cluster.

    >[AZURE.NOTE] Fill-in os valores na tabela. Isto vai ser úteis para percorrer este tutorial.

## <a name="jump-start"></a>Saltar iniciar

Existem várias formas de utilizar Ambari para monitorizar a HDInsight clusters.

**Utilizar o Azure PowerShell**

Segue-se um script do Azure PowerShell para obter as informações de controlador da tarefa de MapReduce *num cluster HDInsight 3.1.*  A diferença de chave é que recomendamos separar-se destes detalhes a partir do serviço de FIO (em vez de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Segue-se um script do Azure PowerShell para obter o MapReduce tarefa controlador informações *num cluster HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

O resultado é:

![Jobtracker saída][img-jobtracker-output]

**Utilizar o laço**

Segue-se um exemplo de obter informações de cluster utilizando Laço:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

O resultado é:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para 10/8/2014 solte**:

Ao utilizar o ponto final Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* devolve o nome de domínio completamente qualificado (FQDN) do nó em vez do nome do anfitrião. Antes do lançamento 8/10/2014, neste exemplo devolvido simplesmente "**headnode0**". Após o lançamento de 8/10/2014, obtém o FQDN "**headnode0. { ClusterDNS} .azurehdinsight .net**", conforme mostrado no exemplo anterior. Esta alteração foi necessário para facilitar a cenários onde vários tipos de cluster (como HBase e Hadoop) podem ser implementados numa rede virtual (VNET). Isto acontece, por exemplo, quando utilizar HBase como uma plataforma back-end para Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari APIs de monitorização

A tabela seguinte lista algumas da Ambari mais comuns monitorizar API chamadas. Para mais informações sobre a API, consulte o artigo [Referência da API Ambari][ambari-api-reference].

Monitor API chamada|URI|Descrição
---|---|---
Obter clusters|`/api/v1/clusters`|
Obter informações de cluster.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|clusters, serviços, anfitriões
Obter serviços|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Serviços de incluem: hdfs, mapreduce
Obter informações de serviços.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Obter componentes de serviço|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker
Obter informações de componente.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo, componentes de anfitrião, métricas
Obter anfitriões|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
Obter informações de anfitrião.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Obter alojar componentes|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
Obter informações de componente de anfitrião.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, componente, anfitrião, métricas
Obter configurações|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Tipos de config: core de sites, hdfs de sites, mapred de sites, ramo de sites
Obter informações de configuração.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Tipos de config: core de sites, hdfs de sites, mapred de sites, ramo de sites


##<a name="next-steps"></a>Próximos passos

Agora que tem aprendeu como utilizar Ambari monitorizar API chamadas. Para obter mais informações, consulte o artigo:

- [Gerir clusters HDInsight utilizando o Portal do Azure][hdinsight-admin-portal]
- [Gerir clusters HDInsight através do PowerShell do Azure][hdinsight-admin-powershell]
- [Gerir clusters HDInsight utilizando interface da linha de comandos][hdinsight-admin-cli]
- [Documentação HDInsight][hdinsight-documentation]
- [Introdução ao HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
