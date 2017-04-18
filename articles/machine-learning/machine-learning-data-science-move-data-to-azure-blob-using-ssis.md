<properties
    pageTitle="Mover os dados para ou a partir do armazenamento de Blobs do Azure utilizando SSIS conexões | Microsoft Azure"
    description="Mova os dados para ou a partir do armazenamento de Blobs do Azure utilizando SSIS conexões."
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover os dados para ou a partir do armazenamento de Blobs do Azure utilizando SSIS conectores

O [SQL Server Integration Services Feature Pack para Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornece componentes para ligar ao Azure, dados de transferência entre o Azure e origens de dados no local e processar dados armazenados no Azure.

Orientações sobre tecnologias utilizadas para mover os dados para e/ou a partir do armazenamento de Blobs do Azure estão ligados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Depois dos clientes tiverem dados no local para a nuvem, que podem aceder aos mesmos a partir de qualquer serviço Azure para tirar partido da potencialidade completa do conjunto de tecnologias Azure. -Pode ser utilizado, por exemplo, no Azure máquina formação ou num HDInsight cluster.

Este é normalmente ser o primeiro passo para os tutoriais [SQL](machine-learning-data-science-process-sql-walkthrough.md) e [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) .

Para um debate canónico cenários que utilizar SSIS para efetuar as necessidades da empresa comum em cenários de integração de dados híbridos, consulte o artigo [fazer mais com SQL Server Integration Services Feature Pack para Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogue.

> [AZURE.NOTE] Para obter uma introdução concluída ao armazenamento de Blobs do Azure, referir [Noções básicas de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e ao [Serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## <a name="prerequisites"></a>Pré-requisitos

Para executar as tarefas descritas neste artigo, tem de ter uma subscrição do Azure e uma conta de armazenamento Azure configurada. Tem de conhecer o seu armazenamento Azure nome e conta chave da conta para carregar ou transferir dados.

- Para configurar uma **subscrição do Azure**, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).

- Para obter instruções sobre como criar uma **conta de armazenamento** e para o conta e informações da chave, consulte [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).


Para utilizar os **conectores SSIS**, tem de transferir:

- **2014 do SQL Server ou 2016 padrão (ou acima)**: instalar inclui o SQL Server Integration Services.
- **Microsoft SQL Server 2014 ou 2016 Integration Services Feature Pack para Azure**: estes podem ser transferidos, respetivamente, das páginas do [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) e o [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [AZURE.NOTE] SSIS é instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais as aplicações incluídas no vários edições do SQL Server, consulte o artigo [Edições do SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

Para materiais de formação no SSIS, consulte o artigo [Mãos na formação para SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Para obter informações sobre como obter a seta para cima e execução utilizar SISS para construir simple extração, da transformação e pacotes de carregamento (ETL), consulte o artigo [SSIS Tutorial: criar um pacote de ETL simples](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Transferir o conjunto de dados de veículo pt  
O exemplo descrito aqui utilize um conjunto de dados publicamente disponível – o conjunto de dados de [Viagens de veículo pt](http://www.andresmh.com/nyctaxitrips/) . O conjunto de dados é composta por cerca de 173 milhões veículo de partilha de automóveis no pt no ano 2013. Existem dois tipos de dados: viagem de detalhes de dados e tarifa de dados. Porque não existe um ficheiro para cada mês, temos 24 ficheiros em todas as, cada um dos quais é aproximadamente a 2GB, não comprimido.


## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados ao armazenamento de Blobs do Azure
Para mover dados utilizando o SSIS funcionalidade pacote a partir de no local ao armazenamento de Blobs do Azure, utilizamos uma instância da [**Tarefa de carregar de Blobs do Azure**](https://msdn.microsoft.com/library/mt146776.aspx), é mostrado aqui:

![configurar-dados-ciência-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


Os parâmetros de que a tarefa utiliza estão descritos aqui:


Campo|Descrição|
----------------------|----------------|
**AzureStorageConnection**|Especifica um Gestor de ligação de armazenamento do Azure existente ou cria uma nova que se refere a uma conta de armazenamento Azure que aponta para o local onde estão alojados os ficheiros de Blobs.|
**BlobContainer**|Especifica o nome do contentor blob que mantenha os ficheiros carregados como blobs.|
**BlobDirectory**|Especifica o directório de BLOBs localização onde armazenou o ficheiro transferido como um blob bloco. O directório blob é uma estrutura hierárquica virtual. Se o blob já existir, it ia substituída.|
**LocalDirectory**|Especifica o directório local que contém os ficheiros ser carregado.|
**Nome de ficheiro**|Especifica um filtro de nome para selecionar os ficheiros com o padrão de nomes especificados. Por exemplo, MySheet\*. xls\* inclui ficheiros como MySheet001.xls e MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Especifica um filtro de intervalo de tempo. Ficheiros alterada depois de *TimeRangeFrom* e antes de *TimeRangeTo* são incluídos.|


> [AZURE.NOTE] As credenciais de **AzureStorageConnection** tem de ser correto e os **BlobContainer** tem de existir previamente é tentada a transferência.

## <a name="download-data-from-azure-blob-storage"></a>Transferir dados do armazenamento de Blobs do Azure

Para transferir dados do armazenamento de Blobs do Azure para local armazenamento com SSIS, utilize uma instância da [Tarefa de carregar de Blobs do Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##<a name="more-advanced-ssis-azure-scenarios"></a>Cenários do SSIS Azure mais avançados
Estamos aqui, repare que o SSIS feature pack permite monetários mais complexos para serem processados pelo tarefas embalagem em conjunto. Por exemplo, os dados de BLOBs poderiam feed diretamente para um cluster de HDInsight, cuja saída foi possível transferir novamente para um blob e, em seguida, ao armazenamento no local. SSIS pode executar tarefas ramo e porco num cluster HDInsight utilização de conectores SSIS adicionais:

- Para executar um script ramo num cluster Azure HDInsight com SSIS, utilize o [Azure HDInsight Hive tarefa](https://msdn.microsoft.com/library/mt146771.aspx).
- Para executar um script porco num cluster Azure HDInsight com SSIS, utilize o [Azure HDInsight porco tarefa](https://msdn.microsoft.com/library/mt146781.aspx).
