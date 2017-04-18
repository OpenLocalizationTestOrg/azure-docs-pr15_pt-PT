<properties
    pageTitle="Utilização de recursos API de inquilinos | Microsoft Azure"
    description="Referência para utilização de recursos API, que obter informações sobre a utilização do Azure pilha."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="tenant-resource-usage-api"></a>Utilização de recursos API de inquilinos

Um inquilino pode utilizar a API do inquilino para ver os dados de utilização de recursos do inquilino. Esta API é consistente com a API a utilização do Azure (atualmente em pré-visualização privado).

Pode utilizar o cmdlet do Windows PowerShell **Get-UsageAggregates** para obter dados de utilização, como no Azure.

## <a name="api-call"></a>Chamada à API

### <a name="request"></a>Pedido

O pedido obtém detalhes consumo para as subscrições pedidas e para o período de tempo requerido. Não existe nenhuma corpo do pedido.

| **Método**  | **URI pedido** |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OBTER         | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumentos

| **Argumento**             | **Descrição** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *Armendpoint*             | Azure ponto final de Gestor de recursos do seu ambiente do Azure pilha. |
| *subId*                   | ID da subscrição do utilizador ao qual está a fazer a chamada. Pode utilizar esta API apenas a consulta para uma utilização única de uma subscrição. Fornecedores podem utilizar a API da utilização de recursos fornecedor a utilização de consulta para todos os inquilinos. |
| *reportedStartTime*       | Hora de início a consulta. O valor de *data/hora* deve ser na UTC e no início da hora, por exemplo, 13:00. Para agregação diária, defina este valor para meia-noite do UTC. O que é o formato *escape* ISO 8601, por exemplo, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, onde os dois pontos são escape para % 3a e plus escape para % 2b, para que seja URI amigável. |
| *reportedEndTime*         | Hora de fim da consulta. As restrições de que se aplicam a *reportedStartTime* também se aplicam a este argumento. O valor para *reportedEndTime* não pode ser no futuro. |
| *aggregationGranularity*  | Parâmetro opcional que tem dois valores de potenciais descontínuos: diariamente e por hora. À medida que os valores sugerem, um devolve os dados no granularidade diária e o outro é uma resolução de hora a hora. A opção diária é a predefinição. |
| *subscriberId*            | ID da subscrição. Para obter dados filtrados, é necessário o ID da subscrição de um inquilino direto do fornecedor. Se não for especificado nenhum parâmetro de ID da subscrição, a chamada devolve dados de utilização para inquilinos direta do fornecedor. |
| *versão da API*             | Versão do protocolo que é utilizado para tornar este pedido. Tem de utilizar 2015-06-01-pré-visualização. |
| *continuationToken*       | Token obtidos a partir da última chamada para o fornecedor de utilização de API. Isto é necessário quando uma resposta for maior que 1.000 linhas. Este é o marcador para o progresso da. Se não existir, os dados serem recuperados desde o início do dia ou hora, com base na granularidade transmitido. |

### <a name="response"></a>Resposta

OBTER /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diária & versão api = 1.0

{

"valor":\[

{

"id": "/ subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",

"nome": "sub1 meterID1"

"tipo": "Microsoft.Commerce/UsageAggregate",

"Propriedades": {

"subscriptionId": "sub1",

"usageStartTime": "2015-03-03T00:00:00 + 00:00",

"usageEndTime": "2015-03-04T00:00:00 + 00:00",

"instanceData": "{\\" Microsoft.Resources\\": {\\" URI do recurso\\":\\" resourceUri1\\",\\" localização\\":\\" Alasca\\",\\" etiquetas\\": nulo,\\" informações adicionais\\": nulo}}",

"quantidade":2.4000000000,

"meterId": "meterID1"

}

},

…

### <a name="response-details"></a>Detalhes da resposta

| **Argumento**      | **Descrição** |
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *ID*              | ID exclusivo do agregado de utilização |
| *nome*            | Nome do agregado de utilização |
| *tipo*            | Definição de recursos |
| *subscriptionId*  | Identificador de subscrição do utilizador Azure |
| *usageStartTime*  | UTC hora de início a balde de utilização a que pertence este agregado de utilização |
| *usageEndTime*    | Hora de fim UTC da balde de utilização a que pertence este agregado de utilização |
| *instanceData*    | Valor da chave pares dos detalhes de instância (num novo formato):<br>  *URI do recurso*: totalmente qualificado ID do recurso, incluindo os grupos de recursos e o nome da instância <br>  *localização*: região em que este serviço foi executado <br>  *etiquetas*: etiquetas de recursos que especifica o utilizador <br>  *informações adicionais*: mais detalhes sobre o recurso a ser consumido, por exemplo, tipo de imagem ou a versão do SO |
| *quantidade*        | Quantidade de consumo de recursos que ocorreram neste período de tempo |
| *meterId*         | ID exclusivo para o recurso que foi consumidas (também denominados *ResourceID*) |

## <a name="next-steps"></a>Próximos passos

[Perguntas mais frequentes relacionados com a utilização](azure-stack-usage-related-faq.md)
