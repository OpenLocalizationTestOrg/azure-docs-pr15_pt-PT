<properties
    pageTitle="Analisar a utilização de dados no registo de análise | Microsoft Azure"
    description="Pode utilizar a página de utilização na análise de registo para ver a quantidade de dados está a ser enviada para o serviço OMS."
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
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="analyze-data-usage-in-log-analytics"></a>Analisar a utilização de dados no registo de análise

Análise de registo no conjunto de aplicações de gestão de operações (OMS) recolhe dados e envia-o para o serviço OMS periodicamente.  Pode utilizar a página de **utilização** para ver a quantidade de dados está a ser enviada para o serviço OMS. Página de **utilização** de também mostra-lhe quantidade de dados está a ser enviada diariamente por soluções e com que frequência os seus servidores de vai enviar dados.

>[AZURE.NOTE] Se tiver uma conta gratuita criada utilizando o [Web site OMS](http://www.microsoft.com/oms), está limitado a enviar 500 MB de dados para o serviço OMS diariamente. Se a chegar ao limite diário, análise de dados serão parar e retomar no início do dia seguinte. Também terá de reenviar quaisquer dados que não foi aceitaram ou processados pelos OMS.

Pode ver a sua utilização utilizando o mosaico de **utilização** no dashboard de **Descrição geral** no OMS.

![mosaico de utilização](./media/log-analytics-usage/usage-tile.png)

Se tiver excedido o limite da sua utilização diária ou se estiver perto seu limite, opcionalmente, pode remover uma solução para reduzir a quantidade de dados que enviar para o serviço OMS. Para mais informações sobre como remover soluções, consulte o artigo [Adicionar o registo de análise soluções a partir da Galeria de soluções](log-analytics-add-solutions.md).

![dashboard de utilização](./media/log-analytics-usage/usage-dashboard.png)

Página de **utilização** de apresenta as seguintes informações:

- Utilização de média por dia
- Utilização de dados para cada solução dos últimos 30 dias
- Quanto dados os servidores no seu ambiente estão a enviar para o serviço OMS dos últimos 30 dias
- O plano de dados preços camada e custo estimado
- Obter informações sobre o contrato de nível de serviço (SLA), incluindo quanto tempo demora OMS para processar os seus dados

## <a name="to-work-with-usage-data"></a>Para trabalhar com dados de utilização

1. Na página **Descrição geral** , clique no mosaico de **utilização** .
2. Na página **utilização** , ver as categorias de utilização que mostram áreas que estiver preocupado.
3. Se tiver uma solução que está a consumir demasiado diária carregamento da quota da sua, recomendamos remover essa solução.

## <a name="to-view-your-estimated-cost-and-billing-information"></a>Para ver as informações de faturação e o custo estimado
1. Na página **Descrição geral** , clique no mosaico de **utilização** .
2. Na página **utilização** em **utilização**, clique na divisa (**>**) junto a **estimativa de custo**.
3. No expandido detalhes do **seu plano de dados** , pode ver o estimado custo mensal.  
    ![Plano de dados](./media/log-analytics-usage/usage-data-plan.png)
4. Se pretender ver a informação de faturação, clique em **Ver a minha conta** para ver as informações de subscrição.
    - Na página subscrições, clique em ver detalhes e uma lista de item de linha da utilização da sua subscrição.  
        ![subscrição](./media/log-analytics-usage/usage-sub01.png)
    - Na página de resumo para a sua subscrição, pode executar uma variedade de tarefas para gerir e ver mais detalhes acerca da sua subscrição.  
        ![Detalhes da subscrição](./media/log-analytics-usage/usage-sub02.png)

## <a name="to-view-data-batches-for-your-sla"></a>Para ver as secções de dados para o seu SLA
1. Na página **Descrição geral** , clique no mosaico de **utilização** .
2. Em **Contrato de nível de serviço**, clique em **Transferir SLA detalhes**.
3. Um ficheiro do Excel XLSX é transferido para deseja rever.  
    ![Detalhes de SLA](./media/log-analytics-usage/usage-sla-details.png)

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [pesquisas de registo no registo Analytics](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pela soluções.
