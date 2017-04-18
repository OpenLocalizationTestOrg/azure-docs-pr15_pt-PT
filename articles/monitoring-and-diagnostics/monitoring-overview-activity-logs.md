<properties
    pageTitle="Descrição geral do registo de atividade Azure | Microsoft Azure"
    description="Saiba o que é o registo de atividade do Azure e como pode utilizá-lo para compreender os eventos que ocorram na sua subscrição do Azure."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Descrição geral do registo de atividade Azure
O **Registo de atividade Azure** é um registo que fornece visão as operações que foram efetuadas no recursos na sua subscrição. O registo de atividade anteriormente era conhecido como "Registos de auditoria" ou "Registos operacionais", uma vez que os relatórios de eventos de plano de controlo para as suas subscrições. Utilizar o registo de atividade, pode determinar o ' o quê, quem e quando ' para qualquer operações (hoje, publicar, DELETE) tomadas sobre os recursos na sua subscrição de escrita. Também pode compreender o estado da operação e outras propriedades relevantes. O registo de atividade não inclui operações de leitura (GET).

O registo de atividade difere [Registos de diagnóstico](./monitoring-overview-of-diagnostic-logs.md), que são todos os registos emitidos por um recurso. Estes registos fornecem dados sobre o funcionamento do recurso, em vez de operações desse recurso.

Pode obter eventos a partir do seu registo de atividade utilizando o portal do Azure, clip, os cmdlets do PowerShell e Azure Monitor REST API.

