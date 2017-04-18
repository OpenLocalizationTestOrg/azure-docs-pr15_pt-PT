<properties 
   pageTitle="Configurar a replicação de HBase entre dois centros de dados | Microsoft Azure" 
   description="Saiba como configurar a replicação de HBase em centros de dados de duas e sobre casos de utilização para a replicação de cluster." 
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
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configurar HBase geo-replicação no HDInsight

> [AZURE.SELECTOR]
- [Configurar a conectividade VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar o DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar a replicação HBase](hdinsight-hbase-geo-replication.md) 
 
Saiba como configurar a replicação de HBase em centros de dados de duas. Alguns casos de utilização para a replicação cluster incluem:

- Recuperação de cópia de segurança e falhas
- Agregação de dados
- Distribuição de dados geográficos
- Ingestão de dados online combinado com a análise de dados offline

Cluster replicação utiliza uma metodologia de push de origem. Um cluster de HBase pode ser uma fonte, um destino, ou pode cumprir ambas as funções ao mesmo tempo. Replicação é assíncrona, e o propósito de replicação consistência eventual. Quando a origem recebe um Editar para uma família de coluna com replicação ativada, essa editar é propagado a todos os clusters de destino. Quando dados são replicados a partir de um cluster para outra, o cluster de origem e todos os clusters que já tenham consumida os dados são registados para impedir que ciclos de replicação. Para mais informações, neste tutorial, irá configurar uma replicação de destino de origem.  Para outras topologias cluster, consulte o artigo [Guia de referência de HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Esta é a terceira parte da série de:

- [Configurar uma ligação VPN entre duas redes virtuais][hdinsight-hbase-replication-vnet]
- [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns]
- Configurar a replicação de geo HBase (Este tutorial)

O diagrama seguinte ilustra as duas redes virtuais e a conectividade de rede que criou no [configurar um grupo análise entre duas redes virtuais] [ hdinsight-hbase-geo-replication-vnet] e [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns]: 

![Diagrama de rede virtual HDInsight HBase replicação][img-vnet-diagram]

## <a id="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o Azure PowerShell**.

    Para executar scripts de PowerShell, tem de executar o Azure PowerShell como administrador e definir a política de execução para *RemoteSigned*. Consulte o artigo utilizar o cmdlet Set-ExecutionPolicy.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Azure duas rede virtual com conectividade de VPN e com DNS configurado**.  Para obter instruções, consulte o artigo [Configurar uma ligação VPN entre duas redes virtuais Azure][hdinsight-hbase-replication-vnet]e [Configurar o DNS entre duas redes virtuais Azure][hdinsight-hbase-replication-dns].


    Antes de executar scripts de PowerShell, certifique-se de que está ligado à sua subscrição do Azure utilizando o cmdlet seguinte:

        Add-AzureAccount

    Se tiver múltiplas subscrições Azure, utilize o cmdlet seguinte para definir a subscrição atual:

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Aprovisionar clusters de HBase HDInsight

Em [Configurar uma ligação VPN entre duas redes virtuais Azure][hdinsight-hbase-replication-vnet], ter criado uma rede virtual uma Europa Centro de dados e uma rede virtual num centro de dados dos EUA. Está ligado à rede virtual duas através de VPN. Nesta sessão, irá aprovisionar um cluster de HBase em cada uma das redes virtuais. Mais tarde neste tutorial, irá tornar um dos sectores HBase para criar uma réplica outro cluster HBase.

Portal clássica do Azure não suporta aprovisionamento clusters de HDInsight com opções de configuração personalizado. Por exemplo, defina *hbase.replication* para *true*. Se definir o valor no ficheiro de configuração depois de um cluster está aprovisionado, perderá a definição depois do cluster está a ser reimaged. Para mais informações, consulte [clusters de aprovisionar Hadoop HDInsight][hdinsight-provision]. Uma das opções de aprovisionamento de cluster de HDInsight com opções personalizadas está a utilizar o PowerShell Azure.


**Aprovisionar um Cluster de HBase Contoso-VNet-UE** 

1. A partir de estação de trabalho, abra o Windows PowerShell ISE.
2. Definir as variáveis no início do script e, em seguida, execute o script.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Aprovisionar um Cluster de HBase Contoso-VNet-pt** 

- Utilize o mesmo script com os seguintes valores:

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Uma vez que já tem ligados à sua conta Azure, que não precisa para executar as seguintes comlets deixem:

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Configurar reencaminhador condicional DNS

Em [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns], tiver configurado servidores DNS para as duas redes. Os clusters HBase tem sufixos de domínio diferente. Por isso, tem de configurar adicionais reencaminhadores condicionais de DNS.

Para configurar reencaminhador condicional, tem de conhecer sufixos de domínio do dois clusters de HBase. 

**Para localizar sufixos de domínio do dois clusters de HBase**

1. RDP para **Contoso-HBase-UE**.  Para obter instruções, consulte o artigo [Gerir Hadoop clusters no HDInsight utilizando o Portal clássica Azure][hdinsight-manage-portal]. É realmente headnode0 do cluster.
2. Abra uma consola do Windows PowerShell, ou uma linha de comandos.
3. Executar o **ipconfig**e anote **sufixo DNS específico da ligação**.
4. Não, feche a sessão RDP.  Terá de-la mais tarde para testar a resolução do nome de domínio.
5. Repita os mesmos passos para saber o **sufixo DNS específico da ligação** de **Contoso-HBase-US**.


**Para configurar reencaminhadores de DNS**
 
1.  RDP para **Contoso-DNS-UE**. 
2.  Clique na tecla do Windows no canto inferior esquerdo.
2.  Clique em **Ferramentas administrativas**.
3.  Clique em **DNS**.
4.  No painel esquerdo, expanda **DSN**, **Contoso-DNS-UE**.
5.  Com o botão direito **Reencaminhadores condicionais**e, em seguida, clique em **Novo reencaminhador condicional**. 
5.  Introduza as seguintes informações:
    - **Domínio de DNS**: introduza o sufixo DNS da Contoso-HBase-US. Por exemplo: Contoso-HBase-US.f5.internal.cloudapp.net.
    - **Endereços IP dos servidores principais**: introduza 10.2.0.4, que é o endereço IP do Contoso-DNS-e.u.a.. Verifique se o PI. O servidor de DNS, pode ter um endereço IP diferente.
6.  Prima **ENTER**e, em seguida, clique em **OK**.  Agora pode poderão resolver endereço IP do Contoso-DNS-e.u.a. da Contoso-DNS-UE.
7.  Repita os passos para adicionar um reencaminhador condicional de DNS para o serviço DNS no computador, virtual Contoso-DNS-US com os seguintes valores:
    - **Domínio de DNS**: introduza o sufixo DNS da Contoso-HBase-UE. 
    - **Endereços IP dos servidores principais**: introduza 10.2.0.4, que é o endereço IP Contoso-DNS-da UE.

**Para testar a resolução do nome de domínio**

1. Mudar para a janela da Contoso-HBase-UE RDP.
2. Abra uma linha de comandos.
3. Execute o comando ping:

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    O protocolo ICM está ativado os nós de trabalho de sectores pertencem a HBase

4. Não feche a sessão RDP. Irá necessitá-la mais tarde no tutorial.
5. Repita os mesmos passos para executar o ping headnode0 da Contoso-HBase-UE da Contoso-HBase-US.

>[AZURE.IMPORTANT] Tem funcionam os registos DNS para o poder continuar para os passos.

## <a name="enable-replication-between-hbase-tables"></a>Activar a replicação entre HBase tabelas

Agora, pode criar uma tabela de HBase de exemplo, ativar replicação e, em seguida, testá-la com alguns dados. A tabela de exemplo que irá utilizar tem dois famílias de tipos de coluna: pessoal e o Office. 

Neste tutorial, irá tornar o cluster de Europe HBase como o cluster de origem e o cluster EUA HBase como cluster de destino.

Crie tabelas de HBase com os mesmos nomes e famílias de tipos de coluna em clusters de origem e de destino, para que o cluster de destino sabe onde armazenar dados irão receber. Para obter mais informações sobre como utilizar a shell de HBase, consulte o artigo [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started].

**Para criar uma tabela de HBase no Contoso-HBase-UE**

1. Mudar para a janela RDP **Contoso-HBase-UE** .
2. A partir do ambiente de trabalho, clique em **linha de comandos Hadoop**.
2. Altere a pasta para o diretório de HBase:

        cd %HBASE_HOME%\bin
3. Abra a shell de HBase:

        hbase shell
4. Crie uma tabela do HBase:

        create 'Contacts', 'Personal', 'Office'
5. Não feche a sessão RDP nem a janela de linha de comandos Hadoop. Irá necessitá-las mais tarde no tutorial.
    
**Para criar uma tabela de HBase no Contoso-HBase-pt**

- Repita os mesmos passos para criar a mesma tabela no Contoso-HBase-US.


**Para adicionar Contoso-HBase-US como um ponto de replicação**

1. Mudar para a janela RDP **Contso HBase_EU** .
2. A partir da janela de shell HBase, adicione o cluster de destino (Contoso-HBase-US) como um ponto, por exemplo:

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    No exemplo, o sufixo do domínio é *contoso-hbase-us.d4.internal.cloudapp.net*. Tem de atualizar para corresponder ao seu sufixo de domínio do cluster HBase-nos. Certifique-se de que existe sem espaços entre os nomes de anfitriões.

**Para configurar família cada coluna ser replicadas num cluster de origem**

1. A partir da janela de shell HBase da sessão RDP **Contso-HBase-UE** , configure família cada coluna ser replicadas:

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Para carregar dados para a tabela HBase em massa**

Um ficheiro de dados de exemplo foi carregado para um contentor de Blobs do Azure público com o seguinte URL:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

O conteúdo do ficheiro:

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

Pode carregar o mesmo ficheiro de dados para o seu cluster HBase e importar os dados a partir daí.

1. Mudar para a janela RDP **Contoso-HBase-UE** .
2. A partir do ambiente de trabalho, clique em **linha de comandos Hadoop**.
3. Altere a pasta para o diretório de HBase:

        cd %HBASE_HOME%\bin

4. carregar os dados:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Certifique-se de que a replicação dados está a demorar local

Pode verificar que a replicação está a demorar local através da pesquisa as tabelas a partir de ambos os clusters com os comandos de shell HBase seguintes:

        Scan 'Contacts'


## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como configurar a replicação de HBase através de dois centros de dados. Para saber mais sobre HDInsight e HBase, consulte o artigo:

- [Página de serviço do HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentação HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started]
- [Descrição geral de HDInsight HBase][hdinsight-hbase-overview]
- [Aprovisionar HBase clusters rede Virtual do Azure][hdinsight-hbase-provision-vnet]
- [Analisar em tempo real Twitter sentimento com HBase][hdinsight-hbase-twitter-sentiment]
- [Analisar dados de sensor com tempestade e HBase no HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
