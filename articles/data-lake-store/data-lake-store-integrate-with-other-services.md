<properties
   pageTitle="Integrar o arquivo de dados de Lake com outros serviços do Azure | Microsoft Azure"
   description="Compreender como arquivo de dados de Lake integra-se com outros serviços do Azure"
   documentationCenter=""
   services="data-lake-store"
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

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrar o arquivo de dados de Lake com outros serviços do Azure

Arquivo de Lake Azure dados podem ser utilizado em conjunto com outros serviços do Azure para ativar uma vasta gama de cenários. O seguinte artigo lista os serviços de arquivo de dados de Lake pode ser integrado com.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Utilizar dados Lake loja com Azure HDInsight

Pode aprovisionar um cluster de [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que utiliza o arquivo de dados de Lake como o armazenamento compatível com HDFS. Nesta versão, para Hadoop e tempestade clusters no Windows e Linux, pode utilizar arquivo de dados de Lake apenas como um armazenamento adicional. Este tipo de clusters continuar a utilizar Azure armazenamento (WASB) como o armazenamento de predefinido. No entanto, para HBase clusters no Windows e Linux, pode utilizar arquivo de dados de Lake como o armazenamento de predefinido, armazenamento adicional ou ambos.

Para obter instruções sobre como pode aprovisionar um cluster de HDInsight com o arquivo de dados de Lake, consulte:

* [Aprovisionar um cluster de HDInsight com o arquivo de dados de Lake através do Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprovisionar um cluster de HDInsight com o arquivo de dados de Lake através do PowerShell do Azure](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Prefere vídeos?** Siga as ligações abaixo para ver vídeos com instruções sobre como utilizar o arquivo de dados de Lake com HDInsight clusters.

* [Criar um cluster de HDInsight com acesso ao arquivo de dados de Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Quando o cluster estiver configurado, [dados do Access no arquivo de Lake dados de utilização de scripts de ramo e porco](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Utilizar dados Lake loja com a análise de dados Azure Lake

[Azure dados Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) permite-lhe trabalhar com dados grande escala na nuvem. Dinamicamente disposições recursos e permite-lhe fazer a análise no terabytes ou até mesmo exabytes de dados que podem ser armazenados num número de origens de dados suportados, uma da ser arquivo de dados de Lake. A análise de dados Lake especialmente está optimizada para funcionar com o Azure dados Lake Store - fornecer o nível mais alto de desempenho, débito e parallelization por si cargas de trabalho de dados grande.

Para obter instruções sobre como utilizar a análise de dados Lake com dados Lake loja, consulte o artigo [Introdução ao utilizar o arquivo de dados de Lake a análise de dados Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Prefere vídeos?** Siga as ligações abaixo para ver vídeos com instruções sobre como utilizar o arquivo de dados de Lake com HDInsight clusters.

* [Ligar a análise de dados Azure Lake ao arquivo de Lake de dados do Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Arquivo do Lake de dados do Access Azure através do Lake a análise de dados](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Utilizar dados Lake loja com dados Azure fábrica

Pode utilizar o [Azure dados fábrica](https://azure.microsoft.com/services/data-factory/) para ingerir esta última dados a partir do Azure tabelas, base de dados do SQL Azure, DataWarehouse de SQL Azure, Azure armazenamento de Blobs e bases de dados no local. A ser um cidadão primeira classe no Azure ecossistema, Azure dados fábrica podem ser utilizada para orquestrar ingestão de dados a partir destes origem para Azure dados Lake loja.

Para obter instruções sobre como utilizar o Azure fábrica de dados com o arquivo de dados de Lake, consulte o artigo [mover os dados de e para a loja de Lake dados utilizando a fábrica de dados](../data-factory/data-factory-azure-datalake-connector.md).

**Vídeos novamente!** Consulte o artigo [Orchestration dados utilizando o Azure dados fábrica para Azure dados Lake arquivo](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiar dados a partir do Azure armazenamento de Blobs para o arquivo de dados de Lake

Azure dados Lake arquivo fornece uma ferramenta de linha de comandos, AdlCopy, que permite-lhe copiar dados a partir do armazenamento de Blobs do Azure para uma conta do arquivo de Lake de dados. Para mais informações, consulte o artigo [copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiar dados entre base de dados do SQL Azure e o arquivo de dados de Lake

Pode utilizar Apache Sqoop para importar e exportar dados entre a base de dados do SQL Azure e o arquivo de Lake de dados. Para mais informações, consulte o artigo [copiar dados entre o arquivo de dados de Lake e base de dados do Azure SQL utilizando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Veja este vídeo** no [Utilizando Sqoop Apache para mover dados entre relacionais origens e Azure dados Lake loja](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Utilizar dados Lake loja com a análise da cadeia

Pode utilizar o arquivo de dados de Lake como um dos resultados para armazenar dados transmitido em sequência utilizando a análise da cadeia de Azure. Para mais informações, consulte o artigo [dados de sequência a partir do Azure armazenamento Blob para o arquivo de dados de Lake utilizando a análise da cadeia de Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Utilizar dados Lake loja com o Power BI

Pode utilizar o Power BI para importar dados a partir de uma conta de arquivo de Lake de dados para analisar e visualizar os dados. Para mais informações, consulte o artigo [Analisar dados no arquivo de dados de Lake através do Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Utilizar dados Lake loja com o catálogo de dados

Pode registar dados a partir do arquivo de dados de Lake no catálogo de dados do Azure para tornar os dados detetável toda a organização. Para obter mais informações consulte o artigo [registar dados a partir do arquivo de dados de Lake no catálogo de dados do Azure](data-lake-store-with-data-catalog.md).


## <a name="see-also"></a>Consulte também

- [Descrição geral do Azure Lake de arquivo de dados](data-lake-store-overview.md)
- [Introdução ao arquivo de dados de Lake através do Portal](data-lake-store-get-started-portal.md)
- [Introdução ao arquivo de dados de Lake através do PowerShell](data-lake-store-get-started-powershell.md)  
