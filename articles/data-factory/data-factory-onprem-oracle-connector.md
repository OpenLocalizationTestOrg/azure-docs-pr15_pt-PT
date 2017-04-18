<properties 
    pageTitle="Mover os dados para/a partir do Oracle utilizando dados fábrica | Microsoft Azure" 
    description="Saiba como mover dados para a base de dados Oracle que está no local utilizando Azure fábrica de dados." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Mover os dados para a no local Oracle utilizando a fábrica de dados do Azure 

Este artigo descreve como pode utilizar a actividade fábrica copiar dados para mover os dados para a Oracle para ou para outros dados armazenar. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

## <a name="installation"></a>Instalação 
Para o serviço do Azure dados fábrica para conseguir ligar à base de dados Oracle no local, tem de instalar os seguintes componentes: 

- O Data Management Gateway no mesmo computador que aloja a base de dados ou num computador em separado para evitar a competir para recursos com a base de dados. O Data Management Gateway é um agente do cliente que liga as origens de dados no local a serviços em nuvem de forma segura e geridos. Consulte o artigo [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway. 
- Fornecedor de dados Oracle para .NET. Este componente é incluído no [Oracle dados Access componentes para Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão adequada (32/64 bits) no computador anfitrião onde está instalado o gateway. [Oracle dados fornecedor .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) de conseguir aceder à base de dados Oracle 10g lançamento 2 ou posterior.

    Se optar por "XCopy instalação", siga os passos a Readme. Recomendamos que escolha o instalador com IU (que não sejam-XCopy um). 
 
    Depois de instalar o fornecedor, reinicie o serviço anfitrião do Gateway de gestão de dados no seu computador utilizar serviços aplicação (ou) Gestor de configuração de Gateway de gestão de dados.  

> [AZURE.NOTE] Consulte o artigo [resolver problemas de gateway problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para sugestões sobre resolução de problemas de ligação/gateway relacionados com problemas. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados ou para uma base de dados Oracle para qualquer uma dos arquivos de dados suportados sink é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados ou para uma base de dados Oracle para a armazenamento de Blobs do Azure que sejam. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Exemplo: Copiar dados do Oracle para BLOBs do Azure
Este exemplo mostra como copiar dados a partir de uma base de dados do Oracle no local para um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.  Uma [tubagem](data-factory-create-pipelines.md) com atividade de cópia que utiliza o [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como origem e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como sink.

O exemplo copia os dados de uma tabela numa base de dados Oracle no local para um blob por hora. Para mais informações sobre vários propriedades utilizadas na amostra, consulte a documentação nas secções seguir os exemplos.

**Serviço de Oracle ligado:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Armazenamento de Blobs do Azure ligadas serviço:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Conjunto de dados entrado de Oracle:**

O exemplo assume ter criado uma tabela "AMinhaTabela" Oracle e que contém uma coluna denominada "timestampcolumn" para os dados da série de tempo. 

A definição "externos": "true" informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica dados.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
            },
          "policy": {     
               "externalData": {        
                    "retryInterval": "00:01:00",    
                    "retryTimeout": "00:10:00",       
                    "maximumRetry": 3       
                }     
              }
        }
    }


**Conjunto de dados de saída de Blobs do Azure:**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliadas com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.
    
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Gasoduto com atividade copiar:**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendado para ser executado por hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **OracleSource** e **sink** tipo está definido como **BlobSink**.  A consulta SQL especificada com **oracleReaderQuery** propriedade seleciona os dados na última hora para copiar.

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Precisa de ajustar a cadeia de consulta com base na forma como as datas estão configuradas na base de dados Oracle. Se vir a seguinte mensagem de erro: 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

poderá ter alterar a consulta conforme mostrado no exemplo seguinte (utilizando a função to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Exemplo: Copiar dados de Blobs do Azure base de dados Oracle
Este exemplo mostra como copiar dados a partir de um armazenamento de Blobs do Azure para uma base de dados do Oracle no local. No entanto, podem ser copiada **diretamente** a partir de qualquer uma da origens indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
5.  Uma [tubagem](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como origem [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como sink.

O exemplo copia os dados a partir de um blob a uma tabela numa base de dados Oracle no local cada hora. Para mais informações sobre vários propriedades utilizadas na amostra, consulte a documentação nas secções seguir os exemplos.

**Serviço de Oracle ligado:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Armazenamento de Blobs do Azure ligadas serviço:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Conjunto de dados de entrada Blob Azure**

Dados são selecionados para cima a partir de um novo blob cada hora (frequência: hora, o intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliadas com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte da hora da hora de início. "externos": "true" definição informa o serviço de dados fábrica que esta tabela é a fábrica de dados externa e não é produzida por uma actividade na fábrica de dados.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Conjunto de dados do Oracle saída:**

O exemplo assume que ter criado uma tabela "AMinhaTabela" Oracle. Crie a tabela Oracle com o mesmo número de colunas, como esperado o ficheiro CSV de BLOBs conter. Novas linhas são adicionadas à tabela de cada hora.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Gasoduto com atividade copiar:**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **BlobSource** e o tipo de **sink** está definido para **OracleSink**.  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Propriedades do serviço Oracle ligada

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço Oracle ligado. 

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
tipo | A propriedade de tipo tem de estar definida: **OnPremisesOracle** | Sim
connectionString | Especifique as informações necessárias para ligar para a instância de base de dados Oracle para a propriedade connectionString. | Sim 
gatewayName | Nome do gateway que é utilizado para ligar ao servidor Oracle no local | Sim

Para obter detalhes sobre como definir credenciais para uma origem de dados Oracle no local, consulte [definir as credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .
## <a name="oracle-dataset-type-properties"></a>Propriedades do tipo de conjunto de dados Oracle

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Oracle, BLOBs do Azure, tabela do Azure, etc.).
 
A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties para o conjunto de dados do tipo OracleTable tem as seguintes propriedades:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
Tabela | Nome da tabela na base de dados Oracle que se refira o serviço ligado a. | N (se não for especificado **oracleReaderQuery** de **OracleSource** )

## <a name="oracle-copy-activity-type-properties"></a>Propriedades do tipo de atividade de cópia de Oracle

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

> [AZURE.NOTE]
A atividade de cópia assume apenas uma entrada e produz apenas uma saída.

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

### <a name="oraclesource"></a>OracleSource
Numa atividade de cópia, quando a origem for tipo **OracleSource** as seguintes propriedades estão disponíveis na secção **typeProperties** :

Propriedade | Descrição |Valores permitidos | Obrigatório
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utilize a consulta personalizada para ler dados. | Cadeia de consulta SQL. Por exemplo: selecione *from MinhaTabela <br/> <br/>se não for especificado, a instrução SQL que é executada: selecione* from MinhaTabela | N (se a **tabela** de **conjunto de dados** for especificada)

### <a name="oraclesink"></a>OracleSink
**OracleSink** suporta as seguintes propriedades:

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
writeBatchTimeout | Aguarde hora para a operação de inserir batch de concluir antes de tempo limite. | TimeSpan<br/><br/> Exemplo: 00:30:00 (30 minutos). | N
writeBatchSize | Insere os dados na tabela do SQL quando o tamanho da memória intermédia atingir writeBatchSize.   | Número inteiro (número de linhas)| Não (predefinido: 10000)  
sqlWriterCleanupScript | Especificar uma consulta para copiar atividade executar assim que são desorganizados; dados de um setor específico. | Uma instrução de consulta. | N
sliceIdentifierColumnName | Especifique o nome da coluna para copiar atividade preencher com identificador de setor automática gerada, que é utilizado para limpar os dados de um setor específico quando voltar a executar. | Nome da coluna de uma coluna com o tipo de dados do binary(32). | N


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oracle

Tal como mencionado na atividade de cópia de artigo de [atividades de movimento dados](data-factory-data-movement-activities.md) executa conversões de tipo automático a partir de tipos de origens de receber tipos com a seguinte abordagem passo 2:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Ao mover dados do Oracle, são utilizados os mapeamentos seguintes a partir do tipo de dados Oracle tipo .NET e vice versa.

Tipo de dados Oracle | Tipo de dados .NET framework
---------------- | ------------------------
BFICHEIRO | Byte]
BLOB | Byte]
CARÁCT | Cadeia
CLOB | Cadeia
DATA | Data/hora
FLUTUANTE | Decimal
NÚMERO INTEIRO | Decimal
ANO DE INTERVALO PARA O MÊS | Int32
DIA DO INTERVALO SEGUNDO | TimeSpan
LONGO | Cadeia
TEMPO NÃO PROCESSADOS | Byte]
NCHAR | Cadeia
NCLOB | Cadeia
NÚMERO | Decimal
NVARCHAR2 | Cadeia
OBSERVOU | Byte]
ID DA LINHA | Cadeia
DATA/HORA | Data/hora
CARIMBO DE DATA/HORA COM O FUSO HORÁRIO LOCAL | Data/hora
CARIMBO DE DATA/HORA COM O FUSO HORÁRIO | Data/hora
INTEIRO | Número
VARCHAR2 | Cadeia
XML | Cadeia

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

**Problema:** Verá a seguinte **mensagem de erro**: atividade cópia satisfeitas parâmetros inválidos: 'UnknownParameterName', mensagem detalhada: não é possível localizar o pedido .net Framework fornecedor de dados. Poderá não ser instalado".  

**Possíveis causas:**

1. O fornecedor de dados do .NET Framework para Oracle não foi instalado.
2. O fornecedor de dados do .NET Framework para Oracle foi instalado para .NET Framework 2.0 e não se encontra nas pastas .NET Framework 4.0. 

**Resolução/solução:**

1. Se ainda não tiver instalado o fornecedor de .NET para Oracle, [instalá-lo](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e repetir o cenário. 
2. Se receber a mensagem de erro persistir após instalar o fornecedor, efetue os seguintes passos: 
    1. Abrir a configuração de máquina de .NET 2.0 a partir da pasta: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Pesquisa para o **Fornecedor de dados Oracle para .NET**e, deverá conseguir encontrar uma entrada, conforme apresentado na unwn seguinte de exemplo no seguinte exemplo under **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  Copie esta entrada ao ficheiro Machine. config na seguinte pasta v 4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e altere a versão para 4.xxx.x.x.
3.  Instalar "< ODP.NET instalados caminho > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" na cache de assemblagem global (GAC) ao executar `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.
