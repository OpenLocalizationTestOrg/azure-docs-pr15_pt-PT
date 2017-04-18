<properties
pageTitle="SendGrid | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Fornecedor da ligação SendGrid permite-lhe enviar mensagens de e-mail e gerir listas de destinatários."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Começar a trabalhar com o conector de SendGrid

Fornecedor da ligação SendGrid permite-lhe enviar mensagens de e-mail e gerir listas de destinatários.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. 

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

A conexão SendGrid pode ser utilizada como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector de SendGrid tem as seguintes ações disponíveis. Não existem sem accionadores.

### <a name="sendgrid-actions"></a>Ações de SendGrid
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[EnviarCorreioElectrónico](connectors-create-api-sendgrid.md#sendemail)|Envia uma mensagem de e-mail utilizando SendGrid API (limitado a 10.000 destinatários)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Adicionar um destinatário individual para uma lista de destinatários|


## <a name="create-a-connection-to-sendgrid"></a>Criar uma ligação a SendGrid
Para criar aplicações de lógica com SendGrid, tem primeiro criar uma **ligação** , em seguida, fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|ApiKey|Sim|Fornecer a sua chave de Api SendGrid|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

Depois de criar a ligação, pode utilizá-lo para executar as ações e ouvir para accionadores descritos neste artigo.

## <a name="reference-for-sendgrid"></a>Referência para SendGrid
Aplica-se para a versão: 1.0

## <a name="sendemail"></a>EnviarCorreioElectrónico
Enviar mensagens de e-mail: envia uma mensagem de e-mail utilizando SendGrid API (limitado a 10.000 destinatários) 

```POST: /api/mail.send.json``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|pedido| |Sim|corpo|nenhum|Mensagem de correio eletrónico para enviar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|429|Demasiadas pedido|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Adicionar destinatário à lista: adicionar um destinatário individual para uma lista de destinatários 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|listId|cadeia|Sim|caminho|nenhum|Id exclusivo da lista de destinatário|
|recipientId|cadeia|Sim|caminho|nenhum|Id exclusivo do destinatário|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto 

### <a name="emailrequest"></a>EmailRequest


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|a partir do|cadeia|Sim |
|deNome|cadeia|N |
|para|cadeia|Sim |
|tonome|cadeia|N |
|assunto|cadeia|Sim |
|corpo|cadeia|Sim |
|ishtml|Booleano|N |
|Cc|cadeia|N |
|ccname|cadeia|N |
|Bcc|cadeia|N |
|bccname|cadeia|N |
|ReplyTo|cadeia|N |
|data|cadeia|N |
|cabeçalhos|cadeia|N |
|ficheiros|matriz|N |
|nomes de ficheiros|matriz|N |



### <a name="emailresponse"></a>EmailResponse


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|mensagem|cadeia|N |



### <a name="recipientlists"></a>RecipientLists


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|listas|matriz|N |



### <a name="recipientlist"></a>RecipientList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|nome|cadeia|N |
|recipient_count|número inteiro|N |



### <a name="recipients"></a>Destinatários


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|destinatários|matriz|N |



### <a name="recipient"></a>Destinatário


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|mensagem de correio electrónico|cadeia|N |
|Apelido|cadeia|N |
|nome|cadeia|N |
|ID|cadeia|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)