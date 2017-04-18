<properties 
    pageTitle="Modelo de dados de informações de aplicação" 
    description="Descreve as propriedades exportados de exportação contínua em JSON e utilizados como filtros." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação de informações de aplicação

Esta tabela lista as propriedades de telemetria enviados a partir dos SDK [Informações de aplicação](app-insights-overview.md) para o portal. Irá ver estas propriedades no resultado dos dados a partir de [Exportar contínuo](app-insights-export-telemetry.md).
Também aparecer em filtros de propriedade no [Explorador de métrica](app-insights-metrics-explorer.md) e [Pesquisa de diagnóstico](app-insights-diagnostic-search.md).

Pontos a tenha em atenção:

* `[0]`Estas tabelas indica um ponto no caminho onde tem para inserir um índice remissivo; mas não é sempre 0.
* Durações de tempo são em décimas de microssegundos, por isso, 10000000 = = 1 segundo.
* Datas e horas estão UTC e são fornecidas no formato de ISO`yyyy-MM-DDThh:mm:ss.sssZ`

Existem várias [amostras](app-insights-export-telemetry.md#code-samples) que ilustram o modo como utilizá-las.



## <a name="example"></a>Exemplo

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Contexto

Todos os tipos de telemetria são acompanhar por uma secção contexto. Todos estes campos não são transmitidos com cada ponto de dados.



|Caminho|Tipo|Notas|
|---|---|---|
| Context.Custom.dimensions [0]  | [objeto]  | Pares de cadeia de valor de chave definido pelo parâmetro propriedades personalizadas. Chave do comprimento máximo 100, valores comprimento máximo 1024. Mais de 100 valores exclusivos, a propriedade pode ser procurada mas não pode ser utilizada para segmentação. Teclas de 200 máximo por ikey.  |
| Context.Custom.Metrics [0]  | [objeto]  | Pares de chave-valor definido pelo parâmetro medidas personalizado e por TrackMetrics. Comprimento máximo chave 100, valores podem ser numéricos. |
| context.data.eventTime | cadeia | UTC |
| context.data.isSynthetic | Booleano | Pedido de aparenta ser a partir de um teste bot ou na web. |
| context.data.samplingRate | número | Percentagem de telemetria gerada pelo SDK, que é enviado para o portal. Intervalo 0,0 100.0.|
| Context.Device | objecto | Dispositivo de cliente |
| Context.Device.browser | cadeia | IE, Chrome,... |
| context.device.browserVersion | cadeia | Chrome 48.0,... |
| context.device.deviceModel | cadeia | |
| context.device.deviceName | cadeia | |
| Context.Device.ID | cadeia | |
| Context.Device.locale | cadeia | en-GB, de,... |
| Context.Device.Network | cadeia | |
| context.device.oemName | cadeia | |
| context.device.osVersion | cadeia | Anfitrião SO |
| context.device.roleInstance | cadeia | ID do anfitrião do servidor |
| context.device.roleName | cadeia | |
| Context.Device.Type | cadeia | PC, Browser,... |
| Context.location | objecto | Derivado de clientip. |
| Context.location.City | cadeia | Derivado de clientip, se conhecido  |
| Context.location.ClientIP | cadeia | Último octógono é anónimo 0. |
| Context.location.continent | cadeia | |
| Context.location.Country | cadeia | |
| Context.location.Province | cadeia | Distrito |
| Context.Operation.ID | cadeia | Itens que têm o mesmo id de operação são apresentados como itens relacionados no portal. Normalmente, o id do pedido. |
| Context.Operation.Name | cadeia | URL ou pedido de nome |
| context.operation.parentId | cadeia | Permite aninhados itens relacionados. |
| Context.Session.ID | cadeia | ID de um grupo de operações a partir da mesma origem. Um período de 30 minutos sem uma operação sinais de fim de uma sessão. |
| context.session.isFirst | Booleano | |
| context.user.accountAcquisitionDate | cadeia | |
| context.user.anonAcquisitionDate | cadeia | |
| context.user.anonId | cadeia | |
| context.user.authAcquisitionDate | cadeia | [Utilizador autenticado](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | Booleano | |
| internal.data.documentVersion | cadeia | |
| INTERNAL.data.ID | cadeia | |



## <a name="events"></a>Eventos

Eventos personalizados gerados pelo [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Caminho|Tipo|Notas|
|---|---|---|
| Contagem de eventos [0] | número inteiro | 100 / (taxa;[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| nome do evento [0] | cadeia | Nome do evento.  Comprimento máximo 250. |
| url do evento [0] | cadeia | |
| urlData.base evento [0] | cadeia | |
| urlData.host evento [0] | cadeia | |

## <a name="exceptions"></a>Exceções

Relatórios [exceções](app-insights-asp-net-exceptions.md) no servidor e no browser. 


|Caminho|Tipo|Notas|
|---|---|---|
| assemblagem basicException [0] | cadeia | |
| Contagem de basicException [0] | número inteiro | 100 / (taxa;[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| exceptionGroup basicException [0] | cadeia | |
| exceptionType basicException [0] | cadeia | |cadeia | |
| failedUserCodeMethod basicException [0] | cadeia | |
| failedUserCodeAssembly basicException [0] | cadeia | |
| handledAt basicException [0] | cadeia | |
| hasFullStack basicException [0] | Booleano | |
| id de basicException [0] | cadeia | |
| método de basicException [0] | cadeia | |
| mensagem de basicException [0] | cadeia | Mensagem de exceção. Comprimento máximo 10k.|
| outerExceptionMessage basicException [0] | cadeia | |
| outerExceptionThrownAtAssembly basicException [0] | cadeia | |
| outerExceptionThrownAtMethod basicException [0] | cadeia | |
| outerExceptionType basicException [0] | cadeia | |
| outerId basicException [0] | cadeia | |
| assemblagem de parsedStack [0] basicException [0] | cadeia | |
| o nome de ficheiro do basicException [0] parsedStack [0] | cadeia | |
| nível de parsedStack [0] basicException [0] | número inteiro | |
| linha de parsedStack [0] basicException [0] | número inteiro | |
| método de parsedStack [0] basicException [0] | cadeia | |
| pilha de basicException [0] | cadeia | Comprimento máximo k de 10|
| NomeTipo basicException [0] | cadeia | |



## <a name="trace-messages"></a>Rastreio de mensagens

Enviado por [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)e pelo [registo placas](app-insights-asp-net-trace-logs.md).


|Caminho|Tipo|Notas|
|---|---|---|
| mensagem [0] NomeRegisto | cadeia ||
| parâmetros de mensagem [0] | cadeia ||
| mensagem [0] bruto | cadeia | A mensagem de registo, comprimento máximo de 10k. |
| mensagem [0] severityLevel | cadeia | |



## <a name="remote-dependency"></a>Dependência remota

Enviado ao TrackDependency. Utilizado para o desempenho do relatório e a utilização de [chamadas para dependências](app-insights-asp-net-dependencies.md) no servidor e AJAX chamadas no browser.

|Caminho|Tipo|Notas|
|---|---|---|
| remoteDependency [0] assíncrona | Booleano | |
| baseName remoteDependency [0] | cadeia |  |
| nomecomando remoteDependency [0] | cadeia | Por exemplo "base/index" |
| Contagem de remoteDependency [0] | número inteiro | 100 / (taxa;[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| dependencyTypeName remoteDependency [0] | cadeia | HTTP, SQL,... |
| durationMetric.value remoteDependency [0] | número | Hora da chamada para a conclusão da resposta pela dependência |
| id de remoteDependency [0] | cadeia | |
| nome remoteDependency [0] | cadeia | URL. Comprimento máximo 250.|
| resultCode remoteDependency [0] | cadeia | a partir de dependência HTTP |
| sucesso remoteDependency [0] | Booleano | |
| tipo de remoteDependency [0] | cadeia | Http, Sql,... |
| url remoteDependency [0] | cadeia |  Comprimento máximo 2000 |
| urlData.base remoteDependency [0] | cadeia | Comprimento máximo 2000 |
| urlData.hashTag remoteDependency [0] | cadeia | |
| urlData.host remoteDependency [0] | cadeia | Comprimento máximo 200 |


## <a name="requests"></a>Pedidos de

Enviado ao [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Os módulos padrão utilizam esta opção para tempo de resposta do servidor de relatórios, medido no servidor. 


|Caminho|Tipo|Notas|
|---|---|---|
| Contagem de pedido [0] | número inteiro | 100 / (taxa;[amostragem](app-insights-sampling.md) ). Por exemplo: 4 =&gt; 25%. |
| durationMetric.value pedido [0] | número | Tempo do pedido que chega à resposta ao pedido de. 1e7 = = 1s |
| id de pedido [0] | cadeia | Id de operação |
| nome do pedido [0] | cadeia | REGISTAR/obter + url base.  Comprimento máximo 250 |
| responseCode pedido [0] | número inteiro | Resposta de HTTP enviada para o cliente |
| pedir success [0] | Booleano | Predefinição = = (responseCode &lt; 400) |
| url do pedido [0] | cadeia | Não incluindo anfitrião |
| urlData.base pedido [0] | cadeia | |
| urlData.hashTag pedido [0] | cadeia |  |
| urlData.host pedido [0] | cadeia | |


## <a name="page-view-performance"></a>Desempenho da vista de página

Enviado pelo browser. Medidas o tempo para processar numa página, a partir do utilizador que inicia o pedido para apresentar concluída (excluindo assíncrona AJAX chamadas).

Mostram valores de contexto SO cliente e versão do browser. 


|Caminho|Tipo|Notas|
|---|---|---|
| clientProcess.value clientPerformance [0] | número inteiro | Hora de fim de receber o código HTML para apresentar a página. |
| nome clientPerformance [0] | cadeia | |
| networkConnection.value clientPerformance [0] | número inteiro | Tempo despendido para estabelecer uma ligação de rede. |
| receiveRequest.value clientPerformance [0] | número inteiro | Hora de fim de enviar o pedido para receber o HTML na resposta. |
| sendRequest.value clientPerformance [0] | número inteiro | Tempo de redirecionado para enviar o pedido de HTTP. |
| total.value clientPerformance [0] | número inteiro | Hora seja iniciado de forma enviar o pedido para apresentar a página. |
| url clientPerformance [0] | cadeia | URL deste pedido |
| urlData.base clientPerformance [0] | cadeia | |
| urlData.hashTag clientPerformance [0] | cadeia | |
| urlData.host clientPerformance [0] | cadeia | |
| urlData.protocol clientPerformance [0] | cadeia | |

## <a name="page-views"></a>Vistas de página

Enviado por trackPageView() ou [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Caminho|Tipo|Notas|
|---|---|---|
| Contagem de vista [0] | número inteiro | 100 / (taxa;[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| Vista [0] durationMetric.value | número inteiro | Valor definido opcionalmente no trackPageView() ou startTrackPage() - stopTrackPage(). Não é o mesmo como clientPerformance valores. |
| nome da vista [0] | cadeia | Título da página.  Comprimento máximo 250 |
| url de vista [0] | cadeia | |
| Vista [0] urlData.base | cadeia | |
| Vista [0] urlData.hashTag | cadeia | |
| Vista [0] urlData.host | cadeia | |



## <a name="availability"></a>Disponibilidade

[Testes de web disponibilidade](app-insights-monitor-web-app-availability.md)de relatórios.

|Caminho|Tipo|Notas|
|---|---|---|
| Disponibilidade [0] availabilityMetric.name | cadeia | disponibilidade |
| Disponibilidade [0] availabilityMetric.value | número |1.0 ou 0,0 |
| Contagem de disponibilidade [0] | número inteiro | 100 / (taxa;[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| Disponibilidade [0] dataSizeMetric.name | cadeia | |
| Disponibilidade [0] dataSizeMetric.value | número inteiro | |
| Disponibilidade [0] durationMetric.name | cadeia | |
| Disponibilidade [0] durationMetric.value | número | Duração de teste. 1e7 = = 1s |
| mensagem de disponibilidade [0] | cadeia | Falha de diagnóstico |
| resultado de disponibilidade [0] | cadeia | Pass/falhas |
| Disponibilidade [0] runLocation | cadeia | Origem de geo de http necessário |
| Disponibilidade [0] nomedoteste | cadeia | |
| Disponibilidade [0] testRunId | cadeia | |
| Disponibilidade [0] testTimestamp | cadeia | |




## <a name="metrics"></a>Métricas

Gerado pelo TrackMetric().

O valor de métrica encontra-se na context.custom.metrics[0]

Por exemplo:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Acerca dos valores métricas

Métricas valores, tanto em relatórios métricos e qualquer outro lugar, são reportados com uma estrutura de objeto padrão. Por exemplo:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Atualmente - apesar Isto pode mudar no futuro - em todos os valores denunciadas entre os módulos SDK padrão, `count==1` e apenas a `name` e `value` campos são úteis. As maiúsculas/minúsculas apenas onde deverá ser diferentes seria se escrever o seus próprio chamadas TrackMetric nas quais é definida os outros parâmetros. 

É o objetivo dos outros campos permitir que métricas é necessário agregar no SDK, para reduzir o tráfego para o portal. Por exemplo, poderia médios várias medições sucessivas antes de enviar cada relatório métrico. Em seguida, teria calcular o mínimo, máximo, o desvio-padrão e valor agregado (soma ou média) e definir contagem para o número de medições representada pelo relatório. 

Em tabelas, podemos ter sido omitidas a contagem de campos raramente utilizado, mínimo, máximo, stdDev e sampledValue.

Em vez de métricas previamente agrupamento, pode utilizar [amostragem](app-insights-sampling.md) se precisar de reduzir o volume de telemetria.


### <a name="durations"></a>Durações

Salvo indicação em contrário contrário, durações são representadas em décimas de um microssegundos, para que 10000000.0 significa 1 segundo.



## <a name="see-also"></a>Consulte também

* [Informações de aplicação](app-insights-overview.md) 
* [Exportar contínuo](app-insights-export-telemetry.md)
* [Exemplos de código](app-insights-export-telemetry.md#code-samples)


