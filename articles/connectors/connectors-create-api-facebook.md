<properties
    pageTitle="Adicionar o conector do Facebook nas suas aplicações de lógica | Microsoft Azure"
    description="Descrição geral da conexão Facebook com parâmetros REST API"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-facebook-connector"></a>Introdução ao conector do Facebook
Ligar ao Facebook e publicar uma linha cronológica, obtenha uma página feed e muito mais. 

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica.


Com o Facebook, pode:

- Crie o seu fluxo de negócio com base em dados que obtém do Facebook. 
- Utilize um acionador de quando é recebida uma nova mensagem.
- Ações de utilização que publicam à sua linha cronológica, obtenha uma página feed e muito mais. Estas ações obtém uma resposta e, em seguida, disponibilizam o resultado para outras ações. Por exemplo, quando existe uma nova mensagem na sua linha cronológica, pode tirar essa mensagem e enviá-lo para o seu feed de Twitter. 



Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções
O conector do Facebook inclui as seguintes acionador e ações. 

| Accionadores | Ações|
| --- | --- |
| <ul><li>Quando existe uma mensagem nova no meu da linha cronológica</li></ul> |<ul><li>Obter feed a partir do meu da linha cronológica</li><li>Publicar no meu da linha cronológica</li><li>Quando existe uma mensagem nova no meu da linha cronológica</li><li>Página feed</li><li>Obter a linha cronológica de utilizador</li><li>Publicar a página</li></ul>

Todas as conexões suportam dados nos formatos JSON e XML.

## <a name="create-a-connection-to-facebook"></a>Criar uma ligação ao Facebook
Quando adiciona este conector às suas aplicações de lógica, tem de autorizar lógica aplicações para ligar à sua Facebook.

1. Inicie sessão sua conta do Facebook
2. Selecione **autorizar**e permitir que as aplicações de lógica ligar e utilizar o Facebook. 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Pode utilizar esta ligação Facebook mesmo nas outras aplicações de lógica.

## <a name="swagger-rest-api-reference"></a>Referência de REST API swagger
Aplica-se para a versão: 1.0.

### <a name="get-feed-from-my-timeline"></a>Obter feed a partir do meu da linha cronológica
Obtém os feeds da linha de tempo do utilizador com sessão iniciada.  
```GET: /me/feed```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|campos|cadeia|nenhum|consulta|nenhum |Especifique os campos que pretende que sejam devolvidos. Exemplo (id, nome, imagem).|
|limite|número inteiro|nenhum|consulta| nenhum|Número máximo de mensagens a serem obtidos|
|com|cadeia|nenhum|consulta| nenhum|Restringir a lista de mensagens apenas com a localização anexada.|
|filtro|cadeia|nenhum|consulta| nenhum|Obtenha apenas as mensagens que correspondem a um filtro da cadeia específico.|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


### <a name="post-to-my-timeline"></a>Publicar no meu da linha cronológica
Publicar uma mensagem de estado para a linha de tempo do utilizador com sessão iniciada.  
```POST: /me/feed```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar|cadeia |Sim|corpo|nenhum |Nova mensagem para ser publicado|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>Quando existe uma mensagem nova no meu da linha cronológica
Um novo fluxo de accionadores quando existe uma mensagem nova linha de tempo do utilizador com sessão iniciada.  
```GET: /trigger/me/feed```

Não existem parâmetros. 

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


### <a name="get-page-feed"></a>Página feed
Obter novas publicações a partir do feed de uma página especificado.  
```GET: /{pageId}/feed```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|pageId|cadeia|Sim|caminho| nenhum|ID da página a partir do qual tem mensagens a serem obtidos.|
|limite|número inteiro|nenhum|consulta| nenhum|Número máximo de mensagens a serem obtidos|
|include_hidden|Booleano|nenhum|consulta|nenhum |Se pretende ou não incluir qualquer mensagens que tenham sido oculto por página|
|campos|cadeia|nenhum|consulta|nenhum |Especifique os campos que pretende que sejam devolvidos. Exemplo (id, nome, imagem).|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


