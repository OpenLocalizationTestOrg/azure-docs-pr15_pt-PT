<properties 
    pageTitle="Criar/agenda tubagens, corrente de bicicleta atividades nos dados fábrica | Microsoft Azure" 
    description="Saiba como criar uma tubagem de dados no Azure dados fábrica para mover e transformar os dados. Crie um fluxo de trabalho de dados condicionada por para produzir pronto para utilizar as informações." 
    keywords="dados em curso, dados condicionada pelo fluxo de trabalho"
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="pipelines-and-activities-in-azure-data-factory"></a>Tubagens e atividades nos dados Azure fábrica
Este artigo ajuda-o a compreender tubagens e atividades no Azure fábrica de dados e utilizá-los para construir ponto a ponto condicionados por dados fluxos de trabalho para o seu movimento de dados e cenários de processamento de dados.  

> [AZURE.NOTE] Este artigo assume que foram trocadas através de [Introdução ao Azure fábrica de dados](data-factory-introduction.md). Se não tiver prático-on-experiência com a criação de dados fábricas, passam pelo [construir a sua primeira fábrica de dados](data-factory-build-your-first-pipeline.md) tutorial seriam ajudar a compreender a este artigo melhor.  

## <a name="what-is-a-data-pipeline"></a>O que é uma tubagem de dados?
**Em curso** é um agrupamento de relacionados logicamente **atividades**. É utilizada para atividades de grupo numa unidade que efetua uma tarefa. Para compreender melhor tubagens, é necessário compreender uma actividade pela primeira vez. 

## <a name="what-is-an-activity"></a>O que é uma atividade?
Atividades de definem as ações para efetuar dos seus dados. Cada actividade leva-o até zero ou mais [conjuntos de dados](data-factory-create-datasets.md) como entradas e produz um ou mais conjuntos de dados como uma saída. 

