<properties
   pageTitle="Criar clusters baseados no Windows Hadoop no HDInsight com o clip do Azure"
    description="Saiba como criar clusters para Azure HDInsight, utilizando o clip do Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
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

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Criar clusters baseados no Windows Hadoop no HDInsight com o clip do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar clusters HDInsight com o clip do Azure. Para outras criação do cluster ferramentas e funcionalidades, clique em Selecionar o separador na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Antes de começar as instruções neste artigo, tem de ter o seguinte procedimento:

- **Subscrição do azure**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Clip azure**.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>Ligar ao Azure

Utilize o seguinte comando para ligar ao Azure:

    azure login

Para mais informações sobre a autenticação utilizando uma conta escolar ou profissional, consulte o artigo [ligar a uma subscrição Azure a partir do clip o Azure](../xplat-cli-connect.md).

Utilize o seguinte comando para mudar para o modo de processador:

    azure config mode arm

Para obter ajuda, utilize o parâmetro **-h** .  Por exemplo:

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>Criar clusters

Tem de ter uma gestão de recursos do Azure (processador) e uma conta de armazenamento de Blobs do Azure antes de poder criar um cluster de HDInsight. Para criar um cluster de HDInsight, tem de especificar o seguinte procedimento:

- **Grupo de recursos do Azure**: conta A dados Lake Analytics tem de ser criada dentro de um grupo de recursos do Azure. Gestor de recursos do Azure permite-lhe trabalhar com os recursos na sua aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos para a sua aplicação numa operação de única e coordenada.

    Para listar os grupos de recursos na sua subscrição:

        azure group list

    Para criar um novo grupo de recursos:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome do cluster HDInsight**

- **Localização**: um dos centros de dados Azure que suporta HDInsight clusters. Para obter uma lista de localizações suportadas, consulte o artigo [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Predefinir uma conta de armazenamento**: HDInsight utiliza um contentor de armazenamento de Blobs do Azure como o sistema de ficheiro predefinido. É necessária uma conta de armazenamento do Windows Azure antes de poder criar um cluster de HDInsight.

    Para criar uma nova conta de armazenamento Azure:

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]A conta de armazenamento tem de ser co-instalados com HDInsight no Centro de dados.
    > O tipo de conta de armazenamento não pode ser ZRS, porque ZRS não suporta a tabela.

    Para obter informações sobre como criar uma conta de armazenamento do Windows Azure utilizando o Portal do Azure, consulte o artigo [criar, gerir ou eliminar uma conta de armazenamento] [storageaccount de criar de azure].

    Se já tiver uma conta de armazenamento, mas não souber o nome da conta e a chave de conta, pode utilizar os comandos seguintes para obter as informações:

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    Para obter detalhes sobre como obter as informações utilizando o Portal do Azure, consulte a secção "Gerir a sua conta de armazenamento" [Azure sobre contas de armazenamento](../storage/storage-create-storage-account#manage-your-storage-account).

- **(Opcional) predefinido Blob contentor**: O comando **Criar azure hdinsight cluster** cria o contentor se não existe. Se optar por criar previamente o contentor, pode utilizar o seguinte comando:

    criar o contentor de armazenamento Azure – nome da conta "<Storage Account Name>" – chave da conta <Storage Account Key> [ContainerName]

Assim que tiver a conta de armazenamento preparada, está pronto para criar um cluster:


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>Criar clusters de utilizar ficheiros de configuração
Normalmente, crie um cluster de HDInsight, executar tarefas no mesmo e, em seguida, elimine o cluster para cortar para baixo o custo. A interface de comandos dá-lhe a opção para guardar as configurações para um ficheiro, para que possa reutilizar sempre que cria um cluster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Exemplo: Crie um ficheiro de configuração que contém uma acção de script para ser executada quando criar um cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>Criar clusters com ação de script

Crie um ficheiro de configuração que contém uma acção de script para ser executada quando criar um cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Criar um cluster com uma acção de script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Para obter informações de ação de script geral, consulte o artigo [Personalizar HDInsight clusters utilizando Script ação (Linux)](hdinsight-hadoop-customize-cluster.md).


## <a name="create-clusters-using-arm-templates"></a>Criar clusters de utilização de modelos de processador

Pode utilizar o clip para criar clusters ao contactar o suporte modelos de processador. Consulte o artigo [Implementar com clip Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Consulte também

- [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Saiba como começar a trabalhar com o seu cluster HDInsight
- [Submeter Hadoop através de programação de tarefas](hdinsight-submit-hadoop-jobs-programmatically.md) - Saiba como através de programação submeter tarefas para HDInsight
- [Gerir clusters Hadoop no HDInsight utilizando o clip do Azure](hdinsight-administer-use-command-line.md)
- [Utilizar o clip Azure para Mac, Linux e Windows com a gestão de serviço do Azure](../virtual-machines-command-line-tools.md)