### <a name="get-user-timeline"></a>Obter a linha cronológica de utilizador
Obter as mensagens da linha cronológica de um utilizador.  
```GET: /{userId}/feed```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID de utilizador|cadeia|Sim|caminho|nenhum |ID de utilizador cuja linha cronológica tem de ser obtidos.|
|limite|número inteiro|nenhum|consulta|nenhum |Número máximo de mensagens a serem obtidos|
|com|cadeia|nenhum|consulta|nenhum |Restringir a lista de mensagens apenas com a localização anexada.|
|filtro|cadeia|nenhum|consulta| nenhum|Obtenha apenas as mensagens que correspondem a um filtro da cadeia específico.|
|campos|cadeia|nenhum|consulta| nenhum|Especifique os campos que pretende que sejam devolvidos. Exemplo (id, nome, imagem).|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


### <a name="post-to-page"></a>Publicar a página
Publicar uma mensagem a uma página do Facebook como o utilizador com sessão iniciada.  
```POST: /{pageId}/feed```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|pageId|cadeia|Sim|caminho|nenhum |ID da página para publicar.|
|publicar|muitos |Sim|corpo|nenhum |Nova mensagem para ser publicado.|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto

#### <a name="getfeedresponse"></a>GetFeedResponse

|Nome da propriedade | Tipo de dados | Obrigatório|
|---|---|---|
|dados|matriz|nenhum|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|dados|matriz|nenhum|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: Mostrada uma entrada de um perfil do feed
O perfil pode ser um utilizador, a página, a aplicação ou o grupo. 

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|admin_creator|matriz|nenhum|
|Legenda|cadeia|nenhum|
|created_time|cadeia|nenhum|
|Descrição|cadeia|nenhum|
|feed_targeting|não definido|nenhum|
|a partir do|não definido|nenhum|
|ícone|cadeia|nenhum|
|is_hidden|Booleano|nenhum|
|is_published|Booleano|nenhum|
|ligação|cadeia|nenhum|
|mensagem|cadeia|nenhum|
|nome|cadeia|nenhum|
|object_id|cadeia|nenhum|
|imagem|cadeia|nenhum|
|local|não definido|nenhum|
|privacidade|não definido|nenhum|
|Propriedades|matriz|nenhum|
|origem|cadeia|nenhum|
|status_type|cadeia|nenhum|
|história|cadeia|nenhum|
|filtragem|não definido|nenhum|
|para|matriz|nenhum|
|tipo|cadeia|nenhum|
|updated_time|cadeia|nenhum|
|with_tags|não definido|nenhum|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: Mostrada uma entrada de um perfil do feed
O perfil pode ser um utilizador, a página, a aplicação ou o grupo.

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|created_time|cadeia|nenhum|
|a partir do|não definido|nenhum|
|mensagem|cadeia|nenhum|
|tipo|cadeia|nenhum|

#### <a name="adminitem"></a>AdminItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|ligação|cadeia|nenhum|

#### <a name="propertyitem"></a>PropertyItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|nome|cadeia|nenhum|
|texto|cadeia|nenhum|
|href da|cadeia|nenhum|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|mensagem|cadeia|Sim|
|ligação|cadeia|nenhum|
|imagem|cadeia|nenhum|
|nome|cadeia|nenhum|
|Legenda|cadeia|nenhum|
|Descrição|cadeia|nenhum|
|local|cadeia|nenhum|
|etiquetas|cadeia|nenhum|
|privacidade|não definido|nenhum|
|object_attachment|cadeia|nenhum|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|mensagem|cadeia|Sim|
|ligação|cadeia|nenhum|
|imagem|cadeia|nenhum|
|nome|cadeia|nenhum|
|Legenda|cadeia|nenhum|
|Descrição|cadeia|nenhum|
|ações|matriz|nenhum|
|local|cadeia|nenhum|
|etiquetas|cadeia|nenhum|
|object_attachment|cadeia|nenhum|
|filtragem|não definido|nenhum|
|feed_targeting|não definido|nenhum|
|publicado|Booleano|nenhum|
|scheduled_publish_time|cadeia|nenhum|
|backdated_time|cadeia|nenhum|
|backdated_time_granularity|cadeia|nenhum|
|child_attachments|matriz|nenhum|
|multi_share_end_card|Booleano|nenhum|

