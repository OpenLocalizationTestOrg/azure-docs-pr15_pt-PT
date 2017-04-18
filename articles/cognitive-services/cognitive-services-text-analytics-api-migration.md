<properties
    pageTitle="Atualizar para a versão 2 a análise de texto API | Microsoft Azure"
    description="Azure máquina texto Analytics - atualização para a versão 2 de formação"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Atualizar para a versão 2 as análises de texto API #

Este guia irá levá-lo durante o processo de atualizar o seu código do através da [primeira versão, de API](../machine-learning/machine-learning-apps-text-analytics.md) para utilizar a versão segunda. 

Se não tiver utilizado a API e gostaria de saber mais, pode **[obter mais informações sobre a API aqui](//go.microsoft.com/fwlink/?LinkID=759711)** ou **[siga o guia de introdução](//go.microsoft.com/fwlink/?LinkID=760860)**. Para referência técnica, consulte a **[Definição de API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Parte 1. Obter uma nova chave ###

Em primeiro lugar, terá de obter uma nova chave de API a partir do **Azure Portal**:

1. Navegue para o serviço de análise de texto através da [Galeria de informações da empresa Cortana](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Aqui, também irá encontrar ligações para os exemplos de documentação e o código.

1. Clique em **Inscrever-se**. Esta ligação será direcionado para o portal de gestão Azure, onde pode inscrever-se para o serviço.

1. Selecione um plano. Poderá seleccionar a **camada gratuita para 5.000 transações/mês**. Como é um plano livre, não será cobrado para utilizar o serviço. Terá de iniciar sessão à sua subscrição do Azure. 

1. Após se inscrever para análise de texto, ser-lhe-á dada uma **Chave de API**. Copie esta tecla, tal como irá precisar ao utilizar os serviços de API.

### <a name="part-2-update-the-headers"></a>Parte 2. Atualizar os cabeçalhos ###

Atualize os valores de cabeçalho submetido, conforme apresentado abaixo. Tenha em atenção que a chave de conta já não é codificada.

**Versão 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versão 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Parte 3. Atualizar o URL de base ###

**Versão 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versão 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Parte 4. Atualizar os formatos de sentimento, expressões-chave e idiomas ###

#### <a name="endpoints"></a>Pontos finais ####

OBTER os pontos finais agora tem sido preteridos, para que todas as entradas devem ser apresentadas como um pedido de mensagem. Atualize os pontos finais para aqueles apresentado abaixo.

| |Ponto final de única versão 1|Ponto final do lote de versão 1|Ponto final de versão 2|
|---|---|---|---|
|Tipo de chamada|OBTER|MENSAGEM|MENSAGEM|
|Sentimento|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Expressões-chave|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Idiomas|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formatos de entrada ####

Tenha em atenção que formato de mensagem apenas agora for aceite, para que deve reformatar qualquer entrada que utilizava anteriormente os pontos finais único documento em conformidade. Entradas não são sensíveis a maiúsculas e minúsculas.

**Versão 1 (lote)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versão 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Saída de sentimento ####

**Versão 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Saída de expressões-chave ####

**Versão 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Saída de idiomas ####


**Versão 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Parte 4. oB. Atualizar os formatos de tópicos ###

#### <a name="endpoints"></a>Pontos finais ####

| |Ponto final de versão 1 | Ponto final de versão 2|
|---|---|---|
|Submeter para deteção tópico (POST)|```StartTopicDetection```|```topics```|
|Obter resultados de tópico (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formatos de entrada ####

**Versão 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versão 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Resultados de submissão ####

**Versão 1 (POST)**

Anteriormente, quando tiver terminado a tarefa de, recebe JSON a saída seguinte, onde o jobId seria ser anexado a um URL para obter o resultado.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versão 2 (POST)**

A resposta agora incluirá um valor de cabeçalho da seguinte forma, onde `operation-location` é utilizado como o ponto final para as inquérito para os resultados:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Resultados da operação ####

**Versão 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2 (GET)**

Como antes, é devolvido **periodicamente as inquérito a saída** (o período sugerido é minuto) até que a saída. 

Quando tiver terminado a API de tópicos, de leitura de estado `succeeded` será devolvido. Em seguida, isto irá incluir os resultados de saída no formato apresentado abaixo:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Parte 5. Testá-la! ###

Deverá agora estar pronto! Teste o seu código com uma amostra pequenas para se certificar de que pode processar dados com êxito.
