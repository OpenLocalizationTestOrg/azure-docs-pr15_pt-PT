<properties
    pageTitle="Arquivar o registo de atividade Azure | Microsoft Azure"
    description="Saiba como arquivar o registo de atividade Azure para a longo prazo retenção numa conta de armazenamento."
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>Arquivar o registo de atividade Azure
Neste artigo, iremos mostrar como pode utilizar o portal do Azure, os cmdlets do PowerShell ou em diferentes plataformas clip para arquivar o [**Registo de atividade Azure**](monitoring-overview-activity-logs.md) numa conta de armazenamento. Esta opção é útil se pretender manter o registo de atividade mais de cerca de 90 dias (com o controlo total sobre a política de retenção) auditoria, análise estático ou cópia de segurança. Se só precisa de manter os seus eventos de 90 dias ou menos que não é necessário configurar o arquivo para uma conta de armazenamento, uma vez que eventos de registo de atividade são mantidos na plataforma Azure para cerca de 90 dias sem activar arquivo.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para o qual pode arquivar o registo de atividade. Recomendamos vivamente que não utilize uma conta de armazenamento existente que tenha outros, não monitorização dados armazenados no mesmo para que melhor pode controlar o acesso a dados de monitorização. No entanto, se também são arquivar registos de diagnóstico e métricas a uma conta de armazenamento, poderá fazer sentido para utilizar essa conta de armazenamento para o registo de atividade para manter todos os dados de monitorização numa localização central. A conta de armazenamento que utiliza tem de ser uma conta de armazenamento de objectivo geral, não uma conta de armazenamento de Blobs.

