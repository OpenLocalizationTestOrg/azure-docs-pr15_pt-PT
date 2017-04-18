<properties 
    pageTitle="Aplicação de aprendizagem máquina: serviço de detecção anomalia | Microsoft Azure" 
    description="API de deteção de anomalia é um exemplo criado com o Microsoft Azure máquina Learning que Deteta anomalias nos dados da série de tempo com valores numéricos que são uniformemente espaçados no tempo." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Serviço de detecção do máquina aprendizagem anomalia#

##<a name="overview"></a>Descrição geral

[API de deteção de anomalia](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) é um exemplo criado com formação de máquina de Azure Deteta anomalias nos dados da série de tempo com valores numéricos que são uniformemente espaçados no tempo. 

Esta API pode detetar os seguintes tipos de padrões discordantes nos dados da série de tempo:

* **Positivos e negativos tendências**: por exemplo, quando monitorizar a utilização de memória na informática uma tendência para cima poderão estar de interesse que pode ser indicativo de perda de memória,

* **Alterações no intervalo dinâmico de valores**: por exemplo, quando monitorizar as exceções iniciadas por um serviço na nuvem, quaisquer alterações no intervalo de valores dinâmico indiquem instabilidade no estado de funcionamento do serviço, e

* **Pontas e desce**: por exemplo, quando monitorizar o número de insucessos de início de sessão num serviço ou o número de saídas no site de comércio electrónico, desportiva ou diminuições podem indicar um comportamento anormal.

Estes detectores de aprendizagem máquina monitorizar essas alterações nos valores ao longo do tempo e o relatório de alterações em curso nos respetivos valores como as pontuações de anomalia. Não necessitem de sintonização do ad hoc limiar e os respetivos pontuações podem ser utilizadas para controlar a taxa positiva falsa. A deteção de anomalia API é útil em cenários várias como monitorização do serviço de ao controlar KPIs ao longo do tempo, a utilização de monitorização através de métricas de como o número de pesquisas, números de cliques, monitorização de desempenho através de contadores como memória, CPU, ficheiro lê, etc. ao longo do tempo.

A oferta de deteção de anomalia vem com ferramentas úteis para começar a utilizar. 

