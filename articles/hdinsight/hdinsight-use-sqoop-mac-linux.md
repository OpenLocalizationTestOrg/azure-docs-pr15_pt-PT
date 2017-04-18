<properties
    pageTitle="Utilizar Hadoop Sqoop no baseado em Linux HDInsight | Microsoft Azure"
    description="Saiba como executar Sqoop importar e exportar entre um Hadoop baseado em Linux num cluster de HDInsight e uma base de dados do Azure SQL."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Utilizar Sqoop com Hadoop no HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar Sqoop para importar e exportar entre um cluster de baseado em Linux HDInsight e a base de dados de base de dados do SQL Azure ou do SQL Server.

> [AZURE.NOTE] Os passos neste artigo utilizam SSH para ligar a um cluster de baseado em Linux HDInsight. Os clientes Windows também podem utilizar PowerShell do Azure e HDInsight .NET SDK para trabalhar com Sqoop no clusters baseados em Linux. Utilize o Seletor de tabulação para abrir esses artigos.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Cluster de respostas Hadoop no HDInsight** e uma __Base de dados do SQL Azure__: os passos neste documento são baseados no cluster e a base de dados criada utilizando o documento de [Criar cluster e base de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database) . Se já tiver um cluster de HDInsight e a base de dados SQL, pode substituir os aqueles para os valores utilizados neste documento.
- **Estação de trabalho**: um computador com um cliente SSH.

##<a name="install-freetds"></a>Instalar FreeTDS

1. Utilize SSH para ligar ao cluster baseado em Linux HDInsight. O endereço para utilizar ao ligar é `CLUSTERNAME-ssh.azurehdinsight.net` e a porta é `22`.

    Para obter mais informações sobre como utilizar SSH para ligar ao HDInsight, consulte os seguintes documentos:

    * **Clientes Linux, Unix ou OS X**: consulte o artigo [ligar a um cluster de baseado em Linux HDInsight Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Os clientes do Windows**: consulte o artigo [ligar a um cluster de baseado em Linux HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Utilize o seguinte comando para instalar FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS será utilizada em vários passos para ligar à base de dados SQL.

##<a name="create-the-table-in-sql-database"></a>Criar a tabela na base de dados SQL

> [AZURE.IMPORTANT] Se estiver a utilizar um cluster de HDInsight e a base de dados SQL criado utilizando os passos no [Criar cluster e base de dados SQL](hdinsight-use-sqoop.md), ignore os passos nesta secção, tal como a base de dados e a tabela que foram criados como parte dos passos nesse documento.

1. A partir da ligação SSH HDInsight, utilize o seguinte comando para ligar para o servidor de base de dados SQL e Creta a tabela que será utilizada no resto destes passos:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Receberá saída semelhante ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Na `1>` pedido, introduza as linhas seguintes:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Quando o `GO` declaração introduzida, serão avaliadas as declarações anterior. Em primeiro lugar, a tabela **mobiledata** é criada, em seguida, um índice agrupado é adicionado à mesma (obrigatório base de dados SQL.)

    Utilize o seguinte para confirmar que foi criada a tabela:

        SELECT * FROM information_schema.tables
        GO

    Deverá visualizar saída semelhante ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Introduza `exit` na `1>` perguntar se quer saia do utilitário tsql.

##<a name="sqoop-export"></a>Exportar Sqoop

3. A partir da ligação SSH HDInsight, se o seguinte comando para verificar que Sqoop podem ver a sua base de dados do SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Isto deve devolver uma lista de bases de dados, incluindo a base de dados **sqooptest** que criou anteriormente.

4. Utilize o seguinte comando para exportar dados do **hivesampletable** à tabela **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Isto indica Sqoop para ligar à base de dados SQL, a base de dados **sqooptest** e exportar dados a partir do **wasbs: / / / ramo/armazém/hivesampletable** (ficheiros físicos para *hivesampletable*,) para a tabela **mobiledata** .

5. Após completa o comando, utilize o seguinte para ligar à base de dados utilizando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Quando estabelecida, utilize as instruções seguintes para verificar que foram exportados os dados para a tabela **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Deverá ver uma lista de dados na tabela. Tipo de `exit` para sair o utilitário tsql.

##<a name="sqoop-import"></a>Importar Sqoop

1. Utilize o seguinte procedimento para importar dados a partir da tabela **mobiledata** numa base de dados do SQL, para o **wasbs: / / / tutoriais/usesqoop/importeddata** directório no HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Os dados importados terão campos separados por um caráter de tabulação e as linhas serão terminadas por um caráter de nova linha.

2. Quando a importação estiver concluída, utilize o seguinte comando para a lista de dados no diretório novo:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>Utilizar o SQL Server

Também pode utilizar Sqoop para importar e exportar dados do SQL Server, no Centro de dados ou num computador Virtual alojado no Azure. São as diferenças entre utilizar a base de dados SQL e o SQL Server:

* HDInsight e o SQL Server tem de estar ligado à mesma rede Virtual do Azure

    > [AZURE.NOTE] HDInsight suporta apenas baseadas na localização redes virtuais e -atualmente não funciona com redes virtuais com base no grupo afinidade.

    Quando estiver a utilizar o SQL Server no seu centro de dados, tem de configurar a rede virtual como *site para o site* ou *site de ponto*.

    > [AZURE.NOTE] Para redes virtuais **ponto-para-site** , SQL Server tem de executar o cliente VPN aplicação de configuração, o que está disponível a partir do **Dashboard** da configuração de rede virtual Azure.

    Para mais informações rede Virtual Azure, consulte o artigo [Descrição geral da rede Virtual](../virtual-network/virtual-networks-overview.md).

* SQL Server tem de ser configurado para permitir uma autenticação SQL. Para obter mais informações, consulte o artigo [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx)

* Poderá ter de configurar o SQL Server para aceitar ligações remotas. Veja [como resolver problemas de ligação para o motor de base de dados do SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) para obter mais informações

* Tem de criar a base de dados **sqooptest** SQL Server utilizando um utilitário como **SQL Server Management Studio** ou **tsql** - os passos para utilizar o clip do Azure só funcionam para base de dados do SQL Azure

    As declarações TSQL para criar a tabela **mobiledata** são semelhantes aos utilizados para base de dados do SQL, à exceção dos criar um clusterd index - esta não é necessário para o SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Quando ligar para o SQL Server do HDInsight, poderá ter de utilizar o endereço IP do SQL Server, a menos que configurou um sistema de nomes de domínio (DNS) para resolver os nomes na rede Virtual Azure. Por exemplo:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Limitações

* Em volume export - baseado em com Linux HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou base de dados do SQL Azure atualmente não suporta insere em volume.

* De lotes - com baseado em Linux HDInsight, ao utilizar o `-batch` mudar quando efetuar inserções, Sqoop irá efetuar várias insere em vez de lotes as operações de inserir.

##<a name="next-steps"></a>Próximos passos

Agora que tem aprendeu como utilizar Sqoop. Para obter mais informações, consulte o artigo:

- [Utilizar Oozie com HDInsight][hdinsight-use-oozie]: utilizar Sqoop ação num fluxo de trabalho Oozie.
- [Analisar os dados de atrasos em voos utilizando HDInsight][hdinsight-analyze-flight-data]: utilizar Hive para analisar voo atrasar dados e, em seguida, utilize Sqoop para exportar dados para uma base de dados do Azure SQL.
- [Carregar dados ao HDInsight][hdinsight-upload-data]: encontrar outros métodos para carregar os dados ao armazenamento Blob HDInsight/Azure.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
