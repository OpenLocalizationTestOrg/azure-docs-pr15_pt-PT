<properties
pageTitle="RSS | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Conexão RSS permite que os utilizadores publicar e obter itens de alimentação. Também permite que os utilizadores acionado operações quando um novo item é publicado no feed."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-rss-connector"></a>Começar a trabalhar com o conector RSS
RSS é um formato de sindicação de web populares utilizado para publicar o conteúdo actualizado frequentemente – como entradas de blogue e títulos de notícias.  Muitos fabricantes de conteúdo fornecem um feed RSS para permitir que utilizadores subscrever ao mesmo.  Utilize o conector RSS para obter feeds fluxos de informações e o acionador quando novos itens são publicados num RSS feed.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. 

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

O conector RSS pode ser utilizado como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector RSS tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="rss-actions"></a>Ações de RSS
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obter RSS todos os itens de alimentação.|
### <a name="rss-triggers"></a>Accionadores RSS
Pode ouvir para estes evento (s):

|Accionador | Descrição|
|--- | ---|
|Quando um novo item de alimentação publicado|Accionar um fluxo de trabalho quando um novo feed é publicado|


## <a name="create-a-connection-to-rss"></a>Criar uma ligação RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="reference-for-rss"></a>Referência para RSS
Aplica-se para a versão: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Quando um novo item de alimentação publicado: accionadores um fluxo de trabalho quando um novo feed é publicado 

```GET: /OnNewFeed``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|feedUrl|cadeia|Sim|consulta|nenhum|Url do feed|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="listfeeditems"></a>ListFeedItems
Listar todos os RSS feeds de itens.: obter RSS todos os itens de alimentação. 

```GET: /ListFeedItems``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|feedUrl|cadeia|Sim|consulta|nenhum|Url do feed|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido inválido|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="feeditem"></a>FeedItem


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|Sim |
|título|cadeia|Sim |
|conteúdo|cadeia|Sim |
|ligações|matriz|N |
|updatedOn|cadeia|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)