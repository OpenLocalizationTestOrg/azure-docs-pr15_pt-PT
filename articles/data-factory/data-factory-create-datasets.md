<properties 
    pageTitle="Criar conjuntos de dados no Azure dados fábrica | Microsoft Azure" 
    description="Saiba como criar conjuntos de dados no Azure dados fábrica com exemplos que utilizam propriedades, tais como o deslocamento e anchorDateTime."
    keywords="criar o conjunto de dados, conjunto de dados de exemplo, desvio de exemplo"
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
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure dados fábrica
Este artigo descreve conjuntos de dados no Azure dados fábrica e inclui exemplos como deslocamento, anchorDateTime e deslocamento/estilo bases de dados.

Quando cria um conjunto de dados, que está a criar um ponteiro para os dados que pretende processar. Os dados são processados (entrada/saída) uma actividade e uma actividade está contida numa tubagem. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline de e um conjunto de dados de saída representa a exportação para a atividade.

Conjuntos de dados identificam dados dentro de arquivos de dados diferentes, tais como tabelas, ficheiros, pastas e documentos. Depois de criar um conjunto de dados, pode utilizá-lo com atividades numa tubagem. Por exemplo, um conjunto de dados pode ser um conjunto de dados de entrada/saída de uma atividade de copiar ou de uma atividade de HDInsightHive. Portal do Azure dá-lhe um esquema visual dos seus dados e tubagens entradas e saídas. De imediato, vê todas as relações e dependências do seu tubagens na todas as suas fontes para que saiba sempre onde está a chegar dados a partir de e onde é aplicado.

Em Azure fábrica de dados, pode obter dados a partir de um conjunto de dados utilizando a atividade de cópia numa tubagem.

> [AZURE.NOTE] Se estiver familiarizado com o Azure fábrica de dados, consulte [Introdução ao Azure fábrica de dados](data-factory-introduction.md) para uma descrição geral do serviço de fábrica do Azure dados. Consulte o artigo [criar a sua primeira fábrica de dados](data-factory-build-your-first-pipeline.md) para obter um tutorial para criar a sua primeira fábrica de dados. Estes duas artigos fornecem em segundo plano informações necessárias para compreender melhor deste artigo. 

## <a name="define-datasets"></a>Definir conjuntos de dados
Um conjunto de dados no Azure fábrica de dados é definido da seguinte forma: 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

A tabela seguinte descreve as propriedades na JSON acima:   

