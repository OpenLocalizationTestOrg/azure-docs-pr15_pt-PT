<properties 
   pageTitle="Cenários de dados que envolvam arquivo de dados de Lake | Microsoft Azure" 
   description="Compreender a cenários diferentes e ferramentas utilizando os dados que podem penetração, processadas, transferidos e visualizar os num arquivo de dados Lake" 
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
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Utilizar o arquivo do Azure dados Lake para requisitos de dados grande

Existem quatro fases principais no grande processamento de dados:

* Ingesting grandes quantidades de dados para um arquivo de dados em tempo real no ou em lotes
* Processamento dos dados
* Transferir os dados
* Visualizar os dados

Neste artigo, iremos observe estas fases relativamente às Azure dados Lake arquivo para compreender as opções e ferramentas disponíveis para corresponder às suas necessidades de dados grande.

## <a name="ingest-data-into-data-lake-store"></a>Ingerir esta última dados para o arquivo de dados de Lake

Esta secção realça diferentes origens de dados e as diferentes maneiras na qual podem ser penetração esses dados para uma conta do arquivo de Lake de dados.

![Dados de Ingest para o arquivo de dados de Lake] (./media/data-lake-store-data-scenarios/ingest-data.png "Dados de Ingest para o arquivo de dados de Lake")

### <a name="ad-hoc-data"></a>Dados ad hoc

Isto representa mais pequenos conjuntos de dados que são utilizados para protótipos de uma aplicação de dados grande. Existem maneiras diferentes de dados de ad hoc ingesting consoante a origem dos dados.

