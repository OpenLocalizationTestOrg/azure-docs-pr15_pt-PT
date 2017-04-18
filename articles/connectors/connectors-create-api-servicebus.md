<properties
pageTitle="Saiba como utilizar o conector de Azure Service Bus nas suas aplicações de lógica | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Ligar ao serviço de Azure Bus para enviar e receber mensagens. Pode executar ações como enviar para a fila de espera, enviar para o tópico, receber de fila de espera e receber da subscrição."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Começar a trabalhar com o conector de Bus de serviço do Azure

Ligar ao serviço de Azure Bus para enviar e receber mensagens. Pode executar ações como enviar para a fila de espera, enviar para o tópico, receber de fila de espera e receber da subscrição.

Para utilizar [qualquer conexão](./apis-list.md), tem primeiro criar uma aplicação de lógica. Pode começar por [criar uma aplicação de lógica agora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Ligar ao serviço Bus

Antes de pode aceder a sua aplicação de lógica qualquer serviço, primeiro tem de criar uma ligação ao serviço. Uma [ligação](./connectors-overview.md) fornece conectividade entre uma aplicação de lógica e outro serviço.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Utilize um acionador de Bus de serviço

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação de lógica. [Saiba mais sobre os accionadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Utilizar uma ação de Bus de serviço

Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação de lógica. [Saber mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Detalhes técnicos

Aqui estão os detalhes sobre a accionadores, ações e as respostas que suporta esta ligação.

### <a name="service-bus-triggers"></a>Serviço Bus accionadores

Serviço Bus tem os seguintes accionadores:  

|Accionador | Descrição|
|--- | ---|
|[Quando é recebida uma mensagem de uma fila](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Esta operação de um fluxo de accionadores quando for recebida uma mensagem de uma fila.|
|[Quando é recebida uma mensagem numa subscrição do tópico](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Esta operação accionadores um fluxo de quando é recebida uma mensagem numa subscrição de tópico.|


### <a name="service-bus-actions"></a>Ações de Bus de serviço

Serviço Bus tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Enviar mensagem](connectors-create-api-servicebus.md#send-message)|Esta operação envia uma mensagem para uma fila ou um tópico.|
### <a name="action-and-trigger-details"></a>Detalhes de ação e o acionador

Aqui estão os detalhes das ações e accionadores para este conexão, juntamente com as respostas.



#### <a name="send-message"></a>Enviar mensagem

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ContentData *|Conteúdo|Conteúdo da mensagem.|
|ContentType|Tipo de conteúdo|Tipo de conteúdo do conteúdo da mensagem.|
|Propriedades|Propriedades|Valor da chave pares para cada controlada propriedade.|
|entityName *|Nome do tópico/fila de espera|Nome da fila ou tópico.|

Estas avançadas parâmetros também estão disponíveis:

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|MessageId|Id da mensagem|Um valor definido pelo utilizador que serviço Bus pode utilizar para identificar mensagens de duplicados, se ativado.|
|Para|Para|Endereço para enviar para.|
|ReplyTo|Responder a|Endereço da fila de espera para responder a.|
|ReplyToSessionId|Responder a Id da sessão|Identificador da sessão para responder a.|
|Etiqueta|Etiqueta|Etiqueta específicos da aplicação.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Data e hora, de acordo com UTC, quando a mensagem será adicionada à fila de espera.|
|ID de sessão|Id de sessão|Identificador da sessão.|
|CorrelationId|Id de correlação|Identificador do correlação.|
|TimeToLive|Time To Live|A duração, em escala, que uma mensagem é válida. Começa a duração da quando a mensagem é enviada para Bus de serviço.|



Um * indica que não é necessária uma propriedade.


#### <a name="when-a-message-is-received-in-a-queue"></a>Quando é recebida uma mensagem de uma fila

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|NomeFila *|Nome da fila|Nome da fila de espera.|


Um * indica que não é necessária uma propriedade.


##### <a name="output-details"></a>Detalhes de saída

ServiceBusMessage: Este objeto tem o conteúdo e as propriedades de uma mensagem de Bus de serviço.


| Nome da propriedade | Tipo de dados | Descrição |
|---|---|---|
|ContentData|cadeia|Conteúdo da mensagem.|
|ContentType|cadeia|Tipo de conteúdo do conteúdo da mensagem.|
|Propriedades|objecto|Valor da chave pares para cada controlada propriedade.|
|MessageId|cadeia|Um valor definido pelo utilizador que serviço Bus pode utilizar para identificar mensagens de duplicados, se ativado.|
|Para|cadeia|Envie para o endereço.|
|ReplyTo|cadeia|Endereço da fila de espera para responder a.|
|ReplyToSessionId|cadeia|Identificador da sessão para responder a.|
|Etiqueta|cadeia|Etiqueta específicos da aplicação.|
|ScheduledEnqueueTimeUtc|cadeia|Data e hora, de acordo com UTC, quando a mensagem será adicionada à fila de espera.|
|ID de sessão|cadeia|Identificador da sessão.|
|CorrelationId|cadeia|Identificador do correlação.|
|TimeToLive|cadeia|A duração, em escala, que uma mensagem é válida. Começa a duração da quando a mensagem é enviada para Bus de serviço.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Quando é recebida uma mensagem numa subscrição do tópico

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|topicName *|Nome do tópico|Nome do tópico.|
|subscriptionName *|Nome da subscrição tópico|Nome da subscrição do tópico.|


Um * indica que não é necessária uma propriedade.


##### <a name="output-details"></a>Detalhes de saída

ServiceBusMessage: Este objeto tem o conteúdo e as propriedades de uma mensagem de Bus de serviço.


| Nome da propriedade | Tipo de dados | Descrição |
|---|---|---|
|ContentData|cadeia|Conteúdo da mensagem.|
|ContentType|cadeia|Tipo de conteúdo do conteúdo da mensagem.|
|Propriedades|objecto|Valor da chave pares para cada controlada propriedade.|
|MessageId|cadeia|Um valor definido pelo utilizador que serviço Bus pode utilizar para identificar mensagens de duplicados, se ativado.|
|Para|cadeia|Envie para o endereço.|
|ReplyTo|cadeia|Endereço da fila de espera para responder a.|
|ReplyToSessionId|cadeia|Identificador da sessão para responder a.|
|Etiqueta|cadeia|Etiqueta específicos da aplicação.|
|ScheduledEnqueueTimeUtc|cadeia|Data e hora, de acordo com UTC, quando a mensagem será adicionada à fila de espera.|
|ID de sessão|cadeia|Identificador da sessão.|
|CorrelationId|cadeia|Identificador do correlação.|
|TimeToLive|cadeia|A duração, em escala, que uma mensagem é válida. Começa a duração da quando a mensagem é enviada para Bus de serviço.|



### <a name="http-responses"></a>Respostas HTTP

Ações e accionadores anterior podem devolver um ou mais dos seguintes códigos de estado HTTP:

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido inválido|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido.|
|predefinido|A operação falhou.|

## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
