<properties
    pageTitle="Agendamento e execução com dados fábrica | Microsoft Azure"
    description="Saiba aspetos agendamento e execução de modelo de aplicação do Azure fábrica de dados."
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
    ms.date="08/22/2016"
    ms.author="spelluru"/>

# <a name="data-factory-scheduling-and-execution"></a>Agendamento de fábrica do mesmo de dados e execução
Este artigo explica os agendamento e execução aspetos do modelo de aplicação Azure fábrica de dados. 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que compreender Noções básicas dos conceitos de modelo de aplicação fábrica de dados, incluindo atividade, tubagens, serviços ligados e conjuntos de dados. Para obter conceitos básicos do Azure fábrica de dados, consulte os seguintes artigos:

- [Introdução a fábrica de dados](data-factory-introduction.md)
- [Tubagens](data-factory-create-pipelines.md)
- [Conjuntos de dados](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>Agendar uma actividade

Com a secção scheduler da atividade JSON, pode especificar uma agenda periódica para uma atividade. Por exemplo, pode agendar uma actividade de cada hora da seguinte forma:

    "scheduler": {
        "frequency": "Hour",
        "interval": 1
    },  

![Exemplo de programador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Como é mostrado no diagrama, especificar uma agenda para a atividade cria uma série de tumbling windows. Tumbling windows são uma série de intervalos de tempo de tamanho fixo, não sobrepostos, contíguas. Estas janelas tumbling lógicos da atividade de chamam- *windows atividade*.

Para a janela de atividade atualmente execução, pode aceder ao intervalo de tempo associado a janela de atividade com [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) e [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) variáveis do sistema na atividade de JSON. Pode utilizar estas variáveis para fins diferentes na sua atividade JSON. Por exemplo, pode utilizá-los para selecionar dados de entrada e saídos conjuntos de dados que representa os dados da série de tempo.

A propriedade **Agendador** suporta as mesmas sub propriedades como a propriedade de **disponibilidade** num conjunto de dados. Ver a [disponibilidade de conjunto de dados](data-factory-create-datasets.md#Availability) para obter detalhes. Exemplos: agendar um desvio de tempo específico ou definir o modo para alinhar o processamento no início ou fim do intervalo para a janela de atividade.

Pode especificar propriedades do **Programador** para uma atividade, mas esta propriedade é **opcional**. Se especificar uma propriedade, tem de corresponder cadence que especificou na definição do conjunto de dados de saída. Atualmente, o conjunto de dados de saída é o que unidades agenda, para que o mesmo se a atividade não produz qualquer saída, tem de criar um conjunto de dados de saída. Se a atividade não tomar qualquer entrada, também pode ignorar a criar o conjunto de dados de entrada.

## <a name="time-series-datasets-and-data-slices"></a>Ciclos de dados e conjuntos de dados de série de tempo

Dados da série de tempo são uma sequência contínua de pontos de dados que normalmente consiste em medições sucessivas estabelecidas através de um intervalo de tempo. Exemplos comuns de dados da série de tempo incluem sensor dados e os dados de telemetria de aplicação.

Com a fábrica de dados, pode processar tempo de execução de dados da série de uma forma por lotes com atividade. Normalmente, é uma cadence periódica às quais os dados de entrada chegam e precisa de ser produzidos dados de saída. Este cadence é modelado ao especificar **disponibilidade** no conjunto de dados da seguinte forma:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Cada unidade de dados consumidas e produzidos pela executar atividade chama um setor de dados. O diagrama seguinte mostra um exemplo de uma atividade com um conjunto de dados de entrada e saída de um conjunto de dados. Estes conjuntos de dados tem **disponibilidade** definido como uma preço por hora frequência.

![Programador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama anterior mostra os preço por hora dados os setores do gráfico para o conjunto de dados de entrada e saído. O diagrama mostra três entradas os setores do gráfico que estão prontos para processamento. A atividade de 10-11 AM está em curso, que produza no setor de saída de 10-11 AM.

Pode aceder ao intervalo de tempo associado no setor atual endereçados produzido no conjunto de dados JSON com variáveis [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Atualmente, a fábrica de dados requer que a agenda especificada na atividade de exatamente corresponde a agenda especificada na **disponibilidade** do conjunto de dados de saída. Por conseguinte, **WindowStart**, **WindowEnd**, **SliceStart**e **SliceEnd** sempre mapear para o mesmo período de tempo e um setor individual de saída.

Para mais informações sobre as propriedades diferentes disponíveis para a secção de disponibilidade, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md).

## <a name="move-data-from-sql-database-to-blob-storage"></a>Mover os dados a partir da base de dados SQL ao armazenamento Blob

Vamos colocar algumas coisas em conjunto e em ação através da criação de uma tubagem que copia os dados de uma tabela de base de dados do Azure SQL ao armazenamento de Blobs do Azure cada hora.

**Entrada: Conjunto de dados de base de dados SQL Azure**

    {
        "name": "AzureSqlInput",
        "properties": {
            "published": false,
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
            "policy": {}
        }
    }


**Frequência** é definido para **hora** e o **intervalo** está definido para **1** na secção disponibilidade.

**Saída: Conjunto de dados de armazenamento de Blobs do Azure**

    {
        "name": "AzureBlobOutput",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
                "format": {
                    "type": "TextFormat"
                },
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
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Frequência** é definido para **hora** e o **intervalo** está definido para **1** na secção disponibilidade.



**Atividade: Atividade de cópia**

    {
        "name": "SamplePipeline",
        "properties": {
            "description": "copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 100000,
                            "writeBatchTimeout": "00:05:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureSQLInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            ],
            "start": "2015-01-01T08:00:00Z",
            "end": "2015-01-01T11:00:00Z"
        }
    }


O exemplo mostra a agenda de atividade e secções de disponibilidade do conjunto de dados definido como uma preço por hora frequência. O exemplo mostra como pode utilizar **WindowStart** e **WindowEnd** para selecionar dados relevantes para uma actividade de executar e copiá-lo para um blob com o **caminhopasta**adequado. O **caminhopasta** é parametrizadas de ter uma pasta separada para cada hora.

Quando executar três dos setores entre 8 – devolve 11 AM, os dados na base de dados do SQL Azure são da seguinte forma:

![Entrada de exemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Depois de ser implementada as em curso, de Blobs do Azure são preenchida da seguinte forma:

-   Ficheiro de 2015/mypath/1/8/1/dados. &lt;Guid&gt;. txt com dados

            10002345,334,2,2015-01-01 08:24:00.3130000
            10002345,347,15,2015-01-01 08:24:00.6570000
            10991568,2,7,2015-01-01 08:56:34.5300000

    > [AZURE.NOTE] &lt;GUID&gt; é substituído por um guid real. Exemplo de nome de ficheiro: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
-   Ficheiro de 2015/mypath/1/9/1/dados. &lt;Guid&gt;. txt com dados:

            10002345,334,1,2015-01-01 09:13:00.3900000
            24379245,569,23,2015-01-01 09:25:00.3130000
            16777799,21,115,2015-01-01 09:47:34.3130000
-   Ficheiro de 2015/mypath/1/10/1/dados. &lt;Guid&gt;. txt sem dados.


## <a name="active-period-for-pipeline"></a>Período ativo para em curso

[Criar tubagens](data-factory-create-pipelines.md) introduzida o conceito de um período ativo para uma tubagem especificado ao definir as propriedades de **início** e de **fim** .

Pode definir a data de início para o período de ativos de tubagem no passado. Dados fábrica automaticamente calcula (preenchimentos Retroceder) todos os dados os setores do gráfico no passado e começa a processá-los.

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo de dados os setores do gráfico
Pode configurar os dados de back preenchido os setores do gráfico a ser executado em paralelo, definindo a propriedade de **simultaneidade** na secção de política da atividade JSON. Para mais informações sobre esta propriedade, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md).

## <a name="rerun-a-failed-data-slice"></a>Volte a executar o setor uma falha de dados 
Pode monitorizar execução dos setores do gráfico de uma forma visual e avançada. Consulte o artigo [monitorização e gestão de tubagens utilizando Azure pás portais](data-factory-monitor-manage-pipelines.md) ou [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) para obter detalhes.

Considere o seguinte exemplo, que mostra duas atividades. Activity1 gera um conjunto de dados de série de tempo com os setores do gráfico, como saída consumida como entrada pelos Activity2 para produzir o conjunto de dados de série de tempo saída final.

![Falhado do setor](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que terminar três recentes os setores do gráfico, Ocorreu uma falha que produza no setor AM 9-10 para Dataset2. Dados fábrica controla automaticamente dependência para o conjunto de dados de série de tempo. Como resultado, não é iniciado a atividade executar para no setor descendentes 9-10 AM.

Ferramentas de monitorização e gestão de dados fábrica permitem-lhe explorar os registos de diagnóstico no setor falhado encontrar a causa de raiz para o problema e corrigi-lo facilmente. Depois de ter resolvi o problema, pode facilmente iniciar a atividade executar para produzir no setor falhado. Para obter mais detalhes sobre como executar novamente e compreender as transições de estado para dados os setores do gráfico, consulte o artigo [monitorização e gestão de tubagens utilizando Azure pás portais](data-factory-monitor-manage-pipelines.md) ou [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md).

Depois de executar novamente no setor 9-10 AM para **Dataset2**, dados fábrica inicia a executar no setor dependentes 9-10 AM no conjunto de dados final.

![Voltar a executar falha setor](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>Executar actividades numa sequência
Pode corrente duas atividades (executar uma actividade após o outro) ao definir o conjunto de dados de saída de uma atividade de como o conjunto de dados de entrada da atividade de outros da. As atividades podem ser no pipeline de mesmo ou no tubagens diferentes. A segunda atividade executa apenas quando a primeira parte é concluída com êxito.

Por exemplo, considere as maiúsculas/minúsculas seguintes:

1.  Em curso P1 tem A1 atividade que requer dataset de teclado externo D1 e produz o conjunto de dados de saída D2.
2.  Em curso P2 tem A2 de atividade que necessita de intervenção do conjunto de dados D2 e produz o conjunto de dados de saída D3.

Neste cenário, atividades A1 e A2 estão tubagens diferentes. A atividade é executado A1 quando os dados externos estão disponíveis e é atingida a frequência de disponibilidade agendada. A atividade A2 é executada quando os setores agendados a partir do D2 ficam disponíveis e a frequência de disponibilidade agendada é atingida. Se existir um erro de uma dos setores no conjunto de dados D2, A2 não são executadas para esse setor até se transformar disponível.

A vista de diagrama teria o seguinte o diagrama seguinte aspeto:

![Interligação de atividades no duas tubagens](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Como mencionado anteriormente, podem ser as atividades no pipeline de mesmo. A vista de diagrama com ambas as atividades no pipeline de mesmo teria o seguinte o diagrama seguinte aspeto:

![Interligação de atividades no pipeline de mesmo](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>Copiar sequencialmente
É possível executar várias operações de copiar um após o outro de forma sequencial/ordenada. Por exemplo, poderá ter duas atividades de cópia no pipeline (CopyActivity1 e CopyActivity2) com os conjuntos de dados de saída de dados de entrada seguintes:   

CopyActivity1

Introdução: conjunto de dados. Resultado: Dataset2.

CopyActivity2

Introdução: Dataset2.  Resultado: Dataset3.

CopyActivity2 seria executado apenas se o CopyActivity1 tem de executar com êxito e Dataset2 está disponível.

Eis pipeline de exemplo JSON:

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob1ToBlob2",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset3"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob2ToBlob3",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2016-08-25T01:00:00Z",
            "end": "2016-08-25T01:00:00Z",
            "isPaused": false
        }
    }

Tenha em atenção que no exemplo, o conjunto de dados de saída à primeira atividade de cópia (Dataset2) está especificado como entrada para a segunda atividade. Por conseguinte, a segunda atividade é executado apenas quando estiver pronto o conjunto de dados de saída à primeira atividade.  

No exemplo, CopyActivity2 pode ter uma entrada diferentes, tais como Dataset3, mas especificar Dataset2 como uma entrada a CopyActivity2, para que a atividade não são executadas até CopyActivity1 estar concluída. Por exemplo:

CopyActivity1

Introdução: Dataset1. Resultado: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Resultado: Dataset4.

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlobToBlob",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset3"
                        },
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset4"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob3ToBlob4",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2017-04-25T01:00:00Z",
            "end": "2017-04-25T01:00:00Z",
            "isPaused": false
        }
    }


Tenha em atenção que no exemplo, os dois conjuntos de dados de entrada estão especificados para a segunda actividade de cópia. Quando são especificadas múltiplas entradas, apenas o primeira entrado conjunto de dados é utilizado para copiar os dados, mas outras conjuntos de dados são utilizados como dependências. CopyActivity2 seria iniciado apenas depois de forem cumpridas as seguintes condições:

- CopyActivity1 foi concluída com êxito e Dataset2 está disponível. Este conjunto de dados não é utilizado ao copiar dados para Dataset4. Apenas funciona como a dependência de agendamento para CopyActivity2.   
- Dataset3 está disponível. Este conjunto de dados representa os dados que são copiados para o destino.  



## <a name="model-datasets-with-different-frequencies"></a>Modelo conjuntos de dados com frequência diferente

As amostras, frequência para conjuntos de dados de entrada e saídos e a janela de agenda de atividade foram da mesma. Alguns cenários de requerem a capacidade para produzir saída com uma diferente a frequência de uma ou mais entradas do tipo de frequência. Dados fábrica suporta estes cenários de modelação.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exemplo 1: Produzir um relatório de saída diária para dados de entrada que está disponíveis a cada hora

Considere a hipótese de um cenário na qual que têm de entrada dos dados de medição de sensores disponíveis cada hora no armazenamento de Blobs do Azure. Pretende produzir um relatório de agregação diário com as estatísticas como média, máximo e mínimo para o dia com [dados fábrica ramo de registo de atividade](data-factory-hive-activity.md).

Eis como pode modelar este cenário com fábrica de dados:

**Conjunto de dados de entrada**

Os ficheiros de entrada de hora a hora são ignorados na pasta para o dia específico. Disponibilidade introduções estiver definida **hora** (frequência: hora, o intervalo: 1).

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Conjunto de dados de saída**

Um ficheiro de exportação é criado diariamente na pasta do dia. Disponibilidade de saída estiver definida **dia** (frequência: dia e intervalo: 1).


    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Atividade: atividade no pipeline de um ramo**

O script ramo recebe as informações de *data/hora* adequadas, como parâmetros que utilizam a variável de **WindowStart** , conforme mostrado no seguinte fragmento. O script ramo utiliza esta variável para carregar os dados a partir da pasta correta para o dia e executar a agregação para gerar o resultado.

        {  
            "name":"SamplePipeline",
            "properties":{  
            "start":"2015-01-01T08:00:00",
            "end":"2015-01-01T11:00:00",
            "description":"hive activity",
            "activities": [
                {
                    "name": "SampleHiveActivity",
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adftutorial\\hivequery.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                            "Month": "$$Text.Format('{0:%M}',WindowStart)",
                            "Day": "$$Text.Format('{0:%d}',WindowStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },          
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 2,
                        "timeout": "01:00:00"
                    }
                 }
             ]
           }
        }

O diagrama seguinte mostra o cenário a partir de um ponto de dados dependência de vista.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

No setor de saída para cada dia depende 24 setores hora a hora a partir de um conjunto de dados de entrada. Dados fábrica fórmula calcula estas dependências automaticamente pelo perceber os dados de entrada os setores do gráfico que se encontrem no mesmo período de tempo no setor de saída para ser apresentado. Se qualquer um dos setores entrados 24 não estiver disponível, espera fábrica de dados para o entrada setor para pronta a ser antes de iniciar a atividade diária executar.


### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exemplo 2: Especifique dependência com expressões e funções de fábrica de dados

Vamos considere outro cenário. Suponha que tem uma atividade de ramo processa dois conjuntos de dados de entrada. Uma delas tem novos dados diariamente, mas uma delas obtém dados novos todas as semanas. Suponha que pretendia efetuar uma associação ao longo de duas entradas e produzir um resultado diariamente.

A abordagem simple no qual fábrica dados automaticamente ilustrações saída direita entrada os setores do gráfico para processar por alinhar para a saída de tempo do setor dados período não funciona.

Tem de especificar que para cada actividade executar, a fábrica de dados deverá utilizar setor de dados de semana passada para o conjunto de dados de entrada semanal. Utilizar funções de fábrica do Azure dados conforme mostrado no seguinte fragmento para implementar o este comportamento.

**Input1: BLOBs do Azure**

A primeira entrada é de Blobs do Azure ser actualizado diariamente.

    {
      "name": "AzureBlobInputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Input2: BLOBs do Azure**

Input2 é de Blobs do Azure ser actualizado semanais.

    {
      "name": "AzureBlobInputWeekly",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 7
        }
      }
    }

**Resultado: BLOBs do Azure**

Um ficheiro de exportação é criado diariamente na pasta para o dia. Disponibilidade de saída está definida para o **dia** (frequência: dia, o intervalo: 1).

    {
      "name": "AzureBlobOutputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Atividade: atividade no pipeline de um ramo**

A atividade de ramo leva-o até duas entradas e produz um setor saída diariamente. Pode especificar setor de saída de todos os dias para dependem setor de entrada da semana anterior introduções semanal da seguinte forma.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-01-01T08:00:00",
        "end":"2015-01-01T11:00:00",
        "description":"hive activity",
        "activities": [
          {
            "name": "SampleHiveActivity",
            "inputs": [
              {
                "name": "AzureBlobInputDaily"
              },
              {
                "name": "AzureBlobInputWeekly",
                "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
                "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutputDaily"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
              "scriptPath": "adftutorial\\hivequery.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                "Month": "$$Text.Format('{0:%M}',WindowStart)",
                "Day": "$$Text.Format('{0:%d}',WindowStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            },          
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 2,  
              "timeout": "01:00:00"
            }
           }
         ]
       }
    }


## <a name="data-factory-functions-and-system-variables"></a>Funções de dados da fábrica e as variáveis do sistema   

Consulte [funções de dados fábrica e as variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções e variáveis de sistema que suporte a fábrica de dados.

## <a name="data-dependency-deep-dive"></a>Vôo abrangente picado dependência de dados

Para gerar um setor do conjunto de dados ao executar atividade, dados fábrica utiliza o *modelo de dependência* de seguinte para determinar as relações entre conjuntos de dados consumidas e produzidos por uma actividade.

O intervalo de tempo dos entrada conjuntos de dados necessários para gerar no setor do conjunto de dados de saída chama-se o *período de dependência*.

Executar uma atividade gera um setor do conjunto de dados, apenas depois dos setores de dados no teclado conjuntos de dados dentro do período de dependência estão disponíveis. Por outras palavras, todos os setores entrados que inclua o período de dependência devem estar no estado **pronto** para a atividade executar para produzir um setor do conjunto de dados de saída.

Para gerar no setor do conjunto de dados [**início**, **fim**], uma função tem de mapear no setor do conjunto de dados para o período de dependência. Esta função é, essencialmente, uma fórmula que converte o início e fim no setor do conjunto de dados para o início e de fim do período de dependência. Mais formal:

    DatasetSlice = [start, end]
    DependecyPeriod = [f(start, end), g(start, end)]

**F** e **g** são o mapeamento de funções que calculam a início e de fim do período de dependência para cada actividade de entrada.

Conforme visto no amostras, o período de dependência é o mesmo que o período de no setor de dados é produzido. Nestes casos, a fábrica de dados automaticamente calcula os setores entrados que se encontrem no período de tempo dependência.  

Por exemplo, a amostra de agregação onde saída é produzida diariamente e dados de entrada estão disponíveis cada hora, o período de setor de dados é 24 horas. Dados fábrica localiza a entrada de hora a hora relevante os setores do gráfico para este período de tempo e faz com que o setor saída dependente no setor entrado.

Também pode fornecer o seu próprio mapeamento para o período de dependência conforme mostrado no exemplo, onde é uma das entradas semanal e o setor de saída é produzido diariamente.

## <a name="data-dependency-and-validation"></a>Dependência de dados e validação

Um conjunto de dados pode ter definida uma política de validação que especifica como os dados gerados pelo execução de um setor podem ser validados face antes de está pronto para consumo. Consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) para obter detalhes.

Nestes casos, após no setor já terminou execução, o estado do setor de saída é alterado para **a aguardar** com um subestado de **validação**. Depois dos setores são validados, o estado de setor muda para **pronto**.

Se um setor dados tenha sido produzida, mas não passou a validação, será executado de atividade para setores descendentes que dependem este setor não é processados.

[Monitor e gerir tubagens](data-factory-monitor-manage-pipelines.md) abrange os vários Estados de dados os setores do gráfico na fábrica de dados.

## <a name="external-data"></a>Dados externos

Um conjunto de dados pode ser marcado como externas (como mostrado no seguinte fragmento JSON), conferir que não foi gerado com dados de fábrica do mesmo. Neste caso, a política de conjunto de dados pode ter um conjunto adicional de parâmetros descrever validação e volte a tentar política para o conjunto de dados. Consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) para obter uma descrição de todas as propriedades.

Semelhante ao conjuntos de dados que são produzidos pela fábrica de dados, os setores de dados para os dados externos precisam de ser preparado antes de ser processados setores dependentes.

    {
        "name": "AzureSqlInput",
        "properties":
        {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties":
            {
                "tableName": "MyTable"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1     
            },
            "external": true,
            "policy":
            {
                "externalData":
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }  
        }
    }


## <a name="onetime-pipeline"></a>Em curso vez
Pode criar e agendar uma tubagem para executar periodicamente (por exemplo: por hora ou diariamente) dentro as horas de início e de fim que especificar na definição em curso. Consulte o artigo [atividades de agendamento](#scheduling-and-execution) para obter detalhes. Também pode criar uma tubagem que é executada apenas uma vez. Para fazê-lo, defina a propriedade **pipelineMode** na definição do pipeline para **vez** conforme mostrado no seguinte exemplo JSON. O valor predefinido para esta propriedade é **agendada**.

    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ]
                    "name": "CopyActivity-0"
                }
            ]
            "pipelineMode": "OneTime"
        }
    }

Tenha em atenção o seguinte procedimento:

- Horas de **início** e de **fim** para pipeline de não estão especificadas.
- **Disponibilidade** de conjuntos de dados de entrada e saídas é especificada (**frequência** e **intervalo**), apesar da fábrica de dados não utiliza os valores.  
- Vista de diagrama não apresentar tubagens únicas. Este comportamento é.
- Tubagens únicas não podem ser atualizadas. Pode clonar uma única tubagem, mude o nome, a atualizar as propriedades e implementá-lo para criar outra.
