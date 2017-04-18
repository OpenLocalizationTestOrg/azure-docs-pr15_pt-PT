<properties
    pageTitle="Máquina APIs de formação: Análise de texto | Microsoft Azure"
    description="Máquina aprendizagem texto Analytics APIs da Microsoft podem ser utilizados para analisar o texto não estruturado para análise sentimento, extração de expressão chave, deteção de idioma e deteção de tópico."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>Máquina APIs de formação: A chave de texto a análise de sentimento, extração de expressão, deteção de idioma e deteção de tópico

>[AZURE.NOTE] Este guia é direcionado a API a versão 1. Para a versão 2, [**consulte este documento**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Versão 2 é agora a versão desta API preferida.

## <a name="overview"></a>Descrição geral

A API de análise de texto é um conjunto de aplicações de texto analytics [serviços web](https://datamarket.azure.com/dataset/amla/text-analytics) criadas com Azure máquina aprendizagem. API pode ser utilizada para analisar o texto não estruturado para tarefas, como análise sentimento, extração de expressão chave, deteção de idioma e deteção de tópico. Sem dados de formação são necessário para utilizar esta API: basta trazer os dados de texto. Esta API utiliza técnicas de processamento de idioma natural avançado para entregar melhor no previsões de classe.

Pode ver a análise de texto em ação no nosso [site demonstração](https://text-analytics-demo.azurewebsites.net/), onde também irá encontrar as [amostras](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) como implementar a análise de texto no c# e Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Análise de sentimento

API devolve numéricas entre 0 e 1. As pontuações perto 1 indicam positivo sentimento, enquanto as pontuações perto 0 indicam sentimento negativo. Pontuação sentimento é gerada utilizando técnicas de classificação. As funcionalidades de entrada ao classificador incluem n-g, funcionalidades geradas a partir da peça de voz etiquetas e incorporações do word. Atualmente, inglês é o idioma suportado apenas.
 
## <a name="key-phrase-extraction"></a>Extração de expressão chave

API devolve uma lista de cadeias indicando os pontos de debate chave no texto da entrada. Podemos utilizar técnicas de toolkit de processamento de linguagem Natural sofisticada Microsoft Office. Atualmente, inglês é o idioma suportado apenas.

## <a name="language-detection"></a>Deteção de idioma

API devolve o idioma detectado e numéricas entre 0 e 1. As pontuações perto 1 indicam 100% tenha a certeza de que o idioma identificado é verdadeiro. Um total de 120 idiomas são suportados.

## <a name="topic-detection"></a>Deteção de tópico

Este é um API disponibilizado recentemente que devolve a parte superior detetado tópicos para uma lista de submetido registos de texto. Um tópico é identificado com uma expressão chave, que pode ser uma ou mais relacionado com palavras. Esta API requer um mínimo de 100 texto registos para ser apresentados, mas foi concebido para detetar tópicos nos centenas a milhares de registos. Note que esta API dos encargos 1 da transação por texto registo submetido. API foi concebida para funcionar bem para texto escrito breve, humano como revisões e comentários dos utilizadores.

---

## <a name="api-definition"></a>Definição de API

### <a name="headers"></a>Cabeçalhos

Certifique-se de que inclui os cabeçalhos corretos no seu pedido, o que deve ser da seguinte forma:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Pode encontrar a chave de conta da sua conta no [Azure Data Market](https://datamarket.azure.com/account/keys). Tenha em atenção que é aceite JSON atualmente apenas para os formatos de entrada e saídos. XML não é suportada.

---

## <a name="single-response-apis"></a>APIs única resposta

### <a name="getsentiment"></a>GetSentiment

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Pedido de exemplo**

Na chamada abaixo, podemos está a pedir análise do sentimento para a expressão "Olá mundo":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Isto irá devolver uma resposta da seguinte forma:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Pedido de exemplo**

Na chamada abaixo, podemos está a pedir as expressões-chave que se encontram no texto "Foi num hotel maravilhosa para se manter, com decor exclusivo e funcionários amigáveis":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Isto irá devolver uma resposta da seguinte forma:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Pedido de exemplo**

Na chamada GET abaixo, podemos pedir para sentimento para as expressões-chave no texto *Olá mundo*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Isto irá devolver uma resposta da seguinte forma:

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Parâmetros opcionais**

`NumberOfLanguagesToDetect`é um parâmetro opcional. A predefinição é 1.

---

## <a name="batch-apis"></a>Batch APIs

O serviço de texto Analytics permite-lhe fazer sentimento e extracções frase-chave no modo de lote. Note que cada um dos registos classificados contagens como uma transação. Por exemplo, se pedir sentimento para registos de 1000 numa única chamada, as transações de 1000 serão deduzidas.

Note que os IDs de introduzidos no sistema são os IDs de devolvido pelo sistema. O serviço web não verifica que estes IDs são exclusivos. Cabe o autor da chamada para verificar exclusividade. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Pedido de exemplo**

Na chamada mensagem abaixo, podemos pedir para elementos das frases "Olá mundo", "Olá Foo mundo" e "Olá meu mundo" no corpo do pedido:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo do pedido:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Na resposta abaixo, obtém a lista de resultados associados com o texto Ids:

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Pedido de exemplo**

Neste exemplo, vamos pedir para a lista de elementos para as expressões-chave nos seguintes textos: 

* "Foi num hotel maravilhosa para se manter, com decor exclusivo e funcionários amigáveis"
* "Foi uma conferência de compilação surpreendente, com falam muito interessantes"
* "O tráfego foi terrible, despendido a três horas Ir para o aeroporto"

Este pedido for feito como uma chamada para o ponto final de uma mensagem:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo do pedido:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Na resposta abaixo, obtém a lista de expressões-chave associados com o texto Ids:

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "Inglês",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "Francês",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>APIs de deteção de tópico

Este é um API disponibilizado recentemente que devolve a parte superior detetado tópicos para uma lista de submetido registos de texto. Um tópico é identificado com uma expressão chave, que pode ser uma ou mais relacionado com palavras. Note que esta API dos encargos 1 da transação por texto registo submetido.

Esta API requer um mínimo de 100 texto registos para ser apresentados, mas foi concebido para detetar tópicos nos centenas a milhares de registos.


### <a name="topics--submit-job"></a>Tópicos – submeter tarefa

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Pedido de exemplo**


Na chamada mensagem abaixo, podemos está a pedir tópicos para um conjunto de 100 artigos, onde são apresentados os artigos introdução primeiros e últimos e duas StopPhrases são incluídas.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corpo do pedido:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Na resposta abaixo, obtém a JobId para a tarefa submetida:

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Uma lista de uma única palavra ou o word várias expressões que não deve ser devolvido como tópicos. Pode ser utilizado para filtrar os tópicos muito genéricos. Por exemplo, de um conjunto de dados sobre hotel revisões, "hotel" e "pousadas" podem ser parar cuidados expressões.  

### <a name="topics--poll-for-job-results"></a>Tópicos – inquérito para os resultados de tarefa

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Pedido de exemplo**

Passe JobId devolvido do passo 'Submeter tarefa' para obter os resultados. Recomendamos que ligar este ponto final de cada minuto até estado = 'Concluída' na resposta. Irá demorar cerca de 10 minutos para uma tarefa de concluída ou mais longa para os projectos com vários milhares de registos.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Enquanto está a processar, a resposta será da seguinte forma:

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


API devolve o resultado no formato JSON no seguinte formato:

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


As propriedades para cada parte da resposta são da seguinte forma:

**Propriedades de TopicInfo**

| Chave | Descrição |
|:-----|:----|
| TopicId | Um identificador exclusivo para cada tópico. |
| Pontuação | Contagem de registos atribuídos para tópico. |
| KeyPhrase | Uma summarizing palavra ou expressão para o tópico. Pode ser 1 ou várias palavras. |

**Propriedades de TopicAssignment**

| Chave | Descrição |
|:-----|:----|
| ID | Identificador do registo. Equivale a ID incluído na entrada de dados. |
| TopicId | O ID do tópico que o registo tiver sido atribuído a. |
| Distância | Confiança que o registo pertence para o tópico. Distância mais perto a zero indica confiança superior. |
