<properties
    pageTitle="Real-Time-estatística no Azure CDN | Microsoft Azure"
    description="Estatísticas em tempo real fornecem dados em tempo real sobre o desempenho do Azure CDN quando distribuir conteúdo aos seus clientes."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estatística em tempo real no Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

Este documento explica estatística em tempo real no Microsoft Azure CDN.  Esta funcionalidade fornece dados em tempo real, tais como largura de banda, os Estados de cache e ligações em simultâneo ao seu perfil CDN quando distribuir conteúdo aos seus clientes. Isto permite monitorização contínua do Estado de funcionamento do serviço em qualquer altura, incluindo eventos de ativação.

Os gráficos seguintes estão disponíveis:

* [Largura de banda](#bandwidth)
* [Códigos de estado](#status-codes)
* [Estados de cache](#cache-statuses)
* [Ligações](#connections)


## <a name="accessing-real-time-stats"></a>Aceder a estatística em tempo real

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu perfil CDN.

    ![Pá de perfil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)

2. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

3. Paire sobre o separador **Analytics** , em seguida, coloque o cursor sobre a lista de opções de **Estatística em tempo real** .  Clique no **objeto grande HTTP**.

    ![Portal de gestão de CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)

    São apresentados os gráficos de estatística em tempo real.
    
Cada um dos gráficos apresenta estatísticas em tempo real para o intervalo de tempo selecionado, começando quando a página for carregada.  Os gráficos atualizar automaticamente todas as alguns segundos.  Botão **Atualizar Graph** , se existir, irá desmarque graph, após o qual será só apresentar os dados selecionados.

## <a name="bandwidth"></a>Largura de banda

![Gráfico de largura de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

O gráfico de **largura de banda** apresenta a quantidade de largura de banda utilizada para a plataforma actual sobre o intervalo de tempo selecionado. A parte do gráfico sombreada indica a utilização de largura de banda. O valor exato da largura de banda atualmente a ser utilizado é apresentado diretamente abaixo do gráfico de linha.

## <a name="status-codes"></a>Códigos de estado

![Gráfico de código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

Gráfico de **Códigos de estado** indica com que frequência ocorrem determinados códigos de resposta HTTP sobre o intervalo de tempo selecionado.

> [AZURE.TIP]  Para obter uma descrição de cada opção de código de estado HTTP, consulte o artigo [Códigos de estado do Azure CDN HTTP](https://msdn.microsoft.com/library/mt759238.aspx).

É apresentada uma lista de códigos de estado HTTP diretamente por cima do gráfico. Esta lista indica cada código de estado que pode ser incluído num gráfico de linha e o número de ocorrências por segundo para esse código de estado atual. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar por só monitorizar os códigos de estado que têm um significado especial para a configuração de CDN. Para fazer isto, verifique os códigos de estado pretendido e desmarque todas as outras opções, em seguida, clique em **Atualizar Graph**. 

Pode ocultar temporariamente os dados registados para um código de estado específico.  A partir de legenda diretamente abaixo do gráfico, clique no código de estado que pretende ocultar. O código de estado estará oculta imediatamente a partir do gráfico. Clicar esse código de estado novamente causará essa opção ser apresentado novamente.

## <a name="cache-statuses"></a>Estados de cache

![Gráfico de Estados de cache](./media/cdn-real-time-stats/cdn-cache-status.png)

Gráfico de **Estados de Cache** indica com que frequência ocorrem determinados tipos de Estados de cache sobre o intervalo de tempo selecionado. 

> [AZURE.TIP]  Para obter uma descrição de cada opção de código de estado de cache, consulte o artigo [Códigos de estado do Azure CDN Cache](https://msdn.microsoft.com/library/mt759237.aspx).

É apresentada uma lista de códigos de estado de cache diretamente por cima do gráfico. Esta lista indica cada código de estado que pode ser incluído num gráfico de linha e o número de ocorrências por segundo para esse código de estado atual. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar por só monitorizar os códigos de estado que têm um significado especial para a configuração de CDN. Para fazer isto, verifique os códigos de estado pretendido e desmarque todas as outras opções, em seguida, clique em **Atualizar Graph**. 

Pode ocultar temporariamente os dados registados para um código de estado específico.  A partir de legenda diretamente abaixo do gráfico, clique no código de estado que pretende ocultar. O código de estado estará oculta imediatamente a partir do gráfico. Clicar esse código de estado novamente causará essa opção ser apresentado novamente.

## <a name="connections"></a>Ligações

![Gráfico de ligações](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica foram estabelecidas quantas ligações para os servidores edge. Cada pedido para atravessa resultados do nosso CDN numa ligação de um ativo.

## <a name="next-steps"></a>Próximos passos

- Receber uma notificação com [alertas em tempo real no Azure CDN](cdn-real-time-alerts.md)
- Procurar forma mais aprofundada com [relatórios HTTP avançados](cdn-advanced-http-reports.md)
- Analisar [os padrões de utilização](cdn-analyze-usage-patterns.md)

