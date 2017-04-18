<properties 
    pageTitle="Análise - a ferramenta de pesquisa avançada de informações da aplicação | Microsoft Azure" 
    description="Descrição geral das Analytics, a ferramenta de pesquisa de diagnóstico poderosas de informações da aplicação. " 
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
    ms.date="07/26/2016" 
    ms.author="awills"/>


# <a name="analytics-in-application-insights"></a>Análise na aplicação informações


[Análise](app-insights-analytics.md) é a funcionalidade de pesquisa avançada do [Informações de aplicação](app-insights-overview.md). Estas páginas descrevem lanquage de consulta a análise. 

* **[Veja o vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Unidade de teste Analytics nos nossos dados simulados](https://analytics.applicationinsights.io/demo)** se a sua aplicação não está a enviar dados para informações de aplicação ainda.

## <a name="queries-in-analytics"></a>Consultas no Analytics
 
Uma consulta normal é uma tabela de *origem* seguida de uma série de *operadores* separados por `|`. 

Por exemplo, vamos Descubra que horas do dia cidadãos da Hyderabad experimentar o nosso web app. E enquanto estamos lá, vamos ver quais os códigos de resultado são devolvidos para os seus pedidos HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
  	| where timestamp > ago(7d) and client_City == "Hyderabad"
  	| summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
  	| extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Vamos contar o número de endereços IP do cliente distintos, agrupamento-los por hora do dia ao longo dos últimos 7 dias. 

Vamos apresentar os resultados com a apresentação de gráfico de barras, se optar por empilhar os resultados dos códigos de resposta diferente:

![Selecione o gráfico de barras, x e y eixos, em seguida, segmentação](./media/app-insights-analytics/020.png)

Parece nossa aplicação está mais popular à hora de almoço e cama-hora no Hyderabad. (E podemos deverá investigar esses 500 códigos.)


Também existem operações de estatísticas eficientes:

![](./media/app-insights-analytics/025.png)


O idioma tem muitas funcionalidades apelativos:

* [Filtro](app-insights-analytics-reference.md#where-operator) de telemetria sua aplicação não processado por quaisquer campos, incluindo os seus propriedades personalizadas e métricas.
* [Participar em](app-insights-analytics-reference.md#join-operator) várias tabelas – pedidos correlate com vistas de página, chamadas de dependência, exceções e registo controla.
* Estatística poderosas [agregações](app-insights-analytics-reference.md#aggregations).
* Como poderosas como SQL, mas muito mais fácil para consultas complexas: em vez de declarações de aninhamento, encaminhar os dados a partir de uma operação de ensino básico para o próximo.
* Visualizações de imediatas e poderosas.







## <a name="connect-to-your-application-insights-data"></a>Ligar aos seus dados de informações de aplicação


Abra a análise da sua aplicação [pá de descrição geral](app-insights-dashboards.md) de informações da aplicação: 

![Abrir portal.azure.com, abra o seu recurso de informações da aplicação e clique em análise.](./media/app-insights-analytics/001.png)


## <a name="limits"></a>Limites

Em apresentar, os resultados da consulta são limitados ao apenas através de uma semana dos últimos dados.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Próximos passos


* Recomendamos que começam a [visita guiada do idioma](app-insights-analytics-tour.md).