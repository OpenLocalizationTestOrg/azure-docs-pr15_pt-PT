<properties
    pageTitle="Criar um dashboard personalizado no registo de análise | Microsoft Azure"
    description="Este guia ajuda-lo a compreender como registo Analytics Dashboards pode visualizar todas as suas pesquisas de registo guardado, que lhe dá uma única lente para ver o seu ambiente."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="create-a-custom-dashboard-in-log-analytics"></a>Criar um dashboard personalizado no registo de análise

Este guia ajuda-lo a compreender como dashboards de análise de registo podem visualizar a todas as suas pesquisas de registo guardado, que lhe dá uma única lente para ver o seu ambiente.

![Dashboard de exemplo](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Todos os dashboards personalizados que criar no portal do OMS também estão disponíveis na aplicação móvel OMS. Consulte as páginas seguintes para mais informações sobre as aplicações.

- [Aplicação móvel do OMS a partir da Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
- [Aplicação móvel do OMS a partir do iTunes da Apple](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![dashboard móvel](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Como posso fazer para criar o meu dashboard?

Para começar, vá para a página de descrição geral de OMS. Verá o mosaico do **Meu Dashboard** à esquerda. Clique na mesma para desagregar o dashboard.

![Descrição geral](./media/log-analytics-dashboards/oms-dashboards-overview.png)


## <a name="adding-a-tile"></a>Adicionar um mosaico

Nos dashboards, mosaicos são recorrendo a suas pesquisas de registo guardado. OMS vem com muitas previamente efetuadas pesquisas registo guardado, para que possa começar de imediato. Utilize os passos seguintes que descrevem como começar.

No meu Dashboard vista, clique simplesmente em **Personalizar** para introduzir personalizar modo.

![Ilustrações](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 O painel que é aberto no lado direito da página mostra todas as pesquisas de registo guardado a área de trabalho. Para visualizar uma pesquisa de registo guardado como um mosaico, paire sobre uma procura guardada e, em seguida, clique no símbolo **plus** .

![Adicionar mosaicos 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Quando clica no símbolo **plus** , um novo mosaico aparece na vista do meu Dashboard.

![Adicionar mosaicos 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)


## <a name="edit-a-tile"></a>Editar um mosaico

No meu Dashboard vista, clique simplesmente em **Personalizar** para introduzir personalizar modo. Clique no mosaico que pretende editar. As alterações do painel da direita para editar e dá uma seleção das opções:

![Editar o mosaico](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Editar o mosaico](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Visualizações em mosaico#
Existem três tipos de visualizações em mosaico à escolha:

|tipo de gráfico|o que faz|
|---|---|
|![Gráfico de barras](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|Apresenta uma linha cronológica dos resultados da sua pesquisa de registo guardado como um gráfico de barras, ou uma lista de resultados por um campo dependendo se a sua pesquisa de registo agrega resultados por um campo ou não.
|![métrica](./media/log-analytics-dashboards/oms-dashboards-metric.png)|Apresenta os acertos de resultado de pesquisa do registo total como um número num mosaico. Mosaicos métricos permitem-lhe definir um limite que irá realçar o mosaico quando o limiar for atingido.|
|![linha](./media/log-analytics-dashboards/oms-dashboards-line.png)|Apresenta uma linha cronológica do seu acertos de resultado de pesquisa do registo guardado com valores como um gráfico de linhas.|

### <a name="threshold"></a>Limiar
Pode criar um determinado limiar num mosaico utilizando a visualização métrica. Selecione para criar um valor limite no mosaico. Escolha se pretende realçar o mosaico quando o valor é acima ou abaixo do limiar de que selecionou, em seguida, defina o valor de limiar abaixo.

## <a name="organizing-the-dashboard"></a>Organizar o dashboard
Para organizar o seu dashboard, navegue para a vista do meu Dashboard e clique em **Personalizar** para introduzir personalizar modo. Clique e arraste o mosaico que pretende mover e mova-o para onde pretende que o seu mosaico para ser.

![Organizar o seu Dashboard](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Remover um mosaico
Para remover um mosaico, navegue para a vista do meu Dashboard e clique em **Personalizar** para introduzir personalizar modo. Selecione o mosaico que pretende remover e, em seguida, no painel direito, selecione **Remover dispor em mosaico**.

![Remover um mosaico](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Próximos passos

- Crie [alertas](log-analytics-alerts.md) no registo Analytics para gerar notificações e para solucionar uma problemas.
