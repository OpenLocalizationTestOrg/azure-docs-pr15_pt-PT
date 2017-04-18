<properties
   pageTitle="Limites de pedido de Gestor de recursos Azure | Microsoft Azure"
   description="Descreve como utilizar a limitação com pedidos de Gestor de recursos do Azure quando atingiu limites de subscrição."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>Limitação de pedidos de Gestor de recursos

Para cada subscrição e inquilino, limites de Gestor de recursos pedidos de leitura para 15.000 por hora pedidos e escreverem para 1.200 por hora. Se a sua aplicação ou script atingir estes limites, tem de optimizar a pedidos de. Este tópico mostra-lhe como determinar os pedidos de restantes que tiver antes de atingir o limite e como responder quando atingiu o limite.

Quando chegar o limite de, recebe o código de estado HTTP **demasiados 429 pedidos de muitos**.

O número de pedidos está confinado a sua subscrição ou o inquilino. Se tiver múltiplos, aplicações em simultâneo, fazer pedidos na sua subscrição, os pedidos de nessas aplicações são adicionados em conjunto para determinar o número de pedidos de restantes.

Pedidos de subscrição confinado são aqueles involve prisma o seu id da subscrição, tal como obter o recurso grupos na sua subscrição. Pedidos de inquilino confinado não inclui o seu id da subscrição, tal como a obter localizações Azure válidas.

## <a name="remaining-requests"></a>Pedidos de restantes

Saber qual é o número de pedidos restantes, examinar os cabeçalhos de resposta. Cada pedido inclui valores para o número de leitura restante e pedidos de escrita. A tabela seguinte descreve os cabeçalhos de resposta que pode examinar esses valores:

| Cabeçalho de resposta | Descrição |
| --------------- | ----------- |
| x-MS-ratelimit-Remaining-Subscription-Reads | Subscrição confinada lê restante |
| x-MS-ratelimit-Remaining-Subscription-Writes | Subscrição confinada escreve restante |
| x-MS-ratelimit-Remaining-tenant-Reads | Inquilino confinado lê restante |
| x-MS-ratelimit-Remaining-tenant-Writes | Inquilino confinado escreve restante |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests | Subscrição delimitada pedidos de tipo de recurso restante.<br /><br />Este valor cabeçalho apenas é devolvido se um serviço substituiu o limite predefinido. Gestor de recursos adiciona este valor em vez de subscrição lê ou escritas. |
| x-MS-ratelimit-Remaining-Subscription-Resource-ENTITIES-Read | Subscrição delimitada pedidos da colecção de tipo de recurso restante.<br /><br />Este valor cabeçalho apenas é devolvido se um serviço substituiu o limite predefinido. Este valor fornece o número de pedidos de coleções de sites restantes (recursos da lista). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests | Inquilino delimitada pedidos de tipo de recurso restante.<br /><br />Este cabeçalho é adicionado apenas para pedidos de ao nível do inquilino e só se um serviço substituiu o limite predefinido. Gestor de recursos adiciona este valor em vez de inquilino lê ou escritas. |
| x-MS-ratelimit-Remaining-tenant-Resource-ENTITIES-Read | Inquilino delimitada pedidos da colecção de tipo de recurso restante.<br /><br />Este cabeçalho é adicionado apenas para pedidos de ao nível do inquilino e só se um serviço substituiu o limite predefinido. |

## <a name="retrieving-the-header-values"></a>Obter os valores de cabeçalho

Obter estes valores de cabeçalho no seu código ou script é não diferente de obtenção de qualquer valor de cabeçalho. 

Por exemplo, no **c#**, obtém o valor de cabeçalho a partir de um objeto de **HttpWebResponse** denominado **resposta** com o seguinte código:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

**PowerShell**, obtém o valor de cabeçalho a partir de uma operação de invocar WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
Ou, se pretende ver os restantes pedidos de depuração, pode fornecer a **-Depurar** parâmetro na sua cmdlet do **PowerShell** .

    Get-AzureRmResourceGroup -Debug
    
Que devolve muitas informações, incluindo o valor de resposta seguintes:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

**Clip do Azure**, obtém o valor do cabeçalho utilizando a opção mais verbosa.

    azure group list -vv --json

Que devolve muitas informações, incluindo o objeto seguinte:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>A aguardar antes de enviar o pedido seguinte

Quando chegar o limite do pedido, Gestor de recursos devolve o código de estado **429** HTTP e um **Depois de repetir** valor no cabeçalho. A **Repetir-após** o valor Especifica o número de segundos da aplicação deverá aguardar (ou suspensão) antes de enviar o pedido seguinte. Se enviar um pedido de antes do valor de repetir decorrido, o pedido não está a ser processado e é devolvido um novo valor de repetir.