## <a name="log-profile"></a>Perfil de registo
Para arquivar o registo de atividade utilizar qualquer um dos métodos abaixo, defina o **Registo de perfil** para uma subscrição. O perfil de registo define o tipo de eventos que são armazenados ou transmitido em sequência e saídas de — concentrador de conta e/ou evento de armazenamento. Também define a política de retenção (número de dias para reter) para eventos armazenados numa conta de armazenamento. Se a política de retenção é definida como zero, eventos são armazenados indefinidamente. Caso contrário, este pode ser definido para qualquer valor entre 1 e 2147483647. [Pode ler mais acerca do registo de perfis de aqui](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## <a name="archive-the-activity-log-using-the-portal"></a>Arquivar o registo de atividade através do portal
1. No portal do, clique na ligação de **Registo de atividade** na navegação do lado esquerdo. Se não vir uma ligação para o registo de atividade, clique na ligação de **Mais serviços** pela primeira vez.

    ![Navegue para pá de registo de atividade](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Na parte superior da pá, clique em **Exportar**.

    ![Clique no botão Exportar](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Na pá que aparece, selecione a caixa para **Exportar para uma conta de armazenamento** e selecione uma conta de armazenamento.

    ![Configurar uma conta de armazenamento](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Utilizar o controlo de deslize ou caixa de texto, defina um número de dias para a qual devem ser mantidos em eventos de registo de atividade na sua conta de armazenamento. Se preferir ter os seus dados persistentes na conta de armazenamento indefinidamente, defina este número como zero.
5. Clique em **Guardar**.

## <a name="archive-the-activity-log-via-powershell"></a>Arquivar o registo de atividade através do PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Propriedade         | Obrigatório | Descrição                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | N       | ID do recurso da conta de armazenamento à qual deve ser guardados registos de atividade.                                                                                                                                                                                                                        |
| Localizações        | Sim      | Lista das regiões para as quais gostaria de recolher eventos de registo de atividade separados por vírgulas. Pode ver uma lista de todas as regiões [acedendo a esta página](https://azure.microsoft.com/en-us/regions) ou ao utilizar [A gestão de REST API do Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays  | Sim      | Número de dias para os eventos que devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente (uma eternidade).                                                                                                                                                                                             |
| Categorias       | Sim      | Lista de categorias de eventos que devem ser recolhidos separados por vírgulas. Valores possíveis são escrita, eliminar e ação.                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>Arquivar o registo de atividade através do Clip
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Propriedade        | Obrigatório | Descrição                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome            | Sim      | Nome do seu perfil de registo.                                                                                                                                                                                                                                                                         |
| storageId       | N       | ID do recurso da conta de armazenamento à qual deve ser guardados registos de atividade.                                                                                                                                                                                                                        |
| localizações       | Sim      | Lista das regiões para as quais gostaria de recolher eventos de registo de atividade separados por vírgulas. Pode ver uma lista de todas as regiões [acedendo a esta página](https://azure.microsoft.com/en-us/regions) ou ao utilizar [A gestão de REST API do Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Sim      | Número de dias para os eventos que devem ser mantidos, entre 1 e 2147483647. Um valor de zero irá guardar os registos indefinidamente (uma eternidade).                                                                                                                                                                                             |
| categorias      | Sim      | Lista de categorias de eventos que devem ser recolhidos separados por vírgulas. Valores possíveis são escrita, eliminar e ação.                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>Esquema de armazenamento do registo de atividade
Assim que configurou arquivo, um contentor de armazenamento será criado na conta de armazenamento assim que ocorre um evento de registo de atividade. Blobs dentro do contentor sigam o mesmo formato no registo de atividade e os registos de diagnóstico. A estrutura destes blobs é:

> informações operacionais nome-de-registos / = predefinido/resourceId = / subscrições / {ID da subscrição} / y = {quatro dígitos numérico ano} / m = {dois dígitos numéricos mês} / d = {dois dígitos numérico dia} / h = {dois dígitos relógio de 24 hour}/m=00/PT1H.json

Por exemplo, poderá ser um nome de BLOBs:

> insights-Operational-logs/Name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON

Cada blob PT1H.json contém um blob JSON dos eventos que ocorreram na hora especificada no blob URL (por exemplo, h = 12). Durante a hora atual, eventos são anexados para o ficheiro PT1H.json medida que estes ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que eventos de registo de atividade são divididos em blobs individuais por hora.

Dentro do ficheiro PT1H.json, cada evento é armazenado na matriz "registos", seguir este formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nome do elemento    | Descrição                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| tempo            | Carimbo quando o evento foi gerado pelo serviço Azure processamento do pedido correspondente o evento.    |
| resourceId      | ID do recurso do recurso afetado.                                                                          |
| operationName   | Nome da operação.                                                                                         |
| categoria        | Categoria de ação, por ex. Escrever, ler, a ação.                                                               |
| resultType      | O tipo de resultado, por ex. Êxito, falha, início                                                            |
| resultSignature | Depende do tipo de recurso.                                                                                  |
| durationMs      | Duração de operação em milissegundos                                                                      |
| callerIpAddress | Endereço IP do utilizador ao qual foi efetuada a operação, afirmação UPN ou afirmação SPN com base na disponibilidade.         |
| correlationId   | Normalmente, um GUID no formato de cadeia. Eventos que partilham um correlationId pertencem à ação uber mesmo.         |
| identidade        | Blob JSON que descrevem a autorização e em afirmações.                                                             |
| autorização   | Blob das propriedades RBAC do evento. Normalmente, inclui as propriedades "ação", "função" e "âmbito".            |
| nível           | Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativo" e "Verboso" |
| localização        | Região em que ocorreu a localização (ou global).                                                             |
| Propriedades      | Conjunto de `<Key, Value>` pares (ou seja, dicionário) que descrevem os detalhes do evento.                             |

> [AZURE.NOTE] As propriedades e a utilização dessas propriedades podem variar consoante o recurso.

## <a name="next-steps"></a>Próximos passos
- [Transferir blobs para uma análise](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [O registo de atividade para concentradores evento a sequência](monitoring-stream-activity-logs-event-hubs.md)
- [Saiba mais acerca do registo de atividade](monitoring-overview-activity-logs.md)
