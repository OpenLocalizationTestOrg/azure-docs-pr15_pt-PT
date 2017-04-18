<properties
pageTitle="Adicionar o conector do Yammer no seu aplicações lógica | Microsoft Azure"
description="Descrição geral do conector do Yammer com parâmetros REST API"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-yammer-connector"></a>Começar a trabalhar com o conector do Yammer

Ligar para o Yammer para as conversações por acesso na rede da sua empresa.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica.

Com o Yammer, pode:

- Crie o seu fluxo de negócio com base em dados que obtém do Yammer. 
- Utilizar accionadores para quando existe uma nova mensagem de um grupo ou um feed o seguinte.
- Utilize as ações para publicar uma mensagem, obter todas as mensagens e muito mais. Estas ações obtém uma resposta e, em seguida, disponibilizam o resultado para outras ações. Por exemplo, quando for apresentada uma nova mensagem, pode enviar uma mensagem de e-mail com o Office 365.

Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções
Yammer inclui as seguintes accionadores e ações. 

Accionador | Ações
--- | ---
<ul><li>Quando existe uma nova mensagem num grupo</li><li>Quando existe uma nova mensagem no meu seguinte feed</li></ul>| <ul><li>Obter todas as mensagens</li><li>Recebe mensagens num grupo</li><li>Feed de mensagens a partir do meu seguinte obtém</li><li>Mensagem de mensagem</li><li>Quando existe uma nova mensagem num grupo</li><li>Quando existe uma nova mensagem no meu seguinte feed</li></ul>

Todas as conexões suportam dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-yammer"></a>Criar uma ligação para o Yammer
Para utilizar o conector do Yammer, pode primeiro cria uma **ligação** , em seguida, fornece os detalhes destas propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer as credenciais do Yammer|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="yammer-rest-api-reference"></a>Yammer referência REST API
Esta documentação é para a versão: 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obter todas as mensagens públicas na rede do Yammer do utilizador com sessão iniciada
Corresponde a "Todas" as conversações na interface de web do Yammer.  
```GET: /messages.json```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|older_then|número inteiro|nenhum|consulta|nenhum|Devolve as mensagens mais antigas do que o ID da mensagem especificado como uma cadeia numérica. Isto é útil para paginação mensagens. Por exemplo, se de que está a ver atualmente 20 mensagens e o mais antigo é o número 2912, poderia acrescentar "? older_than = 2912″ ao seu pedido para obter as 20 mensagens antes de aqueles que está a ver.|
|newer_then|número inteiro|nenhum|consulta|nenhum|Devolve as mensagens mais recentes do que o ID da mensagem especificado como uma cadeia numérica. Isto deve ser utilizado quando de consulta para novas mensagens. Se estiver à procura de mensagens e as mensagens mais recentes devolvido é 3516, pode fazer um pedido de com o parâmetro "? newer_than = 3516″ para se certificar de que não obter duplicadas cópias das mensagens já na sua página.|
|limite|número inteiro|nenhum|consulta|nenhum|Devolve o número especificado de mensagens.|
|página|número inteiro|nenhum|consulta|nenhum|Obter a página especificada. Se devolvida são maiores que o limite de dados, pode utilizar este campo para aceder a páginas subsequentes|


### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|408|Tempo limite do pedido|
|429|Demasiados pedidos|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|503|Yammer serviço indisponível|
|504|Tempo limite do gateway|
|predefinido|A operação falhou.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Publicar uma mensagem a um grupo ou toda a empresa Feed
Se for fornecida ID de grupo, mensagem será registada para o grupo especificado else que será registado no Feed de empresa de todos os.    
```POST: /messages.json``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|introdução| |Sim|corpo|nenhum|Pedido de mensagem de mensagem|


### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|



## <a name="object-definitions"></a>Definições de objecto

#### <a name="message-yammer-message"></a>Mensagem: Mensagem no Yammer

| Nome | Tipo de dados | Obrigatório |
|---|---| --- | 
|ID|número inteiro|nenhum|
|content_excerpt|cadeia|nenhum|
|sender_id|número inteiro|nenhum|
|replied_to_id|número inteiro|nenhum|
|created_at|cadeia|nenhum|
|network_id|número inteiro|nenhum|
|message_type|cadeia|nenhum|
|sender_type|cadeia|nenhum|
|URL|cadeia|nenhum|
|web_url|cadeia|nenhum|
|group_id|número inteiro|nenhum|
|corpo|não definido|nenhum|
|thread_id|número inteiro|nenhum|
|direct_message|Booleano|nenhum|
|client_type|cadeia|nenhum|
|client_url|cadeia|nenhum|
|idioma|cadeia|nenhum|
|notified_user_ids|matriz|nenhum|
|privacidade|cadeia|nenhum|
|liked_by|não definido|nenhum|
|system_message|Booleano|nenhum|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: Representa um pedido de mensagem para conector do Yammer publicar no yammer

| Nome | Tipo de dados | Obrigatório |
|---|---| --- | 
|corpo|cadeia|Sim|
|group_id|número inteiro|nenhum|
|replied_to_id|número inteiro|nenhum|
|direct_to_id|número inteiro|nenhum|
|difusão|Booleano|nenhum|
|tópico1|cadeia|nenhum|
|tópico2|cadeia|nenhum|
|topic3|cadeia|nenhum|
|topic4|cadeia|nenhum|
|topic5|cadeia|nenhum|
|topic6|cadeia|nenhum|
|topic7|cadeia|nenhum|
|topic8|cadeia|nenhum|
|topic9|cadeia|nenhum|
|topic10|cadeia|nenhum|
|topic11|cadeia|nenhum|
|topic12|cadeia|nenhum|
|topic13|cadeia|nenhum|
|topic14|cadeia|nenhum|
|topic15|cadeia|nenhum|
|topic16|cadeia|nenhum|
|topic17|cadeia|nenhum|
|topic18|cadeia|nenhum|
|topic19|cadeia|nenhum|
|topic20|cadeia|nenhum|

#### <a name="messagelist-list-of-messages"></a>MessageList: Lista de mensagens

| Nome | Tipo de dados | Obrigatório |
|---|---| --- | 
|mensagens|matriz|nenhum|


#### <a name="messagebody-message-body"></a>MessageBody: Corpo da mensagem

| Nome | Tipo de dados | Obrigatório |
|---|---| --- | 
|analisado|cadeia|nenhum|
|simples|cadeia|nenhum|
|formatado|cadeia|nenhum|

#### <a name="likedby-liked-by"></a>LikedBy: Gosto por

| Nome | Tipo de dados | Obrigatório |
|---|---| --- | 
|contar|número inteiro|nenhum|
|nomes|matriz|nenhum|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: Gosto por

| Nome | Tipo de dados | Obrigatório |
|---|---| --- | 
|tipo|cadeia|nenhum|
|ID|número inteiro|nenhum|
|full_name|cadeia|nenhum|


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
