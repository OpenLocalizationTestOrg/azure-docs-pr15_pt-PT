<properties
pageTitle="Utilizar o conector de vídeos do Office 365 no seu aplicações lógica | Microsoft Azure"
description="Começar a utilizar o conector de vídeos do Office 365 no seu aplicações de lógica de serviço da aplicação Microsoft Azure"
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

# <a name="get-started-with-the-office365-video-connector"></a>Começar a trabalhar com o conector de vídeo do Office 365
Ligar ao vídeos do Office 365 para obter informações sobre o Office 365 vídeo, obter uma lista das vídeos e muito mais. Pode ser utilizado o conector de vídeos do Office 365 a partir de:

- Aplicações de lógica 

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. Este conector não é suportado em qualquer versões anteriores do esquema.

Com o vídeos do Office 365, pode:

- Crie o seu fluxo de negócio com base em dados que receber do vídeos do Office 365. 
- Utilize as ações que verificar o estado de portal de vídeo, obter uma lista de todos os vídeo num canal e muito mais. Estas ações obtém uma resposta e, em seguida, disponibilizam o resultado para outras ações. Por exemplo, pode utilizar o conector de pesquisa do Bing para procurar vídeos do Office 365 e, em seguida, utilize o conector de vídeo do Office 365 para obter informações sobre se o vídeo. Se o vídeo corresponde às suas necessidades, pode publicar este vídeo no Facebook. 

Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

O conector de vídeos do Office 365 tem as seguintes ações disponíveis. Não existem sem accionadores.

| Accionadores | Ações|
| --- | --- |
| Nenhum | <ul><li>Verifica o estado de portal de vídeo</li><li>Obter todos os canais visualizáveis</li><li>Obter o url de reprodução do manifesto dos serviços de multimédia do Azure para um vídeo</li><li>Obter a ligação OAuth para aceder a desencriptar o vídeo</li><li>Obtém informações sobre um determinado office365 vídeo</li><li>Listas de que apresentam todos os vídeos do Office 365 num canal</li></ul>

Todas as conexões suportam dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Criar uma ligação ao conector de vídeo do Office 365
Quando adiciona este conector às suas aplicações de lógica, tem de entrada para a sua conta do vídeos do Office 365 e permitir que as aplicações de lógica ligar à sua conta.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Depois de criar a ligação, introduza as propriedades de vídeos do Office 365, como o nome de inquilino ou canal ID. A **REST API referência** neste tópico descreve estas propriedades.

>[AZURE.TIP] Pode utilizar esta ligação de vídeos do Office 365 mesmo nas outras aplicações de lógica.

## <a name="swagger-rest-api-reference"></a>Referência de REST API swagger
Aplica-se para a versão: 1.0.

### <a name="checks-video-portal-status"></a>Verifica o estado de portal de vídeo 
Verifica se o estado de portal de vídeo para ver se vídeo são os serviços activados.  
```GET: /{tenant}/IsEnabled``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|inquilino|cadeia|Sim|caminho|nenhum|O nome de inquilino do diretório do utilizador faz parte de|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|404|Não foi encontrado|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|



### <a name="get-all-viewable-channels"></a>Obter todos os canais visualizáveis 
Obtém todos os canais que o utilizador tem a ver o acesso a.  
```GET: /{tenant}/Channels``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|inquilino|cadeia|Sim|caminho|nenhum|O nome de inquilino do diretório do utilizador faz parte de|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|404|Não foi encontrado|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Listas de que apresentam todos os vídeos do Office 365 num canal 
Listas de que apresentam todos os vídeos do Office 365 num canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|inquilino|cadeia|Sim|caminho|nenhum|O nome de inquilino do diretório do utilizador faz parte de|
|channelId|cadeia|Sim|caminho|nenhum|O id do canal a partir do qual precisa de vídeos para serem obtidos|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|404|Não foi encontrado|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|




### <a name="gets-information-about-a-particular-office365-video"></a>Obtém informações sobre um determinado office365 vídeo 
Obtém informações sobre um determinado office365 vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|inquilino|cadeia|Sim|caminho|nenhum|O nome de inquilino do diretório do utilizador faz parte de|
|channelId|cadeia|Sim|caminho|nenhum|O id de canal|
|videoId|cadeia|Sim|caminho|nenhum|O id de vídeo|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|404|Não foi encontrado|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obter o url de reprodução do manifesto dos serviços de multimédia do Azure para um vídeo 
Obter o url de reprodução do manifesto dos serviços de multimédia do Azure para um vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|inquilino|cadeia|Sim|caminho|nenhum|O nome de inquilino do diretório do utilizador faz parte de|
|channelId|cadeia|Sim|caminho|nenhum|O id de canal|
|videoId|cadeia|Sim|caminho|nenhum|O id de vídeo|
|streamingFormatType|cadeia|Sim|consulta|nenhum|Tipo de formato de transmissão. 1 - suave transmissão ou MPEG-TRAÇO. 0 - HLS transmissão|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|404|Não foi encontrado|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obter a ligação OAuth para aceder a desencriptar o vídeo 
Obter a ligação OAuth para aceder a desencriptar o vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|inquilino|cadeia|Sim|caminho|nenhum|O nome de inquilino do diretório do utilizador faz parte de|
|channelId|cadeia|Sim|caminho|nenhum|O id de canal|
|videoId|cadeia|Sim|caminho|nenhum|O id de vídeo|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi efetuada com êxito|
|400|BadRequest|
|401|Não autorizado|
|404|Não foi encontrado|
|500|Erro de servidor interno|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto

#### <a name="channel-channel-class"></a>Canal: Classe de canal

| Nome | Tipo de dados | Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|Título|cadeia|nenhum|
|Descrição|cadeia|nenhum|


#### <a name="video"></a>Vídeo 

| Nome | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|Título|cadeia|nenhum|
|Descrição|cadeia|nenhum|
|CreationDate|cadeia|nenhum|
|Proprietário|cadeia|nenhum|
|ThumbnailUrl|cadeia|nenhum|
|VideoUrl|cadeia|nenhum|
|VideoDuration|número inteiro|nenhum|
|VideoProcessingStatus|número inteiro|nenhum|
|ViewCount|número inteiro|nenhum|


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
