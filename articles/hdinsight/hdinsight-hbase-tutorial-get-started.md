<properties
    pageTitle="HBase tutorial: introdução ao HBase no Hadoop | Microsoft Azure"
    description="Siga este tutorial de HBase para começar a utilizar Apache HBase com Hadoop no HDInsight. Criar tabelas a partir da shell de HBase e consultá-los com ramo."
    keywords="Apache hbase, hbase, hbase shell, hbase tutorial"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>HBase tutorial: começar a utilizar Apache HBase com baseados no Windows Hadoop no HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Saiba como criar HBase clusters no HDInsight, criar tabelas de HBase e as tabelas de consulta utilizando Apache Hive. Para obter informações gerais do HBase, consulte o artigo [Descrição geral de HDInsight HBase][hdinsight-hbase-overview].

As informações neste documento são específicas a clusters HDInsight baseados no Windows. Para obter informações sobre clusters baseados no Windows, utilize o Seletor de tabulação na parte superior da página para alternar.

> [AZURE.NOTE] HBase (versão 0.98.0) no baseados no Windows HDInsight só está disponível para utilização com o HDInsight 3.1 clusters (com base no Apache Hadoop e FIO 2.4.0). Para informações sobre a versão, consulte o artigo [quais são as novidades nas versões cluster Hadoop fornecidas pela HDInsight?][hdinsight-versions]

## <a name="before-you-begin"></a>Antes de começar

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar este tutorial HBase, tem de ter o seguinte procedimento:

