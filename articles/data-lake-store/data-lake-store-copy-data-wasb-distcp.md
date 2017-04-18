<properties
   pageTitle="Copiar dados de e para a WASB para o arquivo de dados de Lake utilizando Distcp | Microsoft Azure"
   description="Utilize a ferramenta de Distcp para copiar dados para e a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Utilizar Distcp para copiar dados entre o Azure armazenamento de Blobs e o arquivo de dados de Lake

> [AZURE.SELECTOR]
- [Utilizar DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Utilizar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Depois de ter criado um cluster de HDInsight tem acesso a uma conta do arquivo de Lake de dados, pode utilizar ferramentas de ecossistema Hadoop como Distcp para copiar dados **de e para** um armazenamento de cluster HDInsight (WASB) para uma conta do arquivo de Lake de dados. Este artigo fornece instruções sobre como alcançar isto.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Ativar a sua subscrição do Azure** para pré-visualização de público do arquivo de Lake de dados. Consulte as [instruções](data-lake-store-get-started-portal.md#signup).
- **Cluster azure HDInsight** com acesso a uma conta do arquivo de Lake de dados. Consulte o artigo [criar um cluster de HDInsight com dados Lake arquivo](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o ambiente de trabalho remoto para cluster.

## <a name="do-you-learn-fast-with-videos"></a>Saiba rapidamente com vídeos?

[Veja este vídeo](https://mix.office.com/watch/1liuojvdx6sie) sobre como copiar dados entre o Azure armazenamento de Blobs e arquivo de dados de Lake utilizando DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Utilizar Distcp a partir do ambiente de trabalho remoto (cluster do Windows) ou SSH (Linux cluster)

Um cluster de HDInsight é fornecido com o utilitário Distcp, que pode ser utilizado para copiar dados de origens diferentes para um cluster de HDInsight. Se tiver configurado o cluster HDInsight para utilizar o arquivo de dados de Lake como um armazenamento adicional, o utilitário Distcp pode ser utilizado out-de-de-box para copiar dados para e de uma conta do arquivo de Lake dados também. Esta secção podemos veja como utilizar o utilitário Distcp.

1. Se tiver um cluster do Windows, remoto para um cluster de HDInsight que tem acesso a uma conta do arquivo de Lake de dados. Para obter instruções, consulte o artigo [ligar a clusters utilizando o RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). A partir do cluster de ambiente de trabalho, abra a linha de comandos Hadoop.

    Se tiver um cluster de Linux, utilize SSH para ligar ao cluster. Consulte o artigo [ligar a um cluster de baseado em Linux HDInsight](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Execute os comandos a partir da linha de comandos do SSH.

3. Verifique se pode aceder a Azure armazenamento de Blobs (WASB). Execute o seguinte comando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Isto deve fornecer uma lista de conteúdos no blob de armazenamento.

4. Da mesma forma, verifique se pode aceder a conta de arquivo de Lake dados a partir do cluster. Execute o seguinte comando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Isto deve fornecer uma lista de ficheiros/pastas na conta de arquivo de Lake de dados.

5. Utilize Distcp para copiar dados de WASB para uma conta do arquivo de Lake de dados.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Isto irá copiar o conteúdo da **pasta/exemplo** dados/gutenberg/no WASB para **/myfolder** na conta de arquivo de Lake de dados.

6. Da mesma forma, utilize Distcp para copiar dados a partir da conta do arquivo de Lake de dados para WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Isto irá copiar o conteúdo de **/myfolder** na conta de arquivo de Lake de dados para a **pasta/exemplo** dados/gutenberg/no WASB.

## <a name="see-also"></a>Consulte também

- [Copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
