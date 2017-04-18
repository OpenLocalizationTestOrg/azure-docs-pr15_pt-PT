Existem alguns limites para o número de métricas e eventos por aplicação (ou seja, por chave instrumentação). 

Limites dependem de [preços camada](https://azure.microsoft.com/pricing/details/application-insights/) que escolher.

**Recurso** | **Limite predefinido** | **Limite máximo**
-------- | ------------- | -------------
Pontos de dados da sessão<sup>1, 2</sup> por mês | ilimitado | 
Pontos de dados total por mês para pedido, eventos, dependência, rastreio, exceção e vista de página | 5 milhões | 50 milhões de<sup>3</sup>
[Rastreio e registo](../articles/application-insights/app-insights-search-diagnostic-logs.md) a taxa de dados | 200 dp/s | 500 dp/s
Taxa de [exceção](../articles/application-insights/app-insights-asp-net-exceptions.md) de dados | 50 dp/s | 50 dp/s
Taxa de total de dados para o pedido, eventos, dependência e telemetria de vista de página | 200 dp/s | 500 dp/s
Retenção de dados não processados para a [pesquisa](../articles/application-insights/app-insights-diagnostic-search.md) e [análise](../articles/application-insights/app-insights-analytics.md) | 7 dias
Retenção de dados agregados para [explorer métricas](../articles/application-insights/app-insights-metrics-explorer.md) | cerca de 90 dias
Contagem de nome da [propriedade](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Comprimento do nome de propriedade | 150 | 
Comprimento do valor de propriedade | 8192 | 
Rastreio e exceção comprimento da mensagem | 10000 |
[Métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) contagem de nome | 100 |
Comprimento do nome métrica |  150 | 
[Testes de disponibilidade](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> um ponto de dados é um valor métrico individual ou evento, com as propriedades anexadas e medidas.

<sup>2</sup> A sessão ponto de dados os registos de início ou fim de uma sessão e os registos de identidade do utilizador.

<sup>3</sup> pode comprar capacidade adicional para além das milhões de 50.
 
[Informações sobre preços e quotas de recursos na aplicação de informações](../articles/application-insights/app-insights-pricing.md)
