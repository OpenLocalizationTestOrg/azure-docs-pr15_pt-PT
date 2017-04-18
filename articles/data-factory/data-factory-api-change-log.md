<properties 
    pageTitle="Dados Factory - registo de alterações de API do .NET | Microsoft Azure" 
    description="Descreve as alterações de última hora, adições de funcionalidade, correções de erros etc... numa versão específica do .NET API para a fábrica de dados do Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Azure dados Factory - registo de alterações da .NET API 
Este artigo fornece informações sobre as alterações ao SDK de fábrica do Azure dados numa versão específica. Pode localizar o pacote de NuGet mais recente para a fábrica de dados do Azure [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>Versão 4.11.0
Funcionalidade adições:

- Os seguintes tipos de serviço ligadas foram adicionados:
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- Os seguintes tipos de conjunto de dados foram adicionados: 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- Os seguintes tipos de origem de cópia foram adicionados:
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>Versão 4.10.0
- Foram adicionadas as seguintes propriedades opcionais para TextFormat:
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [Primeiralinhacomocabeçalho](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- Os seguintes tipos de serviço ligadas foram adicionados:
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- Os seguintes tipos de conjunto de dados foram adicionados:
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- Os seguintes tipos de origem de cópia foram adicionados:
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- Adicionar propriedade [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) AzureMLBatchExecutionActivity 
    - Ativar prisma web várias entradas de serviço para uma experiência de formação de máquina do Azure


## <a name="version-491"></a>Versão 4.9.1

### <a name="bug-fix"></a>Corrigir erros

- Preterir autenticação baseada em WebApi para [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versão 4.9.0

### <a name="feature-additions"></a>Funcionalidade adições

- Adicione propriedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Consulte o artigo [Copiar de faseada](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre a funcionalidade.


### <a name="bug-fix"></a>Corrigir erros

- Apresentar uma sobrecarga de método de [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) , que assume uma instância de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
- Marca [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcional no CopySink.

## <a name="version-480"></a>Versão 4.8.0

### <a name="feature-additions"></a>Funcionalidade adições
- As seguintes propriedades opcionais foram adicionadas ao tipo de atividade de cópia para ativar a otimização do desempenho copiar:
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versão 4.7.0

### <a name="feature-additions"></a>Funcionalidade adições
* Adicionado novo StorageFormat tipo [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar os ficheiros no formato de (ORC) colunas linha otimizada.
* Adicione propriedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings a SqlDWSink.
    * Permite a utilização da PolyBase para copiar dados para armazém de dados SQL.

## <a name="version-461"></a>Versão 4.6.1

### <a name="bug-fixes"></a>Correções de erros
* Corrige o pedido de HTTP para indicar windows atividade.
    * Remove o nome do grupo de recursos e o nome de fábrica do mesmo de dados da carga útil pedido.

## <a name="version-460"></a>Versão 4.6.0

### <a name="feature-additions"></a>Funcionalidade adições

- Foram adicionadas para [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)as seguintes propriedades:
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [Conjuntos de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Foram adicionadas para [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)as seguintes propriedades:
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Nova adição [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) escreva tipo de [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir cujos dados estão no formato JSON de conjuntos de dados. 

## <a name="version-450"></a>Versão 4.5.0

### <a name="feature-additions"></a>Funcionalidade adições
* Adicionado [operações de lista para a janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Adicionado métodos para obter o windows atividade com os filtros baseados nos tipos de entidade (ou seja, fábricas de dados, conjuntos de dados, tubagens e atividades).
* Os seguintes tipos de serviço ligadas foram adicionados: 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados: 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Os seguintes tipos de origem de cópia foram adicionados:  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versão 4.4.0

### <a name="feature-additions"></a>Funcionalidade adições

- O seguinte tipo de serviço ligadas foi adicionado como origens de dados e afunde para atividades de cópia:
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte o artigo [Azure armazenamento SA ligadas serviço](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações conceptuais e exemplos. 

## <a name="version-430"></a>Versão 4.3.0

### <a name="feature-additions"></a>Funcionalidade adições

- A seguinte haven de tipos de serviço ligadas foi adicionado como origens de dados para atividades de cópia:
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte o artigo [mover os dados a partir do HDFS utilizando a fábrica de dados](data-factory-hdfs-connector.md) para obter informações conceptuais e exemplos. 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte o artigo [mover dados do ODBC dados armazena utilizando Azure dados fábrica](data-factory-odbc-connector.md) para obter informações conceptuais e exemplos. 

## <a name="version-420"></a>Versão 4.2.0

### <a name="feature-additions"></a>Funcionalidade adições

- O novo tipo de atividade seguinte foi adicionado: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter detalhes sobre a atividade, consulte o artigo [modelos de atualizar o Azure ML utilizando a atividade de recursos de atualização](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Foi adicionado um novo de propriedade opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) para a [classe de AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Propriedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) foram adicionadas à classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) . 
- Permitir a configuração de limites de tempo para chamadas de cliente para o serviço de dados fábrica do mesmo. 


## <a name="version-410"></a>Versão 4.1.0

### <a name="feature-additions"></a>Funcionalidade adições
* Os seguintes tipos de serviço ligadas foram adicionados: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Os seguintes tipos de atividade foram adicionados: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Os seguintes tipos de origem e sink para copiar atividade foram adicionados:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>Versão 4.0.1

### <a name="breaking-changes"></a>Força de alterações
As seguintes classes nomes foram mudadas. Os novos nomes foram os nomes de classes originais antes de libertar 4.0.0. 
 
O nome na 4.0.0 | O nome na 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>Versão 4.0.0

### <a name="breaking-changes"></a>Força de alterações



- As seguintes classes/interfaces nomes foram mudadas.

| Nome antigo | Novo nome |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabela | [Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- Os métodos de **lista** devolvem resultados paginados agora. Se a resposta contiver uma propriedade de **NextLink** não vazia, a aplicação de cliente tem de continuar a obter a página seguinte até todas as páginas são devolvidas.  Eis um exemplo: 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- **Lista de** tubagem API devolve apenas o resumo de uma tubagem em vez de detalhes na totalidade. Por exemplo, atividades num resumo de tubagem contenham apenas o nome e tipo.

### <a name="feature-additions"></a>Funcionalidade adições
- A classe de [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) suporta duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para que suportem idempotent cópia para armazém de dados do SQL Azure. Consulte o artigo [Armazém de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md) , especificamente, o [mecanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) e [2 mecanismo](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) secções, para obter detalhes sobre estas propriedades.

- Suportamos agora a executar o procedimento armazenado contra a base de dados do SQL Azure e armazém de dados do SQL Azure origens como parte da atividade de cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte os artigos de [Base de dados do SQL Azure](data-factory-azure-sql-connector.md#sqlsource) e [Armazém de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) no Azure.com para obter detalhes sobre estas propriedades.  