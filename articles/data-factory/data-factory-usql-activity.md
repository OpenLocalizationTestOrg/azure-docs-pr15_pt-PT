<properties 
    pageTitle="Executar o script U SQL no Azure dados Lake análise a partir do Azure fábrica de dados" 
    description="Saiba como a processar dados ao executar scripts U SQL Azure dados Lake Analytics cluster serviço." 
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
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Executar o script U SQL no Azure dados Lake análise a partir do Azure fábrica de dados
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)
 
Uma tubagem numa fábrica dados Azure processa dados nos serviços do armazenamento ligadas, utilizando os serviços de cluster ligadas. Contém uma sequência de atividades onde cada actividade executa uma operação de processamento específico. Este artigo descreve a **Dados Lake análise U SQL atividade** que executa um script **U SQL** num serviço de cluster ligada **Azure dados Lake Analytics** . 

> [AZURE.NOTE] 
> Crie uma conta Azure dados Lake Analytics antes de criar uma tubagem com uma dados Lake análise U SQL atividade. Para saber mais sobre Azure dados Lake Analytics, consulte o artigo [Introdução ao Azure dados Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Reveja [construir a sua primeira tutorial de tubagem](data-factory-build-your-first-pipeline.md) para obter passos detalhados para criar uma fábrica de dados, serviços ligados, conjuntos de dados e uma tubagem. Utilize fragmentos JSON com o Editor de fábrica do mesmo de dados ou Visual Studio ou Azure PowerShell para criar entidades fábrica de dados.

## <a name="azure-data-lake-analytics-linked-service"></a>A análise de dados Azure Lake ligadas serviço
Criar um serviço de **Análise de Lake Azure dados** ligados para ligar um serviço de cluster Azure dados Lake Analytics a uma fábrica dados Azure. A atividade de dados Lake Analytics U-SQL no pipeline de que se refere a este serviço ligado. 

O exemplo seguinte fornece definição JSON para um serviço de análise de Lake Azure dados ligados. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON. 

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
Tipo | A propriedade de tipo deve estar definida: **AzureDataLakeAnalytics**. | Sim
accountName | Nome da conta a análise de dados Azure Lake. | Sim
dataLakeAnalyticsUri | URI de Lake análise de dados Azure. |  N 
autorização | Código de autorização é obtido automaticamente depois de clicar no botão **autorizar** no Editor de fábrica do mesmo de dados e concluir OAuth login.  | Sim 
subscriptionId | Id da subscrição Azure | Não (se não for especificado, a subscrição da fábrica dados é utilizada). 
resourceGroupName | Nome do grupo de recursos Azure |  Não (se não for especificado, grupo de recursos da fábrica dados é utilizado).
ID de sessão | id da sessão a partir da sessão de autorização OAuth. Cada id da sessão é exclusivo e pode ser utilizada apenas uma vez. A sessão Id é gerado automaticamente no Editor de fábrica do mesmo de dados. | Sim

O código de autorização gerado utilizando o botão **autorizar** expira após algures. Consulte a tabela seguinte para os tempos de expiração para diferentes tipos de contas de utilizador. Poderá ver a seguinte mensagem de erro de mensagens quando a autenticação **token expirar**: erro de operação de credenciais: invalid_grant - AADSTS70002: erro validar credenciais. AADSTS70008: A conceder acesso fornecido está expirada ou revogado. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: carimbo de data/hora fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

 
| Tipo de utilizador | Expira após |
| :-------- | :----------- | 
| Contas de utilizador não são geridas pelo Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 horas |
| Contas de utilizadores geridas pelo Azure Active Directory (AAD) | executar 14 dias após o último setor. <br/><br/>cerca de 90 dias, se um setor com base no serviço ligadas com base em OAuth for executado pelo menos uma vez a cada 14 dias. |

Para evitar/resolver este erro, autorizar utilizando o **autorizar** no botão quando o **token expirar** e novo lançamento o serviço ligado. Também pode gerar os valores das propriedades de **ID de sessão** e **autorização** através de programação utilizando código na secção seguinte. 

  
### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para gerar através de programação valores de ID de sessão e autorização 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consulte os tópicos de [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService escolares](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes fábrica de dados utilizadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe de WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U SQL Lake análise de dados 

O seguinte fragmento de JSON define uma tubagem com um dados Lake análise U SQL atividade. A definição de atividade tem uma referência para o serviço do Azure dados Lake Analytics ligadas que criou anteriormente.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


A tabela seguinte descreve os nomes e descrições de propriedades que são específicas para este atividade. 

Propriedade | Descrição | Obrigatório
:-------- | :----------- | :--------
tipo | A propriedade tipo tem de estar definida para **DataLakeAnalyticsU SQL**. | Sim
CaminhoScript | Caminho para a pasta que contém o script U-SQL. Nome do ficheiro é sensível às maiúsculas. | Não (se utilizar o script)
scriptLinkedService | Serviço ligadas que liga o armazenamento que contém o script para a fábrica de dados | Não (se utilizar o script)
script | Especificar um script inline em vez de especificando CaminhoScript e scriptLinkedService. Por exemplo: "script": "Teste criar base de dados". | Não (se utilizar CaminhoScript e scriptLinkedService)
degreeOfParallelism | O número máximo de nós em simultâneo utilizado para executar a tarefa. | N
prioridade | Determina as tarefas que terminar tudo o que são colocadas na fila deverão estar seleccionadas para executar o primeiro. No canto inferior o número, maior é a prioridade. | N 
parâmetros | Parâmetros para o script de U SQL | N 

Consulte o artigo [SearchLogProcessing.txt Script definição](#script-definition) para a definição de script. 

## <a name="sample-input-and-output-datasets"></a>Exemplo de entrada e saída conjuntos de dados

### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem no arquivo de Lake de dados de Azure (ficheiro SearchLog.tsv na pasta datalake/entrada). 

    {
        "name": "DataLakeTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script de U SQL estão armazenados no arquivo de Lake de dados de Azure (datalake/saída pasta). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Arquivo de Lake de dados de exemplo ligadas serviço
Segue-se a definição da amostra que Azure dados Lake loja ligadas serviço utilizado pelos conjuntos de dados de entrada/saída. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Consulte o artigo [mover os dados de e para a loja do Azure dados Lake](data-factory-azure-datalake-connector.md) artigo para obter descrições das propriedades JSON. 

## <a name="sample-u-sql-script"></a>Exemplo de Script de U SQL 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Os valores para **@in** e **@out** parâmetros no script U SQL são transmitidos dinamicamente pelo ADF utilizando a secção 'parâmetros'. Consulte a secção 'parâmetros' na definição em curso.

Também pode especificar outras propriedades, tais como degreeOfParallelism e prioridade na sua definição de tubagem para as tarefas que executam o serviço do Azure dados Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição de tubagem do exemplo, e reduzir parâmetros são atribuídos com valores codificada. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

É possível utilizar parâmetros dinâmicos em vez disso. Por exemplo: 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

Neste caso, ficheiros de entrada ainda são recolhidos a partir da pasta /datalake/input e ficheiros de saída são gerados na pasta /datalake/output. Os nomes de ficheiro são baseados na dinâmicos a hora de início do setor.  