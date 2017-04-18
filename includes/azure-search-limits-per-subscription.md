Pode criar vários serviços dentro de uma subscrição, cada um deles aprovisionadas numa camada específica, limitada apenas pelo número de serviços permitido em cada camada. Por exemplo, podia criar até 12 serviços na camada básica e outra 12 na camada S1 dentro da mesma subscrição. Para mais informações sobre camadas, consulte o artigo [Escolher um SKU ou camada para a pesquisa do Azure](../articles/search/search-sku-tier.md).

Limites de máxima de serviço podem ser elevados a pedido. Contacte o suporte do Azure se precisar de mais serviços na mesma subscrição.

Recurso|Gratuito|Básicas|S1|S2|S3 |S3 HD <sup>1</sup>
---|---|---|---|----|---|----
Serviços de máximos |1 |12 |12  |6 |6 |6 
Escala máxima no DOM <sup>2</sup>|N / <sup>3</sup>|3 DOM <sup>4</sup> |DOM 36|DOM 36|DOM 36|DOM 12, 3 DOM <sup>5</sup>

<sup>1</sup> S3 HD não suporta [indexadores](../articles/search/search-indexer-overview.md) neste momento. 

<sup>2</sup> unidades de pesquisa (DOM) são unidades cobrar por serviço, atribuídos como uma *réplica* ou uma *partição*. Precisa de ambos os recursos para armazenamento, a indexação e operações de consulta. Para saber mais sobre as combinações válidas que manter-se dos limites máximos, consulte o artigo [níveis de recursos de escala para consulta e índice remissivo das cargas de trabalho](../articles/search/search-capacity-planning.md). 

<sup>3</sup> Free baseia recursos partilhados utilizados pelo múltiplos subscritores. Neste camada, não existem recursos dedicados para um subscritor do individual. Por este motivo, escala máxima é assinalada como não aplicável.

<sup>4</sup> basic tem uma partição fixa. Neste camada, SUs adicionais são utilizados para a atribuição de réplicas mais para cargas de trabalho de consulta maior.

<sup>5</sup> S3 HD tem uma estrutura de alocação diferentes em termos de combinações permitidas. Para réplicas, pode ter um máximo de 12. Para a partições, o máximo é 3.




