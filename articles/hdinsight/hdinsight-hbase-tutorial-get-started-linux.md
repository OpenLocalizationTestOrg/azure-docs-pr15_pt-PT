<properties
    pageTitle="HBase tutorial: introdução ao clusters de baseado em Linux HBase Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>HBase tutorial: começar a utilizar Apache HBase com baseado em Linux Hadoop no HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Saiba como criar um cluster de HBase no HDInsight, criar HBase tabelas e tabelas de consulta utilizando ramo. Para obter informações gerais do HBase, consulte o artigo [Descrição geral de HDInsight HBase][hdinsight-hbase-overview].

As informações neste documento são específicas a clusters baseado em Linux HDInsight. Para obter informações sobre clusters baseados no Windows, utilize o Seletor de tabulação na parte superior da página para alternar.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial HBase, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Seguro Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Criar HBase cluster

O procedimento seguinte utiliza um modelo de Gestor de recursos do Azure para criar um cluster de baseado em Linux HBase versão 3.4 e a predefinição dependente conta de armazenamento do Windows Azure. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação de cluster, consulte o artigo [clusters de baseado em criar Linux Hadoop HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado num contentor de BLOBs público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. A partir do pá **implementação personalizada** , introduza o seguinte:

    - **Subscrição**: selecione a sua subscrição do Azure que será utilizada para criar o cluster.
    - **Grupo de recursos**: criar um novo grupo de gestão de recursos do Azure ou utilize um já existente.
    - **Localização**: Especifique a localização do grupo de recursos. 
    - **Nome de cluster**: introduza um nome para o cluster de HBase que irá criar.
    - **Nome de início de sessão do cluster e palavra-passe**: O nome de início de sessão predefinido é **administrador**.
    - **SSH nome de utilizador e palavra-passe**: O nome de utilizador predefinido é **sshuser**.  Pode alterá-lo.
     
    Outros parâmetros são opcionais.  
    
    Cada cluster tem uma dependência de conta de armazenamento de Blobs do Azure. Depois de eliminar um cluster, mantém os dados na conta de armazenamento. O nome de conta de armazenamento do cluster predefinido é o nome de cluster com "store" anexado. É codificado na secção de variáveis de modelo.
        
3. Selecione **posso concordar com os termos e condições indicadas acima**e, em seguida, clique em **comprar**. Demora cerca de 20 minutos para criar um cluster.


>[AZURE.NOTE] Depois de um cluster de HBase é eliminado, pode criar outra HBase cluster utilizando o mesmo contentor de BLOBs predefinido. O novo cluster vai selecionar as tabelas de HBase que criou no original cluster. Para evitar inconsistências, recomendamos que desativar as tabelas HBase antes de eliminar o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Pode utilizar SSH para ligar a HBase clusters e, em seguida, utilize HBase Shell para criar tabelas de HBase, inserir dados e dados da consulta. Para obter informações sobre como utilizar SSH, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix, ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Utilização SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Para a maioria das pessoas, os dados aparecem no formato tabular:

![HDInsight HBase os dados de tabela][img-hbase-sample-data-tabular]

No HBase que é uma implementação do BigTable, os mesmos dados aspeto:

![Dados do HDInsight HBase bigtable][img-hbase-sample-data-bigtable]

Vai tornar mais sentido depois de concluir o procedimento seguinte.  


**Para utilizar a shell de HBase**

1. A partir de SSH, execute o seguinte comando:

        hbase shell

4. Crie uma HBase com famílias de tipos de duas colunas:

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

    Para mais informações sobre o esquema da tabela HBase, consulte o artigo [Introdução à estruturação de esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase][hbase-quick-start].

6. Sair a shell

        exit



**Para em volume carregar dados na tabela de HBase contactos**

HBase inclui vários métodos de carregamento de dados em tabelas.  Para mais informações, consulte o artigo [em volume a ser carregados](http://hbase.apache.org/book.html#arch.bulk.load).


Um ficheiro de dados de exemplo foi carregado a um contentor blob público, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  O conteúdo do ficheiro de dados é:

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

1. A partir do SSH, execute o seguinte comando para transformar o ficheiro de dados para StoreFiles e o arquivo num caminho relativo especificado pelo Dimporttsv.bulk.output:.  Se estiver na Shell de HBase, utilize o comando Sair para sair.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Execute o seguinte comando para carregar os dados a partir do /example/data/storeDataFileOutput à tabela HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Pode abrir a shell de HBase e utilize o comando de pesquisa para listar o conteúdo da tabela.



## <a name="use-hive-to-query-hbase"></a>Utilizar ramo a consulta HBase

Pode consultar dados em tabelas HBase utilizando ramo. Esta secção cria uma tabela Hive que mapas à tabela HBase e utiliza-o para consultar os dados na tabela HBase.

1. Abra o **betumes**e ligar ao cluster.  Consulte as instruções no procedimento anterior.
2. Abra a shell de ramo.

       hive
3. Execute o seguinte script de HiveQL para criar uma tabela Hive que mapas à tabela HBase. Certifique-se de que criou referenciada anteriormente no tutorial utilizando a shell de HBase antes de executar esta declaração da tabela de exemplo.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Execute o seguinte script de HiveQL para consultar os dados na tabela HBase:

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar HBase REST APIs com o laço

> [AZURE.NOTE] Quando utilizar Laço ou outras comunicações de resto com WebHCat, tem de autenticar os pedidos ao fornecer o nome de utilizador e palavra-passe para o administrador de cluster HDInsight. Também tem de utilizar o nome do cluster como parte do URI Uniform Resource Identifier () utilizado para enviar os pedidos para o servidor.
>
> Para os comandos nesta secção, substitua o **nome de utilizador** ao utilizador para autenticar ao cluster e substitua a **palavra-passe** a palavra-passe da conta de utilizador. Substitua o **nome de cluster** com o nome do seu cluster.
>
> REST API está protegida através de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Sempre deve certificar pedidos utilizando HTTP seguro (HTTPS) para ajudar a garantir que as suas credenciais em segurança são enviadas para o servidor.

1. A partir de uma linha de comandos, utilize o seguinte comando para verificar se pode ligar ao seu cluster HDInsight:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Deverá recebe uma resposta semelhante ao seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

    * **-u** - o nome de utilizador e palavra-passe utilizadas para autenticar o pedido.
    * **-G** - indica que este é num pedido GET.

2. Utilize o seguinte comando para listar as tabelas HBase existentes:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Utilize o seguinte comando para criar uma nova tabela HBase com duas famílias de tipos de coluna:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    Esquema de é fornecido no formato de JSon.

4. Utilize o seguinte comando para inserir alguns dados:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    Tem de base64 codificar os valores especificados no parâmetro -d.  No exmaple:

    - MTAwMA = =: 1000
    - UGVyc29uYWw6TmFtZQ = =: pessoal: nome
    - Sm9obiBEb2xl: João Dole

    [tecla de linha falso](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite-lhe inserir várias valor (por lotes).

5. Utilize o seguinte comando para obter uma linha:

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Para mais informações sobre HBase resto, consulte o artigo [Guia de referência de HBase Apache](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Verificar o estado de cluster

HBase no HDInsight é fornecido com uma IU Web para clusters de monitorização. Utilizar a IU da Web, pode pedir estatísticas ou informações acerca de regiões.

SSH também pode ser utilizado para túnel pedidos locais, tal como pedidos de web, ao HDInsight cluster. O pedido, em seguida, será encaminhado para o recurso pedido como se tinha tem origem no nó cabeça de cluster HDInsight. Para mais informações, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Para estabelecer uma sessão de túnel de SSH**

1. Abra **betumes**.  
2. Se tiver fornecido uma tecla SSH quando criou a sua conta de utilizador durante o processo de criação, deve efetuar os passos seguintes para selecionar a chave privada a utilizar ao autenticar ao cluster:

    Na **categoria**, expanda a **ligação**, expanda **SSH**e selecione **Auth**. Por fim, clique em **Procurar** e selecione o ficheiro de .ppk que contém a sua chave privada.

3. Na **categoria**, clique em **sessão**.
4. A partir de opções de básicas para o seu ecrã para sessão, introduza os seguinte valores:

    - **Nome do anfitrião**: o endereço SSH do seu campo de servidor no nome do anfitrião (ou endereço IP) HDInsight. O endereço SSH é o seu nome de cluster, em seguida, **-ssh.azurehdinsight.net**. Por exemplo, *mycluster ssh.azurehdinsight.net*.
    - **Porta**: 22. O ssh porta na headnode primária é 22.  
5. Na secção de **categoria** à esquerda da caixa de diálogo, expanda a **ligação**, expanda **SSH**e, em seguida, clique em **túneis**.
6. Forneça as seguintes informações sobre as opções de controlo de formulário de reencaminhamento de chamadas de porta SSH:

    - **Porta de origem** - a porta no cliente que pretende reencaminhar. Por exemplo, 9876.
    - **Dinâmico** - permite dinâmico SOCKS proxy encaminhamento.
7. Clique em **Adicionar** para adicionar as definições.
8. Clique em **Abrir** na parte inferior da caixa de diálogo para abrir uma ligação de SSH.
9. Quando lhe for pedido, inicie sessão no servidor de utilizar uma conta do SSH. Isto irá estabelecer uma sessão SSH e ativar o túnel.

**Para localizar o FQDN do animais utilizando Ambari**

1. Navegue até ao https://<ClusterName>.azurehdinsight.net/.
2. Introduza as suas credenciais de conta de utilizador de cluster duas vezes.
3. No menu à esquerda, clique em **zookeeper**.
4. Clique das três ligações **ZooKeeper Server** a partir da lista de resumo.
5. Copie o **nome do anfitrião**. Por exemplo, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Para configurar um programa de cliente (Firefox) e verificar o estado de cluster**

1. Abra o Firefox.
2. Clique no botão **Abrir Menu** .
3. Clique em **Opções**.
4. Clique em **Avançadas**, clique em **rede**e, em seguida, clique em **Definições**.
5. Selecione **configuração Manual de proxy**.
6. Introduza os seguinte valores:

    - **Socks anfitrião**: localhost
    - **Porta**: utilizar a mesma porta que configurou na betumes SSH túnel.  Por exemplo, 9876.
    - **SOCKS v5**: (selecionada)
    - **DNS remoto**: (selecionada)
7. Clique em **OK** para guardar as alterações.
8. Navegue até ao http://&lt;o FQDN de uma ZooKeeper >: mestre/60010-estado.

Num cluster de elevada disponibilidade, irá encontrar uma ligação para o active HBase mestra nó actual que aloja a IU da Web.

##<a name="delete-the-cluster"></a>Eliminar o cluster

Para evitar inconsistências, recomendamos que desativar as tabelas HBase antes de eliminar o cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximos passos

Neste tutorial HBase para HDInsight, o que aprendeu como criar um cluster de HBase e como criar tabelas e ver os dados nessas tabelas a partir da shell de HBase. Também que aprendeu como utilizar uma consulta de ramo de dados em tabelas HBase e como utilizar as HBase c# REST APIs para criar uma tabela do HBase e obter dados a partir da tabela.

Para obter mais informações, consulte o artigo:

- [Descrição geral de HDInsight HBase][hdinsight-hbase-overview]: HBase é Apache, código fonte a aberto NoSQL base de dados do criada com base em Hadoop que proporciona acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semistructured.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
