<properties 
    pageTitle="Como configurar os pontos finais de aprendizagem do Azure máquina na sequência Analytics | Microsoft Azure" 
    description="Linguagem máquina funções definidas pelo utilizador em sequência Analytics"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Integração de aprendizagem na sequência de análise de computador

Análise da cadeia suporta as funções definidas pelo utilizador que a chamada para os pontos finais de aprendizagem do Azure máquina. Suporte de REST API para esta funcionalidade é detalhado na [biblioteca da cadeia Analytics REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações suplementares necessárias para implementação com êxito desta capacidade em sequência Analytics. Um tutorial também foi registado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Descrição geral: Terminologia de aprendizagem do Azure máquina

Formação do Microsoft Azure máquina fornece uma ferramenta de colaboração, arrastar e largar, que pode utilizar para criar, testar e implementar soluções de análise de aspeto do Office nos seus dados. Esta ferramenta é denominada o *Azure máquina aprendizagem Studio*. O studio é utilizado para interagir com os recursos de aprendizagem automática e facilmente construir, testar e iteramos sobre a estrutura. Estes recursos e as respetivas definições são descritos a seguir.

- **Área de trabalho**: A *área de trabalho* é um contentor que detém todos os outros recursos de aprendizagem máquina em conjunto num contentor para a gestão e controlo.
- **Experiência**: *experiências* são criados por cientistas dados utilizam conjuntos de dados e formar um modelo de aprendizagem automática.
- **Ponto final**: *os pontos finais* são o objeto de aprendizagem do Azure máquina utilizado para tirar funcionalidades como entrada, aplicar um modelo de aprendizagem do computador especificado e devolver a saída de visita.
- **Pontuação Serviçoweb**: um *pontuação serviçoweb* é um conjunto de pontos finais tal como mencionado acima.

Ponto final de cada tem apis para execução lote e a execução síncrona. Análise da cadeia utiliza síncrona execução. O serviço específico é com o nome de um [Serviço de pedido/resposta](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) no AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Recursos necessários para trabalhos de análise da cadeia de aprendizagem automática

Para efeitos de análise de sequência de processamento de uma tarefa, um ponto final de pedido/resposta, um [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)e uma definição de swagger são todas as necessárias para execução com êxito. Análise de sequência tem um ponto final adicional que constrói o url do ponto final de swagger, procura a interface e devolve uma definição de UDF predefinido ao utilizador.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar um UDF através de REST API de aprendizagem automática e a análise da cadeia

Ao utilizar os REST APIs pode configurar o seu trabalho para chamar funções da linguagem de máquina Azure. Os passos são da seguinte forma:

1. Criar uma tarefa de análise da cadeia
2. Definir uma entrada
3. Definir uma saída
4. Criar uma função definida pelo utilizador (UDF)
5. Escrever uma transformação de análise da cadeia chamadas UDF
6. Iniciar a tarefa

## <a name="creating-a-udf-with-basic-properties"></a>Criar um UDF com propriedades básicas

Por exemplo, o seguinte código de exemplo cria um escalar UDF denominado *newudf* que liga um ponto final Azure máquina aprendizagem. Tenha em atenção que o *ponto final* (serviço URI) pode ser encontrado na página de ajuda de API para o serviço que selecionou e o *apiKey* pode encontrar na página principal de serviços.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corpo do pedido de exemplo:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chamada RetrieveDefaultDefinition ponto final para a predefinição UDF

Depois da estrutura UDF é criado é necessária, a definição de UDF concluída. O ponto final RetreiveDefaultDefinition ajuda-o a preparar a definição de predefinido para uma função escalar que está vinculada a um ponto final Azure máquina aprendizagem. A carga útil abaixo requer que obter a definição de UDF predefinido para uma função escalar que está vinculada a um ponto final Azure máquina aprendizagem. -Não especifica o ponto final real como já foram concedida durante o pedido de colocar. Análise da cadeia chamadas o ponto final fornecido no pedido de se for fornecido explicitamente. Caso contrário, utiliza um originalmente referenciado. Aqui a aceita UDF uma única cadeia parâmetro (uma frase) e devolve um único de saída do tipo de cadeia que indica a etiqueta "sentimento" para esse frase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corpo do pedido de exemplo:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Um exemplo de saída do presente procure algo gostaria abaixo.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Patches UDF com a resposta 

Agora tem de ser corrigido UDF com a resposta anterior, conforme apresentado abaixo.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corpo do pedido (saída de RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar a transformação de análise da cadeia para ligar o UDF

Agora consultar UDF (aqui denominado scoreTweet) para todos os eventos de entrada e escrever uma resposta para esse evento para um resultado.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