* A [aplicação web](http://anomalydetection-aml.azurewebsites.net/) ajuda-o a avaliar e visualizar os resultados de deteção de anomalia APIs dos seus dados. 

* O [código de exemplo](http://adresultparser.codeplex.com/) mostra como aceder a API e analisar os resultados no c# através de programação.

>[AZURE.NOTE]
>Experimente **a solução de TI anomalia informações** recorrendo a [Este API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>Para obter esta solução de ponto a ponto implementada à sua subscrição do Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Comece aqui >**</a>


##<a name="api-definition"></a>Definição de API

O serviço fornece uma API com base no resto por HTTPS que pode ser consumida formas diferentes, incluindo uma web ou aplicação móvel, R, Python, Excel, etc.  Enviar as séries de dados de tempo para este serviço através de uma chamada de REST API e que é executada uma combinação de três anomalia tipos acima descritos. O serviço é executado a plataforma de aprendizagem do Azure máquina, que escalas às suas necessidades de negócio de forma totalmente integrada e fornece SLA.

###<a name="headers"></a>Cabeçalhos
Certifique-se de que inclui os cabeçalhos corretos no seu pedido, o que deve ser da seguinte forma:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Pode encontrar a chave de conta da sua conta no [Azure Data Market](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>API de pontuação

API pontuação é utilizado para executar a deteção de anomalia num dados da série de tempo não sazonal. A API é executado um número de detectores anomalia nos dados e devolve os respetivos pontuações anomalia. A figura seguinte mostra um exemplo de anomalias API pontuação pode detetar. Esta série de tempo tem alterações ao nível do distintas 2 e 3 picos. Os pontos a vermelho mostram a hora em que a alteração ao nível é detectada, enquanto que os pontos pretos mostram os picos detectados.

![API de pontuação][1]
    
**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Corpo do pedido de exemplo**

No pedido de abaixo, iremos enviar uma série de tempo (mostrada truncada) com pontos de dados a partir de 1/3/2016 através de 10/3/2016 e parâmetros de detectores coletor à API para detetar se qualquer um destes pontos de dados forem discordantes. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

A resposta a esta será: 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

ScoreWithSeasonality API é utilizado para executar a deteção de anomalia num séries de tempo que tenham padrões sazonais. Esta API é útil para detetar desvios nas padrões sazonais.  

A figura seguinte mostra um exemplo de anomalias detetado de uma série de tempo sazonal. As séries de tempo tem um coletor (o ponto preto 1. º), dois diminuições (o 2º ponto preto e uma de cada final) e uma alteração ao nível (ponto vermelho). Note que dip no meio as séries de tempo e a alteração nível apenas são discernable depois de componentes de sazonais são removidos da série.

![Sazonalidade API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Corpo do pedido de exemplo**

No pedido de abaixo, iremos enviar uma série de tempo (mostrada truncada) com pontos de dados a partir de 1/3/2016 através de 10/3/2016 e parâmetros à API para detetar se qualquer um destes pontos de dados forem discordantes. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

A resposta a esta será: 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Detectores

A deteção de anomalia API suporta detectores numa 3 vastas categorias. Para obter detalhes sobre os parâmetros de entrada específicos e saídas para cada detector podem ser encontrados na tabela seguinte.

|Categoria de detector|Detector|Descrição|Parâmetros de entrada|Resultados de
|---|---|---|---|---|
|Detectores coletor|TSpike Detector|Detetar picos e diminuições com base na extremidade os valores são a partir do primeiros e terceiros quartis|*tspikedetector.sensitivity:* leva-o até valor de número inteiro no intervalo predefinido de 1-10: 3; Valores mais elevados vão chamar mais representarem valores extremos, pelo que se tornou menos sensíveis|TSpike: valores binários – 1 se é detetado um coletor/dip, "0" caso contrário|
||ZSpike Detector|Detetar picos e diminuições com base em são os pontos de dados como extremidade da média|*zspikedetector.sensitivity:* tirar o valor de número inteiro no intervalo predefinido de 1-10: 3; Valores mais elevados vão chamar mais representarem valores extremos tornando menos sensíveis|ZSpike: valores binários – 1 se é detetado um coletor/dip, "0" caso contrário|
|Detector tendência lenta|Detector tendência lenta|Detetar lenta tendência positiva de acordo com a sensibilidade conjunto|*trenddetector.sensitivity:* limiar de pontuação detector (predefinido: 3,25, 3,25 – 5 for um intervalo razoável para selecionar esta partir do; Quanto maior menos sensível)|TScore: flutuantes número que representa a pontuação anomalia no tendência|
|Alterar nível detectores|Alterar nível unidireccional Detector|Detetar nível para cima alterar de acordo com a sensibilidade conjunto|*upleveldetector.sensitivity:* limiar de pontuação detector (predefinido: 3,25, 3,25 – 5 for um intervalo razoável para selecionar esta partir do; Quanto maior menos sensível)|PScore: flutuantes número que representa a pontuação anomalia para cima alterar de nível|
||Nível de bidireccional alterar Detector|Detetar para cima e para baixo alterar nível de acordo com a sensibilidade conjunto|*bileveldetector.sensitivity:* limiar de pontuação detector (predefinido: 3,25, 3,25 – 5 for um intervalo razoável para selecionar esta partir do; Quanto maior menos sensível)|RPScore: flutuantes número anomalia longevidade pontuação para cima e para baixo nível alterar

###<a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre estes parâmetros de entrada são apresentadas na tabela abaixo:

|Parâmetros de entrada|Descrição|Predefinição|Tipo|Intervalo válido|Intervalo sugerido|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervalo de agregação em segundos para agregar entrada séries de tempo|0 (não é agregação)|número inteiro|0: Ignorar caso contrário, agregação, > 0|5 minutos para 1 dia, dependente de séries de tempo
|preprocess.aggregationFunc|Função utilizada para agregar dados para o AggregationInterval especificado|média|enumerado|soma, média, comprimento|N/D|
|preprocess.replaceMissing|Valores utilizados para imputar dados em falta|lkv (última conhecida valor)|enumerado|zero, lkv, média|N/D|
|detectors.historyWindow|Histórico (em # de pontos de dados) utilizado para anomalia cálculo de pontuação|500|número inteiro|10-2000|Séries de tempo dependentes|
|upleveldetector.Sensitivity|Sensibilidade para nível para cima alterar detector. |3,25|dupla|Nenhum|3,25 5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Sensibilidade para bidireccional alteração ao nível do detector. |3,25|dupla|Nenhum|3,25 5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Sensibilidade para detector tendência positiva. |3,25|dupla|Nenhum|3,25 5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Sensibilidade para TSpike Detector|3|número inteiro|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Sensibilidade para ZSpike Detector|3|número inteiro|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.Enable|Se a análise de sazonalidade está a ser executado|VERDADEIRO|Booleano|VERDADEIRO, FALSO|Séries de tempo dependentes|
|seasonality.numSeasonality |Número máximo de ciclos periódicos para ser detetados|1|número inteiro|1, 2|1-2|
|seasonality.Transform |Se sazonal (e) componentes de tendência devem ser removidos antes de ser aplicada a deteção de anomalia|deseason|enumerado|nenhuma, deseason, deseasontrend|N/D|
|postprocess.tailRows |Número os mais recentes de pontos de dados sejam guardadas nos resultados de saída|0|número inteiro|0 (manter todos os pontos de dados), ou especificar o número de pontos a ter em resultados|N/D|

###<a name="output"></a>Saída
API é executado detectores todos os seus dados de série de tempo e devolve pontuações anomalia e indicadores binário coletor para cada ponto de hora. A tabela abaixo apresenta resultados a partir da API. 

|Resultados de|Descrição|
|---|---|
|Tempo|Selos de tempo a partir de dados não processados ou dados agregados (e/ou) imputados se agregação (e/ou) em falta é aplicada imputação de dados|
|OriginalData|Valores a partir de dados não processados ou dados agregados (e/ou) imputados se agregação (e/ou) em falta é aplicada imputação de dados|
|ProcessedData|Um dos seguintes procedimentos: <ul><li>Sazonalidade séries de tempo se sazonalidade significativa foi detectado e deseason opção selecionada;</li><li>sazonalidade ajustado e detrended séries de tempo se foi detectada sazonalidade significativa e opção deseasontrend selecionada</li><li>caso contrário, este é o mesmo que OriginalData</li>|
|TSpike|Indicador binário para indicar se uma coletor é detetado pelo TSpike Detector|
|ZSpike|Indicador binário para indicar se uma coletor é detetado pelo ZSpike Detector|
|Pscore|Um número flutuante representando pontuação anomalia no nível para cima alterar|
|Palert|valor de 1/0 que indica existe um nível para cima alterar anomalia com base na entrada sensibilidade|
|RPScore|Uma anomalia longevidade número flutuante pontuação no alteração ao nível do bidireccional|
|RPAlert|valor de 1/0 que indica existe um nível de bidireccional alterar anomalia com base na entrada sensibilidade|
|TScore|Uma anomalia longevidade número flutuante pontuação no tendência positiva|
|TAlert|valor de 1/0 indicar que não existem é uma anomalia tendência positiva com base na entrada sensibilidade|


Este resultado pode ser analisado utilizando um [analisador simple](https://adresultparser.codeplex.com/) - que tem o código de exemplo que mostra como ligar à API e analisar a saída. Anomalias detetadas podem ser visualizadas num dashboard e/ou passadas para especialistas humanas para ações lentes ou integradas bilheteira sistemas.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 
