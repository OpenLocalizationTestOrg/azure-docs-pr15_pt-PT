<properties
    pageTitle="Criar clusters de HBase numa rede Virtual | Microsoft Azure"
    description="Começar a utilizar HBase no Azure HDInsight. Saiba como criar HDInsight HBase clusters de rede Virtual Azure."
    keywords=""
    services="hdinsight,virtual-network"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Criar clusters de HBase na rede Virtual do Azure 

Saiba como criar Azure HDInsight HBase clusters numa [Rede Virtual Azure][1].

Com a integração de rede virtual, podem ser implementados HBase clusters à mesma rede virtual como as aplicações para que as aplicações possam comunicar com HBase diretamente. Os benefícios incluem:

- Conectividade direta da aplicação web para os nós do cluster HBase, que permite aos comunicação através de procedimento remoto HBase Java chamar APIs (RPC).
- Melhor desempenho por não ter o tráfego da sua aceda ao longo do várias gateways e Balanceadores de carga.
- A capacidade de informações confidenciais do processo de uma forma mais segura sem expor um ponto final de público.

###<a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o Azure PowerShell**. Consulte o artigo [instalar e utilizar o PowerShell Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Criar HBase cluster na rede virtual

Nesta secção, irá criar um cluster de baseado em Linux HBase com a conta de armazenamento do Windows Azure dependente numa rede virtual Azure utilizando um [modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md). Para outros métodos de criação de cluster e Noções sobre as definições, consulte o artigo [Criar HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como utilizar um modelo para criar Hadoop clusters no HDInsight, consulte o artigo [Criar Hadoop clusters no HDInsight utilizar modelos de Gestor de recursos do Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Algumas propriedades tem sido codificada para o modelo. Por exemplo:
>
> * __Localização__: Leste dos EUA 2
> * Versão __Cluster: 3.4.
> * __Contagem de nó clusters trabalhador__: 4
> * __Conta de armazenamento predefinida__: &lt;nome Cluster > armazenar
> * __Nome de rede virtual__: &lt;nome Cluster >-vnet
> * __Espaço de endereços de rede virtual__: 10.0.0.0/16
> * __Nome de sub-rede__: predefinido
> * __Intervalo de endereços de sub-rede__: 10.0.0.0/24
>
> &lt;Nome do cluster > será substituído pelo nome do cluster fornecer ao utilizar o modelo.

1. Clique na imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado num contentor de BLOBs público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. A partir do pá **implementação personalizada** , introduza o seguinte:

    - **Subscrição**: selecione uma subscrição do Azure utilizada para criar o cluster de HDInsight, a conta de armazenamento dependente e a rede virtual Azure.
    - **Grupo de recursos**: selecione **Criar novo**e, especifique um nome do novo grupo de recursos.
    - **Localização**: selecione uma localização para o grupo de recursos.
    - **Nome de cluster**: introduza um nome para o cluster de Hadoop que irá criar.
    - **Nome de início de sessão do cluster e palavra-passe**: O nome de início de sessão predefinido é **administrador**.
    - **SSH nome de utilizador e palavra-passe**: O nome de utilizador predefinido é **sshuser**.  Pode alterá-lo. 
    - **Posso concorda com os termos e condições indicadas acima**: (selecione)
    

3. Clique em **comprar**. Bastam sobre cerca de 20 minutos para criar um cluster. Quando o cluster estiver criado, pode clicar na pá cluster no portal para o abrir.

Depois de concluir o tutorial, poderá pretender eliminar o cluster. Com HDInsight, os dados são armazenados no armazenamento do Windows Azure, para que pode eliminar com segurança um cluster quando já não for utilizado. Também são cobradas para um cluster de HDInsight, mesmo quando não está em utilização. Uma vez que os encargos para o cluster são mais do número de vezes que os encargos de armazenamento, faz sentido económico para eliminar clusters quando não estejam em utilização. Para obter instruções de eliminar um cluster, consulte o artigo [Gerir Hadoop clusters no HDInsight utilizando o portal do Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Para começar a trabalhar com o seu novo cluster de HBase, pode utilizar os procedimentos que se encontram no [começar a utilizar o HBase com Hadoop no HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Ligar ao cluster HBase utilizando HBase Java RPC APIs

1.  Crie uma infraestrutura como uma máquina de virtual do serviço (IaaS) na mesma rede virtual Azure e à mesma sub-rede. Para obter instruções sobre como criar uma nova máquina de virtual IaaS, consulte o artigo [criar uma Máquina Virtual a executar o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Quando seguir os passos neste documento, tem de utilizar o seguinte procedimento para a configuração de rede:

    - __Rede virtual__: &lt;nome Cluster >-vnet
    - __Sub-rede__: predefinido

    > [AZURE.IMPORTANT] Substituir &lt;nome Cluster > com o nome que utilizou quando criar o cluster de HDInsight nos passos anteriores.

    Utilizar estes valores irá configurar a máquina virtual para utilizar a mesma rede virtual e sub-rede como HDInsight cluster. Isto permitirá-las diretamente comunicar com os outros.

2.  Ao utilizar uma aplicação Java para ligar ao HBase remotamente, tem de utilizar o nome de domínio completamente qualificado (FQDN). Para determinar isto, terá de obter o sufixo DNS específicos da ligação do HBase cluster. Para o fazer, pode utilizar um dos seguintes métodos:

    * Utilize um browser Web para efetuar uma chamada de Ambari:
    
        Navegue até ao https://&lt;nome de cluster >.azurehdinsight.net/api/v1/clusters/&lt;nome de cluster > / hosts?minimal_response = true. Se um ficheiro JSON com os sufixos de DNS.

    * Utilize o Web site Ambari:

        1. Navegue até ao https://&lt;nome de cluster >. azurehdinsight.net.
        2. Clique em **anfitriões** a partir do menu superior.

    * Utilizar o laço para efetuar chamadas do resto:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Nos dados com notação de objeto JavaScript (JSON) devolvidos, localize a entrada de "host_name". Isto irá conter o FQDN para os nós no cluster. Por exemplo:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        A parte do início do nome de domínio com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.

    * Utilizar o Azure PowerShell
    
        Utilize o seguinte script do Azure PowerShell para registar a função **ClusterDetail obter** , que pode ser utilizada para devolver o sufixo de DNS:

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Depois de executar o script do Azure PowerShell, utilize o seguinte comando para devolver o sufixo de DNS utilizando a função **Get-ClusterDetail** . Especifique o nome de cluster HDInsight HBase, nome de administrador e palavra-passe de administrador quando utilizar este comando.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Isto irá devolver o sufixo de DNS. Por exemplo, **yourclustername.b4.internal.cloudapp.net**.

    * Utilizar o RDP
    
        Também pode utilizar o ambiente de trabalho remoto para ligar ao cluster HBase (será ligado ao nó cabeça) e executar **ipconfig** a partir de uma linha de comandos para obter o sufixo de DNS. Para obter instruções sobre como ativar o protocolo de ambiente de trabalho remoto (RDP) e ligar ao cluster utilizando RDP, consulte o artigo [Gerir Hadoop clusters no HDInsight através do portal Azure][hdinsight-admin-portal].
        
        ![hdinsight.hbase.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Para verificar que a máquina virtual pode comunicar com o cluster HBase, utilize o comando `ping headnode0.<dns suffix>` de máquina virtual. Por exemplo, ping headnode0.mycluster.b1.cloudapp.net.

Para utilizar estas informações numa aplicação Java, pode seguir os passos no [Maven utilizar para criar aplicações de Java que utilizam o HBase com HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para criar uma aplicação. Para que a aplicação de ligar a um servidor de HBase remoto, modifique o ficheiro **hbase site.xml** neste exemplo para utilizar o FQDN para Zookeeper. Por exemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Para mais informações sobre resolução de nomes no Azure virtuais redes, incluindo como utilizar o seu próprio DNS server, consulte o artigo [Resolução de nomes (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como criar um cluster de HBase. Para obter mais informações, consulte o artigo:

- [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurar a replicação de HBase no HDInsight](hdinsight-hbase-geo-replication.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md)
- [Começar a utilizar HBase com Hadoop no HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analisar Twitter sentimento com HBase no HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Descrição geral de rede virtual][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Aprovisionar detalhes para o novo cluster de HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Utilizar a ação de Script para personalizar um cluster de HBase"

[azure-preview-portal]: https://portal.azure.com