Por exemplo, pode utilizar uma atividade de cópia para orquestrar copiar dados a partir do arquivo de dados de um arquivo de dados de outra. Da mesma forma, pode utilizar uma atividade de HDInsight Hive para executar uma consulta de ramo num cluster Azure HDInsight para transformar os dados. Azure dados fábrica fornece uma vasta gama de [transformação de dados](data-factory-data-transformation-activities.md)e as atividades de [movimento de dados](data-factory-data-movement-activities.md) . Também poderá optar por criar uma atividade de .NET personalizada para executar o seu próprio código. 

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade de tipo de **cópia** na secção **atividades** . Neste exemplo, [copie atividade](data-factory-data-movement-activities.md) copia os dados a partir de um armazenamento de Blobs do Azure para uma base de dados do Azure SQL. 

    {
      "name": "CopyPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 

Tenha em atenção os seguintes pontos:

- Na secção de atividades, existe apenas uma atividade cujo **tipo** está definido para **Copiar**.
- Modo de entrada para a atividade está definido para **InputDataset** e saída da atividade de está definida para **OutputDataset**.
- Na secção **typeProperties** , **BlobSource** for especificada como o tipo de origem e **SqlSink** for especificada como o tipo de sink.

Para instruções completa da criação de neste curso, consulte o artigo [Tutorial: copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **atividades** . Neste exemplo, a [atividade de HDInsight ramo](data-factory-hive-activity.md) transforma dados a partir de um armazenamento de Blobs do Azure através da execução de um ficheiro de script ramo num cluster Azure HDInsight Hadoop. 

    {
        "name": "TransformPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
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
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }

Tenha em atenção os seguintes pontos: 

- Na secção de atividades, existe apenas uma atividade cujo **tipo** está definido para **HDInsightHive**.
- O ficheiro de script do ramo, **partitionweblogs.hql**, é armazenado na conta de armazenamento Azure (especificado por scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta de **script** no contentor **adfgetstarted**.
- A secção **define** é utilizada para especificar as definições de runtime são transmitidas para o script ramo como valores de configuração do ramo (por exemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

Para instruções completa da criação de neste curso, consulte o artigo [Tutorial: criar a sua primeira pipeline aos dados de processo utilizando Hadoop cluster](data-factory-build-your-first-pipeline.md). 

## <a name="chaining-activities"></a>Interligação de atividades
Se tiver várias atividades numa tubagem e saída de uma atividade de não for uma entrada da outra atividade, as atividades poderão executar paralelamente se os setores do dados de entrada para as atividades estão prontos. 

Pode corrente duas atividades por ter o conjunto de dados de saída de uma atividade, como o conjunto de dados de entrada da atividade de outros da. As atividades podem ser no pipeline de mesmo ou no tubagens diferentes. A segunda atividade executa apenas quando a primeira parte é concluída com êxito. 

Por exemplo, considere as maiúsculas/minúsculas seguintes:
 
1.  Em curso P1 tem A1 atividade que requer dataset de teclado externo D1 e produzir conjunto de dados de **saída** **D2**.
2.  Em curso P2 tem A2 de atividade que necessita de **entrada** do conjunto de dados **D2**e produz o conjunto de dados de saída D3.
 
Neste cenário, a atividade A1 é executada quando os dados externos estão disponíveis e é atingida a frequência de disponibilidade agendada.  A atividade A2 é executada quando os setores agendados a partir do D2 ficam disponíveis e a frequência de disponibilidade agendada é atingida. Se existir um erro de uma dos setores no conjunto de dados D2, A2 não são executadas para esse setor até se transformar disponível.

Vista de diagrama:

![Interligação de atividades no duas tubagens](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Vista de diagrama com ambas as atividades no pipeline de mesmo: 

![Interligação de atividades no pipeline de mesmo](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Para mais informações, consulte [agendar e execução](#chaining-activities). 

## <a name="scheduling-and-execution"></a>Agendamento e execução
Até ao momento ter compreensão o que são tubagens e atividades. Também visualizou como são definidos e alto nível uma vista de atividades no Azure fábrica de dados. Agora diga-nos veja como são executados. 

Uma tubagem está ativa apenas entre o respetivo hora de início e hora de fim. Não é executado antes da hora de início ou após a hora de fim. Se as em curso são colocados em pausa, não obter executada independentemente respectiva hora de início e de fim. Para uma tubagem executar,-deve não ser interrompido. Na verdade, não é pipeline de que obtém executado. É as atividades no pipeline de que obtenham executadas. No entanto estes fazem-lo no contexto geral do pipeline de. 

Consulte o artigo [agendar e execução](data-factory-scheduling-and-execution.md) para compreender como funciona o agendamento e execução no Azure fábrica de dados.

## <a name="create-pipelines"></a>Criar tubagens
Azure dados fábrica fornece mecanismos vários para criar e implementar tubagens (que por sua vez contenham um ou mais atividades nela). 

### <a name="using-azure-portal"></a>Através do portal Azure
Pode utilizar o editor de fábrica de dados no portal do Azure para criar uma tubagem. Consulte o artigo [Introdução ao Azure fábrica de dados (dados fábrica Editor)](data-factory-build-your-first-pipeline-using-editor.md) para obter instruções sobre um ponto a ponto. 

### <a name="using-visual-studio"></a>Utilizando o Visual Studio 
Pode utilizar o Visual Studio para criar e implementar tubagens Azure fábrica de dados. Consulte o artigo [Introdução ao Azure dados fábrica (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter instruções sobre um ponto a ponto. 

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Pode utilizar o PowerShell Azure para criar tubagens no Azure fábrica de dados. Apresentamos, que definiu pipeline de JSON num ficheiro em c:\DPWikisample.json. Pode carregá-lo para a instância do Azure dados fábrica conforme mostrado no exemplo seguinte:

    New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Consulte o artigo [Introdução ao Azure dados fábrica (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) para obter instruções sobre um ponto a ponto para a criação de uma fábrica de dados com uma tubagem. 

### <a name="using-net-sdk"></a>Utilizar o .NET SDK
Pode criar e implementar em curso através do .NET SDK demasiado. Este mecanismo pode ser utilizado para criar tubagens através de programação. Para mais informações, consulte [criar, gerir e monitorizar fábricas de dados através de programação](data-factory-create-data-factories-programmatically.md). 


### <a name="using-azure-resource-manager-template"></a>Utilizar o modelo de Gestor de recursos do Azure
Pode criar e implementar em curso utilizando um modelo de Gestor de recursos do Azure. Para mais informações, consulte o artigo [Introdução ao Azure dados fábrica (Gestor de recursos do Azure)](data-factory-build-your-first-pipeline-using-arm.md). 

### <a name="using-rest-api"></a>Utilizar a REST API
Pode criar e implementar em curso utilizando REST APIs demasiado. Este mecanismo pode ser utilizado para criar tubagens através de programação. Para mais informações, consulte o artigo [criar ou actualizar uma tubagem](https://msdn.microsoft.com/library/azure/dn906741.aspx). 


## <a name="monitor-and-manage-pipelines"></a>Monitorizar e gerir tubagens  
Assim que é implementada uma tubagem, pode gerir e monitorizar o seu tubagens, os setores do gráfico e será executado. Saiba mais sobre-lo aqui: [Monitor do computador e tubagens gerir](data-factory-monitor-manage-pipelines.md).


## <a name="pipeline-json"></a>Em curso JSON   
Diga-nos demorar um olhar sobre como uma tubagem é definida no formato JSON. A estrutura genérica para uma tubagem será apresentada da seguinte forma:

    {
        "name": "PipelineName",
        "properties": 
        {
            "description" : "pipeline description",
            "activities":
            [
    
            ],
            "start": "<start date-time>",
            "end": "<end date-time>"
        }
    }

A secção de **atividades** pode ter um ou mais atividades definidas dentro da mesma. Cada actividade tem a seguinte estrutura de nível superior:

    {
        "name": "ActivityName",
        "description": "description", 
        "type": "<ActivityType>",
        "inputs":  "[]",
        "outputs":  "[]",
        "linkedServiceName": "MyLinkedService",
        "typeProperties":
        {
    
        },
        "policy":
        {
        }
        "scheduler":
        {
        }
    }

Tabela das seguintes descrevem as propriedades no âmbito das definições de JSON atividade e em curso:

Etiqueta | Descrição | Obrigatório
--- | ----------- | --------
nome | Nome da atividade ou as em curso. Especifique um nome que representa a ação que atividade ou tubagem está configurada para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com um número de letra ou um carácter de sublinhado (_)</li><li>Sequência de carateres não são permitidas: ".", "+", "?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> | Sim
Descrição | Texto que descreva a atividade de tubagem ou para que é utilizada | Sim
tipo | Especifica o tipo da atividade. Consulte os artigos [Atividades de movimento de dados](data-factory-data-movement-activities.md) e [As atividades de transformação de dados](data-factory-data-transformation-activities.md) para tipos diferentes de atividades. | Sim
entradas do tipo | Utilizado pela atividade de tabelas de entrada<br/><br/>uma tabela de entrada<br/>"entradas": [{"nome": "inputtable1"}],<br/><br/>duas tabelas de entrada <br/>"entradas": [{"nome": "inputtable1"}, {"nome": "inputtable2"}], | Sim
resultados de | Tabelas de saída utilizado pela activity.// uma tabela de resultados<br/>"exporta": [{"nome": "outputtable1"}],<br/><br/>duas tabelas de saída<br/>"exporta": [{"nome": "outputtable1"}, {"nome": "outputtable2"}], | Sim
linkedServiceName | Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma actividade pode exigir que especifique o serviço ligado que liga para o ambiente de cluster necessários. | Sim para o HDInsight atividade e Azure máquina aprendizagem lote de atividade de pontuação <br/><br/>Não para todas as outras pessoas
typeProperties | Propriedades na secção typeProperties variam consoante o tipo da atividade. | N
política | Políticas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, são utilizadas políticas predefinidas. | N
Iniciar | Data-hora de início de tubagem. Tem de ser no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. <br/><br/>É possível especificar uma hora local, por exemplo, uma data/hora é. Eis um exemplo: "2016-02-27T06:00:00**-05: 00**", que é 6 AM estimado<br/><br/>As propriedades de início e de fim em conjunto especificarem período ativo para as em curso. Saída os setores do gráfico só são produzidas com durante este período ativo. | N<br/><br/>Se especificar um valor para a propriedade de fim, tem de especificar o valor da propriedade de início.<br/><br/>As horas de início e de fim podem de estar vazias para criar uma tubagem. Tem de especificar ambos os valores para definir um período de tubagem executar ativo. Se não especificar horas de início e fim ao criar uma tubagem, pode defini-las utilizando o cmdlet Set-AzureRmDataFactoryPipelineActivePeriod mais tarde.
fim | Data-hora de fim para as em curso. Se especificado tem de estar no formato ISO. Por exemplo: 2014-10-14T17:32:41Z <br/><br/>É possível especificar uma hora local, por exemplo, uma data/hora é. Eis um exemplo: "2016-02-27T06:00:00**-05: 00**", que é 6 AM estimado<br/><br/>Para executar a tubagem indefinidamente, especifique a 9999-09-09 como o valor da propriedade de fim. | N <br/><br/>Se especificar um valor para a propriedade de início, tem de especificar o valor da propriedade de fim.<br/><br/>Ver notas para a propriedade **Iniciar** .
isPaused | Se não for executado definida como verdadeiro as em curso. Valor predefinido = false. Pode utilizar esta propriedade para ativar ou desativar. | N 
Programador | propriedade "scheduler" é utilizada para definir agendamento pretendida para a atividade. Respectivas sub propriedades são os mesmos como aqueles na [propriedade de disponibilidade de um conjunto de dados](data-factory-create-datasets.md#Availability). | N |   
| pipelineMode | É executado o método de agendamento para as em curso. Permitido valores são: agendado (predefinição), vez.<br/><br/>«Regular» indica que é executada pipeline de um intervalo de tempo especificado de acordo com o respectivo período ativo (hora de início e de fim). 'Vez' indica que a tubagem é executado apenas uma vez. Tubagens vez depois de criado não podem ser modificadas/atualizados atualmente. Consulte o artigo [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes sobre a definição vez. | N | 
| expirationTime | Duração de tempo após a criação para o qual a tubagem é válida e deve permanecer aprovisionada. Se não tem qualquer ativo, falha ou pendente é executada, pipeline de é eliminado automaticamente depois de atingir o tempo de expiração. | N | 
| conjuntos de dados | Lista de conjuntos de dados para ser utilizado por atividades definidas no pipeline de. Esta propriedade pode ser utilizada para definir conjuntos de dados que são específicos para este tubagem e não definido dentro da fábrica de dados. Conjuntos de dados definidos dentro este pipeline só podem ser utilizados por este canalização e não podem ser partilhados. Consulte o artigo [Scoped conjuntos de dados](data-factory-create-datasets.md#scoped-datasets) para obter detalhes.| N |  
 

### <a name="policies"></a>Políticas
Políticas de afetam o comportamento de tempo de execução de uma atividade, especificamente quando é processado do setor de uma tabela. A tabela seguinte fornece os detalhes.

Propriedade | Valores permitidos | Valor predefinido | Descrição
-------- | ----------- | -------------- | ---------------
simultaneidade | Número inteiro <br/><br/>Valor máximo: 10 | 1 | Número de execuções em simultâneo da atividade.<br/><br/>Determina o número de execuções atividades paralelas que pode ocorrer os setores do gráfico diferentes. Por exemplo, se necessitar de uma atividade até um grande conjunto de dados disponíveis, está a ter um valor de simultaneidade maior acelera o processamento de dados. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Determina a ordenação dos dados os setores do gráfico que estão a ser processadas.<br/><br/>Por exemplo, se tiver setores 2 (um acontece em 4 pm e outro às 17: 00), e ambas são pendentes execução. Se definir executionPriorityOrder para ser NewestFirst, setor às 17: 00 é processado pela primeira vez. Da mesma forma se definir executionPriorityORder para ser OldestFIrst, em seguida, no setor na 4 PM é processado. 
Repetir | Número inteiro<br/><br/>Valor máximo pode ser 10 | 3 | Número de tentativas antes do processamento de dados para o setor é assinalado como falha. Execução de atividade para um setor de dados é repetida até a contagem de tentativas especificado. A nova tentativa é feita mais cedo possível após a falha.
tempo limite | TimeSpan | 00:00:00 | Tempo de espera para a atividade. Exemplo: 00:10:00 (implica tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o limite de tempo é infinito.<br/><br/>Se o tempo de processamento de dados num setor excede o valor de tempo de espera, é cancelado e o sistema tenta repetir o processamento. O número de tentativas depende a propriedade de repetir. Quando ocorre o tempo de espera, o estado está definido para tempo limite excedido.
atraso | TimeSpan | 00:00:00 | Especificar o atraso antes de processamento de dados dos inícios setor.<br/><br/>A execução da atividade para um setor de dados é iniciada após o atraso é passado o tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica atraso de 10 minutos)
longRetry | Número inteiro<br/><br/>Valor máximo: 10 | 1 | O número de tentativas de repetição longo antes da execução do setor Ocorreu uma falha.<br/><br/>longRetry tentativas são espaçadas por longRetryInterval. Por isso, se for necessário especificar uma hora entre tentativas de repetição, utilize longRetry. Se forem especificados repetir e longRetry, cada tentativa longRetry inclui tentativas de repetição e o número máximo de tentativas é repetir * longRetry.<br/><br/>Por exemplo, se temos as seguintes definições na política de atividade:<br/>Repetir: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Partem do pressuposto de existe apenas uma setor executar (estado está à espera) e a execução de atividade falha sempre. Inicialmente seria ser 3 tentativas de execução consecutivas. Depois de cada tentativa, o estado do setor seria repetir. Depois de tentativas pela primeira vez 3 não forem sobre, o estado do setor seria LongRetry.<br/><br/>Depois de uma hora (ou seja, valor do longRetryInteval), teria existir outro conjunto de 3 tentativas de execução consecutivas. Após esta ação, o estado do setor seria Ocorreu uma falha com e sem mais repetições seriam ser tentadas. Por conseguinte global 6 tentativas foram feitas.<br/><br/>Se qualquer execução é concluída com êxito, o estado do setor seria pronto e não mais tentativas são tentadas.<br/><br/>longRetry pode ser utilizado em situações onde dados dependentes chegam em alturas não determinista ou o ambiente geral é flaky em ocorre cujo processamento de dados. Nestes casos, ao fazê-lo repetições de um após o outro possam não ajudar a e fazê-lo após um intervalo de tempo, os resultados no resultado pretendido.<br/><br/>Palavra de atenção: não fazer para definir valores elevados para longRetry ou longRetryInterval. Normalmente, os valores mais altos significam outros problemas sistémicos. 
longRetryInterval | TimeSpan | 00:00:00 | O atraso entre tentativas de repetição longo 


## <a name="next-steps"></a>Próximos passos

- Compreenda o [agendamento e execução no Azure fábrica de dados](data-factory-scheduling-and-execution.md).  
- Leia sobre as [capacidades de transformação de dados](data-factory-data-transformation-activities.md) no Azure fábrica de dados e de [movimento de dados](data-factory-data-movement-activities.md)
- Compreenda a [Gestão e monitorização no Azure fábrica de dados](data-factory-monitor-manage-pipelines.md).
- [Criar e implementar o pipeline assegurar](data-factory-build-your-first-pipeline.md). 
