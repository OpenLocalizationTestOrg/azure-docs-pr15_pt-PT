<properties
    pageTitle="Adicionar o conector de utilizadores do Office 365 nas aplicações de lógica | Microsoft Azure"
    description="Descrição geral de conexão de utilizadores do Office 365 com os parâmetros de REST API"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Começar a trabalhar com o conector de utilizadores do Office 365

Ligue aos utilizadores do Office 365 para obter os perfis, os utilizadores de pesquisa e muito mais. 

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica.

Com utilizadores do Office 365, pode:

- Crie o seu fluxo de negócio com base em dados que obtém de utilizadores do Office 365. 
- Ações de utilização que obter diretos, obtenha o perfil de utilizador de um gestor e muito mais. Estas ações obtém uma resposta e, em seguida, disponibilizam o resultado para outras ações. Por exemplo, obter subordinados diretos de uma pessoa e, em seguida, retirar estas informações e actualizar uma base de dados do SQL Azure. 

Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

O conector de utilizadores do Office 365 tem as seguintes ações disponíveis. Não existem sem accionadores.

| Accionadores | Ações|
| --- | --- |
|Nenhum | <ul><li>Obter o Gestor</li><li>Obter o meu perfil</li><li>Obter diretos</li><li>Obter o perfil de utilizador</li><li>Pesquisa para utilizadores</li></ul>|

Todas as conexões suportam dados nos formatos JSON e XML. 


## <a name="create-a-connection-to-office-365-users"></a>Criar uma ligação aos utilizadores do Office 365

Quando adiciona este conector às suas aplicações de lógica, tem de entrada para a sua conta de utilizadores do Office 365 e permitir lógica aplicações ligar à sua conta.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Depois de criar a ligação, introduza as propriedades de utilizadores do Office 365, como o ID de utilizador. A **REST API referência** neste tópico descreve estas propriedades.

>[AZURE.TIP] Pode utilizar esta ligação de utilizadores do Office 365 mesmo nas outras aplicações de lógica.


## <a name="office-365-users-rest-api-reference"></a>Referência de utilizadores REST API do Office 365
Aplica-se para a versão: 1.0.

### <a name="get-my-profile"></a>Obter o meu perfil 
Obtém o perfil do utilizador actual.  
```GET: /users/me``` 

Não existem parâmetros para esta chamada.

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


### <a name="get-user-profile"></a>Obter o perfil de utilizador 
Obtém um perfil de utilizador específico.  
```GET: /users/{userId}``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID de utilizador|cadeia|Sim|caminho|nenhum|Id de correio eletrónico ou nome principal no utilizador|

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


### <a name="get-manager"></a>Obter o Gestor 
Obtém o perfil de utilizador para o Gestor do utilizador especificado.  
```GET: /users/{userId}/manager``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID de utilizador|cadeia|Sim|caminho|nenhum|Id de correio eletrónico ou nome principal no utilizador|

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



### <a name="get-direct-reports"></a>Obter diretos 
Obter diretos.  
```GET: /users/{userId}/directReports``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID de utilizador|cadeia|Sim|caminho|nenhum|Id de correio eletrónico ou nome principal no utilizador|

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



### <a name="search-for-users"></a>Pesquisa para utilizadores 
Obtém resultados dos perfis de utilizador de pesquisa.  
```GET: /users``` 

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|searchTerm|cadeia|nenhum|consulta|nenhum|Cadeia de pesquisa (aplica-se ao: apresentar nome, nome próprio, apelido, correio, correio alcunha e principal nome de utilizador)|

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



## <a name="object-definitions"></a>Definições de objecto

#### <a name="user-user-model-class"></a>Utilizador: Classe de modelo de utilizador

|Nome da propriedade | Tipo de dados |Obrigatório
|---|---|---|
|DisplayName em grupos|cadeia|nenhum|
|GivenName|cadeia|nenhum|
|Apelido|cadeia|nenhum|
|Correio|cadeia|nenhum|
|MailNickname|cadeia|nenhum|
|TelephoneNumber|cadeia|nenhum|
|AccountEnabled|Booleano|nenhum|
|ID|cadeia|Sim
|UserPrincipalName|cadeia|nenhum|
|Departamento|cadeia|nenhum|
|Cargo|cadeia|nenhum|
|mobilePhone|cadeia|nenhum|


## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Regresse à [lista APIs](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
