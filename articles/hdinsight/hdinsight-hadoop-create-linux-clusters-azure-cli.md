<properties
    pageTitle="Criar clusters Hadoop, HBase ou tempestade no Linux no HDInsight utilizando o clip de Azure em diferentes plataformas | Microsoft Azure"
    description="Saiba como criar clusters baseado em Linux HDInsight utilizando o clip de Azure em diferentes plataformas, modelos de Gestor de recursos do Azure e o Azure REST API. Pode especificar o tipo de cluster (Hadoop, HBase ou tempestade) ou utilize scripts para instalar componentes personalizadas..."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Criar clusters baseados em Linux no HDInsight utilizando o clip do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

O clip do Azure é um utilitário de linha de comandos em diferentes plataformas que permite-lhe gerir dos serviços do Azure. Pode ser utilizado, juntamente com os modelos de gestão de recursos do Azure, para criar um cluster de HDInsight, juntamente com contas associadas de armazenamento e outros serviços.

Modelos de gestão de recursos Azure os documentos JSON que descrevem um __grupo de recursos__ e todos os recursos na mesma (como HDInsight.) Esta abordagem com base no modelo permite-lhe definir todos os recursos que precisa para HDInsight num modelo. Também permite-lhe gerir alterações ao grupo como um todo através de __implementações__, as quais aplicar alterações ao grupo inteiro.

Os passos neste documento guiá-lo durante o processo de criar um novo cluster HDInsight utilizando o clip do Azure e um modelo.

> [AZURE.IMPORTANT] Os passos neste documento utilizam o número predefinido de nós do trabalho (4) para um cluster de HDInsight. Se planeia mais de 32 nós de trabalho (durante a criação de cluster ou pelo dimensionamento cluster), tem de selecionar um tamanho de cabeça nó com, pelo menos, 8 núcleos e 14 GB de ram.
>
> Para mais informações sobre tamanhos de nó e custos associados, consulte o artigo [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Clip azure__. Os passos neste documento última foram testados com Azure clip versão 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

Siga os passos que em [ligar a uma subscrição do Azure a partir da linha de comandos Interface o Azure (Azure CLI)](../xplat-cli-connect.md) -se documentados e ligue à sua subscrição utilizando o método de __início de sessão__ .

##<a name="create-a-cluster"></a>Criar um cluster

Os passos seguintes devem ser efetuados a partir de uma linha de comandos da shell de sessão de terminal depois de instalar e configurar o clip do Azure.

1. Utilize o seguinte comando para autenticar à sua subscrição do Azure:

        azure login

    Lhe for pedido para fornecer o nome e palavra-passe. Se tiver múltiplas subscrições Azure, utilize `azure account set <subscriptionname>` para definir a subscrição que utilizam os comandos do clip Azure.

3. Mudar para o modo de Gestor de recursos do Azure utilizando o seguinte comando:

        azure config mode arm

4. Crie um grupo de recursos. Este grupo de recursos irá conter HDInsight cluster e associados a conta de armazenamento.

        azure group create groupname location
        
    * Substitua o __nome de grupo__ com um nome exclusivo para o grupo. 
    * Substitua a região geográfica ao qual pretende criar o grupo na __localização__ . 
    
        Para uma lista de localizações válidas, utilize o `azure location list` comando e, em seguida, utilize uma das localizações da coluna __nome__ .

5. Crie uma conta de armazenamento. Esta conta de armazenamento será utilizada como o armazenamento de predefinida para o cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Substitua o __nome de grupo__ com o nome do grupo que criou no passo anterior.
     * Substitua __localização__ na mesma localização que utilizou no passo anterior. 
     * Substitua __storagename__ um nome exclusivo para a conta de armazenamento.
     
     > [AZURE.NOTE] Para mais informações sobre os parâmetros utilizados neste comando, utilize `azure storage account create -h` para ver a ajuda para este comando.

5. Obter a chave utilizada para aceder à conta de armazenamento.

        azure storage account keys list -g groupname storagename
        
    * Substitua o __nome de grupo__ com o nome do grupo de recursos.
    * Substitua __storagename__ com o nome da conta de armazenamento.
    
    Os dados que é devolvido, guarde o valor de __chave__ para __chave1__.

6. Crie um cluster de HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua o __nome de grupo__ com o nome do grupo de recursos.

    * Substitua __Hadoop__ o tipo de cluster que pretende criar. Por exemplo, `Hadoop`, `HBase`, `Storm` ou `Spark`.

        > [AZURE.IMPORTANT] HDInsight clusters está disponível uma variedade de tipos de, que correspondem à carga de trabalho ou tecnologia cluster está optimizado para. Não existe nenhum método suportado para criar um cluster que combina vários tipos de, tais como tempestade e HBase num cluster de um. 

    * Substitua __localização__ na mesma localização utilizada nos passos anteriores.

    * Substitua o nome da conta de armazenamento __storagename__ .

    * Substitua __propriedade storagekey__ com a tecla obtida no passo anterior. 

    * Para o `--defaultStorageContainer` parâmetro, utilize o mesmo nome à medida que estão a utilizar para o cluster.

    * Substitua __admin__ e __httppassword__ com o nome e palavra-passe que pretende utilizar ao aceder ao cluster através de HTTPS.

    * Substituir __sshuser__ e __sshuserpassword__ com o nome de utilizador e palavra-passe que pretende utilizar ao aceder ao cluster utilizando SSH

    Poderá demorar vários minutos até que o processo de criação de cluster concluir. Normalmente cerca de 15.

##<a name="next-steps"></a>Próximos passos

Agora que criou com êxito um cluster de HDInsight utilizando o clip do Azure, utilize o seguinte para saber como trabalhar com o seu cluster:

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)
* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
* [Utilizar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase clusters

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters tempestade

* [Desenvolver topologias Java para tempestade no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python tempestade no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias com tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