#### <a name="postfeedresponse"></a>PostFeedResponse

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|

#### <a name="profilecollection"></a>ProfileCollection

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|dados|matriz|nenhum|

#### <a name="useritem"></a>UserItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|nome|cadeia|nenhum|
|Apelido|cadeia|nenhum|
|nome|cadeia|nenhum|
|género|cadeia|nenhum|
|sobre|cadeia|nenhum|

#### <a name="actionitem"></a>ActionItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|nome|cadeia|nenhum|
|ligação|cadeia|nenhum|

#### <a name="targetitem"></a>TargetItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|países/regiões|matriz|nenhum|
|nas regiões|matriz|nenhum|
|regiões|matriz|nenhum|
|cidades|matriz|nenhum|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: O objeto que controla as notícias feed de filtragem para esta mensagem
Qualquer pessoa presente nestes grupos é mais provável ver esta mensagem, outras estão menos provável. Aplicável apenas ao páginas.

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|países/regiões|matriz|nenhum|
|regiões|matriz|nenhum|
|cidades|matriz|nenhum|
|age_min|número inteiro|nenhum|
|age_max|número inteiro|nenhum|
|sexos|matriz|nenhum|
|relationship_statuses|matriz|nenhum|
|interested_in|matriz|nenhum|
|college_years|matriz|nenhum|
|interesses|matriz|nenhum|
|relevant_until|número inteiro|nenhum|
|education_statuses|matriz|nenhum|
|nas regiões|matriz|nenhum|

#### <a name="placeitem"></a>PlaceItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|nome|cadeia|nenhum|
|overall_rating|número|nenhum|
|localização|não definido|nenhum|

#### <a name="locationitem"></a>LocationItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|Cidade|cadeia|nenhum|
|país/região|cadeia|nenhum|
|latitude|número|nenhum|
|located_in|cadeia|nenhum|
|longitude|número|nenhum|
|nome|cadeia|nenhum|
|região|cadeia|nenhum|
|Estado|cadeia|nenhum|
|Rua|cadeia|nenhum|
|Postal|cadeia|nenhum|

#### <a name="privacyitem"></a>PrivacyItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|Descrição|cadeia|nenhum|
|valor|cadeia|Sim|
|permitir|cadeia|nenhum|
|Negar|cadeia|nenhum|
|amigos|cadeia|nenhum|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ligação|cadeia|nenhum|
|imagem|cadeia|nenhum|
|image_hash|cadeia|nenhum|
|nome|cadeia|nenhum|
|Descrição|cadeia|nenhum|

#### <a name="postphotorequest"></a>PostPhotoRequest

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|URL|cadeia|Sim|
|Legenda|cadeia|nenhum|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|Sim|
|post_id|cadeia|Sim|

#### <a name="postvideorequest"></a>PostVideoRequest

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|videoData|cadeia|Sim|
|Descrição|cadeia|Sim|
|título|cadeia|Sim|
|uploadedVideoName|cadeia|nenhum|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|dados|não definido|Sim|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|URL|cadeia|Sim|
|is_silhouette|Booleano|Sim|
|altura|cadeia|nenhum|
|largura|cadeia|nenhum|

#### <a name="geteventresponse"></a>GetEventResponse

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|dados|matriz|Sim|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|Sim|
|nome|cadeia|Sim|
|hora_início|cadeia|nenhum|
|end_time|cadeia|nenhum|
|fuso horário|cadeia|nenhum|
|localização|cadeia|nenhum|
|Descrição|cadeia|nenhum|
|ticket_uri|cadeia|nenhum|
|rsvp_status|cadeia|Sim|


## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
