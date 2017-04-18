<properties
    pageTitle="Guia de introdução: máquina aprendizagem texto Analytics APIs | Microsoft Azure"
    description="Azure máquina texto Analytics - guia de introdução de formação"
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

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Introdução ao APIs da análise do texto para detetar sentimento, expressões-chave, tópicos e idioma

<a name="HOLTop"></a>

Este documento descreve como incorporada para o serviço ou aplicação para utilizar a [API de análise de texto](//go.microsoft.com/fwlink/?LinkID=759711).
Pode utilizar estas APIs para detetar sentimento, expressões-chave, tópicos e de idioma do texto. [Clique aqui para ver uma demonstração interativa da experiência.](//go.microsoft.com/fwlink/?LinkID=759712)

Consulte as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para documentação técnica para as APIs.

Este guia é direcionado versão 2 das APIs. Para obter detalhes sobre a versão 1 APIs, [consulte este documento](../machine-learning/machine-learning-apps-text-analytics.md).

No final deste tutorial, será capaz de forma programática detectar:

- **Sentimento** - é texto positivo ou negativo?

- **Expressões de chave** - o que são pessoas debater um único artigo?

- **Tópicos** - o que são pessoas debater nos artigos muitos?

- **Idiomas** - que idioma é o texto escrito?

Note que esta API dos encargos 1 da transação por documento submetido. Por exemplo, se pedir sentimento para documentos de 1000 numa única chamada, as transações de 1000 serão deduzidas.



<a name="Overview"></a>
## <a name="general-overview"></a>Descrição geral ##

Este documento é um guia passo a passo. Sobre nosso objetivo é guiá-lo os passos necessários para formar um modelo de e para apontar para os recursos que permitirão a colocá-lo de produção. Este exercício irá demorar cerca de 30 minutos.

Para estas tarefas, irá precisar de um editor e ligar os pontos finais RESTful no idioma escolhido.

Vamos começar!

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Tarefa 1 - assinatura para APIs da análise do texto ####

Nesta tarefa, irá inscrever-se para o serviço de análise de texto.

1. Navegue para **Serviços disfunção** no [Portal do Azure](//go.microsoft.com/fwlink/?LinkId=761108) e certifique-se de que **A análise de texto** for selecionado como 'tipo de API'.

1. Selecione um plano. Poderá seleccionar a **camada gratuita para 5.000 transações/mês**. Como é um plano livre, não será cobrado para utilizar o serviço. Terá de iniciar sessão para a sua subscrição Azure. 

1. Preencha os campos e criar a sua conta.

1. Após se inscrever para análise de texto, localize a **Chave de API**. Copie a chave primária, tal como será necessário ao utilizar os serviços de API.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Tarefa 2 - detetar sentimento, expressões-chave e idiomas ####

É fácil detetar idiomas, expressões-chave e sentimento no seu texto. Forma programática irá obter os mesmos resultados tal como a [experiência de demonstração](//go.microsoft.com/fwlink/?LinkID=759712) devolve.

>[AZURE.TIP] Para uma análise sentimento, recomendamos que dividir texto por frases. Normalmente, isto leva para uma maior precisão no previsões sentimento.

Note que os idiomas suportados são da seguinte forma:

| Funcionalidade | Códigos de idioma suportados |
|:-----|:----|
| Sentimento | `en`(Em inglês), `es` (espanhol), `fr` (francês), `pt` (Brasil) |
| Expressões-chave | `en`(Em inglês), `es` (espanhol), `de` (alemão), `ja` (japonês) |


1. Terá de definir os cabeçalhos ao seguinte. Note que está atualmente apenas JSON aceites formato de entrada para as APIs. XML não é suportada.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. Em seguida, formate linhas de entrada no JSON. Para sentimento, expressões-chave e idioma, o formato é a mesma. Note que cada ID deve ser único e será o ID devolvido pelo sistema. O tamanho máximo de um documento simples que pode ser submetido é 10KB e o tamanho máximo total de entrada submetido é 1MB. Não mais de 1.000 documentos podem ser apresentados numa chamada. Limitação de velocidade existe uma taxa de 100 chamadas por minuto - por conseguinte, recomendamos que submeter grandes quantidades de documentos numa única chamada. Idioma é um parâmetro opcional que deve ser especificado se analisar não inglesa texto. Um exemplo de entrada é apresentado abaixo, onde o parâmetro opcional `language` para sentimento análise ou tecla extração de expressão está incluída:

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Fazer uma chamada de **mensagem** para o sistema com a entrada de sentimento, expressões-chave e idioma. Os URLs serão o aspeto da seguinte forma:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Esta chamada irá devolver uma JSON formatado resposta com os IDs e detetado propriedades. Um exemplo da exportação para sentimento é mostrado abaixo (com detalhes do erro excluídos). No caso de sentimento, será devolvida uma pontuação entre 0 e 1 para cada documento:

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Tarefa 3 - detetar tópicos num corpo de texto ####

Este é um API disponibilizado recentemente que devolve a parte superior detetado tópicos para uma lista de submetido registos de texto. Um tópico é identificado com uma expressão chave, que pode ser uma ou mais relacionado com palavras. API foi concebida para funcionar bem para texto escrito breve, humano como revisões e comentários dos utilizadores.

Esta API requer **um mínimo de 100 registos de texto** para ser apresentados, mas foi concebido para detetar tópicos nos centenas a milhares de registos. Qualquer registos não inglesa ou registos com menos de 3 palavras serão eliminados e, consequentemente, não serão atribuídos para tópicos. De detecção do tópico, o tamanho máximo de um documento simples que pode ser submetido é 30KB e o tamanho máximo total de entrada submetido é 30MB. Deteção de tópico é taxa limitada a 5 submissões em 5 minutos.

Existem dois parâmetros de entrada adicionais **opcionais** que podem ajudar a melhorar a qualidade dos resultados:

- **Pare de palavras.**  Estas palavras e respectivos formulários Fechar (por exemplo, plurais) serão excluídos do pipeline de deteção de todo o tópico. Utilize esta opção para palavras comuns (por exemplo, "problema", "erro" e "utilizador" podem ser opções adequadas para reclamações de cliente sobre software). Cada cadeia deve ser uma única palavra.
- **Parar de expressões** - destas expressões serão excluídas da lista de tópicos devolvidos. Utilize esta opção para excluir genéricos tópicos que não pretende ver nos resultados de. Por exemplo, "Microsoft" e "Azure" seria escolhas adequadas tópicos excluir. Cadeias podem conter várias palavras.

Siga estes passos para detetar tópicos no seu texto.

1. Formate o de entrada no JSON. Desta vez, pode definir palavras parar e deixar de expressões.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. Utilizar os cabeçalhos da mesma, tal como foi definido no 2 de tarefa, tornar uma **mensagem** a chamada para o ponto final de tópicos:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Isto irá devolver uma `operation-location` como um cabeçalho a resposta, onde o valor é o URL para consultar os tópicos resultantes:

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Consultar o devolvida `operation-location` periodicamente com um pedido de **obter** . Uma vez por minuto é recomendado.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. O ponto final irá devolver uma resposta, incluindo `{"status": "notstarted"}` antes da transformação, `{"status": "running"}` enquanto processamento e `{"status": "succeeded"}` com a saída de uma vez concluída. Em seguida, pode consumir o resultado que será no seguinte formato (nota detalhes como o formato de erro e datas foram excluídas neste exemplo):

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Tenha em atenção que a resposta bem sucedida tópicos a partir do `operations` ponto final terá o seguinte esquema:

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Explicações para cada parte desta resposta são os seguintes:

**Tópicos**

| Chave | Descrição |
|:-----|:----|
| ID | Um identificador exclusivo para cada tópico. |
| pontuação | Contagem de documentos atribuídos a tópico. |
| keyPhrase | Uma summarizing palavra ou expressão para o tópico. |

**topicAssignments**

| Chave | Descrição |
|:-----|:----|
| documentId | Identificador do documento. Equivale a ID incluído na entrada de dados. |
| topicId | O ID do tópico que o documento tiver sido atribuído a. |
| distância | Pontuação de inscrição do documento para o tópico entre 0 e 1. No canto inferior uma distância pontuação é de facto o tópico mais forte. |

**erros**

| Chave | Descrição |
|:-----|:----|
| ID | Identificador exclusivo do documento de entrada o erro que se refere a. |
| mensagem | Mensagem de erro. |

## <a name="next-steps"></a>Próximos passos ##

Parabéns! Agora ter concluído utilizando a análise de texto nos seus dados. Agora pode optar por olhe para utilizar uma ferramenta de como o [Power BI](//powerbi.microsoft.com) para visualizar os seus dados, bem como para os automatizar conhecimentos de forma para dar-lhe uma vista em tempo real dos seus dados de texto.

Para ver como as funcionalidades de análise de texto, tal como sentimento, podem ser utilizadas como parte de um bot, consulte o exemplo [Bot afectivos](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) no site Bot Framework.
