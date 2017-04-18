<properties
pageTitle=" Utilizar o conector de margem nas suas aplicações de lógica | Microsoft Azure"
description="Começar a utilizar o conector de margem nas suas aplicações de lógica de serviço de aplicação do Microsoft Azure"
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

# <a name="get-started-with-the-slack-connector"></a>Começar a trabalhar com o conector de margem

Folga é uma ferramenta de comunicação de equipa, que reúne todas as comunicações de equipa numa colocam, instantaneamente pesquisável e disponíveis onde quer que vá.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica.

Com o conector de margem, pode:

* Utilizá-la para criar aplicações de lógica

Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Vamos falar sobre accionadores e acções

O conector de folga pode ser utilizado como uma ação; Não existem sem accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector de folga tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="slack-actions"></a>Ações de margem
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|PostMessage|Publicar uma mensagem para um canal especificado.|
## <a name="create-a-connection-to-slack"></a>Criar uma ligação a margem
Para utilizar o conector de margem, pode primeiro cria uma **ligação** , em seguida, fornece os detalhes destas propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer as credenciais de margem|

Siga estes passos para iniciar sessão no margem e concluir a configuração da margem **ligação** na sua aplicação de lógica:

1. Selecione **Periodicidade**
2. Selecione uma **frequência** e introduza um **intervalo**
3. Selecione **Adicionar uma ação**  
![Configurar a margem][1]  
4. Introduza a margem na caixa de pesquisa e aguarde que a pesquisa para devolver todas as entradas com folga no nome
5. Selecione a **margem - mensagem mensagem**
6. Selecione **Iniciar sessão no folga**:  
![Configurar a margem][2]
7. Forneça as credenciais folga de início de sessão autorizar a aplicação    
![Configurar a margem][3]  
8. Será redirecionado para a página de início de sessão da sua organização. **Autorize** Margem para interagir com a sua aplicação de lógica:      
![Configurar a margem][5] 
9. Depois de concluída a autorização será redirecionado para a sua aplicação de lógica para executá-la ao configurar a secção **folga - obter todas as mensagens** . Adicione outros accionadores e ações que necessita.  
![Configurar a margem][6]
10. Guarde o seu trabalho ao selecionar a opção **Guardar** na barra de menus acima.


>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="slack-rest-api-reference"></a>Referência de REST API folga
#### <a name="this-documentation-is-for-version-10"></a>Esta documentação é para a versão: 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Publicar uma mensagem para um canal especificado.
**```POST: /chat.postMessage```** 



| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|canal|cadeia|Sim|consulta|nenhum|Canal, grupo privado ou canal de MI para enviar a mensagem para. Pode ser um nome (ex: #general) ou um ID codificado.|
|texto|cadeia|Sim|consulta|nenhum|Texto da mensagem para enviar. Para opções de formatação, consulte o artigo https://api.slack.com/docs/formatting.|
|nome de utilizador|cadeia|nenhum|consulta|nenhum|Nome do bot|
|as_user|Booleano|nenhum|consulta|nenhum|Passar verdadeiro para publicar a mensagem como o utilizador autenticado, em vez de como um bot|
|analisar|cadeia|nenhum|consulta|nenhum|Altere a forma como as mensagens são tratadas. Para obter detalhes, consulte o artigo https://api.slack.com/docs/formatting.|
|link_names|número inteiro|nenhum|consulta|nenhum|Localizar e associar os nomes de canal e nomes de utilizador.|
|unfurl_links|Booleano|nenhum|consulta|nenhum|Passar TRUE para ativar unfurling de principalmente conteúdo de baseado em texto.|
|unfurl_media|Booleano|nenhum|consulta|nenhum|Passe falso para desativar unfurling de conteúdo multimédia.|
|icon_url|cadeia|nenhum|consulta|nenhum|URL para uma imagem para utilizar como um ícone para esta mensagem|
|icon_emoji|cadeia|nenhum|consulta|nenhum|Emoji para utilizar como um ícone para esta mensagem|


### <a name="here-are-the-possible-responses"></a>Eis as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|400|Pedido incorrecto|
|408|Tempo limite do pedido|
|429|Demasiados pedidos|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|503|Folga serviço indisponível|
|504|Tempo limite do gateway|
|predefinido|A operação falhou.|
------



## <a name="object-definitions"></a>Definições de objeto: 

 **Mensagem**: Yammer mensagem

Propriedades necessárias para a mensagem:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|ID|número inteiro|
|content_excerpt|cadeia|
|sender_id|número inteiro|
|replied_to_id|número inteiro|
|created_at|cadeia|
|network_id|número inteiro|
|message_type|cadeia|
|sender_type|cadeia|
|URL|cadeia|
|web_url|cadeia|
|group_id|número inteiro|
|corpo|não definido|
|thread_id|número inteiro|
|direct_message|Booleano|
|client_type|cadeia|
|client_url|cadeia|
|idioma|cadeia|
|notified_user_ids|matriz|
|privacidade|cadeia|
|liked_by|não definido|
|system_message|Booleano|



 **PostOperationRequest**: representa um pedido de mensagem para conector do Yammer publicar no yammer

Propriedades necessárias para PostOperationRequest:

corpo

**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|corpo|cadeia|
|group_id|número inteiro|
|replied_to_id|número inteiro|
|direct_to_id|número inteiro|
|difusão|Booleano|
|tópico1|cadeia|
|tópico2|cadeia|
|topic3|cadeia|
|topic4|cadeia|
|topic5|cadeia|
|topic6|cadeia|
|topic7|cadeia|
|topic8|cadeia|
|topic9|cadeia|
|topic10|cadeia|
|topic11|cadeia|
|topic12|cadeia|
|topic13|cadeia|
|topic14|cadeia|
|topic15|cadeia|
|topic16|cadeia|
|topic17|cadeia|
|topic18|cadeia|
|topic19|cadeia|
|topic20|cadeia|



 **MessageList**: lista de mensagens

Propriedades necessárias para MessageList:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|mensagens|matriz|



 **MessageBody**: corpo da mensagem

Propriedades necessárias para MessageBody:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|analisado|cadeia|
|simples|cadeia|
|formatado|cadeia|



 **LikedBy**: gosto por

Propriedades necessárias para LikedBy:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|contar|número inteiro|
|nomes|matriz|



 **YammmerEntity**: gosto por

Propriedades necessárias para YammmerEntity:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|tipo|cadeia|
|ID|número inteiro|
|full_name|cadeia|


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Definições de objeto: 

 **WebResultModel**: resultados de pesquisa do Bing web

Propriedades necessárias para WebResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia|
|Descrição|cadeia|
|DisplayUrl|cadeia|
|ID|cadeia|
|FullUrl|cadeia|



 **VideoResultModel**: resultados de pesquisa de vídeos do Bing

Propriedades necessárias para VideoResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia|
|DisplayUrl|cadeia|
|ID|cadeia|
|MediaUrl|cadeia|
|Tempo de execução|número inteiro|
|Miniatura|não definido|



 **ThumbnailModel**: propriedades de miniatura do elemento multimédia

Propriedades necessárias para ThumbnailModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|MediaUrl|cadeia|
|ContentType|cadeia|
|Largura|número inteiro|
|Altura|número inteiro|
|FileSize|número inteiro|



 **ImageResultModel**: resultados de pesquisa do Bing imagens

Propriedades necessárias para ImageResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia|
|DisplayUrl|cadeia|
|ID|cadeia|
|MediaUrl|cadeia|
|SourceUrl|cadeia|
|Miniatura|não definido|



 **NewsResultModel**: resultados de pesquisa do Bing notícias

Propriedades necessárias para NewsResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia|
|Descrição|cadeia|
|DisplayUrl|cadeia|
|ID|cadeia|
|Origem|cadeia|
|Data|cadeia|



 **SpellResultModel**: resultados de sugestões de ortografia do Bing

Propriedades necessárias para SpellResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|ID|cadeia|
|Valor|cadeia|



 **RelatedSearchResultModel**: Bing relacionadas com os resultados da pesquisa

Propriedades necessárias para RelatedSearchResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia|
|ID|cadeia|
|BingUrl|cadeia|



 **CompositeSearchResultModel**: resultados de pesquisa compostas do Bing

Propriedades necessárias para CompositeSearchResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|WebResultsTotal|número inteiro|
|ImageResultsTotal|número inteiro|
|VideoResultsTotal|número inteiro|
|NewsResultsTotal|número inteiro|
|SpellSuggestionsTotal|número inteiro|
|WebResults|matriz|
|ImageResults|matriz|
|VideoResults|matriz|
|NewsResults|matriz|
|SpellSuggestionResults|matriz|
|RelatedSearchResults|matriz|


## <a name="object-definitions"></a>Definições de objeto: 

 **PostOperationResponse**: representa resposta de operação de mensagem de margem conexão para lançamento na margem

Propriedades necessárias para PostOperationResponse:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|OK|Booleano|
|canal|cadeia|
|TS|cadeia|
|mensagem|não definido|
|erro|cadeia|



 **MessageItem**: uma mensagem de canal.

Propriedades necessárias para MessageItem:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|texto|cadeia|
|ID|cadeia|
|utilizador|cadeia|
|criado|número inteiro|
|eliminados is_user|Booleano|


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
