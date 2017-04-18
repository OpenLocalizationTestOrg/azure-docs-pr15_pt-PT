<properties
    pageTitle="Como monitorizar a uma conta de armazenamento | Microsoft Azure"
    description="Saiba como monitorizar uma conta de armazenamento no Azure utilizando o Portal do Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorizar a uma conta de armazenamento no Portal do Azure

## <a name="overview"></a>Descrição geral

Pode monitorizar a sua conta de armazenamento a partir do [Azure Portal](https://portal.azure.com). Quando configurar a sua conta de armazenamento da monitorização através do portal do, o armazenamento do Windows Azure utiliza [A análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343270.aspx) para controlar métricas para a sua conta e registar dados de pedido.

> [AZURE.NOTE]Os custos adicionais estão associados a análise de dados de monitorização no [Portal do Azure](https://portal.azure.com). Para mais informações, consulte o artigo <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">faturação e de análise de armazenamento</a>. <br />

> Armazenamento de ficheiros Azure atualmente suporta métricas de armazenamento Analytics, mas ainda não suporta registo. Pode ativar a métricas de armazenamento de ficheiros do Azure através de do [Portal do Azure](https://portal.azure.com).

> Contas de armazenamento com um tipo de replicação de zona redundantes armazenamento (ZRS) não possui a métricas ou a funcionalidade de registo ativado neste momento. 

> Para um guia aprofundado sobre como utilizar e outras ferramentas de análise de armazenamento para identificar, diagnosticar e resolução de problemas relacionados com o armazenamento do Windows Azure, consulte o artigo [Monitor, diagnosticar e resolver problemas de armazenamento do Windows Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Como: configurar a monitorização de uma conta de armazenamento

1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e, em seguida, clique no nome da conta de armazenamento para abrir o dashboard.

2. Clique em **Configurar**e desloque para baixo para as definições de **monitorização** para o blob, tabela e serviços de fila.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. No **monitorização**, defina o nível de monitorização e a política de retenção de dados para cada serviço:

    -  Para definir o nível de monitorização, selecione uma das seguintes opções:

      **Mínimas** - recolhe métricas como percentagens penetração/saída, disponibilidade, latência e sucesso, que são agregadas de BLOBs, tabela e serviços de fila.

      **Verboso** - além de métricas mínimas, recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento do Azure. Métricas verbosas ativar análise mais perto dos problemas que ocorram durante operações de aplicações.

      **Desativar** - desativa monitorização. Controlo de dados existentes são mantido até ao final do período de retenção.

- Para definir a política de retenção de dados, **retenção (em dias)**, escreva o número de dias de dados para manter a partir de 1 a 365 dias. Se não pretende definir uma política de retenção, introduza zero. Se não existe nenhuma política de retenção, é até que para eliminar os dados de monitorização. Recomendamos que definir uma política de retenção com base em quanto tempo pretende manter os dados de análise de armazenamento da sua conta para que podem ser eliminados os dados de análise antigas e não utilizada pelo sistema sem custos.

4. Quando terminar a configuração de monitorização, clique em **Guardar**.

Deverá começar a monitorizar os dados no dashboard de e para a página de **Monitor** após cerca uma hora a ver.

Até configurar a monitorização de uma conta de armazenamento, sem dados monitorização são recolhidos e os gráficos de métricas do dashboard e página **Monitor** estão vazios.

Depois de definir os níveis de monitorização e políticas de retenção, pode escolher quais as métricas disponíveis para monitorizar no [Portal do Azure](https://portal.azure.com)e que métricas de desenhar nos gráficos de métricas. Um conjunto predefinido de métricas é apresentado em cada nível de monitorização. Pode utilizar **Métricas de adicionar** para adicionar ou remover métricas a partir da lista de métricas.

Métricas são armazenadas na conta de armazenamento no quatro tabelas com o nome $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Para mais informações, consulte o artigo [Sobre métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Como: personalizar o dashboard de monitorização

No dashboard de, pode escolher até seis métricas representar no gráfico de métricas de nove métricas disponíveis. Para cada serviço (blob, tabela e fila), estão disponíveis as métricas de disponibilidade, a percentagem de sucesso e Total de pedidos. Métricas disponíveis no dashboard de são os mesmos para mínimas ou verboso monitorização.

1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e, em seguida, clique no nome da conta de armazenamento para abrir o dashboard.

2. Para alterar as métricas que estão desenhadas no gráfico, execute uma das seguintes ações:

    - Para adicionar uma nova métrica ao gráfico, clique na caixa de verificação colorida junto ao cabeçalho métrica na tabela abaixo do gráfico.

    - Para ocultar uma métrica que está desenhada no gráfico, desmarque a caixa de verificação colorida junto ao cabeçalho métrica.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Por predefinição, o gráfico mostra as tendências, apresentar o valor atual de cada métrica (a opção **relativa** na parte superior do gráfico). Para apresentar um eixo do Y para que possa ver absoluto valores, selecione **absolutas**.

4. Para alterar o intervalo de tempo a gráfico apresenta métricas, selecione 6 horas, 24 horas ou dias 7 na parte superior do gráfico.


## <a name="how-to-customize-the-monitor-page"></a>Como: personalizar a página do Monitor

Na página **Monitor** , pode ver o conjunto completo de métricas para a sua conta de armazenamento.

- Se a sua conta de armazenamento tiver monitorização mínimas configurado, métricas como penetração/saída, disponibilidade, latência e percentagens de sucesso são agregadas a blob, tabela e serviços de fila.

- Se a sua conta de armazenamento tiver monitorização verboso configurado, as métricas estão disponíveis com uma resolução melhor individuais das operações de armazenamento para além dos agregados de nível de serviço.

Utilize os seguintes procedimentos para escolher quais métricas de armazenamento para ver os gráficos de métricas e a tabela que são apresentadas na página **Monitor** . Estas definições não afetam as coleções de sites, agregação e armazenamento dos dados na conta de armazenamento de monitorização.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Como: Adicionar métricas à tabela de métricas


1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e, em seguida, clique no nome da conta de armazenamento para abrir o dashboard.

2. Clique em **Monitor**.

    A página **Monitor** abre. Por predefinição, a tabela de métricas apresenta um subconjunto de métricas que estão disponíveis para a monitorização. A ilustração mostra a visualização de Monitor predefinida de uma conta de armazenamento com monitorização verboso configurado para todos os serviços de três. Utilize **Adicionar métricas** para selecionar as métricas que pretende para monitorizar a partir de todas as métricas disponíveis.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Considere custos ao selecionar as métricas. Existem da transação e os custos de saída associados atualizar apresenta monitorização. Para mais informações, consulte o artigo [faturação e de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Clique em **Adicionar métricas**.

    São as métricas de agregação que estão disponíveis no monitorização mínimas na parte superior da lista. Se a caixa de verificação estiver selecionada, a métrica é apresentada na lista de métricas.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Coloque o cursor ao longo do lado direito da caixa de diálogo para apresentar uma barra de deslocamento que pode arrastar para se deslocar métricas adicionais numa vista.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Clique na seta para baixo por uma métrica para expandir uma lista de operações que a métrica do está confinada a incluir. Selecione cada operação que pretende ver na tabela métricas no [Portal do Azure](https://portal.azure.com).

    A ilustração seguinte mostra a percentagem de erro de autorização métrica foi expandida.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Depois de selecionar métricas para todos os serviços, clique em OK (marca de verificação) para atualizar a configuração de monitorização. Métricas selecionadas são adicionadas à tabela de métricas.

7. Para eliminar uma métrica a partir da tabela, clique em métrica para selecioná-la e, em seguida, clique em **Eliminar métrica**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Como: personalizar o gráfico de métricas na página do Monitor

1. Na página de **Monitor** para a conta de armazenamento, na tabela métricas, selecione métricas até 6 representar no gráfico métricas. Para selecionar uma métrica, clique na caixa de verificação o lado esquerdo. Para remover uma métrica do gráfico, desmarque a caixa de verificação.

2. Para mudar o gráfico entre relativas valores (valor final apenas apresentado) e de absoluto (eixo do Y apresentado), selecione **relativas** ou **absoluto** na parte superior do gráfico.

3.  Para alterar o tempo de intervalo a métricas gráfico apresenta, selecione **6 horas**, **24 horas**ou **dias 7** na parte superior do gráfico.



## <a name="how-to-configure-logging"></a>Como: configurar o registo

Para cada um dos serviços de armazenamento disponíveis com a sua conta de armazenamento (blob, tabela e fila), pode guardar registos de diagnóstico para pedidos de leitura, pedidos de escrever e/ou pedidos de eliminar e, pode definir a política de retenção de dados para cada um dos serviços.

1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e, em seguida, clique no nome da conta de armazenamento para abrir o dashboard.

2. Clique em **Configurar**e utilize a seta para baixo no teclado para se deslocar para baixo para o **registo**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Para cada serviço (blob, tabela e fila), configure o seguinte:

    - Os tipos de pedido para iniciar sessão: pedidos de leitura, pedidos de escrever e eliminar pedidos.

    - O número de dias para guardar os dados com sessão iniciada. Introduza zero é se não pretende definir uma política de retenção. Se não definir uma política de retenção, é até que para eliminar os registos.

4. Clique em **Guardar**.

Os registos de diagnóstico são guardados num contentor de BLOBs denominada $logs na sua conta de armazenamento. Para obter informações sobre como aceder ao contentor $logs, consulte o artigo [Acerca do armazenamento a análise de registo](http://msdn.microsoft.com/library/azure/hh343262.aspx).
