<properties
    pageTitle="Configurar o webhooks no Azure alertas métricas | Microsoft Azure"
    description="Reencaminhar o Azure alertas para outros sistemas de que não sejam Azure."
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
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurar um webhook num alerta métrico Azure

Webhooks permitem-lhe encaminhar uma notificação de alerta Azure para outros sistemas para ações pós transformação ou personalizadas. Pode utilizar uma webhook num alerta para encaminhá-lo para serviços que enviar SMS, inicie sessão erros, avise a equipa de através dos serviços conversação/mensagens ou Efetue qualquer número de outras ações. Este artigo descreve como configurar um webhook num alerta métrico Azure e o aspeto de carga útil para que a mensagem de HTTP para um webhook. Para obter informações sobre a configuração e o esquema para um registo de atividade Azure alertar (um alerta eventos), [Consulte esta página em vez disso](./insights-auditlog-to-webhook-email.md).

Azure alertas HTTP POST os conteúdos alertas JSON formatar, esquema definido abaixo, para um webhook URI que fornecem quando criar o alerta. Este URI tem de ser um ponto final válido de HTTP ou HTTPS. Azure regista uma entrada por pedido quando um alerta está ativado.

## <a name="configuring-webhooks-via-the-portal"></a>Configurar webhooks através do portal

Pode adicionar ou atualizar o webhook URI no ecrã criar/actualizar alertas no [portal](https://portal.azure.com/).

![Adicionar uma regra de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

Também pode configurar um alerta para publicar um webhook URI utilizando o [Azure Cmdlets do PowerShell](./insights-powershell-samples.md#create-alert-rules), [Em diferentes plataformas clip](./insights-cli-samples.md#work-with-alerts)ou [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticar o webhook

O webhook pode autenticar utilizando um dos seguintes métodos:

1. **Autorização com base em token** - o webhook URI é guardado com um ID token, por ex. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorização básica** - o webhook URI é guardado com um nome de utilizador e palavra-passe, por ex. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga útil

A operação de mensagem contém a carga útil JSON seguinte e esquema para todos os alertas baseados em métrica.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obrigatório | Conjunto de valores fixos | Notas |
| :-------------| :-------------   | :-------------   | :-------------   |
|Estado|Y|"Ativado", "Resolvido"|Estado para o alerta com base fora as condições que definiu.|
|contexto| Y | | O contexto de alerta.|
|data/hora| Y | | A hora em que foi acionou o alerta.|
|ID | Y | | Cada regra alerta tem um id exclusivo.|
|nome               |Y                  |                   | O nome do alerta.|
|Descrição        |Y                  |                           |Descrição do alerta.|
|conditionType      |Y                  |"Métrica", "Evento"          |Dois tipos de alertas são suportados. Um com base numa condição métrica e o outro baseado num evento no registo de atividade. Utilize este valor para verificar se o alerta é baseado na métrica ou evento.|
|condição          |Y                  |                           | Os campos específicos para verificar a existência com base na conditionType.|
|metricName         |para os alertas métricas  |                           |O nome da métrica que define o que monitoriza a regra.|
|metricUnit         |para os alertas métricas  |"Bytes", "BytesPerSecond", "Contar", "CountPerSecond", "Percentagem", "Segundos"|     A unidade permitida na métrica. [Valores de permitidos estão listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |para os alertas métricas  |                           |O valor real da métrica que causaram o alerta.|
|limiar          |para os alertas métricas  |                           |O valor de limiar em que o alerta é activado.|
|Tamanhodajanela         |para os alertas métricas  |                           |O período de tempo que é utilizado para monitorizar a atividade alerta com base no limiar de. Tem de estar entre 5 minutos e 1 dia. Formato de duração ISO 8601.|
|timeAggregation    |para os alertas métricas  |"Média", "Apelido", "Máximo", "Mínimo", "Nenhuma", "Total de" | Como os dados que são recolhidos devem ser combinados ao longo do tempo. O valor predefinido é média. [Valores de permitidos estão listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operador           |para os alertas métricas  |                           |O operador utilizado para comparar os dados métricos atuais para o limiar do conjunto.|
|subscriptionId     |Y                  |                           |ID da subscrição Azure.|
|resourceGroupName  |Y                  |                           |Nome do grupo de recursos para o recurso afetado.|
|NomeRecurso       |Y                  |                           |Nome do recurso do recurso afetado.|
|tipo de recurso       |Y                  |                           |Tipo de recurso do recurso afetado.|
|resourceId         |Y                  |                           |ID do recurso do recurso afetado.|
|resourceRegion     |Y                  |                           |Região ou localização do recurso afetada.|
|portalLink         |Y                  |                           |Ligação direta para a página de resumo do portal do recurso.|
|Propriedades         |N                  |Opcional                   |Conjunto de `<Key, Value>` pares (ou seja, `Dictionary<String, String>`) que inclua detalhes sobre o evento. O campo de propriedades é opcional. Num personalizado IU ou lógica com base em aplicação fluxo de trabalho, os utilizadores podem introduzir/valores de chave que pode ser passados através de carga útil. A forma alternativa para transmitir propriedades personalizadas novamente para o webhook é através de uri de webhook (como parâmetros de consulta)|


>[AZURE.NOTE] O campo de propriedades só pode ser definido utilizando a [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre alertas Azure e webhooks na vídeo [Integrar o Azure alertas com PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Executar scripts de automatização do Azure (Runbooks) no Azure alertas](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Utilizar a aplicação de lógica para enviar um SMS através do Twilio a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Utilizar a aplicação de lógica para enviar uma mensagem de folga de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Utilizar a aplicação de lógica para enviar uma mensagem para uma fila Azure a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
