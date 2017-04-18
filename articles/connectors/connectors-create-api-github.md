<properties
pageTitle="GitHub | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. GitHub é um repositório de Git baseados na web e serviço de alojamento. Oferece todas as revisão distribuído controlo e origem código gestão (SMS) funcionalidades de Git, bem como adicionar as suas próprias funcionalidades."
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

# <a name="get-started-with-the-github-connector"></a>Começar a trabalhar com o conector de GitHub

GitHub é um repositório de Git baseados na web e serviço de alojamento. Oferece todas as revisão distribuído controlo e origem código gestão (SMS) funcionalidades de Git, bem como adicionar as suas próprias funcionalidades.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. 

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

A conexão GitHub pode ser utilizada como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector de GitHub tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="github-actions"></a>Ações de GitHub
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Cria um problema|
### <a name="github-triggers"></a>GitHub accionadores
Pode ouvir para estes evento (s):

|Accionador | Descrição|
|--- | ---|
|Quando um problema for aberto|É aberto um problema|
|Quando um problema é fechado|Um problema estiver fechado|
|Quando é atribuído um problema|É atribuído a um problema|


## <a name="create-a-connection-to-github"></a>Criar uma ligação a GitHub
Para criar aplicações de lógica com GitHub, tem primeiro criar uma **ligação** , em seguida, fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer GitHub credenciais|
Depois de criar a ligação, pode utilizá-lo para executar as ações e ouvir para accionadores descritos neste artigo. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="reference-for-github"></a>Referência para GitHub
Aplica-se para a versão: 1.0

## <a name="createissue"></a>CreateIssue
Criar um problema: cria um problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|repositoryOwner|cadeia|Sim|caminho|nenhum|Proprietário de repositório|
|repositoryName|cadeia|Sim|caminho|nenhum|Nome do repositório|
|issueBasicDetails| |Sim|corpo|nenhum|Detalhes do problema|

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


## <a name="issueopened"></a>IssueOpened
Quando um problema for aberto: é aberto um problema 

```GET: /trigger/issueOpened``` 

Não existem parâmetros para esta chamada
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


## <a name="issueclosed"></a>IssueClosed
Quando estiver fechado um problema: um problema estiver fechado 

```GET: /trigger/issueClosed``` 

Não existem parâmetros para esta chamada
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


## <a name="issueassigned"></a>IssueAssigned
Quando é atribuído um problema: é atribuído a um problema 

```GET: /trigger/issueAssigned``` 

Não existem parâmetros para esta chamada
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

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|título|cadeia|Sim |
|corpo|cadeia|Sim |
|representante|cadeia|Sim |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|título|cadeia|Sim |
|corpo|cadeia|Sim |
|representante|cadeia|Sim |
|número|cadeia|N |
|Estado|cadeia|N |
|created_at|cadeia|N |
|repository_url|cadeia|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)