<properties
    pageTitle="Arquivar os registos de diagnóstico Azure | Microsoft Azure"
    description="Saiba como arquivar os seus registos de diagnóstico do Azure para a longo prazo retenção numa conta de armazenamento."
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
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Arquivar Azure registos de diagnóstico
Neste artigo, iremos mostrar como pode utilizar o portal do Azure, os cmdlets do PowerShell, clip ou REST API para arquivar os seus [Registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md) numa conta de armazenamento. Esta opção é útil se pretender manter os seus registos de diagnóstico com uma política de retenção opcional de auditoria, análise estático ou cópia de segurança.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para o qual pode arquivar registos de diagnóstico. Recomendamos vivamente que não utilize uma conta de armazenamento existente que tenha outros, não monitorização dados armazenados no mesmo para que melhor pode controlar o acesso a dados de monitorização. No entanto, se também são arquivar o registo de atividade e diagnóstico métricas a uma conta de armazenamento, poderá fazer sentido para utilizar essa conta de armazenamento para os seus registos de diagnóstico para manter todos os dados de monitorização numa localização central. A conta de armazenamento que utiliza tem de ser uma conta de armazenamento de objectivo geral, não uma conta de armazenamento de Blobs.

## <a name="diagnostic-settings"></a>Definições de diagnóstico
Para arquivar os seus registos de diagnóstico utilizar qualquer um dos métodos abaixo, definir uma **Definição diagnóstico** para um recurso em particular. Uma definição de diagnóstico para um recurso define as categorias de registos que se encontrem que são armazenados ou transmitido em sequência e saídas de — concentrador de conta e/ou evento de armazenamento. Também define a política de retenção (número de dias para reter) para eventos de cada categoria de registo armazenados numa conta de armazenamento. Se estiver definida uma política de retenção a zero, eventos para essa categoria registo são armazenados indefinidamente (isto é uma eternidade dizer). Uma política de retenção caso contrário, pode ser qualquer número de dias entre 1 e 2147483647. [Pode ler mais sobre definições de diagnóstico aqui](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Registos de diagnóstico arquivo através do portal

1. No portal do, clique em para o pá recurso para o recurso no qual pretende ativar o arquivo de registos de diagnóstico.
2. Na secção de **monitorização** do menu de definições de recurso, selecione **diagnóstico**.

    ![Secção de monitorização do menu de recursos](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Selecione a caixa para **Exportar para a conta de armazenamento**e, em seguida, selecione uma conta de armazenamento. Opcionalmente, definir um número de dias para guardar estes registos utilizando o **retenção (dias)** controlos de deslize. Uma retenção de zero dias armazena os registos indefinidamente.

    ![Diagnóstico pá de registos](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Clique em **Guardar**.

Registos de diagnóstico são arquivados a essa conta de armazenamento assim novos dados de evento são gerados.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Registos de diagnóstico arquivo via os cmdlets do PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriedade         | Obrigatório | Descrição                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId       | Sim      | ID do recurso do recurso no qual pretende definir uma definição de diagnóstico.                            |
| StorageAccountId | N       | ID do recurso da conta de armazenamento à qual deve ser guardados registos de diagnóstico.                          |
| Categorias       | N       | Lista de categorias de registo para ativar separados por vírgulas.                                                     |
| Ativado          | Sim      | Booleano que indica se diagnósticos estão ativados ou desativados deste recurso.                  |
| RetentionEnabled | N       | Booleano que indica se uma política de retenção estão activadas deste recurso.                            |
| RetentionInDays  | N       | Número de dias para a qual devem ser retidos eventos entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Arquivar o registo de atividade através de clip em diferentes plataformas

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriedade         | Obrigatório | Descrição                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId       | Sim      | ID do recurso do recurso no qual pretende definir uma definição de diagnóstico.                            |
| storageId        | N       | ID do recurso da conta de armazenamento à qual deve ser guardados registos de diagnóstico.                          |
| categorias       | N       | Lista de categorias de registo para ativar separados por vírgulas.                                                     |
| ativado          | Sim      | Booleano que indica se diagnóstico está ativado ou desativado deste recurso.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Registos de diagnóstico arquivo através da API REST
[Consulte este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx) para obter informações sobre como podem configurar uma definição de diagnóstico utilizar a API do Azure Monitor restantes.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema de registos de diagnóstico na conta de armazenamento
Assim que configurou arquivo, um contentor de armazenamento é criado na conta de armazenamento, assim que ocorre um evento de uma das categorias de registo que ativou. Blobs dentro do contentor sigam o mesmo formato nos registos de diagnóstico e registo de atividade. A estrutura destes blobs é:

> informações - registos-{nome da categoria registo} / resourceId = / subscrições / {ID da subscrição} /RESOURCEGROUPS/ {nome do grupo de recursos} /PROVIDERS/ {nome do fornecedor de recurso} / {recurso escreva} / {nome do recurso} / y = {quatro dígitos numérico ano} / m = {dois dígitos numéricos mês} / d = {dois dígitos numérico dia} / h = {dois dígitos relógio de 24 hour}/m=00/PT1H.json

Ou, mais simples,

> informações - registos-{nome da categoria registo} / resourceId = / {recurso Id} / y = {quatro dígitos numérico ano} / m = {dois dígitos numéricos mês} / d = {dois dígitos numérico dia} / h = {dois dígitos relógio de 24 hour}/m=00/PT1H.json

Por exemplo, poderá ser um nome de BLOBs:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Cada blob PT1H.json contém um blob JSON dos eventos que ocorreram na hora especificada no blob URL (por exemplo, h = 12). Durante a hora atual, eventos são anexados para o ficheiro PT1H.json medida que estes ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que eventos de registo de diagnóstico são divididos em blobs individuais por hora.

Dentro do ficheiro PT1H.json, cada evento é armazenado na matriz "registos", seguir este formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nome do elemento  | Descrição                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| tempo          | Carimbo quando o evento foi gerado pelo serviço Azure processamento do pedido correspondente o evento. |
| resourceId    | ID do recurso do recurso afetado.                                                                       |
| operationName | Nome da operação.                                                                                      |
| categoria      | Categoria de registo do evento.                                                                                  |
| Propriedades    | Conjunto de `<Key, Value>` pares (ou seja, dicionário) que descrevem os detalhes do evento.                            |

> [AZURE.NOTE] As propriedades e a utilização dessas propriedades podem variar consoante o recurso.

## <a name="next-steps"></a>Próximos passos
- [Transferir blobs para uma análise](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Registos de diagnóstico da cadeia a concentradores de evento](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Leia mais sobre registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md)
