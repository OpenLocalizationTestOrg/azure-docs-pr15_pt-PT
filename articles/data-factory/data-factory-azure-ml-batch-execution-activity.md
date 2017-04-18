<properties 
    pageTitle="Utilizar as atividades de máquina Learning | Microsoft Azure" 
    description="Descreve como criar criar tubagens aspeto do Office utilizando a fábrica de dados do Azure e aprendizagem de máquina do Azure" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Criar o aspeto do Office tubagens utilizando actividades de aprendizagem de máquina do Azure   
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introdução

[Formação do Azure máquina](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe construir, testar e implementar soluções de análise de aspeto do Office. A partir de um ponto de vista alto nível, é feito em três passos: 

1. **Criar uma experiência de formação**. Efetuar este passo utilizando o Studio ML Azure. O studio ML é um ambiente de colaboração de programação visual que utilizar para dar formação e testar a um modelo de análise de aspeto do Office com dados de formação.
2. **Convertê-la para uma experiência de aspeto do Office**. Assim que o seu modelo tem foi preparado com os dados existentes e estiver pronto para utilizá-la para novos dados de pontuação, prepare e simplificar a sua experiência de pontuação.
3. **Implementá-lo como um serviço web**. Pode publicar a sua experiência pontuação como um serviço Azure web. Pode enviar dados para o seu modelo através deste ponto de final de serviço web e receber previsões resultado d o modelo.  

Azure fábrica de dados permite-lhe criar facilmente tubagens que utilizam um publicados [Azure máquina aprendizagem] [ azure-machine-learning] serviço para análise aspeto do Office web. Consulte os artigos [Introdução ao Azure dados fábrica](data-factory-introduction.md) e [crie a sua primeira pipeline](data-factory-build-your-first-pipeline.md) rapidamente começar com o serviço de fábrica do Azure dados. 

Utilizar a **Atividade de execução do lote** no pipeline fábrica de dados do Azure, pode invocar um serviço web do Azure ML para efetuar previsões nos dados no lote. Consulte a secção [invocar um ML Azure serviço utilizando a atividade de execução do lote web](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) para obter detalhes.

Ao longo do tempo, os modelos de aspeto do Office de ML Azure pontuação experiências tem de ser retrained utilizando novos conjuntos de dados de entrada. Pode Reciclagem um modelo de Azure ML a partir de uma tubagem fábrica de dados executando os seguintes passos: 

1. Publica a experiência de formação (experiência de aspeto do Office não) como um serviço web. O que fazer este passo na Studio ML Azure conforme fez para expor experiência aspeto do Office como um serviço web no cenário anterior.
2. Utilize a atividade de execução do Azure ML lote para invocar o serviço web para a experiência de formação. Basicamente, pode utilizar a atividade de execução do Azure ML lote para invocar serviço web de formação e pontuação serviço web. 
  
Depois de terminar com a Reciclagem, que pretende atualizar o serviço web pontuação (experiência de aspeto do Office exposta como um serviço web) com o modelo recentemente qualificado. Eis os passos: 

1. Adicione um ponto final de não predefinida para o serviço web pontuação. O ponto final predefinido do serviço web não pode ser atualizado, pelo que necessita criar um ponto final não predefinida através do portal Azure. Consulte o artigo [Criar os pontos finais](../machine-learning/machine-learning-create-endpoint.md) para informações conceptuais e procedimentos passos.
2. Atualize os serviços de ML Azure ligadas existentes para pontuação para utilizar o ponto final não predefinida. Começar a utilizar o novo ponto final de utilizar o serviço web que é atualizado.
3. Utilize a **Atividade de recurso atualizar do Azure ML** para atualizar o serviço web com o modelo recentemente qualificado.  

Consulte a secção de [modelos de atualizar o Azure ML utilizando a atividade de recursos de atualização](#updating-azure-ml-models-using-the-update-resource-activity) para obter detalhes. 

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar um serviço web utilizando o lote de execução de atividade

Utilize Azure dados fábrica para orquestrar movimento de dados e processamento de e, em seguida, efetue execução lote utilizando Azure máquina aprendizagem. Eis os passos de nível superiores:

1. Crie um serviço de formação de máquina Azure ligadas. Precisa do seguinte:
    1. **URI do pedido** da execução do lote API. Pode encontrar o URI pedir ao clicar na ligação de **Execução de lote** na página de serviços web.
    1. **Tecla de API** para o serviço de web publicado Azure máquina aprendizagem. Pode localizar a chave de API ao clicar no serviço web que tenha publicado. 
 2. Utilize a atividade de **AzureMLBatchExecution** .

    ![Dashboard de aprendizagem automática](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

    ![Lote URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experimentação utilizando Web serviço entradas/saídas referentes aos dados no armazenamento de Blobs do Azure
Neste cenário, o serviço Web de aprendizagem do Azure máquina torna previsões utilizando dados de um ficheiro num armazenamento de Blobs do Azure e armazena os resultados de previsão no armazenamento de Blobs. A seguinte JSON define uma tubagem fábrica de dados com uma atividade de AzureMLBatchExecution. A atividade tem o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como o resultado. O **DecisionTreeInputBlob** é transmitido como uma entrada ao serviço web utilizando a propriedade JSON **webServiceInput** . O **DecisionTreeResultBlob** é transmitido como uma saída para o serviço Web utilizando a propriedade JSON **webServiceOutputs** .  

> [AZURE.IMPORTANT] 
> Se o serviço web demora múltiplas entradas, utilize a propriedade **webServiceInputs** em vez de utilizar **webServiceInput**. Consulte a secção [múltiplas entradas requer o serviço Web](#web-service-requires-multiple-inputs) para um exemplo de utilizando a propriedade webServiceInputs.
>  
> Conjuntos de dados que são referenciados pela **webServiceInput**/propriedades**webServiceInputs** e **webServiceOutputs** (na **typeProperties**) também devem ser incluídas na atividade **introduções** e **exporta**.
> 
> Na sua experiência do Azure ML, web serviço entrada e saída portas e protocolos parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e definições de globalParameters tem de corresponder exatamente os nomes nas experiências. Pode ver a carga de útil de pedido de exemplo na página de ajuda do lote execução para o ponto final Azure ML verificar o mapeamento do esperado. 


    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "DecisionTreeInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "DecisionTreeResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob"
                }                
            },
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

> [AZURE.NOTE] Apenas entradas e saídas da atividade AzureMLBatchExecution podem ser transmitidas como parâmetros para o serviço Web. Por exemplo, o fragmento de JSON acima, DecisionTreeInputBlob é uma entrada para a atividade de AzureMLBatchExecution, que lhe é transmitida como uma entrada para o serviço Web através do parâmetro webServiceInput.   

### <a name="example"></a>Exemplo

Este exemplo utiliza armazenamento do Windows Azure para armazenarem dados entrados e saídos. 

Recomendamos que, acede através do [construir a sua primeira pipeline com dados fábrica] [ adf-build-1st-pipeline] tutorial antes de passar neste exemplo. Utilize o Editor de fábrica do mesmo de dados para criar artefactos fábrica de dados (serviços ligados, conjuntos de dados, pipeline) neste exemplo.   
 

1. Crie um **serviço ligadas** para o **Armazenamento do Windows Azure**. Se os ficheiros de entrada e saídos estiverem nas contas de armazenamento diferente, terá de dois serviços ligados. Eis um exemplo JSON:

        {
          "name": "StorageLinkedService",
          "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
            }
          }
        }

2. Crie a **entrada** Azure dados fábrica do mesmo **conjunto de dados**. Ao contrário algumas outras dados fábrica conjuntos de dados, estes conjuntos de dados tem de conter valores **caminhopasta** e **nome de ficheiro** . Pode utilizar a partições para fazer com que cada lote a execução (cada setor dados) para processar ou produzir exclusiva entrada e saída de ficheiros. Poderá precisar de incluir algumas montante atividade para transformar a entrada de dados num formato de ficheiro CSV e colocá-la a conta de armazenamento para cada setor. Nesse caso, deverá não incluir as definições de **externos** e **externalData** mostradas no exemplo seguinte e seu DecisionTreeInputBlob seria o conjunto de dados de saída de uma atividade diferente.

        {
          "name": "DecisionTreeInputBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/input",
              "fileName": "in.csv",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Day",
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
    
    O ficheiro csv entrada tem de ter a linha de cabeçalho de coluna. Se estiver a utilizar a **Cópia atividade** para criar/mover de csv para o armazenamento de BLOBs, deve definido a propriedade de sink **blobWriterAddHeader** como **true**. Por exemplo:
    
         sink: 
         {
             "type": "BlobSink",     
             "blobWriterAddHeader": true 
         }
     
    Se o ficheiro csv não tiver a linha de cabeçalho, poderá ver a seguinte mensagem de erro: **erro na atividade: erro cadeia de leitura. Token inesperado: StartObject. Caminho ', linha 1, posicione 1**.
3. Crie a **saída** Azure dados fábrica do mesmo **conjunto de dados**. Este exemplo utiliza a partições para criar um caminho de saída único para cada execução do setor. Sem a criação de partições, a atividade seria substituir o ficheiro.

        {
          "name": "DecisionTreeResultBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/scored/{folderpart}/",
              "fileName": "{filepart}result.csv",
              "partitionedBy": [
                {
                  "name": "folderpart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "yyyyMMdd"
                  }
                },
                {
                  "name": "filepart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HHmmss"
                  }
                }
              ],
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Day",
              "interval": 15
            }
          }
        }

