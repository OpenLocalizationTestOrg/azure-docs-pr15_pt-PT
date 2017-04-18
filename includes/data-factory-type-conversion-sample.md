### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo
O exemplo seguinte é para copiar dados a partir de um Blob para Azure SQL com conversões de tipo.

Suponha que o conjunto de dados de Blobs está no formato CSV e contém 3 colunas. Uma delas é uma coluna de data/hora com um formato de data/hora personalizados, utilizando nomes francês abreviados para o dia da semana.

Vai definir o conjunto de dados de origem Blob da seguinte forma juntamente com definições de tipos de para as colunas.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
            "external": true,
            "availability":
            {
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

Tendo em conta o SQL tipo a tabela de mapeamento do tipo de .NET acima iria definir a tabela do Azure SQL com o esquema que se segue.

| Nome da coluna | Tipo de SQL |
| ----------- | -------- |
| ID de utilizador | bigint |
| nome | texto |
| lastlogindate | data/hora |

Seguinte irá definir o conjunto de dados do Azure SQL da seguinte forma. Nota: Não é necessário especificar a secção "estrutura" com informações do tipo de uma vez que o tipo de informações já está especificado no arquivo de dados subjacentes.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

Neste caso a fábrica de dados automaticamente executará o tipo de conversões, incluindo o campo Data/hora com a data/hora personalizado formatar utilizando a cultura f f ao mover dados do Blob para Azure SQL.


