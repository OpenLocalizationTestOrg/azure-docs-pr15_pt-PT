<properties
    pageTitle="Disponibilidade de Hadoop clusters no HDInsight | Microsoft Azure"
    description="HDInsight ser implementada clusters altamente disponíveis e fiáveis com um nó de cabeça adicional."
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>


#<a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Disponibilidade e fiabilidade dos baseados no Windows Hadoop clusters no HDInsight


>[AZURE.NOTE] Os passos utilizados neste documento são específicos a clusters HDInsight baseados no Windows. Se estiver a utilizar um cluster baseado em Linux, consulte o artigo [disponibilidade e fiabilidade dos clusters de baseado em Linux Hadoop HDInsight](hdinsight-high-availability-linux.md) para informações específicas do Linux.

HDInsight permite que os clientes implementar uma variedade de tipos de cluster, para cargas de trabalho de análise de dados diferentes. Tipos de cluster oferecidos hoje são clusters de Hadoop para consulta e das cargas de trabalho de análise, clusters de HBase para NoSQL cargas de trabalho e clusters de tempestade para processamento em tempo real evento cargas de trabalho. Dentro de um tipo determinado cluster, existem diferentes funções para os vários nós. Por exemplo:



- São implementados clusters de Hadoop para HDInsight com duas funções:
    - Nó de cabeça (2 nós)
    - Nó de dados (1, pelo menos, nó)

- São implementados clusters de HBase para HDInsight com três funções:
    - Servidores de cabeça (2 nós)
    - Servidores de região (nó, pelo menos, 1)
    - Nós de mestre/Zookeeper (3 nós)

- São implementados clusters de tempestade para HDInsight com três funções:
    - Nós nimbus (2 nós)
    - Servidores de autoridade (nó, pelo menos, 1)
    - Nós zookeeper (3 nós)

Implementações padrão de Hadoop clusters normalmente têm um único nó cabeça. HDInsight remove este ponto único de falha com a adição de um nó de cabeça secundário /head nó de servidor/Nimbus para aumentar a disponibilidade e fiabilidade do serviço necessário para gerir das cargas de trabalho. Estes nós de servidores/Nimbus cabeça nós/cabeça são concebidas para gerir a falha de nós do trabalho sem problemas, mas qualquer falhas de serviços mestras em execução no nó cabeça seriam fazer com que o cluster deixem de funcionar.


[ZooKeeper](http://zookeeper.apache.org/ ) nós (ZKs) foram adicionadas e são utilizadas para eleições políticas de caráter de preenchimento de cabeça nós e para garantir que esse trabalho nós e gateways (GWs) saber quando a falha ao longo para o nó de cabeça secundário (cabeça Nó1) quando o nó de cabeça activo (cabeça Node0) se tornar inativo.

![Diagrama dos nós de cabeça altamente fiáveis na aplicação HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## <a name="check-active-head-node-service-status"></a>Verificar o estado de serviço de nó de cabeça activo
Para determinar qual o nó cabeça está ativo e para verificar o estado dos serviços em execução nesse nó cabeça, tem de ligar ao Hadoop cluster utilizando o protocolo de ambiente de trabalho remoto (RDP). Para instruções RDP, consulte o artigo [Gerir Hadoop clusters no HDInsight utilizando o portal do Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Depois de se ter ligado ao cluster, faça duplo clique no ícone de **Hadoop serviço disponível** localizado no ambiente de trabalho para obter o estado sobre qual o nó cabeça a Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 serviços estão a trabalhar, ou para serviços HDI 3.0, Namenode, Gestor de recursos, histórico de servidor, Templeton, Oozieservice, Metastore e Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Na captura de ecrã, o nó de cabeça ativo é *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Ficheiros de registo de acesso no nó de cabeça secundário

Para aceder à tarefa de registos no nó de cabeça secundário se que se tornou nó cabeça activo, navegar na IU JobTracker continua a funcionar como o faz para o nó ativo principal. Para aceder ao JobTracker, tem de ligar ao Hadoop cluster utilizando RDP conforme descrito na secção anterior. Assim que tiver nós para cluster, faça duplo clique no ícone de **Estado do nome do Hadoop nó** localizado no ambiente de trabalho e, em seguida, clique nos **registos de NameNode** para aceder ao diretório de registos no nó de cabeça secundário.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## <a name="configure-head-node-size"></a>Configurar o tamanho de cabeça nó
Os nós de cabeça são atribuídos como grandes máquinas virtuais (VMs) por predefinição. Este tamanho é adequado para a gestão da maioria das tarefas de Hadoop executar no cluster. Mas existem cenários que precisem de VMs muito grandes para os nós de cabeça. Um exemplo é quando o cluster tem que gerir um grande número de pequenos Oozie trabalhos.

Podem ser configuradas VMs muito grandes utilizando os cmdlets do Azure PowerShell ou o SDK HDInsight.

A criação e de um cluster de aprovisionamento utilizando o Azure PowerShell é documentado no [administrar HDInsight através do PowerShell](hdinsight-administer-use-powershell.md). A configuração de um nó de cabeça muito grande requer a adição da `-HeadNodeVMSize ExtraLarge` parâmetro para a `New-AzureRmHDInsightcluster` cmdlet utilizado neste código.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName ` 
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

Para o SDK, é muito semelhante a história. A criação e de um cluster de aprovisionamento utilizando o SDK é documentado no [Utilizando HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk). A configuração de um nó de cabeça muito grande requer a adição da `HeadNodeSize = NodeVMSize.ExtraLarge` parâmetro para a `ClusterCreateParameters()` método utilizado neste código.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Próximos passos

- [Apache ZooKeeper](http://zookeeper.apache.org/ )
- [Ligar a clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Utilizar o HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)
