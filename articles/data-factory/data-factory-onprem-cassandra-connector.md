<properties 
    pageTitle="Mover os dados a partir do Cassandra utilizando dados fábrica | Microsoft Azure" 
    description="Saiba mais sobre como mover dados de uma base de dados no local Cassandra utilizando Azure fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover os dados a partir de uma base de dados no local Cassandra utilizando a fábrica de dados do Azure
Este artigo descreve como pode utilizar a atividade de cópia numa fábrica Azure dados para copiar dados de uma base de dados de Cassandra no local para qualquer arquivo de dados listado em Sink coluna na secção de [origens e suportados sumidouros](data-factory-data-movement-activities.md#supported-data-stores) . Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

Atualmente a fábrica de dados suporta apenas mover dados a partir de uma base de dados Cassandra suportadas [sink arquivos de dados](data-factory-data-movement-activities.md#supported-data-stores), mas não mover dados de outros arquivos de dados para uma base de dados Cassandra.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço do Azure dados fábrica conseguir ligar à sua base de dados de Cassandra no local, tem de instalar o seguinte procedimento: 

- O Data Management Gateway 2.0 ou acima no mesmo computador que aloja a base de dados ou num computador em separado para evitar a competir para recursos com a base de dados. O Data Management Gateway é um software que liga as origens de dados no local a serviços em nuvem de forma segura e geridos. Consulte o artigo [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway.
  
    Quando instala o gateway, instala automaticamente um controlador ODBC do Microsoft Cassandra utilizado para ligar à base de dados Cassandra. 

> [AZURE.NOTE] Consulte o artigo [resolver problemas de gateway problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para sugestões sobre resolução de problemas de ligação/gateway relacionados com problemas. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de uma base de dados Cassandra para qualquer um dos arquivos de dados suportados sink é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados de base de dados Cassandra ao armazenamento de Blobs do Azure que sejam. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Exemplo: Copiar dados de Cassandra para Blob
O exemplo copia os dados a partir de uma base de dados Cassandra para um BLOBs do Azure cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. Dados podem ser copiados diretamente para qualquer uma das PIAs indicadas no artigo [Atividades de movimento de dados](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados. 

- Um serviço do tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties)ligado.
- Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
- Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [CassandraTable](#cassandratable-properties).
- Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [CassandraSource](#cassandrasource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Serviço de Cassandra ligada**

Este exemplo utiliza o serviço de **Cassandra** ligadas. Consulte a secção [Cassandra ligadas serviço](#onpremisescassandra-linked-service-properties) para as propriedades de suportadas por este serviço ligado.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }


**Serviço de armazenamento ligado do Azure**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
        "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

**Conjunto de dados de entrada Cassandra**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

A definição **externos** para **true** informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica de dados.

**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Gasoduto com atividade de cópia**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **CassandraSource** e **sink** tipo está definido como **BlobSink**. 

Ver [Propriedades de tipo de RelationalSource](#cassandrasource-type-properties) para a lista de propriedades suportadas pela RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
            ]   
        }
    }
## <a name="onpremisescassandra-linked-service-properties"></a>Propriedades do serviço OnPremisesCassandra ligada

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de Cassandra ligada.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo tem de estar definida: **OnPremisesCassandra** | Sim |
| anfitrião | Uma ou mais endereços IP ou nomes de anfitrião dos servidores Cassandra.<br/><br/>Especifique uma lista separados por vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. | Sim | 
| porta | A porta TCP que o servidor de Cassandra utiliza para ouvir para ligações de cliente. | Valor predefinido não,: 9042 |
| authenticationType | Básicos ou anónimo | Sim |
| nome de utilizador |Especifique o nome de utilizador para a conta de utilizador. | Sim, se authenticationType estiver definido para Basic. |
| palavra-passe | Especifique a palavra-passe da conta de utilizador.  | Sim, se authenticationType estiver definido para Basic. |
| gatewayName | O nome do gateway utilizado para ligar à base de dados no local Cassandra. | Sim |
| encryptedCredential | Credencial encriptado pelo gateway. | N | 

## <a name="cassandratable-properties"></a>Propriedades de CassandraTable

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| keyspace | Nome do esquema na base de dados Cassandra ou keyspace. | Sim (se a **consulta** para **CassandraSource** não está definida). |
| Tabela | Nome da tabela numa base de dados Cassandra. | Sim (se a **consulta** para **CassandraSource** não está definida). |


## <a name="cassandrasource-type-properties"></a>Propriedades do tipo de CassandraSource
Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Quando a origem for do tipo **CassandraSource**, estão disponíveis as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| consulta | Utilize a consulta personalizada para ler dados. | Consulta SQL-92 ou consulta CQL. Consulte o artigo [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique o **nome de name.table keyspace** para representar a tabela que pretende consultar. | Não (se a tabela e keyspace no conjunto de dados são definidos).  |
| consistencyLevel | O nível de consistência Especifica réplicas quantos tem de responder a um pedido de leitura antes de devolver dados para a aplicação de cliente. Cassandra verifica o número especificado de réplicas para os dados satisfazer o pedido de leitura. | UM, DOIS, TRÊS, QUÓRUM, TODOS, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte o artigo [Configurar consistência dos dados](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. | Não. Valor predefinido é um. |  


### <a name="type-mapping-for-cassandra"></a>Mapeamento de tipo para Cassandra
Tipo de Cassandra | .NET com base no tipo
--------------- | ---------------
ASCII | Cadeia 
BIGINT | Int64
BLOB | Byte]
BOOLEANO | Booleano
DECIMAL | Decimal
DUPLA | Dupla
FLUTUANTE | Única
INET | Cadeia
INT | Int32
TEXTO | Cadeia
DATA/HORA | Data/hora
TIMEUUID | GUID
UUID | GUID
VARCHAR | Cadeia
VARINT | Decimal

> [AZURE.NOTE]  
> Para coleção tipos (mapa, definir, lista, etc.), consulte a secção [trabalhar com tipos de colecção de Cassandra utilizando tabela virtual](#work-with-collections-using-virtual-table) . 
> 
> Tipos de definidos pelo utilizador não são suportados.
> 
> O comprimento da coluna binária e coluna da cadeia de comprimento não pode ser maior do que 4000. 

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções de sites utilizando tabela virtual
Azure dados fábrica utiliza um controlador ODBC incorporado para ligar e copiar dados a partir da sua base de dados Cassandra. Para tipos de coleções de sites incluindo mapa, definir e lista, o controlador da normaliza novamente os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver qualquer das colunas coleções de sites, o controlador da gera virtuais tabelas que se seguem:

-   Uma **tabela base**, que contém os mesmos dados como sendo a tabela real exceto as colunas de coleções de sites. A tabela base utiliza o mesmo nome como sendo a tabela real que representa.
-   Uma **tabela virtual** para cada coluna de coleções de sites, que se expande os dados aninhados. As tabelas virtuais que representam coleções de sites são denominadas utilizando o nome da mesa real, um separador "_vt_" e o nome da coluna.

Tabelas virtuais referem-se aos dados na tabela real, permitindo ao controlador aceder aos dados não normalizados. Consulte a secção de exemplo para obter detalhes. Pode aceder ao conteúdo de colecções de Cassandra consultando e associar as tabelas virtuais.

Pode tirar partido de [Copiar o assistente](data-factory-data-movement-activities.md#data-factory-copy-wizard) para intuitivamente ver a lista de tabelas numa base de dados Cassandra incluindo as tabelas virtuais e pré-visualizar os dados no interior. Também pode construir uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "ExampleTable" seguinte é uma tabela de base de dados de Cassandra que contém a coluna de chave primária de um número inteiro com o nome "pk_int", uma coluna de texto com o valor nome, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (denominado "StringSet").

pk_int | Valor | Lista | Mapa |   StringSet
------ | ----- | ---- | --- | --------
1 | "valor de exemplo 1" | ["1", "2", "3"]  | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valor de exemplo 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

O controlador da iria gerar várias tabelas virtuais para representar esta tabela única. As colunas de chave externas nas tabelas virtuais as colunas de chave primária na tabela real de referência e indicam a linha de mesa real da linha da tabela virtual corresponde ao. 

A primeira tabela virtual é a tabela base designada "ExampleTable" é apresentada na seguinte tabela. A tabela base contém os mesmos dados como sendo a tabela da base de dados original exceto as coleções de ficheiros, o que são omitidos nesta tabela e que expandido noutras tabelas virtual.

pk_int | Valor
------ | -----
1 | "valor de exemplo 1"
3 | "valor de exemplo 3"

As tabelas seguintes mostram as tabelas virtuais que renormalize os dados a partir de colunas de lista, de mapa e StringSet. As colunas com nomes terminem em "_index" ou "_key" indicam a posição dos dados numa lista ou mapa original. As colunas com nomes terminem em "_value" contêm os dados expandidos da coleção.

#### <a name="table-exampletablevtlist"></a>Tabela "ExampleTable_vt_List":
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Tabela "ExampleTable_vt_Map":
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Tabela "ExampleTable_vt_StringSet":
pk_int | StringSet_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | "E"





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e Otimização do  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.
