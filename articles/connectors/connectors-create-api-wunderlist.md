<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Wunderlist fornecem um Gestor de tarefas e a lista todo para ajudar as pessoas despachar os respetivos.  Se estiver a partilhar uma lista de compras por um seus amigos, trabalhar num projeto, ou a planear uma férias, Wunderlist torna mais fácil capturar, partilhar e concluir a sua to¬dos. Wunderlist instantaneamente sincroniza as entre o seu telemóvel, tablet e computador, para que possa aceder a todas as tarefas a partir de qualquer lugar."
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

# <a name="get-started-with-the-wunderlist-connector"></a>Começar a trabalhar com o conector de Wunderlist

Wunderlist fornecem um Gestor de tarefas e a lista todo para ajudar as pessoas despachar os respetivos.  Se estiver a partilhar uma lista de compras por um seus amigos, trabalhar num projeto, ou a planear uma férias, Wunderlist torna mais fácil capturar, partilhar e concluir a sua to¬dos. Wunderlist instantaneamente sincroniza as entre o seu telemóvel, tablet e computador, para que possa aceder a todas as tarefas a partir de qualquer lugar.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. 

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

A conexão Wunderlist pode ser utilizada como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector de Wunderlist tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="wunderlist-actions"></a>Ações de Wunderlist
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Obter as listas associadas à sua conta.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Crie uma lista.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Obtenha tarefas a partir de uma lista específica.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Criar uma tarefa|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Obtenha subtarefas a partir de uma lista específica ou de uma tarefa específica.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Criar uma subtarefa dentro de uma tarefa específica|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recuperar notas para uma lista específica ou uma tarefa específica.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Adicionar uma nota a uma tarefa específica|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Obter comentários de tarefa para uma lista específica ou uma tarefa específica.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Adicionar um comentário a uma tarefa específica|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Obtenha lembretes para uma lista específica ou uma tarefa específica.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Defina um lembrete.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Obter ficheiros para uma lista específica ou uma tarefa específica.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Obtém uma lista específica|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Elimina uma lista|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Atualizar uma lista específica|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Obtém uma tarefa específica|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Atualiza uma tarefa específica|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Elimina uma tarefa específica|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Obtém uma subtarefa específica|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Atualiza uma subtarefa específica|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Elimina uma subtarefa específica|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Obter uma nota específica|
|[ActualizaçãoNota](connectors-create-api-wunderlist.md#updatenote)|Atualizar uma nota específica|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Eliminar uma nota específica|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Obter um comentário de tarefa específica|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Atualizar um lembrete específico|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Eliminar um lembrete específico|
### <a name="wunderlist-triggers"></a>Wunderlist accionadores
Pode ouvir para estes evento (s):

|Accionador | Descrição|
|--- | ---|
|Quando uma tarefa é para conclusão|Quando uma tarefa na lista é apresentada um novo fluxo de accionadores|
|Quando é criada uma nova tarefa|Um novo fluxo de accionadores quando é criada uma nova tarefa na lista|
|Quando ocorre um lembrete|Um novo fluxo de accionadores quando ocorre um lembrete|


## <a name="create-a-connection-to-wunderlist"></a>Criar uma ligação a Wunderlist
Para criar aplicações de lógica com Wunderlist, tem primeiro criar uma **ligação** , em seguida, fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer Wunderlist credenciais|
Depois de criar a ligação, pode utilizá-lo para executar as ações e ouvir para accionadores descritos neste artigo. 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="reference-for-wunderlist"></a>Referência para Wunderlist
Aplica-se para a versão: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Quando uma tarefa vence: quando uma tarefa na lista é apresentada um novo fluxo de accionadores 

```GET: /trigger/tasksdue``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|


## <a name="triggertasknew"></a>TriggerTaskNew
Quando é criada uma nova tarefa: um novo fluxo de accionadores quando é criada uma nova tarefa na lista 

```GET: /trigger/tasksnew``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|


## <a name="triggerreminder"></a>TriggerReminder
Quando ocorre um lembrete: um novo fluxo de accionadores quando ocorre um lembrete 

```GET: /trigger/reminders``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|TASK_ID|número inteiro|nenhum|consulta|nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|


## <a name="retrievelists"></a>RetrieveLists
Obter listas: obter as listas associadas à sua conta. 

```GET: /lists``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="createlist"></a>CreateList
Criar uma lista: criar uma lista. 

```POST: /lists``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar| |Sim|corpo|nenhum|Nova lista para ser criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|predefinido|A operação falhou.|


## <a name="listtasks"></a>ListTasks
Obter tarefas: obter tarefas a partir de uma lista específica. 

```GET: /tasks``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|concluído|Booleano|nenhum|consulta|nenhum|Concluído|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="createtask"></a>CreateTask
Criar uma tarefa: criar uma tarefa 

```POST: /tasks``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar| |Sim|corpo|nenhum|Nova tarefa seja criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="listsubtasks"></a>ListSubTasks
Obter subtarefas: obter subtarefas a partir de uma lista específica ou de uma tarefa específica. 

```GET: /subtasks``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|TASK_ID|número inteiro|nenhum|consulta|nenhum|ID da tarefa|
|concluído|Booleano|nenhum|consulta|nenhum|Concluído|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="createsubtask"></a>CreateSubTask
Criar uma subtarefa: criar uma subtarefa dentro de uma tarefa específica 

```POST: /subtasks``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar| |Sim|corpo|nenhum|Nova subtarefa seja criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="listnotes"></a>ListNotes
Obter notas: Recuperar notas para uma lista específica ou uma tarefa específica. 

```GET: /notes``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|TASK_ID|número inteiro|nenhum|consulta|nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="createnote"></a>CreateNote
Criar uma nota: adicionar uma nota a uma tarefa específica 

```POST: /notes``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar| |Sim|corpo|nenhum|Nova nota seja criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="listcomments"></a>ListComments
Obter comentários da tarefa: obter comentários de tarefa para uma lista específica ou uma tarefa específica. 

```GET: /task_comments``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|TASK_ID|número inteiro|nenhum|consulta|nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="createcomment"></a>CreateComment
Adicionar um comentário a uma tarefa: adicionar um comentário a uma tarefa específica 

```POST: /task_comments``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar| |Sim|corpo|nenhum|Novo comentário de tarefa para ser criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="retrievereminders"></a>RetrieveReminders
Obter lembretes: obter lembretes para uma lista específica ou uma tarefa específica. 

```GET: /reminders``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|TASK_ID|número inteiro|nenhum|consulta|nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="createreminder"></a>CreateReminder
Definir um lembrete: definir um lembrete. 

```POST: /reminders``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|publicar| |Sim|corpo|nenhum|Novo lembrete para ser criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|predefinido|A operação falhou.|


## <a name="retrievefiles"></a>RetrieveFiles
Obter ficheiros: obter ficheiros para uma lista específica ou uma tarefa específica. 

```GET: /files``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|TASK_ID|número inteiro|nenhum|consulta|nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação com êxito|
|400|Pedido incorrecto|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="getlist"></a>GetList
Obter lista: obtém uma lista específica 

```GET: /lists/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho|nenhum|ID de lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="deletelist"></a>DeleteList
Eliminar a lista: elimina uma lista 

```DELETE: /lists/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID de lista|
|revisão|número inteiro|Sim|consulta|nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## <a name="updatelist"></a>UpdateList
Atualizar uma lista: atualizar uma lista específica 

```PATCH: /lists/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID de lista|
|publicar| |Sim|corpo|nenhum|Detalhes da lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="gettask"></a>GetTask
Tarefa de introdução: obtém uma tarefa específica 

```GET: /tasks/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|ID|número inteiro|Sim|caminho|nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="updatetask"></a>UpdateTask
Actualizar uma tarefa: actualiza uma tarefa específica 

```PATCH: /tasks/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|ID|número inteiro|Sim|caminho|nenhum|ID da tarefa|
|publicar| |Sim|corpo|nenhum|Detalhes da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="deletetask"></a>DeleteTask
Eliminar tarefa: elimina uma tarefa específica 

```DELETE: /tasks/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|nenhum|ID de lista|
|ID|número inteiro|Sim|caminho|nenhum|ID da tarefa|
|revisão|número inteiro|Sim|consulta|nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## <a name="getsubtask"></a>GetSubTask
Obter subtarefa: obtém uma subtarefa específica 

```GET: /subtasks/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho|nenhum|ID da subtarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="updatesubtask"></a>UpdateSubTask
Atualizar uma subtarefa: actualiza uma subtarefa específica 

```PATCH: /subtasks/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID da subtarefa|
|publicar| |Sim|corpo|nenhum|Detalhes de subtarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="deletesubtask"></a>DeleteSubTask
Eliminar uma subtarefa: elimina uma subtarefa específica 

```DELETE: /subtasks/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID da subtarefa|
|revisão|número inteiro|Sim|consulta|nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## <a name="getnote"></a>GetNote
Obter uma nota: obter uma nota específica 

```GET: /notes/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho|nenhum|ID de nota|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="updatenote"></a>ActualizaçãoNota
Atualizar uma nota: atualizar uma nota específica 

```PATCH: /notes/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID de nota|
|publicar| |Sim|corpo|nenhum|Detalhes de nota|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="deletenote"></a>DeleteNote
Eliminar uma nota: eliminar uma nota específica 

```DELETE: /notes/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID de nota|
|revisão|número inteiro|Sim|consulta|nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## <a name="getcomment"></a>GetComment
Comentário de tarefa de introdução: obter um comentário de tarefa específica 

```GET: /task_comments/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho|nenhum|ID de comentário|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="updatereminder"></a>UpdateReminder
Atualizar um lembrete: atualizar um lembrete específico 

```PATCH: /reminders/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID de lembrete|
|publicar| |Sim|corpo|nenhum|Detalhes de lembrete|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## <a name="deletereminder"></a>DeleteReminder
Eliminar um lembrete: eliminar um lembrete específico 

```DELETE: /reminders/{id}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|nenhum|ID do lembrete.|
|revisão|número inteiro|Sim|consulta|nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## <a name="object-definitions"></a>Definições de objecto 

### <a name="list"></a>Lista


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|created_at|cadeia|N |
|título|cadeia|N |
|list_type|cadeia|N |
|tipo|cadeia|N |
|revisão|número inteiro|N |



### <a name="createdlist"></a>CreatedList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|created_at|cadeia|N |
|título|cadeia|N |
|revisão|número inteiro|N |
|tipo|cadeia|N |



### <a name="task"></a>Tarefa


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|assignee_id|número inteiro|N |
|assigner_id|número inteiro|N |
|created_at|cadeia|N |
|created_by_id|número inteiro|N |
|due_date|cadeia|N |
|list_id|número inteiro|N |
|revisão|número inteiro|N |
|starred|Booleano|N |
|título|cadeia|N |



### <a name="subtask"></a>Subtarefa


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|TASK_ID|número inteiro|N |
|created_at|cadeia|N |
|created_by_id|número inteiro|N |
|revisão|cadeia|N |
|título|cadeia|N |



### <a name="note"></a>Nota


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|TASK_ID|número inteiro|N |
|conteúdo|cadeia|N |
|created_at|cadeia|N |
|updated_at|cadeia|N |
|revisão|número inteiro|N |



### <a name="comment"></a>Comentário


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|TASK_ID|número inteiro|N |
|revisão|número inteiro|N |
|texto|cadeia|N |
|tipo|cadeia|N |
|created_at|cadeia|N |



### <a name="reminder"></a>Lembrete


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|data|cadeia|N |
|TASK_ID|número inteiro|N |
|revisão|número inteiro|N |
|tipo|cadeia|N |
|created_at|cadeia|N |
|updated_at|cadeia|N |



### <a name="createdreminder"></a>CreatedReminder


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|data|cadeia|N |
|TASK_ID|número inteiro|N |
|revisão|número inteiro|N |
|created_at|cadeia|N |
|updated_at|cadeia|N |



### <a name="file"></a>Ficheiro


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|número inteiro|N |
|URL|cadeia|N |
|TASK_ID|número inteiro|N |
|list_id|número inteiro|N |
|USER_ID|número inteiro|N |
|file_name|cadeia|N |
|CONTENT_TYPE|cadeia|N |
|file_size|número inteiro|N |
|local_created_at|cadeia|N |
|created_at|cadeia|N |
|updated_at|cadeia|N |
|tipo|cadeia|N |
|revisão|número inteiro|N |



### <a name="newtask"></a>NewTask


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|list_id|número inteiro|Sim |
|título|cadeia|Sim |
|assignee_id|número inteiro|N |
|concluído|Booleano|N |
|recurrence_type|cadeia|N |
|recurrence_count|número inteiro|N |
|due_date|cadeia|N |
|starred|Booleano|N |



### <a name="newlist"></a>NewList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|título|cadeia|Sim |



### <a name="newsubtask"></a>NewSubtask


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|título|cadeia|Sim |
|concluído|Booleano|N |



### <a name="newnote"></a>NewNote


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|conteúdo|cadeia|Sim |



### <a name="newcomment"></a>Novocomentário


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|texto|cadeia|Sim |



### <a name="newreminder"></a>NewReminder


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|data|cadeia|Sim |



### <a name="updatetask"></a>UpdateTask


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|revisão|número inteiro|N |
|título|cadeia|N |
|assignee_id|número inteiro|N |
|concluído|Booleano|N |
|recurrence_type|cadeia|N |
|recurrence_count|número inteiro|N |
|due_date|cadeia|N |
|starred|Booleano|N |



### <a name="updatelist"></a>UpdateList


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|revisão|número inteiro|N |
|título|cadeia|N |



### <a name="updatesubtask"></a>UpdateSubtask


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|revisão|número inteiro|N |
|título|cadeia|N |
|concluído|Booleano|N |



### <a name="updatenote"></a>ActualizaçãoNota


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|revisão|número inteiro|N |
|conteúdo|cadeia|N |



### <a name="updatereminder"></a>UpdateReminder


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|data|cadeia|N |
|revisão|número inteiro|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)