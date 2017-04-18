<properties 
    pageTitle="Mapa de aplicação de na aplicação informações | Microsoft Azure" 
    description="Uma apresentação visual das dependências entre componentes de aplicação, identificados com alertas e KPIs." 
    services="application-insights" 
    documentationCenter=""
    authors="SoubhagyaDash" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/15/2016" 
    ms.author="awills"/>
 
# <a name="application-map-in-application-insights"></a>Mapa de aplicação de informações de aplicação

Em [Informações de aplicação do Visual Studio](app-insights-overview.md), mapa de aplicação é um esquema visual as relações de dependência de componentes de aplicação. Cada componente mostra KPIs como carregar, desempenho, falhas e alertas, para ajudar-o a descobrir qualquer componente a causar um problema de desempenho ou falha. Pode clicar a partir de qualquer componente para diagnósticos mais detalhados, a partir de informações da aplicação, e - se a sua aplicação utiliza serviços Azure - diagnóstico do Azure, tais como as recomendações de classificação de base de dados do SQL.

Como outros gráficos, pode afixar um mapa de aplicação para o dashboard Azure, onde está totalmente funcional. 

## <a name="open-the-application-map"></a>Abra o mapa de aplicação

Abra o mapa de pá a descrição geral para a sua aplicação:

![abrir o mapa de aplicação](./media/app-insights-app-map/01.png)

![mapa de aplicação](./media/app-insights-app-map/02.png)

Mostra o mapa:

* Testes de disponibilidade
* Componente de lado do cliente (monitorizada com o SDK JavaScript)
* Componente do lado do servidor
* Dependências de componentes de cliente e servidor

Pode expandir e fechar os grupos de ligação de dependência:

![Fechar](./media/app-insights-app-map/03.png)
 
Se tiver um grande número de dependências de um tipo (SQL, etc. HTTP), podem aparecer agrupados. 


![dependências agrupadas](./media/app-insights-app-map/03-2.png)
 
 
## <a name="spot-problems"></a>Problemas spot

Cada nó tem indicadores de desempenho relevantes, tais como as taxas de carga, desempenho e falha para esse componente. 

Ícones de aviso realçar possíveis problemas. Um aviso de cor de laranja significa que existem falhas nos pedidos, chamadas de dependência ou vistas de página. Vermelho significa que uma taxa de falha acima 5%.


![ícones de falha](./media/app-insights-app-map/04.png)

 
Ativa o alerta também mostrar o: 


![alertas ativas](./media/app-insights-app-map/05.png)
 
Se utiliza o SQL Azure, existe um ícone que mostra quando existem recomendações sobre como pode melhorar o desempenho. 


![Recomendação Azure](./media/app-insights-app-map/06.png)

Clique em qualquer ícone para obter mais detalhes:


![recomendação Azure](./media/app-insights-app-map/07.png)
 
 
## <a name="diagnostic-click-through"></a>Clique em diagnóstico através de

Cada um de nós no mapa oferece clique alvo através dos diagnósticos do. As opções variam consoante o tipo do nó.

![Opções do servidor](./media/app-insights-app-map/09.png)

 
Para os componentes alojadas no Azure, as opções incluem diretas ligações aos mesmos.


## <a name="filters-and-time-range"></a>Intervalo de tempo e filtros

Por predefinição, o mapa resume todos os dados disponíveis para o intervalo de tempo que selecionou. Mas pode filtrar-a para incluir apenas nomes de operação específica ou dependências.

* O nome da operação: Isto inclui vistas de página e tipos de pedido de lado do servidor. Com esta opção, o mapa mostra o KPI no nó do lado do cliente do servidor para as operações selecionadas apenas. Mostra as dependências denominadas no contexto dessas operações específicas.
* Nome da base de dependência: inclui as dependências do lado do browser de AJAX e dependências de lado do servidor. Se o relatório de telemetria de dependência personalizada com a API TrackDependency, estes também mostrará aqui. Pode selecionar as dependências de apresentar no mapa. Tenha em atenção que neste momento, este irá não filtrar os pedidos de lado do servidor ou as vistas de página de lado do cliente.


![Definir filtros](./media/app-insights-app-map/11.png)

 
 
## <a name="save-filters"></a>Guardar filtros

Para guardar os filtros que aplicou, Afixe a vista filtrada para um [dashboard](app-insights-dashboards.md).


![Afixar o dashboard](./media/app-insights-app-map/12.png)
 


## <a name="feedback"></a>Comentários

Utilize a [fornecer comentários através da opção comentários portal](app-insights-get-dev-support.md).


![Imagem de MapLink-1](./media/app-insights-app-map/13.png)


