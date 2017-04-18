<properties
    pageTitle="Configurar um webhook nos alertas de registo de atividade Azure | Microsoft Azure"
    description="Veja como utilizar os alertas de registo de atividade para chamar webhooks. "
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Configurar um webhook sobre alertas um registo de atividade Azure

Webhooks permitem-lhe encaminhar uma notificação de alerta Azure para outros sistemas para ações pós transformação ou personalizadas. Pode utilizar uma webhook num alerta para encaminhá-lo para serviços que enviar SMS, inicie sessão erros, avise a equipa de através dos serviços conversação/mensagens ou Efetue qualquer número de outras ações. Este artigo descreve como configurar um webhook num alerta de registo de atividade do Azure e o aspeto de carga útil para que a mensagem de HTTP para um webhook. Para obter informações sobre a configuração e o esquema de um alerta métrica Azure, [Consulte esta página em vez disso](insights-webhooks-alerts.md). Também pode definir um alerta de registo de atividade para enviar e-mail quando ativado.

>[AZURE.NOTE] Esta funcionalidade está atualmente na pré-visualização, por isso, esperar variável qualidade e o desempenho.

Pode definir um alerta de registo de atividade utilizando o [Azure Cmdlets do PowerShell](insights-powershell-samples.md#create-alert-rules), [Em diferentes plataformas clip](insights-cli-samples.md#work-with-alerts)ou [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticar o webhook
TThe webhook pode autenticar utilizando um dos seguintes métodos:

1. **Autorização com base em token** - o webhook URI é guardado com um ID token, por ex. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorização básica** - o webhook URI é guardado com um nome de utilizador e palavra-passe, por ex. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga útil
A operação de mensagem contém a carga útil JSON seguinte e esquema para todos os alertas com base no registo de atividade. Este esquema é semelhante ao utilizado pelo alertas baseados na métrica.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nome do elemento       |Descrição|
|---                |---|
|Estado             |Utilizado para alertas de métricas. Sempre definida como "ativado" para alertas de registo de atividade.|
|contexto            |Contexto do evento.|
|resourceProviderName|O fornecedor de recursos do recurso afetado.|
|conditionType      |Sempre "evento".|
|nome               |Nome da regra alerta.|
|ID                 |ID do recurso do alerta.|
|Descrição        |Descrição do alerta como conjunto durante a criação do alerta.|
|subscriptionId     |ID da subscrição Azure.|
|data/hora          |Data/hora no qual o evento foi gerado pelo serviço Azure que processada o pedido.|
|resourceId         |ID do recurso do recurso afetado.|
|resourceGroupName  |Nome do grupo de recursos para o recurso afetado|
|Propriedades         |Conjunto de `<Key, Value>` pares (ou seja, `Dictionary<String, String>`) que inclua detalhes sobre o evento.|
|evento              |Elemento que contém metadados sobre o evento.|
|autorização      |As propriedades RBAC do evento. Estes incluem normalmente, a ação"", "função" e "âmbito."|
|categoria           |Categoria do evento. Valores suportados incluem: administrativo, alertar, segurança, ServiceHealth, recomendação.|
|autor da chamada             |Endereço de e-mail do utilizador ao qual efetuada a operação, afirmação UPN ou afirmação SPN com base na disponibilidade. Pode ser nulo para determinados chamadas de sistema.|
|correlationId      |Normalmente, um GUID no formato de cadeia. Eventos com correlationId pertencem, para a mesma acção maior e partilham normalmente uma correlationId.|
|eventDescription   |Descrição de texto estático do evento.|
|eventDataId        |Identificador exclusivo para o evento.|
|OrigemEvento        |Nome do Azure serviço ou infraestrutura que gerou o evento.|
|httpRequest        |Normalmente, inclui o "clientRequestId", "clientIpAddress" e "método" (método HTTP, por exemplo, COLOCA).|
|nível              |Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativo" e "Verboso."|
|IDOperação        |Normalmente, um GUID partilhado entre os eventos correspondente a única operação.|
|operationName      |Nome da operação.|
|Propriedades         |Propriedades do evento.|
|Estado             |Cadeia. Estado da operação. Valores comuns incluem: "Ao", "Em curso", "Foi concluída com êxito", "Falhou", "Active", "Resolvido".|
|Subestado          |Normalmente, inclui o código de estado HTTP da chamada resto correspondente. -Lo também pode incluir outras cadeias que descreve um subestado. Valores de subestado comum incluem: OK (código de estado HTTP: 200), criado (código de estado HTTP: 201), aceite (código de estado HTTP: 202), não conteúdo (código de estado HTTP: 204), pedido incorrecto (código de estado HTTP: 400), não encontrado (código de estado HTTP: 404), conflito (código de estado HTTP: 409), erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503), tempo limite do Gateway (código de estado HTTP: 504)|

## <a name="next-steps"></a>Próximos passos
- [Saiba mais acerca do registo de atividade](monitoring-overview-activity-logs.md)
- [Executar scripts de automatização do Azure (Runbooks) no Azure alertas](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Utilize a aplicação de lógica para enviar um SMS através do Twilio a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo é para métricas alertas, mas poderá ser alterado para trabalhar com um alerta de registo de atividade.
- [Utilize a aplicação de lógica para enviar uma mensagem a partir de um alerta Azure folga](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo é para métricas alertas, mas poderá ser alterado para trabalhar com um alerta de registo de atividade.
- [Utilize a aplicação de lógica para enviar uma mensagem para uma fila Azure a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo é para métricas alertas, mas poderá ser alterado para trabalhar com um alerta de registo de atividade.
