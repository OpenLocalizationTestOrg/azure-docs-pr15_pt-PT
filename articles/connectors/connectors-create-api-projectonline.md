<properties
pageTitle="Project Online | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Project Online é uma solução online flexível para gestão de portefólio de projetos (PPM) e trabalho diário da Microsoft. Project Online entregues através do Office 365, permitem às organizações começar a trabalhar rapidamente com as capacidades da gestão do projeto poderosas para planear, atribuir prioridades e gerir projetos e investimentos do portefólio de projetos — a partir de praticamente qualquer lugar em praticamente qualquer dispositivo."
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

# <a name="get-started-with-the-projectonline-connector"></a>Começar a trabalhar com o conector do Project Online

Project Online é uma solução online flexível para gestão de portefólio de projetos (PPM) e trabalho diário da Microsoft. Project Online entregues através do Office 365, permitem às organizações começar a trabalhar rapidamente com as capacidades da gestão do projeto poderosas para planear, atribuir prioridades e gerir projetos e investimentos do portefólio de projetos — a partir de praticamente qualquer lugar em praticamente qualquer dispositivo.

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica. 

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

O conector do Project Online, pode ser utilizado como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML. 

 O conector do Project Online tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="projectonline-actions"></a>Ações do Project Online
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Lista de projetos no seu site do project online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Cria um novo projeto no seu site do project online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Cria uma nova tarefa no project|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Cria uma recursos de empresa no seu site do project online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Lista as tarefas publicadas num projeto|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Verificações de saída de um projeto no seu site|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Dar entrada e publicar e existentes projeto no seu site|
### <a name="projectonline-triggers"></a>Accionadores do Project Online
Pode ouvir para estes evento (s):

|Accionador | Descrição|
|--- | ---|
|Quando é criado um novo projeto|Um fluxo de accionadores sempre que é criado um novo projeto|
|Quando é criado um novo recurso|Um novo fluxo de accionadores quando é criado um novo recurso|
|Quando é criada uma nova tarefa|Um fluxo de accionadores quando é criada uma nova tarefa|


## <a name="create-a-connection-to-projectonline"></a>Criar uma ligação para Project Online
Para criar aplicações de lógica com o Project Online, pode tem primeiro criar uma **ligação** , em seguida, fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer as credenciais do Project Online|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="reference-for-projectonline"></a>Referência do Project Online
Aplica-se para a versão: 1.0

## <a name="onnewproject"></a>OnNewProject
Quando é criado um novo projeto: um fluxo de accionadores sempre que é criado um novo projeto 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
Quando é criado um novo recurso: um novo fluxo de accionadores quando é criado um novo recurso 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
Quando é criada uma nova tarefa: um fluxo de accionadores quando é criada uma nova tarefa 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
Lista de projetos: listas os projetos no seu site do project online 

```GET: /_api/ProjectServer/Projects``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
Cria o novo projeto: cria um novo projeto no seu site do project online 

```POST: /_api/ProjectServer/Projects``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|Resumo de projecto| |Sim|corpo|nenhum|Novo projeto para criar|

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


## <a name="createtask"></a>CreateTask
Cria a nova tarefa: cria uma nova tarefa no project 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia|Sim|caminho|nenhum|ID exclusivo do projeto para adicionar a tarefa|
|tarefa| |Sim|corpo|nenhum|Nova tarefa para adicionar ao projeto|

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


## <a name="createresource"></a>CreateResource
Criar novo recurso: cria um recursos de empresa no seu site do project online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|recurso| |Sim|corpo|nenhum|Novo recurso de empresa para adicionar ao projeto|

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


## <a name="listtasks"></a>ListTasks
Apresenta as tarefas: lista de tarefas publicadas num projeto 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia|Sim|caminho|nenhum|ID exclusivo do projeto para obter tarefas|

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


## <a name="checkoutproject"></a>CheckoutProject
Dar saída de um projeto: dá saída de um projeto no seu site 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia|Sim|caminho|nenhum|ID exclusivo do projeto para adicionar a tarefa|

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


