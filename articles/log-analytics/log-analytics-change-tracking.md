<properties
    pageTitle="Solução no registo de análise de registo de alterações | Microsoft Azure"
    description="Pode utilizar a solução de registo de alterações de configuração no registo Analytics para ajudá-lo a identificar facilmente o software e alterações de serviços do Windows que ocorram no seu ambiente — identificar estas alterações de configuração pode ajudá-lo pinpoint operacionais problemas."
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="change-tracking-solution-in-log-analytics"></a>Alterar a solução de controlo no registo de análise


Pode utilizar a solução de registo de alterações de configuração no registo Analytics para ajudá-lo a identificar facilmente o software e serviços do Windows alterações e alterações de daemon Linux que ocorram no seu ambiente — identificar estas alterações de configuração pode ajudá-lo pinpoint operacionais problemas.

Instale a solução para atualizar o tipo de agente que tenha instalado. Alterações para instalado o software e serviços do Windows nos servidores monitorizados são lidos e, em seguida, os dados são enviados para o serviço de análise de registo na nuvem para processamento. Lógica é aplicada aos dados recebidos e o serviço de nuvem regista os dados. Quando são encontradas alterações, servidores com as alterações são apresentados no dashboard de registo de alterações. Ao utilizar as informações no dashboard de registo de alterações, pode facilmente ver as alterações efetuadas na sua infraestrutura de servidor.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

- Gestor de operações é necessário para a solução de registo de alterações.
- Tem de ter um agente do Windows ou Gestor de operações em cada computador onde pretende monitorizar as alterações.
- Adicione a solução alterações à área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.


## <a name="change-tracking-data-collection-details"></a>Alterar detalhes de recolha de dados de controlo

Registo de alterações recolhe inventário de software e os metadados do serviço Windows utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o registo de alterações.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png)|![N](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![N](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png)| hora a hora|

## <a name="use-change-tracking"></a>Utilizar o controlo de alteração

Após ter instalada a solução, pode ver o resumo das alterações para os servidores de monitorizada utilizando o mosaico de **Registo de alterações** na página **Descrição geral** na OMS.

![imagem de mosaico de registo de alterações](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

É possível visualizar as alterações às suas infraestrutura e, em seguida, detalhes pormenorizar para as seguintes categorias:

- Alterações feitas por tipo de configuração do software e serviços do Windows
- Alterações do software para aplicações e actualizações para servidores individuais
- Número total de alterações de software para cada aplicação
- Alterações do serviço Windows para servidores individuais

![imagem do dashboard de registo de alterações](./media/log-analytics-change-tracking/oms-changetracking01.png)

![imagem do dashboard de registo de alterações](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para ver alterações para qualquer alteram tipo

1. Na página **Descrição geral** , clique no mosaico de **Registo de alterações** .
2. No dashboard de **Alterar rastreio** , reveja as informações de resumo de uma das lâminas de tipo de alteração e, em seguida, clique em para ver informações detalhadas acerca do mesmo na página de **pesquisa de registo** .
3. Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por hora, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para limitar os resultados.

## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo de análise](log-analytics-log-searches.md) para ver dados detalhada de monitorização.