- **A Microsoft Azure subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Uma estação de trabalho** com o Visual Studio 2013 ou superior: para obter instruções, consulte o artigo [Instalar o Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Criar HBase cluster

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para criar um cluster de HBase utilizando o portal do Azure**

1. Inicie sessão no [portal do Azure][azure-management-portal].
2. Clique em **Novo** ou **+** no canto superior esquerdo de canto e, em seguida, clique em **dados + Analytics**, **HDInsight**.
3. Introduza os seguinte valores:

    - **Nome do cluster** - introduza um nome para este cluster de identificar.
    - **Tipo de cluster** - selecionar **HBase**.
    - O **sistema operativo de cluster** - selecionar **Windows**.  Para criar baseado em Linux HBase cluster, consulte o artigo [HBase tutorial: começar a utilizar Apache HBase com Hadoop no HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Versão** - selecionar uma versão HBase.
    - **Subscrição** - selecione a subscrição Azure utilizada para criar este cluster.
    - **Grupo de recursos** - criar um novo grupo de recursos Azure ou selecione uma existente. Para obter mais informações, consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md)
    - **Credenciais** - para cluster baseado no Windows, pode criar um utilizador de cluster (utilizador a.k.a HTTP, utilizador de serviço web HTTP) e um utilizador de ambiente de trabalho remoto. Clique em **Ativar o ambiente de trabalho remoto** para adicionar as credenciais de utilizador de ambiente de trabalho remoto. A secção seguinte requer RDP.
    - **Origem de dados** - criar uma nova conta de armazenamento Azure ou selecione uma conta de armazenamento Azure existente para ser utilizado como o sistema de ficheiro predefinido para o cluster. A localização de conta de armazenamento predefinida determina a localização da localização cluster. A conta de armazenamento predefinida e o cluster de cocriação terá de localizar no Centro de dados do mesmo.
    - **Camadas de preços de nó** - selecionar o número de servidores de região para o cluster HBase

        > [AZURE.WARNING] Para elevada disponibilidade dos serviços de HBase, tem de criar um cluster que contém pelo menos **três** nós. Este procedimento garante que, se um nó vai para baixo, estão disponíveis em outros nós as regiões de dados HBase.

        > Se está a aprender HBase, sempre selecione 1 para o tamanho de cluster e eliminar o cluster após cada utilização para reduzir o custo.

    - **Configuração opcional** - rede virtual configurar Azure e configurar ações de Script e adicionar contas de armazenamento adicional.

4. Clique em **Criar**.

>[AZURE.NOTE] Depois de um cluster de HBase é eliminado, pode criar outra HBase cluster utilizando a mesma conta de armazenamento predefinida e o contentor de BLOBs predefinido. O novo cluster vai selecionar as tabelas de HBase que criou no original cluster. Para evitar inconsistências, recomendamos que desativar as tabelas HBase antes de eliminar o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Atualmente, existem dois forma para aceder a HBase. Esta secção abrange utilizando a shell de HBase. A secção seguinte abrange utilizando o SDK .NET.

Para a maioria das pessoas, os dados aparecem no formato tabular:

![hdinsight hbase os dados de tabela][img-hbase-sample-data-tabular]

No HBase que é uma implementação do BigTable, os mesmos dados aspeto:

![dados do hdinsight hbase bigtable][img-hbase-sample-data-bigtable]

-Vai fazer mais sentido depois de concluir o procedimento seguinte.  

**Para utilizar a shell de HBase**

1. Utilize RDP para ligar ao seu cluster HBase no HDInsight. Para instruções RDP, consulte o artigo [Gerir Hadoop clusters no HDInsight utilizando o Portal do Azure][hdinsight-manage-portal].
2. No seu sessão RDP, clique no atalho de **linha de comandos Hadoop** localizado no ambiente de trabalho.
3. Abra a shell de HBase:

        cd %HBASE_HOME%\bin
        hbase shell

4. Crie uma HBase com duas famílias de tipos de coluna:

        create 'Contacts', 'Personal', 'Office'
        list
5. Inserir alguns dados:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Obter uma única linha

        get 'Contacts', '1000'

    Verá os mesmos resultados como utilizar o comando de análise porque existe apenas uma linha.

    Para mais informações sobre o esquema da tabela Hbase, consulte o artigo [Introdução à estruturação de esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase][hbase-quick-start].


6. Sair a shell

        exit

**Para em volume carregar dados na tabela de HBase contactos**

HBase inclui vários métodos de carregamento de dados em tabelas. Para mais informações, consulte o artigo [em volume a ser carregados](http://hbase.apache.org/book.html#arch.bulk.load).


Um ficheiro de dados de exemplo foi carregado a um contentor blob público, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. O conteúdo do ficheiro de dados é:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Pode criar um ficheiro de texto e carregue o ficheiro a sua conta de armazenamento, se pretender. Para obter as instruções, consulte o artigo [carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Este procedimento utiliza a tabela de HBase de contactos que criou no último procedimento.

1. No seu sessão RDP, clique no atalho de **linha de comandos Hadoop** localizado no ambiente de trabalho.
2. Alterar o diretório:

        cd %HBASE_HOME%\bin

3. Execute o seguinte comando para transformar o ficheiro de dados para StoreFiles e o arquivo num caminho relativo especificado pelo Dimporttsv.bulk.output:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Execute o seguinte comando para carregar os dados a partir do /example/data/storeDataFileOutput à tabela HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Pode abrir a shell de HBase e utilize o comando de pesquisa para listar o conteúdo da tabela.



## <a name="use-hive-to-query-hbase-tables"></a>Utilizar Hive para tabelas de consulta de HBase

Pode consultar dados armazenados num HBase utilizando ramo. Esta secção cria uma tabela Hive que mapas à tabela HBase e utiliza-o para consultar os dados na tabela HBase.

**Para abrir o dashboard cluster**

1. Navegue até à **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Introduza o nome de utilizador da conta de utilizador de Hadoop e a palavra-passe. O nome de utilizador predefinido é **administrador** e a palavra-passe é introduzido durante o processo de criação. É aberta num novo separador do browser.
6. Clique em **Hive Editor** na parte superior da página. O Editor de Hive tem o seguinte aspeto:

    ![Dashboard de cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Para executar consultas de ramo**

1. Introduza o seguinte script HiveQL Hive Editor e clique em **Submeter** para criar uma tabela Hive que mapas à tabela HBase. Certifique-se de que criou referenciada anteriormente no tutorial utilizando a shell de HBase antes de executar esta declaração da tabela de exemplo.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Aguarde até que as atualizações de **Estado** para **conclusão**.

2. Introduza o seguinte script HiveQL Hive Editor e, em seguida, clique em **Submeter**. A consulta ramo consultas os dados na tabela HBase:

        SELECT count(*) FROM hbasecontacts;

4. Para obter os resultados da consulta ramo, clique na ligação de **Ver os detalhes** na janela da **Tarefa de sessão** quando a tarefa estiver concluída. Haverá apenas um ficheiro de exportação de tarefa porque que coloca um registo numa tabela HBase.




**Para procurar o ficheiro de exportação**

1. Na consola de consulta, clique em **Localizador de ficheiro**.
2. Clique na conta de armazenamento Azure que é utilizada como o sistema de ficheiro predefinido para o cluster HBase.
3. Clique no nome de cluster HBase. O contentor de conta de armazenamento Azure predefinido utiliza o nome do cluster.
4. Clique em **de utilizador**e, em seguida, clique em **Admin**. (Isto é o nome de utilizador Hadoop.)
6. Clique no nome da tarefa com a hora da **Última modificação** que corresponde ao tempo quando executou a consulta SELECIONAR Hive.
4. Clique em **stdout**. Guarde o ficheiro e abri-lo com o bloco de notas. Haverá um ficheiro de exportação.

    ![HDInsight HBase ramo Editor ficheiro Browser][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Utilizar a biblioteca do cliente .NET HBase REST API

Tem de transferir a biblioteca do cliente HBase REST API para .NET a partir do GitHub e construir o projeto para que possa utilizar o HBase .NET SDK. O procedimento seguinte inclui as instruções para esta tarefa.

1. Crie uma nova aplicação c# Visual Studio ambiente de trabalho consola do Windows.
2. Abra a consola do Gestor de pacote NuGet clicando em **Ferramentas de** > **Gestor de pacotes NuGet** > **Consola do Gestor de pacote**.
3. Execute o seguinte comando de NuGet na consola do:

        Install-Package Microsoft.HBase.Client

5. Adicione as seguintes instruções de **utilizar** na parte superior do ficheiro:

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Substitua cuja função **principal** com o seguinte:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Defina as primeiras três variáveis na cuja função **principal** .
8. Prima **F5** para executar a aplicação.

## <a name="check-cluster-status"></a>Verificar o estado de cluster

HBase no HDInsight é fornecido com uma IU Web para clusters de monitorização. Utilizar a IU da Web, pode pedir estatísticas ou informações acerca de regiões.

Para abrir a IU da Web, pode tem RDP para cluster e, em seguida, clique no atalho de HMaster informações Web IU no ambiente de trabalho ou utilize o seguinte URL num web browser:

    http://zookeeper[0-2]:60010/master-status

Num cluster de elevada disponibilidade, vai encontrar uma ligação para o active HBase mestra nó actual que aloja a IU da Web.

##<a name="delete-the-cluster"></a>Eliminar o cluster
Para evitar inconsistências, recomendamos que desativar as tabelas HBase antes de eliminar o cluster.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>O que se segue?
Neste tutorial HBase para HDInsight, o que aprendeu como criar um cluster de HBase e como criar tabelas e ver os dados nessas tabelas a partir da shell de HBase. Pode também aprendeu como utilizar uma consulta de ramo no dados em tabelas de HBase e como utilizar as HBase c# REST APIs para criar uma tabela do HBase e obter dados a partir da tabela.

Para obter mais informações, consulte:

- [Descrição geral de HDInsight HBase][hdinsight-hbase-overview].
HBase é Apache, código fonte a aberto NoSQL base de dados do criada com base em Hadoop que proporciona acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semistructured.
- [Criar clusters de HBase rede Virtual Azure][hdinsight-hbase-provision-vnet].
Com a integração de rede virtual, podem ser implementados HBase clusters à mesma rede virtual como as aplicações para que as aplicações possam comunicar com HBase diretamente.
- [Configurar HBase replicação no HDInsight](hdinsight-hbase-geo-replication.md). Saiba como configurar a replicação de HBase através de dois centros de dados Azure.
- [Analisar Twitter sentimento com HBase no HDInsight][hbase-twitter-sentiment].
Saiba como o fazer em tempo real [análise sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) dos dados grandes utilizando HBase num cluster de Hadoop em HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
