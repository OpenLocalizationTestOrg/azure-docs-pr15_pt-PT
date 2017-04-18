<properties
    pageTitle="Gerir clusters Hadoop utilizando Azure clip | Microsoft Azure"
    description="Como utilizar o clip do Azure para gerir Hadoop clusters no HDIsight"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gerir clusters Hadoop no HDInsight utilizando o clip do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como utilizar a [Interface de comandos do Azure](../xplat-cli-install.md) para gerir Hadoop clusters no Azure HDInsight. O clip do Azure está implementada Node.js. Pode ser utilizada em nenhuma plataforma que suporte Node.js, incluindo o Windows, Mac e Linux.

Este artigo aborda a utilizar apenas o clip do Azure com HDInsight. Para um guia Geral sobre como utilizar o Azure clip, consulte o artigo [instalar e configurar o clip do Azure][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Clip do azure** - consulte [instalar e configurar o clip do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração.
- **Ligar ao Azure**, utilizando o seguinte comando:

        azure login

    Para mais informações sobre a autenticação utilizando uma conta escolar ou profissional, consulte o artigo [ligar a uma subscrição Azure a partir do clip o Azure](xplat-cli-connect.md).
    
- **Mudar para o modo de Gestor de recursos do Azure**, utilizando o seguinte comando:

        azure config mode arm

Para obter ajuda, utilize o parâmetro **-h** .  Por exemplo:

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Criar clusters

Consulte o artigo [baseado em criar Linux clusters de HDInsight utilizando o clip do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Detalhes de cluster de lista e a apresentação
Utilize os seguintes comandos para listar e mostrar detalhes de cluster:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Eliminar clusters

Utilize o seguinte comando para eliminar um cluster de:

    azure hdinsight cluster delete <Cluster Name>

Também pode eliminar um cluster ao eliminar o grupo de recursos que contém o cluster. Tenha em atenção, este procedimento irá eliminar todos os recursos no grupo incluindo a conta de armazenamento predefinida.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Clusters de escala

Para alterar o tamanho do cluster Hadoop:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Ativar/desativar o acesso para um cluster de HTTP

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Ativar/desativar o acesso RDP para um cluster de

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu como desempenhar tarefas administrativas do diferentes HDInsight cluster. Para saber mais, consulte os artigos seguintes:

* [Administrar HDInsight, utilizando o Portal do Azure] [hdinsight-admin-portal]
* [Administrar HDInsight utilizando o PowerShell do Azure] [hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight] [hdinsight-get-started]
* [Como utilizar o clip do Azure] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Clusters de lista e a apresentação"
