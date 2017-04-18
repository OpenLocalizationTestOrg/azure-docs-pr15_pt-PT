<properties
    pageTitle="Introdução ao Azure Monitor | Microsoft Azure"
    description="Começar a utilizar o Azure Monitor para ganhar visão a operação dos seus recursos e proceda de acordo com base fora de dados."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Introdução ao Azure Monitor

Azure Monitor é o serviço da plataforma que fornece uma única origem para monitorizar recursos Azure. Com o Azure Monitor, pode visualizar, consulta, encaminhar, arquivar e tomar medidas na métricas e registos provenientes de recursos no Azure. Pode trabalhar com estes dados utilizando o pá portal Monitor, [Os cmdlets do PowerShell do Monitor](./insights-powershell-samples.md), [Em diferentes plataformas clip](insights-cli-samples.md)ou [Azure Monitor REST APIs](https://msdn.microsoft.com/library/dn931943.aspx). Neste artigo, iremos guiá através de alguns dos componentes principais do Azure Monitor, através do portal para demonstração sobre.

1. No portal do, navegue até ao **mais serviços** e localize a opção de **Monitor** . Clique no ícone de estrela para adicionar esta opção à sua lista de Favoritos para que seja sempre facilmente acessível a partir da barra de navegação no lado esquerdo.

    ![Monitorizar na lista de serviços](./media/monitoring-get-started/monitor-more-services.png)

2. Clique na opção de **Monitor** de abrir o pá **Monitor** . Este pá reunindo a todas as monitorização definições e dados numa vista consolidada. Abre pela primeira vez para a secção de **registo de atividade** .

    ![Navegação de pá monitor](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] As opções de **notificações de serviço** e de **grupos de notificação** apresentadas acima só serão apresentado às pessoas que tiverem aderido a pré-visualização privada destas funcionalidades.

    Azure Monitor tem três categorias de base de dados de monitorização: O **registo de atividade**, **métricas**e **registos de diagnóstico**.

3. Clique em **Iniciar sessão atividade** para se certificar de que a secção de registo de atividade é apresentada.

    ![Pá de registo de atividade](./media/monitoring-get-started/monitor-act-log-blade.png)

    O [**registo de atividade**](./monitoring-overview-activity-logs.md) descreve todas as operações efetuadas no recursos na sua subscrição. Utilizar o registo de atividade, pode determinar o ' o quê, quem e quando ' para qualquer criar, atualizar ou eliminar operações em recursos na sua subscrição. Por exemplo, o registo de atividade mostra-lhe quando uma aplicação web foi parada e quem parado-lo. Eventos de registo de atividade estão armazenadas na plataforma e disponíveis para consultar cerca de 90 dias.
   
    Pode criar e guardar consultas para filtros comuns e depois afixar as consultas mais importantes a um dashboard portal para que sempre saiba se ocorreram eventos que correspondam aos seus critérios.

4. Filtrar a vista para um grupo de recursos específica sobre a semana passada, em seguida, clique no botão **Guardar** .

    ![Guardar a consulta de registo de atividade](./media/monitoring-get-started/monitor-act-log-save.png)

5. Agora, clique no botão **Afixar** .

    ![Clique em afixar para o registo de atividade](./media/monitoring-get-started/monitor-act-log-pin.png)

    A maior parte das vistas neste tutorial pode estar afixada a um dashboard. Isto ajuda a criar uma única origem de informações para dados operacionais no seus serviços. 

6. Regressar ao seu dashboard. Agora pode ver que a consulta (e o número de resultados) são apresentados no dashboard. Isto é útil se pretender ver rapidamente as ações de perfil de alta que tenham ocorrido recentemente na sua subscrição, por ex. tiver sido atribuída uma nova função, ou uma VM foi eliminada.

    ![Registo de atividade afixado ao dashboard](./media/monitoring-get-started/monitor-act-log-db.png)

7. Regresse no mosaico do **Monitor** e clique na secção de **métricas** . Primeiro tem de selecionar um recurso a filtragem e selecionando utilizando a lista pendente Opções no topo da pá.

    ![Filtrar recursos para métricas](./media/monitoring-get-started/monitor-met-filter.png)

    Todos os recursos Azure emitem [**métricas**](./monitoring-overview-metrics.md). Esta vista conjuga todas as métricas num único painel do Lupa para que possa compreender facilmente como os recursos estiver a executar.

8. Assim que tiver selecionado um recurso, todas as métricas disponíveis aparecem no lado esquerdo da pá. Pode gráfico várias métricas em simultâneo ao selecionar métricas e modificar o tipo e tempo de intervalo graph. Também pode ver todos os alertas métricos definir deste recurso.

    ![Métrica pá](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Algumas métricas só estão disponíveis ao ativar [Informações de aplicação](../application-insights/app-insights-overview.md) e/ou Windows ou Linux Azure diagnóstico no seu recurso.

9. Quando estiver satisfeito com o gráfico, pode utilizar o botão **Pin** para afixá-lo para o dashboard.

10. Voltar para o **Monitor** pá e clique em **registos de diagnóstico**.

    ![Pá registos de diagnóstico](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Registos de diagnóstico**](monitoring-overview-of-diagnostic-logs.md) são registos emitidos *por* um recurso que fornece dados acerca da operação desse recurso. Por exemplo, contadores de regra de grupo de segurança de rede e registos de fluxo de trabalho de aplicação de lógica são ambos os tipos de registos de diagnóstico. Estes registos podem ser armazenados numa conta de armazenamento, transmitido em sequência a um concentrador de evento, e/ou enviados para [A análise de registo](../log-analytics/log-analytics-overview.md). Análise de registo é produto de informações da empresa operacionais da Microsoft para procurar e alertar avançadas.
   
    No portal do pode visualizar e filtrar uma lista de todos os recursos na sua subscrição para identificar caso tenham registos de diagnóstico ativados.

11. Clique num recurso no pá a registos de diagnóstico. Se os registos de diagnóstico estão a ser armazenados numa conta de armazenamento, irá ver uma lista de registos de comunicação que pode transferir diretamente.

    ![Registos de diagnóstico para um recurso](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Também pode clicar em **Definições de diagnóstico**, que permite-lhe configurar ou alterar as suas definições de arquivo para uma conta de armazenamento, da transmissão concentradores evento ou enviar para uma área de trabalho de análise de registo.

    ![Ativar registos de diagnóstico](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Se configurou registos de diagnóstico para a análise de registo, em seguida, procure-los na secção **pesquisa de registo** do portal.

12. Navegue até à secção de **alertas** de pá o Monitor.

    ![Pá alertas para o público](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Aqui pode gerir todos os [**alertas**](./monitoring-overview-alerts.md) sobre os recursos do Azure. Isto inclui alertas na métricas, eventos de registo de atividade (na pré-visualização privado), testes de web informações de aplicação (localizações) e diagnósticos de pro-activos informações de aplicação. Alertas podem acionar mensagem HTTP para um URL de webhook ou mensagem de correio electrónico sejam enviadas.
   
13. Clique em **Adicionar alerta métrica** para criar um alerta.

    ![Adicionar alerta métrica](./media/monitoring-get-started/monitor-alerts-add.png)

    Em seguida, pode afixar um alerta para o dashboard para visualizar facilmente o respetivo estado em qualquer altura.

14. A secção Monitor também inclui ligações para [Informações de aplicação de](../application-insights/app-insights-overview.md) aplicações e soluções de gestão de [Análise de registo](../log-analytics/log-analytics-overview.md) . Estes outros produtos Microsoft tem abrangente integração com o Azure Monitor.

15. Se não estiver a utilizar as informações de aplicação ou a análise de registo, provavelmente que Azure Monitor tem uma parceria com o seu registo monitorização, atual e produtos de alerta. Consulte a nossa [página de parceiros](./monitoring-partners.md) para obter uma lista completa e instruções sobre como integrar o.

Ao seguir estes passos e afixação de todos os mosaicos relevantes para um dashboard, pode criar vistas abrangentes da sua aplicação e infraestrutura como este:

![Dashboard do Azure Monitor](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Próximos passos
- Ler um [Descrição geral do Azure Monitor](./monitoring-overview.md)