## <a name="publishproject"></a>PublishProject
Dar entrada e publicar o projeto: dar entrada e publicar e existentes projeto no seu site 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia|Sim|consulta|nenhum|Url do site do seu site de projeto de raiz (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia|Sim|caminho|nenhum|ID exclusivo do projeto para dar entrada|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="triggerproject"></a>TriggerProject


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ProjectStartDate|cadeia|N |
|ProjectFinishDate|cadeia|N |
|ProjectCreatedDate|cadeia|N |
|ProjectId|cadeia|N |
|ProjectModifiedDate|cadeia|N |
|ProjectType|número inteiro|N |
|Nomeprojeto|cadeia|N |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="triggerresource"></a>TriggerResource


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ResourceId|cadeia|N |
|ResourceBaseCalendar|cadeia|N |
|ResourceBookingType|número inteiro|N |
|ResourceCanLevel|Booleano|N |
|ResourceCostPerUse|número|N |
|ResourceCreatedDate|cadeia|N |
|ResourceEarliestAvailableFrom|cadeia|N |
|ResourceEmail|cadeia|N |
|ResourceInitials|cadeia|N |
|ResourceIsActive|Booleano|N |
|ResourceIsGeneric|Booleano|N |
|ResourceLatestAvailableTo|cadeia|N |
|ResourceModifiedDate|cadeia|N |
|NomeRecurso|cadeia|N |
|ResourceStatsuName|cadeia|N |
|Tipo de recurso|número inteiro|N |
|TypeDescription|cadeia|N |
|NomeTipo|cadeia|N |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="triggertask"></a>TriggerTask


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ProjectId|cadeia|N |
|TaskId|cadeia|N |
|Nomeprojeto|cadeia|N |
|Nome da tarefa|cadeia|N |
|TaskCreatedDate|cadeia|N |
|TaskModifieddate|cadeia|N |
|TaskStartDate|cadeia|N |
|TaskFinishDate|cadeia|N |
|TaskPriority|número inteiro|N |
|TaskIsActive|Booleano|N |



### <a name="newproject"></a>NewProject


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Nome|cadeia|Sim |
|Descrição|cadeia|N |
|Iniciar|cadeia|N |



### <a name="newreource"></a>NewReource


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Nome|cadeia|Sim |
|IsBudget|Booleano|N |
|IsGeneric|Booleano|N |
|IsInactive|Booleano|N |



### <a name="project"></a>Projecto


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ApprovedStart|cadeia|N |
|ApprovedEnd|cadeia|N |
|CheckedOutDate|cadeia|N |
|CheckOutDescription|cadeia|N |
|CheckOutId|cadeia|N |
|Datacriação|cadeia|N |
|ID|cadeia|N |
|IsCheckedOut|Booleano|N |
|LastPublishedDate|cadeia|N |
|ÚltimaDataGuardada|cadeia|N |
|OptimizerDecision|número inteiro|N |
|PlannerDecision|número inteiro|N |
|ProjectType|número inteiro|N |
|Nome|cadeia|N |
|WinprojVersion|cadeia|N |



### <a name="projectswrapper"></a>ProjectsWrapper


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="newtask"></a>NewTask


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|parâmetros|não definido|Sim |



### <a name="taskparameters"></a>TaskParameters


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Nome|cadeia|Sim |
|Notas|cadeia|N |
|Iniciar|cadeia|N |
|Duração|cadeia|N |



### <a name="enterpriseresource"></a>EnterpriseResource


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|CanLevel|Booleano|N |
|Código|cadeia|N |
|CostAccrual|número inteiro|N |
|CostCenter|cadeia|N |
|Criado|cadeia|N |
|DefaultBookingType|número inteiro|N |
|Mensagem de correio electrónico|cadeia|N |
|ExternalId|cadeia|N |
|Grupo|cadeia|N |
|HireDate|cadeia|N |
|ID|cadeia|N |
|Iniciais|cadeia|N |
|IsActive|Booleano|N |
|IsBudget|Booleano|N |
|IsCheckedOut|Booleano|N |
|IsGeneric|Booleano|N |
|IsTeam|Booleano|N |
|MaterialLabel|cadeia|N |
|Modificado|cadeia|N |
|Nome|cadeia|N |
|Fonética|cadeia|N |
|Tipo de recurso|número inteiro|N |
|TerminationDate|cadeia|N |



### <a name="taskswrapper"></a>TasksWrapper


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|valor|matriz|N |



### <a name="task"></a>Tarefa


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Criado|cadeia|N |
|Modificado|cadeia|N |
|Iniciar|cadeia|N |
|Concluir|cadeia|N |
|Nome|cadeia|N |
|ID|cadeia|N |
|Prioridade|número inteiro|N |
|PercentagemConcluída|número inteiro|N |
|Notas|cadeia|N |
|Contacto|cadeia|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)