4. Criar um **serviço ligadas** tipo: **AzureMLLinkedService**, fornecendo API chave e lote execução URL do modelo.
        
        {
          "name": "MyAzureMLLinkedService",
          "properties": {
            "type": "AzureML",
            "typeProperties": {
              "mlEndpoint": "https://[batch execution endpoint]/jobs",
              "apiKey": "[apikey]"
            }
          }
        }
5. Por fim, autor uma tubagem contendo uma actividade **AzureMLBatchExecution** . O tempo de execução, pipeline executa os seguintes passos:
    1. Obtém a localização do ficheiro de entrada da sua conjuntos de dados de entrada.
    2. Invocar a execução do lote Azure máquina aprendizagem API
    3. Copia o resultado da execução lote para o blob fornecido no seu conjunto de dados de saída. 

    > [AZURE.NOTE] Atividade de AzureMLBatchExecution pode ter zero ou mais entradas e saídas de uma ou mais.

        {
          "name": "PredictivePipeline",
          "properties": {
            "description": "use AzureML model",
            "activities": [
              {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                  {
                    "name": "DecisionTreeInputBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "DecisionTreeResultBlob"
                  }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                  "concurrency": 3,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 1,
                  "timeout": "02:00:00"
                }
              }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
          }
        }

    Datas e horas de **início** e **fim** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **fim** é opcional. Se não especificar valor da propriedade de **fim** , é calculada como "**Iniciar + 48 horas.**" Para executar a tubagem indefinidamente, especifique a **9999-09-09** como o valor da propriedade de **fim** . Consulte o artigo [Referência de Scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes acerca das propriedades JSON.

    > [AZURE.NOTE] Especificação de entrada para a AzureMLBatchExecution atividade é opcional. 

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Experimentação utilizar Reader/Writer módulos para fazer referência a dados em várias armazenamentos

Outro cenário comum quando criar Azure ML experiências é utilizar o leitor e Writer módulos. Módulo de leitor é utilizado para carregar os dados para uma experiência e o módulo sénior é guardar os dados a partir do seu experiências. Para obter detalhes sobre leitor e sénior módulos, consulte os tópicos de [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [sénior](https://msdn.microsoft.com/library/azure/dn905984.aspx) no MSDN biblioteca.     

Ao utilizar os leitor e writer módulos, é aconselhável utilizar um parâmetro de serviço Web para cada propriedade destes módulos Leitor/gravador. Estes parâmetros web permitem-lhe configurar os valores durante runtime. Por exemplo, pode criar uma experiência com um módulo de leitor que utiliza uma base de dados do SQL Azure: XXX.database.windows.net. Depois de ter sido implementado o serviço web, que pretende ativar os consumidores do serviço web especificar noutro Azure SQL Server denominado YYY.database.windows.net. Pode utilizar um parâmetro de serviço Web para permitir que este valor para ser configurado.

> [AZURE.NOTE] Entrada de serviço Web e de saída são diferentes a partir de parâmetros de serviço Web. O primeiro cenário, viu como uma entrada e saída podem ser especificados para um serviço Web de ML Azure. Neste cenário, passar parâmetros para um serviço Web que correspondem às propriedades dos módulos Leitor/gravador. 

Vamos analisar um cenário para utilizar parâmetros do serviço Web. Tem um serviço de web Azure máquina Learning implementado que utiliza um módulo de leitor para ler os dados a partir de uma das origens de dados suportadas pelo Azure máquina Learning (por exemplo: base de dados do SQL Azure). Após ter sido efetuada a execução do lote, os resultados são escritos utilizando um módulo de sénior (base de dados do SQL Azure).  Sem entradas de serviço de web e saídas são definidas nas experiências. Neste caso, recomendamos que configure parâmetros do serviço web relevantes para os módulos leitor e writer. Esta configuração permite que o reader writer módulos de ser configurado ao utilizar a atividade de AzureMLBatchExecution. Especifique parâmetros do serviço Web na secção **globalParameters** na atividade de JSON da seguinte forma. 


    "typeProperties": {
        "globalParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }

Também pode utilizar [Funções de dados da fábrica](https://msdn.microsoft.com/library/dn835056.aspx) na passagem valores para os parâmetros de serviço Web conforme mostrado no exemplo seguinte:

    "typeProperties": {
        "globalParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Os parâmetros do serviço Web são entre maiúsculas e minúsculas, por isso, certifique-se de que os nomes que especificou na atividade JSON correspondem às exposto pelo serviço Web. 

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Utilizar um módulo de leitor para ler os dados de vários ficheiros de Blobs do Azure
Dados grandes tubagens com atividades como porco e ramo pode produzir um ou mais ficheiros de saída com sem as extensões. Por exemplo, quando especificar uma tabela Hive externa, os dados para a tabela Hive externo podem ser armazenados no armazenamento de Blobs do Azure com as seguintes 000000_0 de nome. Pode utilizar o módulo de leitor numa experiência de vários ficheiros de ler e utilizá-los para previsões. 

Quando utilizar o módulo do leitor uma experiência de aprendizagem do Azure máquina, pode especificar BLOBs do Azure como uma entrada. Os ficheiros no armazenamento de Blobs do Azure podem ser os ficheiros de saída (exemplo: 000000_0) que são produzidos por um script porco e ramo em execução no HDInsight. Módulo de leitor permite-lhe ler ficheiros (com nenhum) ao configurar a **caminho para o contentor, diretório/blob**. Os pontos de **caminho para o contentor** para os pontos de contentor e **diretório/blob** para a pasta que contém os ficheiros, tal como apresentado na seguinte imagem. O asterisco ou seja, \*) **Especifica que todos os ficheiros na pasta/contentor (ou seja, aggregateddata/dados/ano = mês/2014-6 /\*)** são lidos como parte da experiência.

![Propriedades de BLOBs Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### <a name="example"></a>Exemplo 
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Gasoduto com atividade AzureMLBatchExecution com parâmetros de serviço Web

    {
      "name": "MLWithSqlReaderSqlWriter",
      "properties": {
        "description": "Azure ML model with sql azure reader/writer",
        "activities": [
          {
            "name": "MLSqlReaderSqlWriterActivity",
            "type": "AzureMLBatchExecution",
            "description": "test",
            "inputs": [
              {
                "name": "MLSqlInput"
              }
            ],
            "outputs": [
              {
                "name": "MLSqlOutput"
              }
            ],
            "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
                "globalParameters": {
                    "Database server name": "<myserver>.database.windows.net",
                    "Database name": "<database>",
                    "Server user account name": "<user name>",
                    "Server user account password": "<password>"
                }              
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            },
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }
 
No exemplo acima JSON:

- O serviço Web de aprendizagem do Azure máquina implementado utiliza um leitor de e um módulo de sénior para leitura/escrita dados ou para uma base de dados do SQL Azure. Este serviço Web expõe os seguintes quatro parâmetros: nome do servidor, nome da base de dados, nome de conta de utilizador do servidor e palavra-passe do conta de utilizador do servidor de base de dados.  
- Datas e horas de **início** e **fim** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **fim** é opcional. Se não especificar valor da propriedade de **fim** , é calculada como "**Iniciar + 48 horas.**" Para executar a tubagem indefinidamente, especifique a **9999-09-09** como o valor da propriedade de **fim** . Consulte o artigo [Referência de Scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes acerca das propriedades JSON.

### <a name="other-scenarios"></a>Outros cenários

#### <a name="web-service-requires-multiple-inputs"></a>Múltiplas entradas requer o serviço Web
Se o serviço web demora múltiplas entradas, utilize a propriedade **webServiceInputs** em vez de utilizar **webServiceInput**. Conjuntos de dados que são referenciados pela **webServiceInputs** também devem ser incluídos na atividade **entradas do tipo**.
 
Na sua experiência do Azure ML, web serviço entrada e saída portas e protocolos parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e definições de globalParameters tem de corresponder exatamente os nomes nas experiências. Pode ver a carga de útil de pedido de exemplo na página de ajuda do lote execução para o ponto final Azure ML verificar o mapeamento do esperado.


    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [{
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [{
                    "name": "inputDataset1"
                }, {
                    "name": "inputDataset2"
                }],
                "outputs": [{
                    "name": "outputDataset"
                }],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": "inputDataset1",
                        "input2": "inputDataset2"
                    },
                    "webServiceOutputs": {
                        "output1": "outputDataset"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }

#### <a name="web-service-does-not-require-an-input"></a>Serviço Web não requer uma entrada

Azure serviços do ML lote execução web podem ser utilizados para executar fluxos de trabalho, para R ou Python scripts de exemplo, que podem não requerer qualquer entradas do tipo. Em alternativa, pode estar configurada a experiência com um módulo de leitor que não expõe qualquer GlobalParameters. Nesse caso, a atividade de AzureMLBatchExecution seria configurada da seguinte forma:

    {
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### <a name="web-service-does-not-require-an-inputoutput"></a>Serviço Web não requer uma entrada/saída
O serviço de web da execução Azure ML poderá não ter qualquer saída de serviço Web configurada. Neste exemplo, não há serviço Web entrada ou saída, nem estão configurados qualquer GlobalParameters. Ainda existe um resultado configurado na atividade propriamente dito, mas não é dado como um webServiceOutput.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Os leitores de utilizações de serviço Web e escritores e executa a atividade apenas quando outras atividades tem foi concluída com êxito

Os Azure ML web serviço leitor sénior módulos e podem estar configurados para executar com ou sem qualquer GlobalParameters. No entanto, poderá pretende incorporar chamadas de serviço no pipeline de que utiliza dependências de conjunto de dados para o serviço de invocar apenas quando algumas processamento montante concluído. Também pode acionar algumas outras ação depois de concluída a execução do lote utilizar esta abordagem. Nesse caso, pode expressar as dependências utilizando entradas de atividade e saídas, sem atribuir nomes a qualquer uma delas como entradas de serviço Web ou saídas.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "inputs": [
            {
                "name": "upstreamData1"
            },
            {
                "name": "upstreamData2"
            }
        ],
        "outputs": [
            {
                "name": "downstreamData"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

O **takeaways** são:

-   Se a sua experiência endpoint utiliza um webServiceInput:-for representada por um conjunto de dados de BLOBs e está incluída nas entradas de atividade e a propriedade webServiceInput. Caso contrário, a propriedade webServiceInput é omitida. 
-   Se utiliza o seu ponto final experiência webServiceOutput(s): estas são representadas por blob conjuntos de dados e são incluídas no saídas de atividade e na propriedade webServiceOutputs. Exporta a atividade e webServiceOutputs são mapeados pelo nome de cada saída na experiência. Caso contrário, a propriedade webServiceOutputs é omitida.
-   Se o seu ponto final experiência expõe globalParameter(s), são reproduzidas na propriedade de globalParameters atividade como chave, pares valor. Caso contrário, a propriedade globalParameters é omitida. As teclas são entre maiúsculas e minúsculas. [Funções de fábrica do Azure dados](data-factory-scheduling-and-execution.md#data-factory-functions-reference) podem ser utilizados os valores. 
- Conjuntos de dados adicionais poderão ser incluídos nas propriedades de entradas e saídas de atividade, sem a ser referenciadas nas typeProperties a atividade. Estes conjuntos de dados governem execução utilizando setor dependências mas caso contrário, são ignorados pela atividade de AzureMLBatchExecution. 


## <a name="updating-models-using-update-resource-activity"></a>Atualizar Modelos de atividade de recursos de atualização
Ao longo do tempo, os modelos de aspeto do Office de ML Azure pontuação experiências tem de ser retrained utilizando novos conjuntos de dados de entrada. Depois de terminar com a Reciclagem, que pretende atualizar o serviço web pontuação com o modelo de ML retrained. Os passos para ativar readaptação e atualizar os modelos de Azure ML através de serviços web típicos são: 

1. Crie uma experiência no [Azure ML Studio](https://studio.azureml.net). 
2. Quando estiver satisfeito com o modelo, utilize o Azure ML Studio para publicar serviços web para ambas as a **formação experimentar** e pontuação /**experiência aspeto do Office**.

A tabela seguinte descreve os serviços web utilizados neste exemplo.  Para obter mais detalhes, consulte a [formação de máquina de retransmissão modelos através de programação](../machine-learning/machine-learning-retrain-models-programmatically.md) .

| Tipo de serviço web | Descrição 
| :------------------ | :---------- 
| **Serviço web de formação** | Recebe os dados de formação e produz os modelos de formação. O resultado da Reciclagem é um ficheiro de .ilearner um armazenamento de Blobs do Azure.  O **ponto final predefinido** é criado automaticamente quando publicar a experiência de formação como um serviço web. Pode criar mais pontos finais mas o exemplo que utiliza apenas o ponto final predefinido |
| **Serviço web de pontuação** | Recebe exemplos de dados sem nome e torna previsões. O resultado da previsão poderia ter várias formas, tais como um ficheiro. csv ou linhas numa base de dados Azure SQL, consoante a configuração da experiência. O ponto final predefinido é criado automaticamente quando publicar a experiência de aspeto do Office como um serviço web. Crie a segunda **ponto final não predefinida e atualizável** através de do [Azure portal](https://manage.windowsazure.com). Pode criar mais pontos finais mas este exemplo utiliza apenas um não predefinida atualizável ponto final. Consulte o artigo [Criar os pontos finais](../machine-learning/machine-learning-create-endpoint.md) para obter os passos.       
 
A imagem seguinte ilustra a relação entre formação e os pontos finais de pontuação em Azure ML. 

![Serviços Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Pode chamar o **serviço web de formação** , utilizando a **Atividade de execução do Azure ML lote**. Invocar um serviço web de formação é o mesmo que invocar um serviço web de Azure ML (serviço web de pontuação) para dados pontuação. As secções anteriores abrangem como invocar um serviço web do Azure ML de tubagem uma fábrica de dados do Azure detalhadamente. 
  
Pode invocar **pontuação serviço web** utilizando a **Atividade de recurso atualizar do Azure ML** para atualizar o serviço web com o modelo recentemente qualificado. Tal como mencionado na tabela acima, tem de criar e utilizar o ponto final atualizável de não predefinida. Além disso, atualize a quaisquer serviços ligados existentes na sua fábrica de dados para utilizar o ponto final não predefinida para que utilizem o modelo mais recente retrained sempre. 

Cenário que se segue fornece mais detalhes. Tem um exemplo para a Reciclagem e atualizar os modelos de Azure ML de tubagem uma fábrica de dados do Azure. 
 
### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: Reciclagem e atualizar um modelo de Azure ML
Esta secção fornece uma tubagem de exemplo que utiliza a **execução do Azure ML lote atividade** para a Reciclagem de um modelo. Pipeline de também utiliza a **atividade de Azure ML atualizar recurso** para atualizar o modelo no serviço web pontuação. A secção fornece também fragmentos JSON para todos os serviços ligados, conjuntos de dados e em curso no exemplo. 

Eis a vista de diagrama do pipeline de exemplo. Como pode ver, a atividade de execução do Azure ML lote assume a entrada de formação e produz um resultado de formação (iLearner ficheiro). A atividade de recursos de atualização do Azure ML leva-o até esta saída de formação e atualiza o modelo no pontuação web ponto final do serviço. A atividade de recursos de atualização não produz qualquer saída. O placeholderBlob é apenas um resultado fictícios conjunto de dados que é necessário pelo serviço Azure dados fábrica para executar as em curso. 

![Diagrama de tubagem](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### <a name="azure-blob-storage-linked-service"></a>Armazenamento de Blobs do Azure ligadas serviço:
O armazenamento do Windows Azure guarda os seguintes dados:

- dados de formação. Os dados de entrada para o serviço do Azure ML formação web.  
- ficheiro de iLearner. O resultado do serviço web formação Azure ML. Este ficheiro é também a entrada para a atividade de recursos de atualização.  
   
Segue-se a definição de JSON de exemplo do serviço ligada: 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
            }
        }
    }


#### <a name="training-input-dataset"></a>Conjunto de dados entrado de formação:
O conjunto de dados seguinte representa os dados de entrada de formação para o serviço do Azure ML formação web. A atividade de execução do Azure ML lote leva este conjunto de dados como uma entrada. 

    {
        "name": "trainingData",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "labeledexamples",
                "fileName": "labeledexamples.arff",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
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

#### <a name="training-output-dataset"></a>Conjunto de dados de saída formação:
O conjunto de dados seguinte representa o ficheiro de iLearner de saída do serviço web formação Azure ML. A atividade de execução do Azure ML lote produz este conjunto de dados. Este conjunto de dados é também a entrada para a atividade de recursos de atualização do Azure ML.

    {
        "name": "trainedModelBlob",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "trainingoutput",
                "fileName": "model.ilearner",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            }
        }
    }

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço ligadas para o ponto final de formação do Azure ML 
O seguinte fragmento de JSON define um serviço de formação de máquina Azure ligadas que aponta para o ponto final predefinido do serviço web formação. 

    {   
        "name": "trainingEndpoint",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
                "apiKey": "myKey"
            }
        }
    }

No **Azure ML Studio**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique em **Serviços WEB** no menu à esquerda.
2. Clique no **serviço web de formação** na lista de serviços web. 
3. Clique em Copiar junto à caixa de texto de **chave API** . Cole a chave na área de transferência do editor de dados JSON de fábrica do mesmo.
4. No **Azure ML studio**, clique em **Execução do lote de** ligação.
5. Copie o **Pedido URI** a partir da secção **Pedir** e colá-lo no editor de dados JSON de fábrica do mesmo.   


#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço ligadas para o ponto final de pontuação atualizável Azure ML:
O seguinte fragmento de JSON define um serviço de formação de máquina Azure ligadas que aponta para o ponto final atualizável de não predefinida do serviço web pontuação.  

    {
        "name": "updatableScoringEndpoint2",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
                "apiKey": "endpoint2Key",
                "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
            }
        }
    }


Antes de criar e implementar um ML Azure ligado serviço, siga os passos no artigo [Criar os pontos finais](../machine-learning/machine-learning-create-endpoint.md) para criar um segundo (não predefinida e atualizável) ponto final para o serviço web pontuação.

Depois de criar o ponto final atualizável de não predefinida, faça o seguinte:

- Clique em **Execução lote** para obter o valor de URI para a propriedade JSON **mlEndpoint** .
- Clique em **Recurso de actualização de** ligação para obter o valor de URI para a propriedade JSON **updateResourceEndpoint** . A chave de API está na página de ponto final (no canto inferior direito). 

![ponto final atualizável](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### <a name="placeholder-output-dataset"></a>Marcador de posição de saída conjunto de dados:
A atividade de recursos de atualização do Azure ML não gera qualquer saída. No entanto, Azure dados fábrica requer um conjunto de dados de saída para conduzem a agenda de uma tubagem. Por conseguinte, utilizamos um conjunto de dados do marcador de posição/manequim neste exemplo.  

    {
        "name": "placeholderBlob",
        "properties": {
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "any",
                "format": {
                    "type": "TextFormat"
                }
            }
        }
    }


#### <a name="pipeline"></a>Em curso
A tubagem tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução do Azure ML lote coloca os dados de formação como entrada e produz um ficheiro de iLearner como um resultado. A atividade invoca o serviço web de formação (experiência de formação exposta como um serviço web) com os dados de entrada de formação e recebe o ficheiro ilearner do serviçoweb. O placeholderBlob é apenas um resultado fictícios conjunto de dados que é necessário pelo serviço Azure dados fábrica para executar as em curso. 

![Diagrama de tubagem](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


    {
        "name": "pipeline",
        "properties": {
            "activities": [
                {
                    "name": "retraining",
                    "type": "AzureMLBatchExecution",
                    "inputs": [
                        {
                            "name": "trainingData"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "typeProperties": {
                        "webServiceInput": "trainingData",
                        "webServiceOutputs": {
                            "output1": "trainedModelBlob"
                        }              
                     },
                    "linkedServiceName": "trainingEndpoint",
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "02:00:00"
                    }
                },
                {
                    "type": "AzureMLUpdateResource",
                    "typeProperties": {
                        "trainedModelName": "Training Exp for ADF ML [trained model]",
                        "trainedModelDatasetName" :  "trainedModelBlob"
                    },
                    "inputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "placeholderBlob"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "name": "AzureML Update Resource",
                    "linkedServiceName": "updatableScoringEndpoint2"
                }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }


### <a name="reader-and-writer-modules"></a>Leitor e módulos sénior

Um cenário comum para utilizar parâmetros do serviço Web é a utilização de leitores de SQL Azure e escritores. Módulo de leitor é utilizado para carregar os dados para uma experiência de serviços de gestão de dados fora do Azure máquina aprendizagem Studio. O módulo sénior é guardar os dados da sua experiências para serviços de gestão de dados fora do Azure máquina aprendizagem Studio.  

Para obter detalhes sobre Azure Blob/Azure SQL Leitor/gravador, consulte os tópicos de [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [sénior](https://msdn.microsoft.com/library/azure/dn905984.aspx) no MSDN biblioteca. O exemplo na secção anterior utilizado o leitor de Blobs do Azure e sénior de Blobs do Azure. Esta secção explica a utilizar o leitor de Azure SQL e sénior Azure SQL.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q:** Tenho vários ficheiros que são gerados pelo meu tubagens dados grande. Pode utilizar a atividade de AzureMLBatchExecution para trabalhar em todos os ficheiros?

**A:** Sim. Consulte a secção **utilizar um módulo de leitor para ler os dados de vários ficheiros de Blobs do Azure** para obter detalhes. 

## <a name="azure-ml-batch-scoring-activity"></a>Atividade de pontuação Azure ML Batch
Se estiver a utilizar a atividade de **AzureMLBatchScoring** para integrar com o Azure máquina formação, recomendamos que utilize a atividade de **AzureMLBatchExecution** mais recente. 

A atividade de AzureMLBatchExecution é introduzida na versão de Agosto de 2015 do SDK do Azure e Azure PowerShell.

Se quiser continuar a utilizar a atividade de AzureMLBatchScoring, continue a ler através desta secção.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure atividade ML lote pontuação com o armazenamento do Windows Azure para entrada/saída 

    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchScoring",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "ScoringInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "ScoringResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

### <a name="web-service-parameters"></a>Parâmetros de serviço Web
Para especificar valores para os parâmetros de serviço Web, adicione uma secção de **typeProperties** para a secção **AzureMLBatchScoringActivty** no pipeline de JSON conforme mostrado no exemplo seguinte: 

    "typeProperties": {
        "webServiceParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }


Também pode utilizar [Funções de dados da fábrica](https://msdn.microsoft.com/library/dn835056.aspx) na passagem valores para os parâmetros de serviço Web conforme mostrado no exemplo seguinte:

    "typeProperties": {
        "webServiceParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Os parâmetros do serviço Web são entre maiúsculas e minúsculas, por isso, certifique-se de que os nomes que especificou na atividade JSON correspondem às exposto pelo serviço Web. 

## <a name="see-also"></a>Consulte também

- [Mensagem de blogue Azure: introdução ao Azure dados fábrica e Azure máquina Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 
