<properties 
    pageTitle="Monitorize as suas aplicações lógica na aplicação de serviço de Azure | Microsoft Azure" 
    description="Como ver o que tem feito as suas aplicações de lógica" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="monitor-your-logic-apps"></a>Monitorize as suas aplicações de lógica

Depois de [criar uma aplicação de lógica](app-service-logic-create-a-logic-app.md), pode ver o histórico completo da sua execução no portal do Azure.  Também pode configurar os serviços como diagnósticos do Azure e Azure alertas para controlar eventos em tempo real e alerta para eventos gostar "quando mais do que 5 será executado falha dentro de uma hora."

## <a name="monitor-in-the-azure-portal"></a>Monitor no Portal do Azure

Para ver o histórico, selecione **Procurar**e selecione **Aplicações lógica**. É apresentada uma lista de todas as aplicações de lógica na sua subscrição.  Selecione a aplicação de lógica que pretende monitorizar.  Irá ver uma lista de todas as ações e accionadores que tenham ocorrido para esta aplicação lógica.

![Descrição geral](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Existem alguns seções sobre esta pá que são úteis:

- **Resumo** listas **Todos é executado** e o **Acionador histórico**
    - Lista **todas as é executado** a aplicação mais recente do lógica é executado.  Pode clique em qualquer linha para obter detalhes sobre a executar, ou clique no mosaico para listar mais execuções.
    - **Histórico de accionador** lista todos os a atividade de accionador para esta aplicação lógica.  Atividade de accionador poderá ser uma verificação de "Ignorado" para novos dados (por exemplo, quiser ver se um novo ficheiro foram adicionado à FTP), "Foi concluída com êxito" dados foram devolvidos seja acionada uma aplicação de lógica o que significa que, ou "Falhou" correspondente um erro na configuração.
- **Diagnósticos** permite-lhe ver detalhes de runtime e eventos e subscrever [Alertas do Azure](#adding-azure-alerts)

>[AZURE.NOTE] Todos os detalhes de runtime e eventos são encriptados em repouso dentro do serviço de aplicação de lógica. Estes são apenas desencriptar relativamente a um pedido de vista de um utilizador. Acesso a estes eventos também pode ser controlado pelo Azure Role-Based acesso controlo RBCA ().

### <a name="view-the-run-details"></a>Ver os detalhes de executar

Esta lista de será executado mostra o **Estado**, a **Hora de início**e a **duração** de específica executar. Selecione qualquer linha para ver os detalhes sobre o que são executadas.

A vista de monitorização mostra-lhe cada passo de executar, as entradas e saídas e todas as mensagens de erro que poderão ter occurre.

![Executar e ações](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Se precisar de qualquer detalhes adicionais, como o **ID de correlação** executar (que podem ser utilizados para a API REST), pode clicar no botão **Executar detalhes** .  Isto inclui todos os passos, estado e entradas/saídas para executar.

## <a name="azure-diagnostics-and-alerts"></a>Diagnósticos do Azure e alertas

Para além dos detalhes fornecidos pelo Portal do Azure e os REST API acima, pode configurar a sua aplicação de lógica a utilizar o Azure diagnósticos para mais detalhes avançadas e depuração.

1. Clique na secção **Diagnósticos** do pá de aplicação a lógica
1. Clique aqui para configurar as **Definições de diagnóstico**
1. Configurar um concentrador de evento ou a conta de armazenamento a emitir dados para

    ![Definições dos diagnósticos do Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Adicionar alertas Azure

Depois de diagnóstico está configurado, pode adicionar Azure alertas quando são cruzados certos limiares.  Na pá **Diagnósticos** , selecione o mosaico de **alertas** e **Adicionar alerta**.  Isto irá guiá-lo a configurar um alerta com base num número de limiares e métricas.

![Métricas de alertas Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Pode configurar a **condição**, **limiar**e **período** conforme pretender.  Por fim, pode configurar um endereço de e-mail para enviar uma notificação ou configurar uma webhook.  Pode utilizar o [acionador pedido](../connectors/connectors-native-reqres.md) numa aplicação lógica para executar num alerta também (para fazer coisas como [Publicar margem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [Enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)ou [Adicionar uma mensagem para uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### <a name="azure-diagnostics-settings"></a>Definições dos diagnósticos do Azure

Cada um dos seguintes eventos contém detalhes sobre a aplicação de lógica e um evento como estado.  Eis um exemplo de um evento *ActionCompleted* :

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

As duas propriedades que são particularmente úteis para o registo e monitorização são *clientTrackingId* e *trackedProperties*.  

#### <a name="client-tracking-id"></a>ID de controlo do cliente

O cliente ID rastreio é um valor que será correlacionar eventos através de uma aplicação de lógica executar, incluindo quaisquer fluxos de trabalho aninhados chamados como uma parte de uma aplicação de lógica.  Este ID vai ser gerados automaticamente se não for fornecida, mas pode especificar o cliente rastreio ID a partir de um accionador passando manualmente uma `x-ms-client-tracking-id` cabeçalho com o valor de ID no pedido de accionador (accionador pedido, acionador HTTP ou webhook accionador).

#### <a name="tracked-properties"></a>Propriedades registadas

Propriedades registadas podem ser adicionadas para ações na definição do fluxo de trabalho para controlar factores de produção ou saídas nos dados de diagnóstico.  Isto pode ser útil se pretender controlar os dados como um "ID da encomenda" no seu telemetria.  Para adicionar uma propriedade registada, inclua o `trackedProperties` propriedade uma ação.  Faixa de apenas de propriedades registadas podem entradas uma única ações e resultados, mas pode utilizar o `correlation` propriedades dos eventos para correlacionar através de ações num executar.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>Expandir as soluções

Pode tirar partido este telemetria a partir do concentrador de evento ou de armazenamento para outros serviços como o [Conjunto de aplicações de gestão de operações](https://www.microsoft.com/cloud-platform/operations-management-suite), Azure sequência e de [Análise](https://azure.microsoft.com/services/stream-analytics/) [Do Power BI](https://powerbi.com) para ter em tempo real de monitorização dos fluxos de trabalho integração.

## <a name="next-steps"></a>Próximos passos
- [Exemplos de comuns e cenários para as aplicações de lógica](app-service-logic-examples-and-scenarios.md)
- [Criar um modelo de implementação de aplicação de lógica](app-service-logic-create-deploy-template.md)
- [Funcionalidades de integração de empresa](app-service-logic-enterprise-integration-overview.md)
