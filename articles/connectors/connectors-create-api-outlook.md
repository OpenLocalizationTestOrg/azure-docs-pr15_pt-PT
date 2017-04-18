<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Conector do Outlook.com permite-lhe gerir o seu correio, calendários e contactos. Pode executar várias ações, como enviar correio, agendar reuniões, adicionar contactos, etc."
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

# <a name="get-started-with-the-outlookcom-connector"></a>Começar a trabalhar com o conector de Outlook.com

Conector do Outlook.com permite-lhe gerir o seu correio, calendários e contactos. Pode executar várias ações, como enviar correio, agendar reuniões, adicionar contactos, etc.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. 

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

O conector de Outlook.com pode ser utilizado como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector de Outlook.com tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="outlookcom-actions"></a>Ações de Outlook.com
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Obtém os e-mails a partir de uma pasta|
|[EnviarCorreioElectrónico](connectors-create-api-outlook.md#SendEmail)|Envia uma mensagem de e-mail|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Elimina uma mensagem de e-mail por id|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marcas de uma mensagem de e-mail como ter sido lida|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Respostas a uma mensagem de e-mail|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Obtém anexo de e-mail por id|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Enviar um e-mail com várias opções e aguarde que o destinatário apenas responder novamente com uma das opções|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Enviar um e-mail de aprovação e aguardar uma resposta a partir do destinatário|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Obtém calendários|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Obtém os itens a partir de um calendário|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Cria um novo evento|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Obtém um item específico a partir de um calendário|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Elimina um item de calendário|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Parcialmente atualiza um item de calendário|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Obtém pastas de contactos|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Obtém contactos a partir de uma pasta de contactos|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Cria um novo contacto|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Obtém um contacto específico a partir de uma pasta de contactos|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Elimina um contacto|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Parcialmente atualiza um contacto|
### <a name="outlookcom-triggers"></a>Outlook.com accionadores
Pode ouvir para estes evento (s):

|Accionador | Descrição|
|--- | ---|
|No evento iniciar em breve|Quando um evento de calendário futuras está a iniciar um fluxo de accionadores|
|No novo e-mail|Um fluxo de accionadores quando chega um novo e-mail|
|Nos novos itens|Acionou quando é criado um novo item de calendário|
|Em itens atualizados|Acionou quando um item de calendário é modificado|


## <a name="create-a-connection-to-outlookcom"></a>Criar uma ligação ao Outlook.com
Para criar aplicações de lógica com Outlook.com, tem primeiro criar uma **ligação** , em seguida, fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer as credenciais do Outlook.com|
Depois de criar a ligação, pode utilizá-lo para executar as ações e ouvir para accionadores descritos neste artigo.

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.  

## <a name="reference-for-outlookcom"></a>Referência do Outlook.com
Aplica-se para a versão: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
No evento iniciar em breve: quando um evento de calendário futuras está a iniciar um fluxo de accionadores 

```GET: /Events/OnUpcomingEvents``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|consulta|nenhum|Identificador exclusivo do calendário|
|lookAheadTimeInMinutes|número inteiro|nenhum|consulta|15|O tempo (em minutos) para procurar com antecedência eventos futuros|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|202|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="getemails"></a>GetEmails
Obter e-mails: obtém os e-mails a partir de uma pasta 

```GET: /Mail``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|caminhopasta|cadeia|nenhum|consulta|Caixa de entrada|Caminho da pasta para recuperar mensagens de correio eletrónico (predefinido: 'A receber')|
|início|número inteiro|nenhum|consulta|10|Número de mensagens de correio eletrónico para obter (predefinido: 10)|
|fetchOnlyUnread|Booleano|nenhum|consulta|VERDADEIRO|Obter apenas não lidas mensagens de correio eletrónico?|
|includeAttachments|Booleano|nenhum|consulta|FALSO|Se definir a anexos for verdadeiros, também será serem obtido juntamente com o e-mail|
|searchQuery|cadeia|nenhum|consulta|nenhum|Consulta de pesquisa para filtrar mensagens de correio eletrónico|
|Ignorar|número inteiro|nenhum|consulta|0|Número de mensagens de correio eletrónico para ignorar (predefinido: 0)|
|skipToken|cadeia|nenhum|consulta|nenhum|Ignorar token a obtenção nova página|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="sendemail"></a>EnviarCorreioElectrónico
Enviar mensagens de e-mail: envia uma mensagem de e-mail 

```POST: /Mail``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|emailMessage| |Sim|corpo|nenhum|Mensagem de correio electrónico|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="deleteemail"></a>DeleteEmail
Eliminar e-mail: elimina uma mensagem de e-mail por id 

```DELETE: /Mail/{messageId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia|Sim|caminho|nenhum|ID do correio eletrónico para eliminar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="markasread"></a>MarkAsRead
Marcar como lida: marcas uma mensagem de e-mail como ter sido lida 

```POST: /Mail/MarkAsRead/{messageId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia|Sim|caminho|nenhum|ID do correio eletrónico marcado como lido|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="replyto"></a>ReplyTo
Responder ao e-mail: respostas a uma mensagem de e-mail 

```POST: /Mail/ReplyTo/{messageId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia|Sim|caminho|nenhum|ID de e-mail para responder a|
|comentário|cadeia|Sim|consulta|nenhum|Comentário de resposta|
|replyAll|Booleano|nenhum|consulta|FALSO|Responder a todos os destinatários|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="getattachment"></a>GetAttachment
Obter o anexo: obtém anexo de e-mail por id 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia|Sim|caminho|nenhum|ID do correio eletrónico|
|attachmentId|cadeia|Sim|caminho|nenhum|ID do anexo para o transferir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="onnewemail"></a>OnNewEmail
Na nova mensagem de correio: um fluxo de accionadores quando chega um novo e-mail 

```GET: /Mail/OnNewEmail``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|caminhopasta|cadeia|nenhum|consulta|Caixa de entrada|Pasta de correio eletrónico para obter (predefinido: pasta a receber)|
|para|cadeia|nenhum|consulta|nenhum|Endereços de e-mail de destinatários|
|a partir do|cadeia|nenhum|consulta|nenhum|A partir de endereço|
|importância|cadeia|nenhum|consulta|Normal|Importância da mensagem de correio electrónico (como sendo de alta, Normal, baixa) (predefinido: Normal)|
|fetchOnlyWithAttachment|Booleano|nenhum|consulta|FALSO|Obter apenas mensagens de correio eletrónico com um anexo|
|includeAttachments|Booleano|nenhum|consulta|FALSO|Incluir anexos|
|subjectFilter|cadeia|nenhum|consulta|nenhum|Cadeia para procurar no assunto|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|202|Aceites|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
Enviar e-mail com opções: enviar uma mensagem de e-mail com várias opções e aguarde que o destinatário apenas responder novamente com uma das opções 

```POST: /mailwithoptions/$subscriptions``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Sim|corpo|nenhum|Pedido de subscrição para o e-mail de opções|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|201|Subscrição criada|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="sendapprovalmail"></a>SendApprovalMail
Enviar e-mail aprovação: enviar uma mensagem de e-mail de aprovação e aguardar uma resposta a partir do destinatário 

```POST: /approvalmail/$subscriptions``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Sim|corpo|nenhum|Pedido de subscrição para o e-mail de aprovação|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|201|Subscrição criada|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="calendargettables"></a>CalendarGetTables
Obter calendários: obtém calendários 

```GET: /datasets/calendars/tables``` 

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendargetitems"></a>CalendarGetItems
Obter eventos: obtém itens a partir de um calendário 

```GET: /datasets/calendars/tables/{table}/items``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo do calendário para obter|
|$filter|cadeia|nenhum|consulta|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia|nenhum|consulta|nenhum|Uma consulta de OrdenarPor ODATA para especificar a ordem das entradas|
|$skip|número inteiro|nenhum|consulta|nenhum|Número de entradas para ignorar (predefinição = 0)|
|$top|número inteiro|nenhum|consulta|nenhum|Número máximo de entradas para obter (predefinição = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendarpostitem"></a>CalendarPostItem
Criar evento: cria um novo evento 

```POST: /datasets/calendars/tables/{table}/items``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de calendário|
|item| |Sim|corpo|nenhum|Item de calendário para criar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendargetitem"></a>CalendarGetItem
Obter o evento: obtém um item específico a partir de um calendário 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de calendário|
|ID|cadeia|Sim|caminho|nenhum|Identificador exclusivo de um item de calendário para obter|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
Eliminar o evento: Elimina um item de calendário 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de calendário|
|ID|cadeia|Sim|caminho|nenhum|Identificador exclusivo do item de calendário para eliminar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendarpatchitem"></a>CalendarPatchItem
Eventos de atualização: parcialmente atualiza um item de calendário 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de calendário|
|ID|cadeia|Sim|caminho|nenhum|Identificador exclusivo do item de calendário para atualizar|
|item| |Sim|corpo|nenhum|Item de calendário para atualizar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Nos novos itens: acionou quando é criado um novo item de calendário 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de calendário|
|$filter|cadeia|nenhum|consulta|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia|nenhum|consulta|nenhum|Uma consulta de OrdenarPor ODATA para especificar a ordem das entradas|
|$skip|número inteiro|nenhum|consulta|nenhum|Número de entradas para ignorar (predefinição = 0)|
|$top|número inteiro|nenhum|consulta|nenhum|Número máximo de entradas para obter (predefinição = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
No atualizados itens: acionou quando um item de calendário é modificado 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de calendário|
|$filter|cadeia|nenhum|consulta|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia|nenhum|consulta|nenhum|Uma consulta de OrdenarPor ODATA para especificar a ordem das entradas|
|$skip|número inteiro|nenhum|consulta|nenhum|Número de entradas para ignorar (predefinição = 0)|
|$top|número inteiro|nenhum|consulta|nenhum|Número máximo de entradas para obter (predefinição = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="contactgettables"></a>ContactGetTables
Obter as pastas de contactos: obtém pastas de contactos 

```GET: /datasets/contacts/tables``` 

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="contactgetitems"></a>ContactGetItems
Obter contactos: obtém contactos a partir de uma pasta de contactos 

```GET: /datasets/contacts/tables/{table}/items``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo da pasta de contactos para obter|
|$filter|cadeia|nenhum|consulta|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia|nenhum|consulta|nenhum|Uma consulta de OrdenarPor ODATA para especificar a ordem das entradas|
|$skip|número inteiro|nenhum|consulta|nenhum|Número de entradas para ignorar (predefinição = 0)|
|$top|número inteiro|nenhum|consulta|nenhum|Número máximo de entradas para obter (predefinição = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="contactpostitem"></a>ContactPostItem
Criar contacto: cria um novo contacto 

```POST: /datasets/contacts/tables/{table}/items``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de uma pasta de contactos|
|item| |Sim|corpo|nenhum|Contacto para criar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="contactgetitem"></a>ContactGetItem
Obter contactos: obtém um contacto específico a partir de uma pasta de contactos 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de uma pasta de contactos|
|ID|cadeia|Sim|caminho|nenhum|Identificador exclusivo de um contacto para obter|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="contactdeleteitem"></a>ContactDeleteItem
Eliminar contacto: Elimina um contacto 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de uma pasta de contactos|
|ID|cadeia|Sim|caminho|nenhum|Identificador exclusivo do contacto a eliminar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="contactpatchitem"></a>ContactPatchItem
Atualizar contacto: parcialmente atualiza um contacto 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia|Sim|caminho|nenhum|Identificador exclusivo de uma pasta de contactos|
|ID|cadeia|Sim|caminho|nenhum|Identificador exclusivo do contacto para atualizar|
|item| |Sim|corpo|nenhum|Item de contacto para atualizar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [cadeia, objeto]]


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="object"></a>Objecto


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|



### <a name="sendmessage"></a>EnviarMensagem


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Anexos|matriz|N |
|A partir do|cadeia|N |
|Cc|cadeia|N |
|Bcc|cadeia|N |
|Assunto|cadeia|Sim |
|Corpo|cadeia|Sim |
|Importância|cadeia|N |
|IsHtml|Booleano|N |
|Para|cadeia|Sim |



### <a name="sendattachment"></a>SendAttachment


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|@odata.type|cadeia|N |
|Nome|cadeia|Sim |
|ContentBytes|cadeia|Sim |



### <a name="receivemessage"></a>ReceiveMessage


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|N |
|IsRead|Booleano|N |
|HasAttachment|Booleano|N |
|DateTimeReceived|cadeia|N |
|Anexos|matriz|N |
|A partir do|cadeia|N |
|Cc|cadeia|N |
|Bcc|cadeia|N |
|Assunto|cadeia|Sim |
|Corpo|cadeia|Sim |
|Importância|cadeia|N |
|IsHtml|Booleano|N |
|Para|cadeia|Sim |



### <a name="receiveattachment"></a>ReceiveAttachment


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|Sim |
|ContentType|cadeia|Sim |
|@odata.type|cadeia|N |
|Nome|cadeia|Sim |
|ContentBytes|cadeia|Sim |



### <a name="digestmessage"></a>DigestMessage


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Assunto|cadeia|Sim |
|Corpo|cadeia|N |
|Importância|cadeia|N |
|Resumo|matriz|Sim |
|Anexos|matriz|N |
|Para|cadeia|Sim |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="datasetsmetadata"></a>DataSetsMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|em tabela|não definido|N |
|blob|não definido|N |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|origem|cadeia|N |
|DisplayName em grupos|cadeia|N |
|urlEncoding|cadeia|N |
|tableDisplayName|cadeia|N |
|tablePluralName|cadeia|N |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|origem|cadeia|N |
|DisplayName em grupos|cadeia|N |
|urlEncoding|cadeia|N |



### <a name="tablemetadata"></a>TableMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|nome|cadeia|N |
|título|cadeia|N |
|permissão de ms x|cadeia|N |
|capacidades de ms x|não definido|N |
|esquema|não definido|N |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|sortRestrictions|não definido|N |
|filterRestrictions|não definido|N |
|filterFunctions|matriz|N |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ordenável|Booleano|N |
|unsortableProperties|matriz|N |
|ascendingOnlyProperties|matriz|N |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|filtráveis|Booleano|N |
|nonFilterableProperties|matriz|N |
|requiredProperties|matriz|N |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|NotificationUrl|cadeia|N |
|Mensagem|não definido|N |



### <a name="messagewithoptions"></a>MessageWithOptions


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Assunto|cadeia|Sim |
|Opções|cadeia|Sim |
|Corpo|cadeia|N |
|Importância|cadeia|N |
|Anexos|matriz|N |
|Para|cadeia|Sim |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|N |
|recurso|cadeia|N |
|notificationType|cadeia|N |
|notificationUrl|cadeia|N |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|NotificationUrl|cadeia|N |
|Mensagem|não definido|N |



### <a name="approvalmessage"></a>ApprovalMessage


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Assunto|cadeia|Sim |
|Opções|cadeia|Sim |
|Corpo|cadeia|N |
|Importância|cadeia|N |
|Anexos|matriz|N |
|Para|cadeia|Sim |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|SelectedOption|cadeia|N |



### <a name="tableslist"></a>TablesList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="table"></a>Tabela


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Nome|cadeia|N |
|DisplayName em grupos|cadeia|N |



### <a name="item"></a>Item


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ItemInternalId|cadeia|N |



### <a name="calendaritemslist"></a>CalendarItemsList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="calendaritem"></a>CalendarItem


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ItemInternalId|cadeia|N |



### <a name="contactitemslist"></a>ContactItemsList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="contactitem"></a>ContactItem


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ItemInternalId|cadeia|N |



### <a name="datasetslist"></a>DataSetsList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="dataset"></a>Conjunto de dados


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Nome|cadeia|N |
|DisplayName em grupos|cadeia|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)