| Propriedade | Descrição | Obrigatório | Predefinido |
| -------- | ----------- | -------- | ------- |
| nome | Nome do conjunto de dados. Consulte o artigo [Azure dados Factory - regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura. | Sim | DISP |
| tipo | Tipo do conjunto de dados. Especificar um dos tipos de suportados pelo Azure fábrica de dados (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Ver o [Tipo de conjunto de dados](#Type) para obter detalhes. | Sim | DISP |
| estrutura | Esquema do conjunto de dados<br/><br/>Para obter mais detalhes, consulte [A estrutura do conjunto de dados](#Structure) secção. | Não. | DISP |
| typeProperties | Propriedades correspondente ao tipo selecionado. Consulte a secção [Tipo de conjunto de dados](#Type) para obter detalhes sobre os tipos suportados e as respectivas propriedades. | Sim | DISP |
| externos | Sinalizar booleana para especificar se um conjunto de dados é produzido explicitamente por uma tubagem fábrica de dados ou não.  | N | FALSO | 
| disponibilidade | Define a janela de processamento ou o modelo slicing de produção conjunto de dados. <br/><br/>Para obter mais detalhes, consulte o artigo [Disponibilidade do conjunto de dados](#Availability) secção. <br/><br/>Para obter detalhes sobre o conjunto de dados cortar modelo, consulte [agendar e execução](data-factory-scheduling-and-execution.md) artigo. | Sim | DISP
| política | Define os critérios ou a condição de que tem de cumprir os setores do conjunto de dados. <br/><br/>Para obter detalhes, consulte [A política de conjunto de dados](#Policy) secção. | N | DISP |

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo seguinte, o conjunto de dados representa uma tabela com o nome **AMinhaTabela** numa **base de dados Azure SQL**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Tenha em atenção os seguintes pontos: 

- tipo está definido para AzureSqlTable.
- propriedade do tipo de tabela (específica do tipo de AzureSqlTable) está definida para AMinhaTabela.
- linkedServiceName que se refere a um serviço do tipo AzureSqlDatabase ligado. Consulte o artigo a definição do serviço ligada seguinte. 
- frequência de disponibilidade está definida para o dia e intervalo está definido para 1, o que significa que o setor é produzido diariamente.  

AzureSqlLinkedService é definida da seguinte forma:

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

Na JSON acima: 

- tipo está definido como AzureSqlDatabase
- a propriedade tipo CadeiaDeLigação Especifica informações para ligar a uma base de dados do Azure SQL.  


Como pode ver, o serviço ligado define como ligar a uma base de dados do Azure SQL. O conjunto de dados define qual a tabela é utilizada como uma entrada/saída para a atividade numa tubagem. A secção de atividade no seu [pipeline](data-factory-create-pipelines.md) JSON Especifica se o conjunto de dados é utilizado como um conjunto de dados de entrada ou saído.


> [AZURE.IMPORTANT] A menos que um conjunto de dados é endereçados produzido pela fábrica de dados do Azure, deve ser marcada como **externos**. Esta definição aplica-se normalmente, a entradas da primeira atividade numa tubagem.   

## <a name="Type"></a>Tipo de conjunto de dados
Origens de dados e tipos de conjunto de dados são alinhados. Consulte os tópicos referenciados no artigo para obter informações sobre a configuração de conjuntos de dados e tipos de [Dados movimento atividades](data-factory-data-movement-activities.md#supported-data-stores) . Por exemplo, se estiver a utilizar dados a partir de uma base de dados do Azure SQL, clique em base de dados do SQL Azure na lista de arquivos de dados suportadas para ver informações detalhadas.  

## <a name="Structure"></a>Estrutura de conjunto de dados
A secção **estrutura** define o esquema do conjunto de dados. Contém uma coleção de nomes e os tipos de dados de colunas.  No exemplo seguinte, o conjunto de dados tem três colunas slicetimestamp, nomeprojeto e pageviews e são do tipo: cadeia, cadeia e decimais respetivamente.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Disponibilidade de conjunto de dados
A secção **disponibilidade de** um conjunto de dados define a janela de processamento (hora a hora, diária, semanal, etc.) ou o modelo slicing para o conjunto de dados. Consulte o artigo [agendar e execução](data-factory-scheduling-and-execution.md) artigo para obter mais detalhes sobre o modelo de cortar e de dependência de conjunto de dados. 

A seguinte secção disponibilidade Especifica que o conjunto de dados de saída é produzidos hora a hora (ou) entrada conjunto de dados está disponível por hora:

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

A tabela seguinte descreve as propriedades que pode utilizar na secção disponibilidade da: 

| Propriedade | Descrição | Obrigatório | Predefinido |
| -------- | ----------- | -------- | ------- |
| frequência | Especifica a unidade de tempo para produção de setor do conjunto de dados.<br/><br/>**Frequência de suportados**: minuto, hora, dia, semana, mês | Sim | DISP |
| intervalo | Especifica um multiplicador para frequência<br/><br/>"Frequência x intervalo" determina com que frequência é produzido no setor.<br/><br/>Se precisar do conjunto de dados filtrada numa base de hora a hora, definir **frequência** a **hora**e o **intervalo** como **1**.<br/><br/>**Nota:** Se especificar frequência como minutos, recomendamos que o utilizador defina o intervalo para o menor do que 15 | Sim | DISP |
| Estilo | Especifica se deve ser produzido no setor no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se frequência for definida para o mês e conjunto de estilos para EndOfInterval, no setor é produzido no último dia do mês. Se o estilo estiver definido para StartOfInterval, no setor é produzido, o primeiro dia do mês.<br/><br/>Se frequência está definida para o dia e conjunto de estilos para EndOfInterval, no setor é produzido na última hora do dia.<br/><br/>Se frequência for definida para hora e conjunto de estilos para EndOfInterval, no setor é produzido no fim da hora. Por exemplo, para um setor 1 PM – 2 PM período, no setor é apresentado na 2 PM. | N | EndOfInterval |
| anchorDateTime | Define a posição absoluta hora utilizado pelo programador para calcular os limites do setor conjunto de dados. <br/><br/>**Nota:** Se o AnchorDateTime tem partes de data que são mais granulares que a frequência as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o **intervalo** for a **hora** (frequência: hora e o intervalo: 1) e o **AnchorDateTime** contém **minutos e segundos**, em seguida, as partes de **minutos e segundos** da AnchorDateTime são ignoradas. | N | 01/01/0001 |
| deslocamento | TimeSpan pelo qual o início e fim das todos os setores do conjunto de dados são deslocadas para. <br/><br/>**Nota:** Se forem especificados anchorDateTime e deslocamento, o resultado é o combinado shift. | N | DISP |

### <a name="offset-example"></a>exemplo de deslocamento

Diária slices que começam na 6 AM em vez da meia-noite predefinido. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

A **frequência** está definida para **dia** e **intervalo** está definido para **1** (uma vez por dia): Se pretender que o setor para ser produzidas 6 AM em vez de ao tempo predefinido: 12: 00. Lembre-se de que este período de tempo é uma data/hora UTC. 

## <a name="anchordatetime-example"></a>exemplo de anchorDateTime

**Exemplo:** 23 horas conjunto de dados os setores do gráfico que começam no 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>Exemplo de deslocamento/estilo

Se precisar de conjunto de dados no mensalmente na data e hora específicas (suponha no 3 de cada mês na 8:00 AM), utilize a etiqueta de **deslocamento** para definir a data e sincronizá-la deve ser executada. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Política de conjunto de dados

A secção de **política** na definição do conjunto de dados define os critérios ou a condição de que tem de cumprir os setores do conjunto de dados.

### <a name="validation-policies"></a>Políticas de validação

| Nome da política | Descrição | Aplicados a | Obrigatório | Predefinido |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Validar se os dados de uma **Azure blob** cumprem os requisitos de tamanho mínimo (em megabytes). | BLOBs do Azure | N | DISP |
|minimumRows | Validar que os dados de uma **base de dados Azure SQL** ou numa **tabela do Azure** contém o número mínimo de linhas. | <ul><li>Base de dados Azure SQL</li><li>Tabela do Azure</li></ul> | N | DISP

#### <a name="examples"></a>Exemplos

**minimumSizeMB:**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Conjuntos de dados externos

Conjuntos de dados externos são aqueles que não são produzidos por uma tubagem em execução na fábrica de dados. Se o conjunto de dados estiver marcado como **externos**, a política de **ExternalData** pode ser definida para influenciar o comportamento da disponibilidade de setor do conjunto de dados. 

A menos que um conjunto de dados é endereçados produzido pela fábrica de dados do Azure, deve ser marcada como **externos**. Esta definição geralmente aplica-se para as entradas da primeira atividade numa tubagem, a menos que atividade ou interligação de tubagem está a ser utilizado. 

| Nome | Descrição | Obrigatório | Valor predefinido  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tempo de atraso a verificação de disponibilidade dos dados externos no setor determinado. Por exemplo, se os dados são suposto esteja disponível por hora, pode ser atrasada a verificação de para ver dados externos estão disponíveis e o correspondente setor está pronta utilizando dataDelay.<br/><br/>Só se aplica ao tempo de apresentar.  Por exemplo, se for 1:00 PM momento e este valor é 10 minutos, a validação inicia às 1:10 PM.<br/><br/>Esta definição não vai afetar os setores do gráfico no passado (os setores do gráfico com a hora de fim do setor + dataDelay < agora) são processados sem qualquer atraso.<br/><br/>Data/hora maior 23:59 precisar de horas especificado utilizando o formato de day.hours:minutes:seconds. Por exemplo, para especificar a 24 horas, não utilize 24:00:00; em alternativa, utilize 1.00:00:00. Se utiliza o 24:00:00, é tratado como 24 dias (24.00:00:00). 1 dia e 4 horas, especifique 1:04:00:00. | N | 0 |
| retryInterval | O tempo de espera, entre uma falha de e a seguinte repetir tentativa. Aplica-se ao apresentar tempo; Se o anterior tentar falha, podemos Aguarde isto tempo depois do experimentar último. <br/><br/>Se for 1:00 pm neste momento, vamos começar o experimentar primeiro. Se a duração para concluir a verificação de validação primeira é 1 minuto e a operação falhou, a tentativa seguinte é na 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 1:02 pm. <br/><br/>Para os setores do gráfico no passado, não existe nenhuma atraso. A nova tentativa acontece imediatamente. | N | 00:01:00 (1 minuto) | 
| retryTimeout | O tempo limite em cada tentativa de repetir.<br/><br/>Se esta propriedade estiver definida para 10 minutos, a validação tem de ser concluído dentro de 10 minutos. Se demora mais tempo a 10 minutos para executar a validação, a repetir o tempo limite.<br/><br/>Se todas as tentativas para a validação de tempo limite, no setor é assinalado como tempo limite excedido. | N | 10:00:00 (10 minutos) |
| maximumRetry | Número de vezes para verificar a disponibilidade de dados externos. O valor máximo permitido é 10. | N | 3 | 

## <a name="scoped-datasets"></a>Âmbito de conjuntos de dados
Pode criar conjuntos de dados que estão limitadas a uma tubagem, utilizando a propriedade de **conjuntos de dados** . Estes conjuntos de dados só podem ser utilizados pelo atividades dentro neste curso, mas não por atividades no outras tubagens. O exemplo seguinte define uma tubagem com dois conjuntos de dados - InputDataset rdc e OutputDataset rdc - a ser utilizado no pipeline de:  

> [AZURE.IMPORTANT] Âmbito de conjuntos de dados são suportados apenas com tubagens únicas (**pipelineMode** definido para **OneTime**). Consulte o artigo [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes.

    {
        "name": "CopyPipeline-rdc",
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
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }