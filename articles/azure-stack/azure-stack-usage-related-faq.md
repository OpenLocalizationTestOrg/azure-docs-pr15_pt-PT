<properties
    pageTitle="Perguntas mais frequentes relacionados com a utilização | Microsoft Azure"
    description="Lista de metros Azure pilha, de comparação para utilização Azure API, hora de utilização e tempo comunicados, códigos de erro."
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

# <a name="azure-stack-usage-api-faqs"></a>Perguntas mais frequentes do Azure pilha a utilização de API
Este artigo responde a algumas perguntas mais frequentes sobre a utilização de API do Azure pilha.

## <a name="what-meter-ids-can-i-see"></a>O que medidor de IDs de posso ver?

Atualmente, a utilização é comunicada de rede, armazenamento e cluster fornecedores de recursos.

| **Fornecedor de recursos** | **ID de indicador** |**Nome de um indicador de apresentar** | **Unidade** | **Informações adicionais** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Rede** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Utilização de endereço IP público | Endereço IP |                    
| **Armazenamento**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | GB\*horas | Capacidade total média consumida por tabelas |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | GB\*horas | Capacidade total média consumida por blobs de página |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | GB\*horas  | Capacidade total média consumida por fila de espera |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | GB\*horas  | Capacidade total média consumida por blobs de bloco |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Contagem de pedido de 10,000s   | Pedidos de serviço de tabela (no 10,000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Dados de penetração no GB | Penetração de dados de serviço de tabela no GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress em GB | Saída de dados de serviço de tabela no GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Contam o número de pedidos no 10,000s | Pedidos de serviço de BLOBs (no 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Dados de penetração no GB          | Blob penetração de dados de serviço no GB 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress em GB              | Saída de dados de serviço de BLOBs em GB 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Contam o número de pedidos no 10,000s   | Pedidos de serviço de fila de espera (no 10,000s) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Dados de penetração no GB         | Penetração de dados de serviço de fila em GB 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress em GB  | Saída de dados de serviço de fila em GB 
| **Cluster** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | VM tamanho horas | Tamanho de máquina virtual |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Como é que APIs de utilização do Azure pilha comparar à [API de utilização do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (atualmente na pré-visualização do público)?

-   A API a utilização do inquilino é compatível com a API do Azure, com uma exceção: o sinalizador *showDetails* actualmente não é suportado no Azure pilha.

-   Fornecedor de utilização de API só se aplica a pilha de Azure.

-   Atualmente, a [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) que está disponível no Azure não está disponível na pilha de Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>O que é a diferença entre o tempo de utilização e tempo comunicados?

Relatórios de dados de utilização têm dois valores de hora principal:

-   **Comunicado hora**. O tempo quando o evento de utilização introduzido o sistema de utilização

-   **hora de utilização**. A hora de quando o recurso de pilha de Azure foi consumido

Poderá ver uma diferença nos valores para a utilização de tempo e o tempo comunicados para um evento de utilização específicas. O atraso pode ser tão longo como vários horas em qualquer ambiente.

Atualmente, pode consultar *apenas por hora comunicado*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>O que significam estes códigos de erro a utilização de API?

| **Código de estado HTTP** | **Código de erro** | **Descrição** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Pedido 400/inválido        | *NoApiVersion*     | O parâmetro de consulta da *versão da api* está em falta.
| Pedido 400/inválido        | *InvalidProperty*  | Uma propriedade está em falta ou tem um valor inválido. A mensagem no código de erro no corpo da resposta identifica a propriedade em falta.
| Pedido 400/inválido        | *RequestEndTimeIsInFuture*  | O valor de *ReportedEndTime* é no futuro. No futuro não são permitidos valores para este argumento.
| Pedido 400/inválido        | *SubscriberIdIsNotDirectTenant*    | Uma chamada do fornecedor API utilizado um ID da subscrição que não seja um inquilino válido do autor da chamada.
| Pedido 400/inválido        | *SubscriptionIdMissingInRequest*   | O ID da subscrição do autor da chamada está em falta.
| Pedido 400/inválido        | *InvalidAggregationGranularity*   | Foi pedida uma granularidade de agregação inválido. Valores válidos são diária e hora a hora.
| 503                    | *ServiceUnavailable*   | Ocorreu um erro de repetição porque o serviço está ocupado ou a chamada está a ser limitada. |

## <a name="next-steps"></a>Próximos passos
[Cliente faturação e encargos Azure empilhados](azure-stack-billing-and-chargeback.md)

[Utilização de recursos do fornecedor API](azure-stack-provider-resource-api.md)

[Utilização de recursos API de inquilinos](azure-stack-tenant-resource-usage-api.md)
