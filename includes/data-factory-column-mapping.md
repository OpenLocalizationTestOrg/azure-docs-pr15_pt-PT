## <a name="column-mapping-with-translator-rules"></a>Mapeamento de colunas com regras de Minitradutor
Mapeamento de coluna pode ser utilizado para especificar como colunas especificado na "estrutura" do mapa de tabela de origem para colunas especificada na "estrutura" da tabela sink. A propriedade **columnMapping** está disponível na secção de **typeProperties** da atividade de cópia.

Mapeamento de colunas suporta os seguinte cenários:

- Todas as colunas na tabela de origem "estrutura" são mapeadas para todas as colunas na tabela sink "estrutura".
- Um subconjunto das colunas na tabela de origem "estrutura" são mapeados para todas as colunas na tabela sink "estrutura".

A seguinte é condições de erro e irá resultar numa exceção:

- Colunas menos ou mais colunas na "estrutura" da tabela sink que especificado no mapeamento.
- Mapeamento de duplicados.
- Resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

## <a name="column-mapping-samples"></a>Exemplos de mapeamento de coluna
> [AZURE.NOTE] Os exemplos abaixo são para Azure SQL e BLOBs do Azure, mas são aplicáveis a qualquer arquivo de dados que suporta retangular conjuntos de dados. Terá de ajustar o conjunto de dados e as definições de serviço ligadas nos exemplos abaixo para apontarem para os dados na origem de dados relevantes. 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemplo 1 – coluna mapear a partir do Azure SQL para BLOBs do Azure
Neste exemplo, a tabela de entrada tem uma estrutura e aponte para uma tabela numa base de dados Azure SQL SQL.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
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

Neste exemplo, a tabela de resultados tem uma estrutura e aponte para um blob de um armazenamento de Blobs do Azure.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

JSON para a atividade é apresentado abaixo. As colunas de origem mapeados para colunas sink (**columnMappings**) ao utilizar o **Minitradutor** propriedade.

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Fluxo de mapeamento de coluna:**

![Fluxo de mapeamento de coluna](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemplo 2 – coluna mapeamento com a consulta SQL a partir do Azure SQL para BLOBs do Azure
Neste exemplo, uma consulta SQL é utilizada para extrair dados de Azure SQL em vez de simplesmente especificando o nome da tabela e os nomes das colunas na secção "estrutura". 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

Neste caso, os resultados da consulta primeiro são mapeados para colunas especificadas nas "estrutura" da origem. Em seguida, as colunas de origem "estrutura" são mapeadas para colunas sink "estrutura" com regras especificadas no columnMappings.  Suponha que a consulta devolve 5 colunas, duas colunas adicionais, em seguida, os especificados na "estrutura" da origem.

**Fluxo de mapeamento de coluna**

![Mapeamento de coluna fluxo-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)







