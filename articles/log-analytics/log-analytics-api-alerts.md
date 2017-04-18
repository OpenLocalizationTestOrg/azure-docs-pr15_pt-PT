<properties
   pageTitle="Registo Analytics alerta REST API"
   description="Registo de análise de alerta REST API permite-lhe criar e gerir alertas no conjunto de aplicações de gestão de operações (OMS).  Este artigo fornece detalhes de API e vários exemplos para efetuar operações diferentes."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>Registo Analytics alerta REST API

Registo de análise de alerta REST API permite-lhe criar e gerir alertas no conjunto de aplicações de gestão de operações (OMS).  Este artigo fornece detalhes de API e vários exemplos para efetuar operações diferentes.

Registo de análise de pesquisa REST API é RESTful e pode ser acedido através do Gestor de recursos do Azure REST API. Neste documento irá encontrar exemplos onde a API é acedida a partir de uma linha de comandos do PowerShell utilizando [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comandos de abrir origem simplifica invocar a API do Gestor de recursos do Azure. A utilização de ARMClient e PowerShell é uma das várias opções para aceder a API do registo de análise de pesquisa. Pode utilizar a API do Gestor de recursos do Azure RESTful para efetuar chamadas para áreas de trabalho OMS e efetuar comandos de pesquisa nelas com estas ferramentas. API será de saída de resultados de pesquisa formato JSON, permitindo-lhe utilizar os resultados da pesquisa de muitas maneiras diferentes através de programação.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, alertas só podem ser criados com uma procura guardada no registo de análise.  Pode referir-se para o [Registo de pesquisa REST API](log-analytics-log-search-api.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa guardada pode ter uma ou mais agendas. A agenda define com que frequência a pesquisa é use e com o intervalo de tempo através da qual é identificada os critérios.
Agendas tem as propriedades da tabela seguinte.

| Propriedade  | Descrição |
|:--|:--|
| Intervalo | Com que frequência é executada a pesquisa. Medido em minutos. |
| QueryTimeSpan | O intervalo de tempo relativamente à qual os critérios é avaliada. Tem de ser igual ou maior que o intervalo. Medido em minutos. |
| Versão | A versão da API a ser utilizada.  Atualmente, este deve ser sempre definido para 1. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos e Timespan de 30 minutos. Neste caso, a consulta seria será executada a cada 15 minutos e um alerta seria acionado se os critérios de continuação ser resolvido para true sobre um intervalo de 30 minutos.

### <a name="retrieving-schedules"></a>Obter agendas
Utilize o método de introdução para obter todas as agendas para uma procura guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilize o método Get com um ID de agenda para obter uma agenda específica para uma procura guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Segue-se uma resposta de exemplo para uma agenda.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Criar uma agenda
Utilize o método de colocar com um ID de agenda exclusiva para criar uma nova agenda.  Tenha em atenção que duas agendas não podem ter o mesmo ID, mesmo que estão associadas diferentes procuras guardadas.  Quando cria uma agenda na consola do OMS, um GUID é criado para o ID da agenda.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editar uma agenda
Utilize o método de colocar com um ID de agenda existente para a mesma procura guardada para modificar que agenda.  Corpo do pedido de tem de incluir etag da agenda.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminar agendas
Utilize o método Delete com um ID de agenda para eliminar uma agenda.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Uma agenda pode ter várias acções. Uma ação poderá definir um ou mais processos para efetuar como enviar uma mensagem de correio ou iniciar um livro de execuções ou pode definir um limite que determina quando os resultados de uma pesquisa correspondem alguns critérios.  Algumas ações definirá ambos para que os processos são executados quando o limiar de que existe correspondência.

Todas as ações tem as propriedades da tabela seguinte.  Diferentes tipos de alertas têm diferentes propriedades adicionais que são descritas abaixo.

| Propriedade | Descrição |
|:--|:--|
| Tipo | Tipo da ação.  Neste momento, os valores possíveis são alerta e Webhook. |
| Nome | Nome a apresentar para o alerta. |
| Versão | A versão da API a ser utilizada.  Atualmente, este deve ser sempre definido para 1. |

### <a name="retrieving-actions"></a>Obter ações
Utilize o método de introdução para obter todas as ações para uma agenda.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilize o método de introdução com o ID de ação para obter uma acção específica de uma agenda.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criar ou editar acções
Utilize o método de colocar com um ID de ação que seja exclusivo à agenda para criar uma nova ação.  Quando cria uma ação na consola do OMS, um GUID destina-se o ID de ação.

Utilize o método de colocar com um ID de ação existente para a mesma procura guardada para modificar que agenda.  Corpo do pedido de tem de incluir etag da agenda.

O formato de pedido para criar uma nova ação varia consoante o tipo de ação, para que estes exemplos são fornecidos nas secções abaixo.

### <a name="deleting-actions"></a>Eliminar acções
Utilize o método Delete com o ID de ação para eliminar uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Acções de alerta
Uma agenda deve ter apenas uma acção do alerta.  Acções de alerta de ter um ou mais das secções na tabela seguinte.  Cada é descrito detalhadamente mais abaixo.

| Secção | Descrição |
|:--|:--|
| Limiar | Critérios para quando é executada a ação. |  
| EmailNotification | Envie um e-mail a vários destinatários. |
| Remediação | Inicie um livro de execuções no Azure automatização para tentar corrigir o problema identificado. |

#### <a name="thresholds"></a>Limiares
Uma ação alerta deve ter apenas uma limiar.  Quando os resultados de uma pesquisa guardada corresponderem o limiar de uma ação associado com que a procura, quaisquer outros processos nessa ação são executados.  Uma ação também pode conter apenas um determinado limiar para que possa ser utilizado com ações de outros tipos de que não contenham limiares.

Limiares tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Operador | Operador de comparação limiar. <br> gt = maior do que <br> lt = menor que |
| Valor | Valor para o limiar de. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos, Timespan de 30 minutos e um limite superior a 10. Neste caso, a consulta seria será executada a cada 15 minutos e um alerta seria acionado se devolvido-10 eventos que foram criados ao longo de um intervalo de 30 minutos.

Segue-se uma resposta de exemplo para uma ação com apenas um limiar.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Utilize o método de colocar com um ID exclusivo ação para criar uma nova ação limiar para uma agenda.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilize o método de colocar com um ID de ação existente para modificar uma acção do limiar para uma agenda.  Corpo do pedido de tem de incluir etag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notificação de e-mail
Notificações de correio eletrónico enviar correio para um ou mais destinatários.  Incluem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Destinatários | Lista de endereços de correio. |
| Assunto | O assunto da mensagem. |
| Anexo | Anexos não são atualmente suportados, para que isto partilhou terão sempre um valor de "Nenhum". |

Segue-se uma resposta de exemplo para uma ação de notificação de correio eletrónico com um limite.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Utilize o método de colocar com um ID exclusivo ação para criar uma nova ação de correio electrónico para uma agenda.  O exemplo seguinte cria uma notificação de correio eletrónico com um determinado limiar para que o correio é enviado quando os resultados da procura guardada excedem o limiar da.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Utilize o método de colocar com um ID de ação existente para modificar uma ação de correio electrónico para uma agenda.  Corpo do pedido de tem de incluir etag da ação.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Ações de remediação
Remediations iniciar um livro de execuções no Azure automatização que tenta corrigir o problema identificado pelo alerta.  Tem de criar um webhook para o livro de execuções utilizado numa ação de remediação e, em seguida, especifique o URI na propriedade WebhookUri.  Quando criar esta ação utilizando a consola OMS, é criada automaticamente uma nova webhook para o livro de execuções.

Remediations incluem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| RunbookName | Nome do livro de execuções. Tem de corresponder um livro de execuções publicado na conta de automatização configurado na solução automatização na área de trabalho OMS. |
| WebhookUri | URI do webhook.
| Termo | A data de expiração e a hora da webhook.  Se o webhook não tem uma expiração, em seguida, este pode ser qualquer data futura válida. |

Segue-se uma resposta de exemplo para uma acção remediação com um limite.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Utilize o método de colocar com um ID exclusivo ação para criar uma nova ação remediação para uma agenda.  O exemplo seguinte cria uma remediação com um determinado limiar para que o livro de execuções é iniciado quando os resultados da procura guardada excedem o limiar da.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Utilize o método de colocar com um ID de ação existente para modificar uma acção remediação para uma agenda.  Corpo do pedido de tem de incluir etag da ação.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemplo
Segue-se um exemplo para criar um novo alerta de correio eletrónico completo.  Esta ação cria uma nova agenda juntamente com uma ação que contém uma limiar e e-mail.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Ações de Webhook
Ações de Webhook iniciar um processo chamar um URL e, opcionalmente, fornecendo uma carga útil sejam enviadas.  Estes são semelhantes às ações de remediação exceto são destinam da webhooks pode invocar processos diferente de automatização do Azure runbooks.  Também fornecem a opção adicional de fornecer uma carga útil a entrega falhou o processo de remota.

Ações de Webhook não possui um determinado limiar mas em vez disso, devem ser adicionadas a um esquema que tenha uma ação alerta com um limite.  Pode adicionar várias acções de Webhook que serão todos executadas quando o limiar de que existe correspondência.

Acções de Webhook incluem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| WebhookUri | O assunto da mensagem. |
| CustomPayload | Carga útil personalizado a ser enviado para o webhook.  O formato vai variam consoante o que o webhook está à espera. |

Segue-se uma resposta de exemplo para webhook ação e uma ação de alerta associada com um limite.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Criar ou editar uma ação webhook
Utilize o método de colocar com um ID exclusivo ação para criar uma nova ação webhook para uma agenda.  O exemplo seguinte cria uma ação Webhook e uma acção de alerta com um determinado limiar para que o webhook será acionado quando os resultados da procura guardada excedem o limiar de.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Utilize o método de colocar com um ID de ação existente para modificar uma acção webhook para uma agenda.  Corpo do pedido de tem de incluir etag da ação.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Próximos passos

- Utilize a [API REST para efetuar pesquisas de registo](log-analytics-log-search-api.md) no registo de análise.
