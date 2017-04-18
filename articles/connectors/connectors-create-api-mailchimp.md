<properties
pageTitle="MailChimp | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. MailChimp é um serviço de SaaS que lhe permite empresas gerir e automatizar atividades de marketing de correio eletrónico, incluindo o envio de e-mails de marketing, mensagens automatizadas e campanhas direccionadas."
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

# <a name="get-started-with-the-mailchimp-connector"></a>Começar a trabalhar com o conector de MailChimp

MailChimp é um serviço de SaaS que lhe permite empresas gerir e automatizar atividades de marketing de correio eletrónico, incluindo o envio de e-mails de marketing, mensagens automatizadas e campanhas direccionadas.


>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica.

Pode começar por criar uma aplicação de lógica agora, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções

A conexão MailChimp pode ser utilizada como uma ação; tiver accionadores. Todas as conexões suportam dados nos formatos JSON e XML.

 O conector de MailChimp tem os seguintes uma ou mais ações e/ou accionadores disponíveis:

### <a name="mailchimp-actions"></a>Ações de MailChimp
Pode efetuar estas ou mais ações:

|Ação|Descrição|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Criar uma nova campanha baseada num tipo de campanha, lista de destinatários e definições de campanha (linha Assunto, título, from_name e reply_to)|
|[NewList](connectors-create-api-mailchimp.md#newlist)|Criar uma nova lista na sua conta MailChimp|
|[addmember](connectors-create-api-mailchimp.md#addmember)|Adicionar ou atualizar um membro da lista|
|[removemember](connectors-create-api-mailchimp.md#removemember)|Elimine um membro de uma lista.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Atualizar as informações de um membro de lista específica|
### <a name="mailchimp-triggers"></a>MailChimp accionadores
Pode ouvir para estes evento (s):

|Accionador | Descrição|
|--- | ---|
|Quando um membro foi adicionado a uma lista|Accionar um fluxo de trabalho quando tiver sido adicionado um novo membro a uma lista|
|Quando é criada uma nova lista|Accionar um fluxo de trabalho quando é criada uma nova lista|


## <a name="create-a-connection-to-mailchimp"></a>Criar uma ligação a MailChimp
Para criar aplicações de lógica com MailChimp, tem primeiro criar uma **ligação** , em seguida, fornecer os detalhes para as seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer MailChimp credenciais|

>[AZURE.INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Pode utilizar esta ligação nas outras aplicações de lógica.

## <a name="reference-for-mailchimp"></a>Referência para MailChimp
Aplica-se para a versão: 1.0

## <a name="newcampaign"></a>newcampaign
Nova campanha: Criar uma nova campanha baseada num tipo de campanha, lista de destinatários e definições de campanha (linha Assunto, título, from_name e reply_to)

```POST: /campaigns```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|newCampaignRequest| |Sim|corpo|nenhum|Objeto de JSON para enviar no corpo com os parâmetros de pedido de campanha novo|

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


## <a name="newlist"></a>NewList
Nova lista: Criar uma nova lista na sua conta MailChimp

```POST: /lists```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|newListRequest| |Sim|corpo|nenhum|Objeto de JSON para enviar no corpo com os parâmetros de pedido de campanha novo|

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


## <a name="addmember"></a>addmember
Adicionar membro à lista: Adicionar ou atualizar um membro da lista

```POST: /lists/{list_id}/members```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia|Sim|caminho|nenhum|O id exclusivo para a lista|
|newMemberInList| |Sim|corpo|nenhum|Objeto de JSON para enviar no corpo com as novas informações de membro|

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


## <a name="removemember"></a>removemember
Remover membro da lista: eliminar um membro de uma lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia|Sim|caminho|nenhum|O id exclusivo para a lista|
|member_email|cadeia|Sim|caminho|nenhum|O endereço de e-mail do membro a eliminar|

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


## <a name="updatemember"></a>updatemember
Atualizar a informação de membro: atualizar as informações de um membro de lista específica

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia|Sim|caminho|nenhum|O id exclusivo para a lista|
|member_email|cadeia|Sim|caminho|nenhum|O endereço de e-mail exclusivo do membro a atualizar|
|updateMemberInListRequest| |Sim|corpo|nenhum|Objeto de JSON para enviar no corpo com a informação de membro atualizado|

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


## <a name="onmembersubscribed"></a>OnMemberSubscribed
Quando um membro foi adicionado a uma lista: accionadores um fluxo de trabalho quando tiver sido adicionado um novo membro a uma lista

```GET: /trigger/lists/{list_id}/members```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia|Sim|caminho|nenhum|O id exclusivo para a lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="oncreatelist"></a>OnCreateList
Quando é criada uma nova lista: accionadores um fluxo de trabalho quando é criada uma nova lista

```GET: /trigger/lists```

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto

### <a name="newcampaignrequest"></a>NewCampaignRequest


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|tipo|cadeia|Sim |
|destinatários|não definido|Sim |
|definições|não definido|Sim |
|variate_settings|não definido|N |
|controlo|não definido|N |
|rss_opts|não definido|N |
|social_card|não definido|N |



### <a name="recipient"></a>Destinatário


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|list_id|cadeia|Sim |
|segment_opts|não definido|N |



### <a name="settings"></a>Definições


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|subject_line|cadeia|Sim |
|título|cadeia|N |
|from_name|cadeia|Sim |
|reply_to|cadeia|Sim |
|use_conversation|Booleano|N |
|to_name|cadeia|N |
|folder_id|número inteiro|N |
|autenticar|Booleano|N |
|auto_footer|Booleano|N |
|inline_css|Booleano|N |
|auto_tweet|Booleano|N |
|auto_fb_post|matriz|N |
|fb_comments|Booleano|N |



### <a name="variatesettings"></a>Variate_Settings


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|winner_criteria|cadeia|N |
|tempo_espera|número inteiro|N |
|test_size|número inteiro|N |
|subject_lines|matriz|N |
|send_times|matriz|N |
|from_names|matriz|N |
|reply_to_addresses|matriz|N |



### <a name="tracking"></a>Controlo


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|é aberta|Booleano|N |
|html_clicks|Booleano|N |
|text_clicks|Booleano|N |
|goal_tracking|Booleano|N |
|ecomm360|Booleano|N |
|google_analytics|cadeia|N |
|clicktale|cadeia|N |
|equipa de vendas|não definido|N |
|highrise|não definido|N |
|Cápsula|não definido|N |



### <a name="rssopts"></a>RSS_Opts


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|feed_url|cadeia|N |
|frequência|cadeia|N |
|constrain_rss_img|cadeia|N |
|agenda|não definido|N |



### <a name="socialcard"></a>Social_Card


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|image_url|cadeia|N |
|Descrição|cadeia|N |
|título|cadeia|N |



### <a name="segmentopts"></a>Segment_Opts


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|saved_segment_id|número inteiro|N |
|corresponder|cadeia|N |



### <a name="salesforce"></a>Equipa de vendas


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|campanha|Booleano|N |
|notas|Booleano|N |



### <a name="highrise"></a>Highrise


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|campanha|Booleano|N |
|notas|Booleano|N |



### <a name="capsule"></a>Cápsula


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|notas|Booleano|N |



### <a name="schedule"></a>Agenda


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|hora|número inteiro|N |
|daily_send|não definido|N |
|weekly_send_day|cadeia|N |
|monthly_send_date|número|N |



### <a name="dailysend"></a>Daily_Send


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Domingo|Booleano|N |
|Segunda-feira|Booleano|N |
|Terça-feira|Booleano|N |
|Quarta-feira|Booleano|N |
|Quinta-feira|Booleano|N |
|Sexta-feira|Booleano|N |
|Sábado|Booleano|N |



### <a name="campaignresponsemodel"></a>CampaignResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|N |
|tipo|cadeia|N |
|create_time|cadeia|N |
|archive_url|cadeia|N |
|Estado|cadeia|N |
|emails_sent|número inteiro|N |
|send_time|cadeia|N |
|CONTENT_TYPE|cadeia|N |
|destinatário|matriz|N |
|definições|não definido|N |
|variate_settings|não definido|N |
|controlo|não definido|N |
|rss_opts|não definido|N |
|ab_split_opts|não definido|N |
|social_card|não definido|N |
|report_summary|não definido|N |
|delivery_status|não definido|N |
|_links|matriz|N |



### <a name="absplitopts"></a>AB_Split_Opts


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|split_test|cadeia|N |
|pick_winner|cadeia|N |
|wait_units|cadeia|N |
|tempo_espera|número inteiro|N |
|split_size|número inteiro|N |
|from_name_a|cadeia|N |
|from_name_b|cadeia|N |
|reply_email_a|cadeia|N |
|reply_email_b|cadeia|N |
|subject_a|cadeia|N |
|subject_b|cadeia|N |
|send_time_a|cadeia|N |
|send_time_b|cadeia|N |
|send_time_winner|cadeia|N |



### <a name="reportsummary"></a>Report_Summary


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|é aberta|número inteiro|N |
|unique_opens|número inteiro|N |
|open_rate|número|N |
|cliques|número inteiro|N |
|subscriber_clicks|número|N |
|click_rate|número|N |



### <a name="deliverystatus"></a>Delivery_Status


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ativado|Booleano|N |
|can_cancel|Booleano|N |
|Estado|cadeia|N |
|emails_sent|número inteiro|N |
|emails_canceled|número inteiro|N |



### <a name="link"></a>Ligação


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Rel|cadeia|N |
|href da|cadeia|N |
|método|cadeia|N |
|targetSchema|cadeia|N |
|esquema|cadeia|N |



### <a name="newlistrequest"></a>NewListRequest


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|nome|cadeia|Sim |
|contacto|não definido|Sim |
|permission_reminder|cadeia|Sim |
|use_archive_bar|Booleano|N |
|campaign_defaults|não definido|Sim |
|notify_on_subscribe|cadeia|N |
|notify_on_unsubscribe|cadeia|N |
|email_type_option|Booleano|Sim |
|visibilidade|cadeia|N |



### <a name="contact"></a>Contacto


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|empresa|cadeia|Sim |
|endereço 1|cadeia|Sim |
|Endereço2|cadeia|N |
|Cidade|cadeia|Sim |
|Estado|cadeia|Sim |
|Postal|cadeia|Sim |
|país/região|cadeia|Sim |
|telefone|cadeia|Sim |



### <a name="campaigndefaults"></a>Campaign_Defaults


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|from_name|cadeia|Sim |
|from_email|cadeia|Sim |
|assunto|cadeia|N |
|idioma|cadeia|Sim |



### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|Sim |
|nome|cadeia|Sim |
|contacto|não definido|Sim |
|permission_reminder|cadeia|Sim |
|use_archive_bar|Booleano|N |
|campaign_defaults|não definido|Sim |
|notify_on_subscribe|cadeia|N |
|notify_on_unsubscribe|cadeia|N |
|date_created|cadeia|N |
|list_rating|número inteiro|N |
|email_type_option|Booleano|Sim |
|subscribe_url_short|cadeia|N |
|subscribe_url_long|cadeia|N |
|beamer_address|cadeia|N |
|visibilidade|cadeia|N |
|módulos|matriz|N |
|estatística|não definido|N |
|_links|matriz|N |



### <a name="stats"></a>Estatística


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|member_count|número inteiro|N |
|unsubscribe_count|número inteiro|N |
|cleaned_count|número inteiro|N |
|member_count_since_send|número inteiro|N |
|unsubscribe_count_since_send|número inteiro|N |
|cleaned_count_since_send|número inteiro|N |
|campaign_count|número inteiro|N |
|campaign_last_sent|número inteiro|N |
|merge_field_count|número inteiro|N |
|avg_sub_rate|número|N |
|avg_unsub_rate|número|N |
|target_sub_rate|número|N |
|open_rate|número|N |
|click_rate|número|N |
|last_sub_date|cadeia|N |
|last_unsub_date|cadeia|N |



### <a name="getlistsresponsemodel"></a>GetListsResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|listas|matriz|N |
|total_items|número inteiro|N |



### <a name="newmemberinlistrequest"></a>NewMemberInListRequest


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|email_type|cadeia|N |
|Estado|cadeia|Sim |
|merge_fields|não definido|N |
|interesses|cadeia|N |
|idioma|cadeia|N |
|VIP|Booleano|N |
|localização|não definido|N |
|endereço electrónico|cadeia|Sim |



### <a name="firstandlastname"></a>FirstAndLastName


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|FNAME|cadeia|N |
|LNAME|cadeia|N |



### <a name="location"></a>Localização


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|latitude|número|N |
|longitude|número|N |



### <a name="memberresponsemodel"></a>MemberResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|N |
|endereço electrónico|cadeia|N |
|unique_email_id|cadeia|N |
|email_type|cadeia|N |
|Estado|cadeia|N |
|merge_fields|não definido|N |
|interesses|cadeia|N |
|estatística|não definido|N |
|ip_signup|cadeia|N |
|timestamp_signup|cadeia|N |
|ip_opt|cadeia|N |
|timestamp_opt|cadeia|N |
|member_rating|número inteiro|N |
|last_changed|cadeia|N |
|idioma|cadeia|N |
|VIP|Booleano|N |
|email_client|cadeia|N |
|localização|não definido|N |
|last_note|não definido|N |
|list_id|cadeia|N |
|_links|matriz|N |



### <a name="lastnote"></a>Last_Note


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|note_id|número inteiro|N |
|created_at|cadeia|N |
|created_by|cadeia|N |
|Nota|cadeia|N |



### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Membros|matriz|N |
|list_id|cadeia|N |
|total_items|número inteiro|N |



### <a name="object"></a>Objecto






### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|endereço electrónico|cadeia|N |
|email_type|cadeia|N |
|Estado|cadeia|Sim |
|merge_fields|não definido|N |
|interesses|cadeia|N |
|idioma|cadeia|N |
|VIP|Booleano|N |
|localização|não definido|N |



### <a name="getmembersresponsemodel"></a>GetMembersResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|Membros|matriz|N |
|list_id|cadeia|N |
|total_items|número inteiro|N |



### <a name="adduserresponsemodel"></a>AddUserResponseModel


| Nome da propriedade | Tipo de dados | Obrigatório |
|---|---|---|
|ID|cadeia|Sim |
|endereço electrónico|cadeia|Sim |
|unique_email_id|cadeia|N |
|email_type|cadeia|N |
|Estado|cadeia|N |
|merge_fields|não definido|Sim |
|interesses|cadeia|N |
|estatística|não definido|N |
|ip_signup|cadeia|N |
|timestamp_signup|cadeia|N |
|ip_opt|cadeia|N |
|timestamp_opt|cadeia|N |
|member_rating|número inteiro|N |
|last_changed|cadeia|N |
|idioma|cadeia|N |
|VIP|Booleano|N |
|email_client|cadeia|N |
|localização|não definido|N |
|last_note|não definido|N |
|list_id|cadeia|N |
|_links|matriz|N |


## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