| Origem de dados        | Ingeri-lo utilizando esta última                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Computador local     | <ul> <li>[Portal do Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure em diferentes plataformas clip](data-lake-store-get-started-cli.md)</li> <li>[Utilizar ferramentas de Lake de dados para o Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure armazenamento Blob | <ul> <li>[Dados Azure fábrica](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução num cluster de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Dados em sequência

Isto representa os dados que podem ser gerados pelo várias origens como aplicações, dispositivos, sensores, etc. Podem ser penetração estes dados para um arquivo de Lake dados através de ferramentas de variedade. Estas ferramentas normalmente capturar e processar dados numa base de evento ao evento em tempo real e, em seguida, escrever os eventos em lotes para o arquivo de dados de Lake para que possam ser processadas ainda mais. 

Seguem-se ferramentas que pode utilizar:
 
* [Sequência azure Analytics] (.. / da cadeia-analytics--lake-resultado dos dados) - eventos penetração concentradores evento podem ser escritos Azure dados Lake utilizando os resultados de uma loja de Lake Azure dados.
* [Azure HDInsight tempestade](../hdinsight/hdinsight-storm-write-data-lake-store.md) - pode escrever dados diretamente para o arquivo de dados de Lake do cluster tempestade.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – pode receber eventos de concentradores evento e, em seguida, escreva-a no arquivo de dados de Lake utilizando o [Dados Lake loja .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais

Também pode origem dados a partir de bases de dados relacionais. Durante um período de tempo, bases de dados relacionais recolhem grandes quantidades de dados que podem fornecer informações chaves se processadas através do pipeline de dados grande. Pode utilizar as seguintes ferramentas para mover desses dados para o arquivo de dados de Lake.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Dados Azure fábrica](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo de servidor Web (carregamento utilizar aplicações personalizadas)

Este tipo de conjunto de dados é realçado especificamente porque a análise dos dados de registo de servidor web é um caso de utilização comum para as aplicações de dados grande e necessita de grandes volumes de ficheiros de registo ser carregado para o arquivo de Lake de dados. Pode utilizar qualquer uma das seguintes ferramentas para escrever os seus próprios scripts ou aplicações para carregar desses dados.

* [Azure em diferentes plataformas clip](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure dados Lake loja .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dados Azure fábrica](../data-factory/data-factory-data-movement-activities.md)

Para carregar os dados do registo servidor web bem como para carregar outros tipos de dados (por exemplo, dados de rede social elementos), é uma boa abordagem para escrever os seus próprios scripts/aplicações personalizadas porque dá-lhe a flexibilidade de poder incluir os seus dados se carregar o componente como parte da sua aplicação de dados grande maior. Em alguns casos, este código poderá demorar a forma de um script ou um utilitário simples linha de comandos. Em outros casos, o código pode ser utilizado para integrar grande processamento de dados para uma aplicação empresarial ou de uma solução.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados a clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, tempestade) suportam arquivo de dados de Lake como um repositório de armazenamento de dados. HDInsight clusters aceder aos dados a partir de Blobs do Azure armazenamento (WASB). Para um melhor desempenho, pode copiar os dados a partir do WASB para uma conta do arquivo de Lake dados associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Dados Azure fábrica](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Os dados armazenados no local ou IaaS Hadoop clusters

Grandes quantidades de dados podem ser armazenadas em clusters Hadoop existentes, localmente no máquinas utilizando HDFS. Os clusters Hadoop poderão ser uma implementação no local ou podem estar dentro de um cluster de IaaS no Azure. Podem existir requisitos para copiar esses dados ao arquivo de Lake Azure dados para uma abordagem pontual ou de uma forma recorrente. Existem várias opções que pode utilizar para realizar esta. Segue-se uma lista de alternativas e os compromissos associados.

| Abordagem  | Detalhes | Vantagens   | Considerações sobre  |
|-----------|---------|--------------|-----------------|
| Utilizar o Azure dados fábrica (ADF) para copiar dados diretamente a partir do Hadoop clusters ao arquivo de Lake de dados do Azure | [ADF suporta HDFS como uma origem de dados](../data-factory/data-factory-hdfs-connector.md) | ADF fornece suporte de fora da caixa para HDFS e primeira classe ponto a ponto monitorização e gestão | Requer o Data Management Gateway ser implementada no local ou no IaaS cluster |
| Exporte dados do Hadoop como ficheiros. Em seguida, copie os ficheiros ao arquivo de Lake Azure dados utilizando mecanismo adequado.                                   | Pode copiar ficheiros para utilizar o arquivo de Lake Azure dados: <ul><li>[PowerShell para o sistema operativo do Windows Azure](data-lake-store-get-started-powershell.md)</li><li>[Azure em diferentes plataformas clip para que não sejam - sistema operativo Windows](data-lake-store-get-started-cli.md)</li><li>Aplicação personalizada utilizando qualquer SDK de arquivo de Lake de dados</li></ul> | Introdução para começar a utilizar. Pode fazer carregamentos personalizados                                                   | Processo de vários passos que envolve tecnologias múltiplos. Monitorização e gestão aumentará até ser um desafio ao longo do tempo dado a natureza das ferramentas personalizada |
| Utilize Distcp para copiar dados de Hadoop para armazenamento do Windows Azure. Em seguida, copie dados a partir do armazenamento Azure ao arquivo de dados de Lake utilizando mecanismo adequado. | Pode copiar dados a partir do armazenamento Azure para utilizar o arquivo de Lake dados: <ul><li>[Dados Azure fábrica](../data-factory/data-factory-data-movement-activities.md)</li><li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache em execução em HDInsight clusters](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| Pode utilizar ferramentas Abrir origem. | Processo de vários passos que envolve tecnologias múltiplos |

### <a name="really-large-datasets"></a>Muito grandes conjuntos de dados

Para carregar conjuntos de dados que o intervalo em várias terabytes, utilizando os métodos descritos acima pode ser por vezes lento e dispendioso. Nestes casos, pode utilizar as opções abaixo.

* **Utilizar o Azure ExpressRoute**. Azure ExpressRoute permite-lhe criar ligações privada entre o Azure centros de dados e infraestrutura nas suas instalações. Este procedimento fornece uma opção fiável para transferir grandes quantidades de dados. Para mais informações, consulte a [documentação do Azure ExpressRoute](../expressroute/expressroute-introduction.md).


* **"Offline" carregar de dados**. Se utilizar o Azure ExpressRoute não viável por qualquer motivo, pode utilizar o [serviço do Azure importar/exportar](../storage/storage-import-export-service.md) para o envio de unidades de disco rígido com os seus dados para um centro de dados Azure. Os dados pela primeira vez são carregados para o Azure armazenamento de Blobs. Em seguida, pode utilizar [Azure fábrica de dados](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) ou a [ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake.

    >[AZURE.NOTE] Enquanto utilizando o serviço de importação/exportação, os tamanhos de ficheiro nos discos que tenha enviado para o Centro de dados Azure não deve ser maior do que 200 GB.


## <a name="process-data-stored-in-data-lake-store"></a>Processar dados armazenados no arquivo de dados de Lake

Assim que os dados estão disponíveis no arquivo de dados de Lake pode executar a análise nesses dados utilizando as aplicações de dados de grande suportadas. Atualmente, pode utilizar o Azure HDInsight e Azure dados Lake Analytics para executar as tarefas de análise de dados nos dados armazenados no arquivo de dados de Lake. 

![Analisar dados no arquivo de dados de Lake] (./media/data-lake-store-data-scenarios/analyze-data.png "Analisar dados no arquivo de dados de Lake")

Pode observe os exemplos seguintes.

* [Criar um cluster de HDInsight com o arquivo de dados de Lake como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Transferir dados do arquivo de dados de Lake

Também poderá transferir ou mover os dados a partir do Azure dados Lake loja para cenários tais como:

* Mova os dados para outras repositórios interface com o seu tubagens de processamento de dados existente. Por exemplo, poderá pretender mover os dados a partir do arquivo de dados de Lake para a base de dados do Azure SQL ou no local SQL Server.
* Transferi dados no seu computador local para processamento nos ambientes IDE durante a criação de protótipos de aplicação.

![Dados de saída de arquivo de dados de Lake] (./media/data-lake-store-data-scenarios/egress-data.png "Dados de saída de arquivo de dados de Lake")

Nestes casos, pode utilizar qualquer uma das seguintes opções:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Dados Azure fábrica](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Também pode utilizar os seguintes métodos para escrever o seu próprio script pedido para transferir dados do arquivo de dados de Lake.

* [Azure em diferentes plataformas clip](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure dados Lake loja .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualizar dados no arquivo de dados de Lake

Pode utilizar uma mistura de serviços para criar representações visuais das dados armazenados no arquivo de dados de Lake.

![Visualizar dados no arquivo de dados de Lake] (./media/data-lake-store-data-scenarios/visualize-data.png "Visualizar dados no arquivo de dados de Lake")

* Pode iniciar utilizando o [Azure dados fábrica para mover os dados a partir do arquivo de Lake de dados para armazém de dados do SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Após esta ação, pode [integrar o Power BI com armazém de dados do SQL Azure](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) para criar a representação visual dos dados.
