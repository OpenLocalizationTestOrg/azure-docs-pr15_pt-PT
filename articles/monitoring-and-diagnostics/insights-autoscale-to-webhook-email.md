<properties
    pageTitle="Utilize as ações de autoscale para enviar e-mails e webhook as notificações de alerta. | Microsoft Azure"
    description="Veja como utilizar as ações de autoscale para ligar para o URL da web ou enviar notificações de correio eletrónico no Monitor do Azure. "
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
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Utilizar as ações de autoscale para enviar e-mails e webhook as notificações de alerta no Monitor do Azure

Este artigo mostra-lhe como configurar o accionadores para que possa ligar URLs web específica ou enviar e-mails com base no autoscale ações no Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks permitem-lhe encaminhar as Azure notificações de alerta para outros sistemas para notificações pós transformação ou personalizadas. Por exemplo, encaminhamento o alerta para serviços que podem processar um pedido de web recebidas para enviar que SMS, registo de erros, notifique uma equipa utilizar chat ou mensagens de serviços, etc. O webhook URI tem de ser um ponto final válido de HTTP ou HTTPS.

## <a name="email"></a>Mensagem de correio electrónico
Correio eletrónico pode ser enviado para qualquer endereço de e-mail válido. Os administradores e coadministradores da subscrição onde a regra está em execução, também serão notificados.


## <a name="cloud-services-and-web-apps"></a>Serviços em nuvem e Web Apps
Pode optar por não participar na partir do Azure portal para serviços em nuvem e os Farms (Web Apps).

- Selecione a **escala por** métrica.

![Dimensionar por](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de máquina virtual
Para mais recente máquinas virtuais a criados com o Gestor de recursos (conjuntos de escala de Máquina Virtual), pode configurar esta utilizando REST API, modelos de Gestor de recursos, PowerShell e clip. Uma interface portal ainda não está disponível.
Ao utilizar o modelo REST API ou Gestor de recursos, inclua o elemento de notificações com as seguintes opções.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Campo                              |Obrigatório? |Descrição|
|---                                |---        |---|
|operação                          |Sim        |valor tem de ser "Escala"|
|sendToSubscriptionAdministrator    |Sim        |valor tem de ser "true" ou "false"|
|sendToSubscriptionCoAdministrators |Sim        |valor tem de ser "true" ou "false"|
|customEmails                       |Sim        |valor pode ser [nulo ou matriz de cadeia de e-mails|
|webhooks                           |Sim        |valor pode ser Uri nulo ou válido|
|serviceUri                         |Sim        |um https válido Uri|
|Propriedades                         |Sim        |valor tem de ser {} vazia ou pode conter pares valor de chave|


## <a name="authentication-in-webhooks"></a>Autenticação na webhooks
Existem duas formas URI de autenticação:

1. Base de token de autenticação, onde guardar a webhook URI com um ID token como um parâmetro de consulta. Por exemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticação básica, onde utilizar um ID de utilizador e palavra-passe. Por exemplo,https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de carga útil Autoscale notificação webhook
Quando a notificação de autoscale é gerada, os metadados que se segue são incluído na carga webhook útil:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Campo  |Obrigatório?|    Descrição|
|---|---|---|
|Estado |Sim    |O estado que indica que uma ação autoscale foi gerada|
|operação| Sim |Para um aumento de instâncias, irá estar "Escala saída" e para uma diminuição no instâncias, irá estar "Escala em"|
|contexto|   Sim |O contexto de ação autoscale|
|data/hora| Sim |Carimbo de hora quando a ação autoscale foi acionou|
|ID |Sim|   ID do recurso Gestor da definição de autoscale|
|nome   |Sim|   O nome da definição autoscale|
|Detalhes|   Sim |Explicação da ação que tirou com o serviço de autoscale e a alteração na contagem instância|
|subscriptionId|    Sim |ID da subscrição do recurso de destino que está a ser dimensionado|
|resourceGroupName| Sim|    Nome do grupo de recursos do recurso destino que está a ser dimensionado|
|NomeRecurso   |Sim|   Nome do recurso de destino que está a ser dimensionado|
|tipo de recurso   |Sim|   Os três valores de suportados: "microsoft.classiccompute/domainnames/slots/roles" - funções do serviço na nuvem, "microsoft.compute/virtualmachinescalesets" - conjuntos de escala de Máquina Virtual e "Microsoft.Web/serverfarms" - Web App|
|resourceId |Sim|ID do recurso Gestor do recurso de destino que está a ser dimensionado|
|portalLink |Sim    |Azure ligação portal para a página de resumo do recurso de destino|
|oldCapacity|   Sim |A (antigo) instância contagem atual quando Autoscale demorou uma ação de escala|
|newCapacity|   Sim |A contagem de instância nova Autoscale dimensionada o recurso|
|Propriedades|    N| Opcional. Conjunto de < chave, valor > pares (por exemplo, dicionário < cadeia, cadeia >). O campo de propriedades é opcional. Uma interface de utilizador personalizada ou o fluxo de trabalho do lógica aplicação com base, pode introduzir teclas e os valores que podem ser passados utilizando a carga de útil. Uma forma alternativa para transmitir propriedades personalizadas novamente para a chamada de saída webhook é utilizar a webhook URI propriamente dito (como parâmetros de consulta)|