Veja este [vídeo de introdução ao registo de atividade](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>O que pode fazer com o registo de atividade
Aqui estão algumas das coisas que pode fazer com o registo de atividade:

- Consulta e vê-lo no **portal do Azure**.
- Consulta-lo através de REST API, Cmdlet do PowerShell ou clip.
- [Crie um alerta de e-mail ou webhook que accionadores desativar um evento de registo de atividade.](./insights-auditlog-to-webhook-email.md)
- [Guardá-lo para uma **Conta de armazenamento** para inspeção de arquivo ou manual](./monitoring-archive-activity-log.md). Pode especificar o tempo de retenção (em dias) utilizar **Perfis de registo**.
- Analisá-los no obter o [**pacote de conteúdo de obter**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)a utilizar.
- A [sequência-lo a um **Concentrador de evento** ](./monitoring-stream-activity-logs-event-hubs.md) para ingestão por um serviço de terceiros ou solução de análise personalizada como obter.

## <a name="export-the-activity-log-with-log-profiles"></a>Exportar o registo de atividade com os perfis de registo
Um **Perfil de registo** controla a forma como o seu registo de atividade é exportado. Utilizar um perfil de registo, pode configurar:

- Onde o registo de atividade deve ser enviado (conta de armazenamento ou evento concentradores)
- As categorias de eventos (escrita, eliminar, acção) devem ser enviadas
- Que regiões (localizações) devem ser exportadas
- Quanto tempo o registo de atividade devem ser mantido numa conta de armazenamento – uma retenção de zero dias significa que são mantidos uma eternidade registos. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647. Se as políticas de retenção são definidas mas armazenar registos de início de uma conta de armazenamento é desativada (por exemplo, se as opções de evento concentradores ou OMS estiverem selecionadas apenas), as políticas de retenção não tem qualquer efeito.

Estas definições podem ser configuradas através da opção de "Exportar" no pá registo de atividade no portal. Também podem ser configuradas através de programação de [utilizar a API do Azure Monitor resto](https://msdn.microsoft.com/library/azure/dn931927.aspx), os cmdlets do PowerShell ou clip. Uma subscrição só pode ter um perfil de registo.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar perfis de registo através do portal Azure
Pode transmitir em fluxo o registo de atividade a um concentrador de evento ou armazená-los numa conta armazenamento utilizando a opção de "Exportação" no portal do Azure.

1. Navegue para a pá de **Registo de atividade** através do menu no lado esquerdo do portal.

    ![Navegue para o registo de atividade no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** no topo da pá.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na pá que aparece, pode selecionar:  
    - regiões para o qual pretende exportar eventos
    - a conta de armazenamento à qual pretende guardar os eventos
    - o número de dias que pretende manter estes eventos no armazenamento. Uma definição de 0 dias retém uma eternidade os registos.
    - Serviço Bus espaço de nomes na qual gostaria um concentrador de evento seja criado por transmissão estes eventos.

    ![Exportar pá de registo de atividade](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Guardar** para guardar estas definições. As definições estão aplicada imediatamente à sua subscrição.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar perfis de registo com os Cmdlets do PowerShell Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de registo existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de registo
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriedade         | Obrigatório | Descrição   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome             | Sim      | Nome do seu perfil de registo.                                 |
| StorageAccountId | N       | ID do recurso da conta de armazenamento para o qual o registo de atividade deve ser guardado.                         |
| serviceBusRuleId | N       | Serviço Bus ID da regra para o espaço de nomes de Bus de serviço que pretende ter concentradores evento criados no. É uma cadeia com neste formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Localizações        | Sim      | Lista das regiões para as quais gostaria de recolher eventos de registo de atividade separados por vírgulas.              |
| RetentionInDays  | Sim      | Número de dias para os eventos que devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente (uma eternidade). |
| Categorias       | N       | Lista de categorias de eventos que devem ser recolhidos separados por vírgulas. Valores possíveis são escrita, eliminar e ação.                                 |

#### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurar perfis de registo utilizando o clip de em diferentes plataformas Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de registo existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
O `name` propriedade deve ser o nome do seu perfil de registo.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de registo
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriedade         | Obrigatório | Descrição   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome             | Sim      | Nome do seu perfil de registo.                                 |
| storageId        | N       | ID do recurso da conta de armazenamento para o qual o registo de atividade deve ser guardado.                         |
| serviceBusRuleId | N       | Serviço Bus ID da regra para o espaço de nomes de Bus de serviço que pretende ter concentradores evento criados no. É uma cadeia com neste formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| localizações        | Sim      | Lista das regiões para as quais gostaria de recolher eventos de registo de atividade separados por vírgulas.              |
| retentionInDays  | Sim      | Número de dias para os eventos que devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente (uma eternidade).     |
| categorias       | N       | Lista de categorias de eventos que devem ser recolhidos separados por vírgulas. Valores possíveis são escrita, eliminar e ação.                                 |

#### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Esquema de eventos
Cada evento no registo de atividade tem um blob JSON semelhante a este exemplo:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nome do elemento         | Descrição             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorização        | Blob das propriedades RBAC do evento. Normalmente, inclui as propriedades "ação", "função" e "âmbito".|
| autor da chamada               | Endereço de e-mail do utilizador ao qual foi efetuada a operação, afirmação UPN ou afirmação SPN com base na disponibilidade.|
| canais             | Um dos seguintes valores: "Admin", "A operação"|
| correlationId        | Normalmente, um GUID no formato de cadeia. Eventos que partilham um correlationId pertencem à ação uber mesmo.   |
| Descrição          | Descrição em texto estático do evento.                              |
| eventDataId          | Identificador exclusivo do evento.    |
| OrigemEvento          | Nome do Azure serviço ou infraestrutura gerou este evento.    |
| httpRequest          | BLOB que descreve o pedido de Http. Normalmente, inclui o "clientRequestId", "clientIpAddress" e "método" (método HTTP. Por exemplo, colocar).                            |
| nível                | Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativo" e "Verboso"  |
| resourceGroupName    | Nome do grupo de recursos para o recurso afetado.               |
| resourceProviderName | Nome do fornecedor de recursos para o recurso afetado             |
| URI do recurso          | Id do recurso do recurso afetado.                               |
| IDOperação          | Um GUID partilhado entre os eventos que correspondem a uma única operação.         |
| operationName        | Nome da operação.  |
| Propriedades           | Conjunto de `<Key, Value>` pares (ou seja, um dicionário) que descrevem os detalhes do evento.                                |
| Estado               | Cadeia que descreve o estado da operação de. Alguns valores comuns são: iniciado, em curso, com êxito, falha, ativo, resolvido.                                |
| Subestado            | Normalmente, o código de estado HTTP do resto correspondente chamada, mas também pode incluir outras cadeias que descreve um subestado, tal como estes valores comuns: OK (código de estado HTTP: 200), criado (código de estado HTTP: 201), aceite (código de estado HTTP: 202), não conteúdo (código de estado HTTP: 204), pedido incorrecto (código de estado HTTP: 400), não encontrado (código de estado HTTP: 404), conflito (código de estado HTTP : 409), o erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503), tempo limite do Gateway (código de estado HTTP: 504). |
| eventTimestamp       | Carimbo quando o evento foi gerado pelo serviço Azure processamento do pedido correspondente o evento.     |
| submissionTimestamp  | Carimbo quando o evento ficou disponível para consultar.             |
| subscriptionId       | ID da subscrição Azure.  |
| nextLink             | Token de continuação de notas para obter o próximo conjunto de resultados quando estes são divididos em várias respostas. Normalmente, é necessário quando existirem mais de 200 registos.     |

## <a name="next-steps"></a>Próximos passos
- [Saiba mais acerca do registo de atividade (anteriormente registos de auditoria)](../resource-group-audit.md)
- [O registo de atividade Azure concentradores evento a sequência](./monitoring-stream-activity-logs-event-hubs.md)
