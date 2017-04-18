<properties 
   pageTitle="Utilizar Apache Lisboa e de exemplo no HDInsight | Microsoft Azure" 
   description="Saiba como utilizar Apache Lisboa no HDInsight e como instalar e configurar o exemplo no seu estação de trabalho para ligar a um cluster de HBase no HDInsight." 
   services="hdinsight" 
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Utilizar Apache Portalegre com clusters de baseado em Linux HBase no HDInsight  

Saiba como utilizar [Apache Lisboa](http://phoenix.apache.org/) no HDInsight e como utilizar SQLLine. Para mais informações sobre Portalegre, consulte o artigo [Portalegre em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para gramática Portalegre, consulte o artigo [Portalegre gramática](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Para as informações da versão de Lisboa HDInsight, consulte o artigo [quais são as novidades nas versões cluster Hadoop fornecidas pela HDInsight?] [hdinsight-versions].

##<a name="use-sqlline"></a>Utilizar SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comandos para executar SQL. 

###<a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar SQLLine, tem de ter o seguinte procedimento:

- **Cluster de respostas HBase em HDInsight**. Para obter informações sobre aprovisionar HBase cluster, consulte o artigo [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started].
- **Ligar ao cluster HBase através do protocolo de ambiente de trabalho remoto**. Para obter instruções, consulte o artigo [Gerir Hadoop clusters no HDInsight utilizando o Portal clássica Azure][hdinsight-manage-portal].


Ao ligar a um cluster de HBase, terá de ligar a um das animais. Cada cluster HDInsight tem 3 animais. 

**Para descobrir o nome do anfitrião Zookeeper**

1. Abra Ambari ao procurar para **https://<ClusterName>. azurehdinsight.net**.
2. Introduza o nome de utilizador HTTP (cluster) e a palavra-passe para iniciar sessão.
3. Clique em **ZooKeeper** no menu à esquerda. Deve ver 3 **ZooKeeper servidor** listados.
4. Clique das **ZooKeeper servidor** listado. No painel de resumo, localize o **nome do anfitrião**. É semelhante a *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Para utilizar SQLLine**

1. Ligar ao cluster utilizando SSH. Para obter instruções, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix, ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) dependendo do seu computador cliente SO.

2. A partir de SSH, execute os seguintes comandos para executar SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Execute os seguintes comandos para criar uma tabela de HBase e inserir alguns dados:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Para mais informações, consulte o artigo [SQLLine manual](http://sqlline.sourceforge.net/#manual) e a [Gramática de Lisboa](http://phoenix.apache.org/language/index.html).


 
##<a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu como utilizar Apache Lisboa no HDInsight.  Para obter mais informações, consulte o artigo

- [Descrição geral de HDInsight HBase][hdinsight-hbase-overview]: HBase é Apache, código fonte a aberto NoSQL base de dados do criada com base em Hadoop que proporciona acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semistructured.
- [Aprovisionar HBase clusters rede Virtual Azure][hdinsight-hbase-provision-vnet]: com a integração de rede virtual, HBase podem ser implementados clusters à mesma rede virtual como as aplicações para que as aplicações possam comunicar com HBase diretamente.
- [Configurar HBase replicação no HDInsight](hdinsight-hbase-geo-replication.md): Saiba como configurar a replicação HBase através de dois centros de dados Azure. 
- [Analisar Twitter sentimento com HBase no HDInsight][hbase-twitter-sentiment]: Saiba como efetuar em tempo real [análise sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) dos dados grandes utilizando HBase num cluster de Hadoop em HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
