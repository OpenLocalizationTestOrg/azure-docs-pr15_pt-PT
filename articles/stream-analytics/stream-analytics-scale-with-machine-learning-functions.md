<properties
    pageTitle="Dimensionar a tarefa de análise da cadeia com funções de aprendizagem do Azure máquina | Microsoft Azure"
    description="Saiba como correctamente Dimensionar trabalhos de análise da cadeia (partições, quantidade DOM e mais) ao utilizar as funções de aprendizagem do Azure máquina."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Dimensionar a tarefa de análise da cadeia com funções de formação de máquina do Azure

Muitas vezes é muito fácil configurar uma tarefa de análise da cadeia e executar alguns dados de exemplo através do mesmo. O que recomendamos fazer quando for necessária executar a tarefa mesmo com o volume mais alto de dados? Requer-nos compreender como configurar a tarefa de análise da cadeia para que irá dimensionar. Neste documento, iremos destacar os aspetos especiais do dimensionamento das tarefas de análise da cadeia com funções de aprendizagem automática. Para obter informações sobre como dimensionar trabalhos de análise da cadeia em geral consulte o artigo [tarefas de escala](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função de formação de máquina Azure em sequência Analytics?

Uma função de máquina formação em sequência Analytics pode ser utilizada como uma chamada de função normal o idioma de consulta de análise da cadeia. No entanto, atrás da cena, chamadas de função são realmente pedidos de serviço Web do Azure máquina aprendizagem. Serviços de web de aprendizagem máquina suportam "lotes" várias linhas, clique em Guardar lote, denominada na mesma web serviço API chamada, para melhorar a débito global. Consulte os artigos seguintes para obter mais detalhes; [Funções de formação de máquina Azure em sequência Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) e [Serviços Web do Azure máquina aprendizagem](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar uma tarefa de análise da cadeia com funções de aprendizagem automática

Quando configurar uma função de máquina de aprendizagem para tarefa de análise da cadeia, existem dois parâmetros a ter em conta, o tamanho do lote das chamadas de função de aprendizagem automática e as unidades transmissão (SUs) aprovisionada para a tarefa de análise da cadeia. Para determinar os valores adequados para estas, primeiro uma decisão deve ser efectuada entre latência e débito, ou seja, latência da tarefa de análise da cadeia e débito de cada DOM. SUs sempre podem ser adicionados a uma tarefa para aumentar o débito de uma consulta de análise da cadeia bem com a partições, embora SUs adicionais aumenta o custo de executar a tarefa.

Por conseguinte, é importante determinar a *tolerância* da latência em execução de uma tarefa de análise da cadeia. Latência adicional de executarem pedidos de serviço do Azure máquina aprendizagem naturalmente aumentará com o tamanho do lote, que irá compostos a latência da tarefa a análise da cadeia de. Por outro lado, aumentar o tamanho do lote permite à tarefa de análise da cadeia processar *mais eventos com o *mesmo número * de pedidos de serviço web de aprendizagem automática. Muitas vezes do aumento do latência de serviço web de aprendizagem máquina é sub linear ao aumento do tamanho do lote por isso, é importante ter em consideração o tamanho de lote mais custo eficiente para um serviço web de aprendizagem automática em qualquer situação determinado. O tamanho de lote predefinido para os pedidos de serviço web é 1000 e pode ser modificado utilizando a [Sequência de análise REST API](https://msdn.microsoft.com/library/mt653706.aspx "REST API análise da cadeia") ou o [cliente do PowerShell para análise de sequência de](stream-analytics-monitor-and-manage-jobs-use-powershell.md "cliente do PowerShell para análise da cadeia").

Assim que um tamanho de lote foi determinado, pode determinar a quantidade de unidades de transmissão (SUs), com base no número de eventos que precisa da função processar por segundo. Para obter mais informações sobre as unidades de transmissão Consulte o artigo [tarefas de escala de análise da cadeia](stream-analytics-scale-jobs.md#configuring-streaming-units).

Em geral, existem 20 ligações em simultâneo com o serviço web de aprendizagem máquina para cada 6 SUs, exceto os trabalhos de DOM 1 e 3 DOM tarefas de implementação receberão 20 ligações em simultâneo também.  Por exemplo, se a taxa de dados de entrada é 200.000 eventos por segundo e o tamanho do lote é da esquerda para a predefinição de 1000 a latência de serviço web resultante com lote mini de eventos de 1000 é 200ms. Isto significa que todas as ligações podem disponibilizar a 5 pedidos para o serviço web de aprendizagem automática um segundo. Com a 20 ligações, a tarefa de análise da cadeia pode processar 20.000 eventos no 200ms e, consequentemente, 100.000 eventos num segundo. Por isso, para processar 200.000 eventos por segundo, a tarefa de análise da cadeia necessita de 40 ligações em simultâneo, que é SUS 12. O diagrama abaixo ilustra os pedidos da tarefa de análise da cadeia para o ponto final de serviço do máquina aprendizagem web – em cada 6 SUs com 20 das ligações em simultâneo ao serviço web de aprendizagem automática no máximo.

![Análise de sequência de escala com exemplo de tarefa de 2 de funções de aprendizagem automática] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Análise de sequência de escala com exemplo de tarefa de 2 de funções de aprendizagem automática")

Em geral, ***B*** para o tamanho do lote, ***L*** para a latência de serviço web à dimensão do lote B em milissegundos, o débito de uma tarefa de análise da cadeia com ***N*** SUs é:

![Dimensionar a análise da cadeia com fórmula funções de aprendizagem automática] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Dimensionar a análise da cadeia com fórmula funções de aprendizagem automática")

Uma consideração adicional pode ser 'chamadas max em simultâneo' no lado máquina aprendizagem web serviço, é recomendado defina esta opção para o valor máximo (200 atualmente).

Para mais informações sobre este please definição reveja o [artigo de dimensionamento para serviços de Web de aprendizagem máquina](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – sentimento análise

O exemplo seguinte inclui uma tarefa de análise da cadeia com a análise de sentimento função formação de máquina, conforme descrito no [tutorial de integração de formação de máquina de análise da cadeia](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é uma consulta simples totalmente com a partições seguida a função **sentimento** , conforme apresentado abaixo:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Considere o seguinte cenário; com um débito de 10.000 tweets por segundo uma tarefa de análise da cadeia deve ser criada para efetuar uma análise sentimento dos tweets (eventos). Utilizar 1 DOM, poderia esta tarefa da cadeia Analytics conseguir processar o tráfego? Utilizar o tamanho do lote predefinido de 1000 a tarefa deverá poder acompanhar a entrada de dados. Ainda mais a função de aprendizagem máquina adicionada deve gerar não mais do que uma segunda de latência, que é o geral latência predefinido da análise sentimento serviço web de aprendizagem do computador (com um tamanho de lote predefinido de 1000). Latência **Geral** ou ponto a ponto da tarefa de análise da cadeia normalmente seria de aguardar alguns segundos. Veja mais detalhadas para esta tarefa de análise da cadeia, *especialmente* a chamadas de função de aprendizagem automática. Ter o tamanho do lote como 1000, um débito dos 10.000 eventos irá demorar cerca de 10 pedidos ao serviço web. Mesmo com 1 DOM, existem suficiente ligações em simultâneo para permitir o tráfego de entrada.

Mas o que acontece se a taxa de evento de entrada aumenta ao 100 x e agora a tarefa de análise da cadeia necessita processar 1.000.000 tweets por segundo? Existem duas opções:

1.  Aumentar o tamanho do lote, ou
2.  Criar a partições a sequência de entrada para processar eventos em paralelo

Com a primeira opção vai aumentar a tarefa **latência** .

Com a segunda opção, mais SUs seriam necessário ser aprovisionado e, consequentemente, gerar pedidos de serviço de web de aprendizagem máquina mais em simultâneo. Isto significa que aumenta o **custo** de tarefa.


Assuma que a latência da análise sentimento serviço web de aprendizagem automática é 200ms para secções de 1000 evento ou abaixo, 250ms para secções de 5.000 evento, 300ms para secções de evento 10.000 ou 500ms para secções de 25.000 evento.

1. Utilizar a primeira opção, (**não** aprovisionamento SUs mais), o tamanho do lote foi aumentado para **25.000**. Isto sucessivamente permitirá a tarefa processar 1.000.000 eventos com 20 das ligações em simultâneo com o serviço web de aprendizagem automática (com uma latência de 500ms por chamada). Por isso, a latência adicional da tarefa da cadeia Analytics devido as pedidos de função sentimento contra os pedidos de serviço web formação de máquina de seria ser aumentada de **200ms** para **500ms**. No entanto, tenha em atenção que batch tamanho **não pode** ser um aumento infinitamente como os serviços web de aprendizagem máquina requer o tamanho de carga útil de um pedido de 4 MB ou mais pequeno tempo limite de pedidos de serviço web após 100 segundos de operação.
2. Utilizar a segunda opção, o tamanho do lote é para a esquerda na 1000, com uma latência de serviço web de 200ms, todas as ligações em simultâneo 20 ao serviço web seria capazes de processar 1000 *20* 5 eventos = 100,000 por segundo. Por isso, para processar 1.000.000 eventos por segundo, a tarefa seria necessário 60 SUs. Em comparação com a primeira opção, a tarefa de análise da cadeia tornaria mais web lote pedidos de serviço, por sua vez gerar um aumento do custo.

Segue-se uma tabela para o débito da tarefa a análise da cadeia de para SUs diferentes e tamanhos do lote (em número de eventos por segundo).

| tamanho do lote (latência da ML)  | 500 (200ms) | 1.000 (200ms) | 5.000 (250ms) | 10.000 (300ms) | 25.000 (500ms) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 DOM** | 2500 | 5.000 | 20.000 | 30.000 | 50000 |
| **3 SUs** | 2500 | 5.000 | 20.000 | 30.000 | 50000 |
| **6 SUs** | 2500 | 5.000 | 20.000 | 30.000 | 50000 |
| **12 SUs** | 5.000 | 10.000 | 40.000 | 60.000 | 100,000 |
| **18 SUs** | 7.500 euros | 15.000 | 60.000 | 90,000 | 150.000 |
| **24 SUs** | 10.000 | 20.000 | 80,000 | 120.000 | 200.000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25.000 | 50000 | 200.000 | 300.000 | 500.000 |

Por agora, já deve ter uma boa compreensão dos como formação de máquina de funções em sequência Analytics funciona. É provável que também compreender que os dados de análise da cadeia tarefas "solicitação" a partir de origens de dados e cada "solicitação" devolve um lote de eventos para a tarefa de análise da cadeia processar. Como é que o impacto de modelo este solicitação a aprendizagem máquina web pedidos de serviço?

Normalmente, o tamanho do lote que podemos definidos para as funções de aprendizagem máquina exatamente não será divisível pelo número de eventos devolvido por cada tarefa de análise da cadeia "solicitação". Quando ocorre que o serviço web de aprendizagem máquina será chamado com secções "parciais". Isto é feito para não assumir gerais de latência tarefa adicionais em eventos Coalescer solicitação os solicitação.

## <a name="new-function-related-monitoring-metrics"></a>Nova relacionados com a função monitorização de métricas de

Na área de Monitor de uma tarefa de análise da cadeia, foram adicionadas três métricas adicionais relacionados com a função. São pedidos de função, função eventos e falhou pedidos de função, conforme mostrado no gráfico abaixo.

![Dimensionar a análise da cadeia com funções métricas de aprendizagem automática] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Dimensionar a análise da cadeia com funções métricas de aprendizagem automática")

São definida da seguinte forma:

**Função pedidos**: O número de pedidos de função.

**Função eventos**: os eventos números nos pedidos de função.

**Ocorreu uma falha com pedidos de função**: O número de pedidos de função falha.

## <a name="key-takeaways"></a>Chaves Takeaways  

Para resumir os pontos principais, para poder dimensionar uma tarefa de análise da cadeia com funções de formação de máquina, tem de ser considerados os seguintes itens:

1.  A taxa de evento de entrada
2.  A latência tolerada para a tarefa de análise da cadeia em execução (e, por conseguinte, o tamanho do lote de pedidos de serviço de web máquina formação)
3.  O SUs de análise da cadeia aprovisionada e o número de pedidos de serviço web de aprendizagem automática (os adicionais relacionados com a função custos)

Uma consulta de análise da cadeia totalmente com a partições foi utilizada como um exemplo. Se for necessária uma consulta mais complexa o [Fórum do Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) é um ótimo recurso para obter ajuda adicional da equipa do análise da cadeia.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a análise da cadeia, consulte o artigo:

- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
