<properties
    pageTitle="Monitorizar e gerir um conjunto de base de dados flexível com o portal do Azure | Microsoft Azure"
    description="Saiba como utilizar o portal do Azure e intelligence incorporada da base de dados de SQL para gerir, monitorizar e à direita de tamanho de um conjunto de base de dados flexível dimensionáveis para otimizar o desempenho da base de dados e gerir o custo."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Monitorizar e gerir um conjunto de base de dados flexível com o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Pode utilizar o portal do Azure para monitorizar e gerir um agrupamento de base de dados flexível e as bases de dados no conjunto. A partir do portal, pode monitorizar a utilização de um conjunto de dados flexível e as bases de dados dentro desse conjunto. Também pode efetuar um conjunto de alterações ao seu conjunto flexível e submeter todas as alterações ao mesmo tempo. Estas alterações incluem adicionar ou remover bases de dados, alterar as suas definições de agrupamento flexível ou alterar as suas definições de base de dados.

O gráfico abaixo mostra um agrupamento flexível de exemplo. A vista inclui:

*  Gráficos para monitorizar a utilização de recursos de conjunto de flexível e as bases de dados contidos no conjunto.
*  O botão de agrupamento de **Configurar** para efetuar alterações ao agrupamento de flexível.
*  O botão **Criar base de dados** que cria uma nova base de dados e adiciona-a para o conjunto de flexível atual.
*  Trabalhos flexível que ajudá-lo a gerir grandes quantidades de bases de dados ao executar scripts de Transact SQL contra todas as bases de dados numa lista.

![Vista de conjunto de dados][2]

Para trabalhar através dos passos neste artigo, terá de SQL server no Azure pelo menos uma base de dados e um conjunto de dados flexível. Se não tiver um agrupamento de flexível, consulte o artigo [criar um conjunto de dados](sql-database-elastic-pool-create-portal.md); Se não tiver uma base de dados, consulte o [tutorial de base de dados do SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Monitorização do agrupamento flexível

Pode aceder a um conjunto específico de para ver a sua utilização de recursos. Por predefinição, o agrupamento está configurado para mostrar a utilização de armazenamento e eDTU de última hora. O gráfico pode ser configurado para mostrar métricas diferentes ao longo do tempo vários o windows.

1. Selecione um conjunto para trabalhar com.
2. Em **Monitorização do agrupamento de flexível** é um gráfico com nome na **utilização de recursos**. Clique no gráfico.

    ![Monitorização do agrupamento flexível][3]

    Será aberto o pá **métrica** , que mostra uma vista detalhada das métricas especificadas sobre a janela de tempo especificado.   

    ![Métrica pá][9]

### <a name="to-customize-the-chart-display"></a>Para personalizar a apresentação de gráfico

Pode editar o gráfico e a pá para apresentar outras métricas como percentagem da CPU, percentagem de es de dados e percentagem de IO registo utilizada métrico.

2. Na pá métrica, clique em **Editar**.

    ![Clique em Editar][6]

- Na pá **Editar gráfico** , selecione um novo intervalo de tempo (passado hora, hoje, ou semana passada) ou clique em **personalizado** para selecionar um intervalo de datas nas últimas duas semanas. Selecione o tipo de gráfico (barra ou linha) e, em seguida, selecione os recursos para monitorizar a.

> Nota: Apenas métricas com a mesma unidade de medida podem ser apresentadas no gráfico ao mesmo tempo. Por exemplo, se selecionar "eDTU percentagem", em seguida, só será possível selecionar outras métricas com percentagem como a unidade de medida.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Em seguida, clique em **OK**.


## <a name="elastic-database-monitoring"></a>Monitorização de base de dados flexível

Também podem ser monitorizadas potenciais problemas em bases de dados individuais.

1. Em **Flexível monitorização de base de dados**, existe um gráfico que apresenta métricas para cinco bases de dados. Por predefinição, o gráfico apresenta as principais bases de 5 dados no conjunto de por utilização de média eDTU na última hora. Clique no gráfico.

    ![Monitorização do agrupamento flexível][4]

2. É apresentada a pá de **Utilização de recursos base de dados** . Este procedimento fornece uma vista detalhada de utilização de base de dados no conjunto. Utilizar a grelha na parte inferior da pá, pode selecionar qualquer bases de dados no conjunto de para apresentar a sua utilização no gráfico (até 5 bases de dados). Também pode personalizar a janela de métricas e a hora apresentada no gráfico ao clicar em **Editar gráfico**.

    ![Pá de utilização de recursos base de dados][8]

### <a name="to-customize-the-view"></a>Para personalizar a vista

1. Na pá **utilização de recursos base de dados** , clique em **Editar gráfico**.

    ![Clique em Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Na pá para **Editar** gráfico, selecione um novo intervalo de tempo (passado hora ou últimas 24 horas) ou clique em **personalizado** para selecionar um dia diferente das últimas 2 semanas para apresentar.

    ![Clique em personalizado](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Clique no menu pendente de **bases de dados através de comparar** para selecionar uma métrica diferente para utilizar ao comparar bases de dados.

    ![Editar o gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Para selecionar para monitorizar bases de dados

Na lista de base de dados na pá a **Utilização de recursos base de dados** , pode encontrar as bases de dados específicos ao consultar as páginas na lista ou ao escrever na nome de uma base de dados. Utilize a caixa de verificação para selecionar a base de dados.

![Procurar bases de dados monitorizar a][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Adicionar um alerta para um recurso de conjunto de dados

Pode adicionar regras para recursos que enviam e-mail para outras pessoas ou cadeias de alertas para os pontos finais de URL quando o recurso de acertos um limite de utilização que configurou.

> [AZURE.IMPORTANT]Utilização de recursos de monitorização para agrupamentos flexível tem um atraso de pelo menos de 20 minutos. Definir alertas de menos de 30 minutos para flexível conjuntos de dados não é suportada. Todos os alertas definido para agrupamentos flexível com um ponto final (parâmetro chamado "-Tamanhodajanela" na API do PowerShell) de 30 minutos a menor que não podem ser acionou. Certifique-se de que todos os alertas que definir para agrupamentos flexível para utilizam um período (tamanhojanela) de 30 minutos ou mais.

**Para adicionar um alerta a qualquer outro recurso:**

1. Clique no gráfico de **utilização de recursos** para abrir o pá **métrica** , clique no **alerta de adicionar**e, em seguida, preencha as informações na pá **Adicionar uma regra de alerta** (**recurso** é automaticamente configurado para ser o agrupamento de que está a trabalhar com).
2. Escreva um **nome** e uma **Descrição** que identifica o alerta para si e para os destinatários.
3. Selecione uma **métrica** que pretende para alertar o partir da lista.

    O gráfico mostra dinamicamente utilização de recursos para esse métrica para ajudar a escolher um determinado limiar.

4. Selecione uma **condição** (maior que, menor que, etc.) e um **limite**.
5. Clique em **OK**.



## <a name="move-a-database-into-an-elastic-pool"></a>Mover uma base de dados para um conjunto de dados flexível

Pode adicionar ou remover bases de dados de um conjunto existente. As bases de dados podem estar em outros conjuntos de dados. No entanto, só pode adicionar bases de dados que estão no mesmo servidor lógico.

1. No pá para o conjunto de, em **flexível bases de dados** , clique em **Configurar agrupamento**.

    ![Clique em Configurar conjunto][1]

2. Na pá **Configurar agrupamento** , clique em **Adicionar ao agrupamento**.

    ![Clique em Adicionar ao agrupamento](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Na pá **Adicionar bases de dados** , selecione a base de dados ou a bases de dados para adicionar ao conjunto de. Em seguida, clique em **Selecionar**.

    ![Selecione as bases de dados para adicionar](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    O **conjunto de configurar** pá lista agora a base de dados que selecionou para ser adicionada, com o seu estado definido como **pendente**.

    ![Adições agrupamento pendentes](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Em **Configurar pá de agrupamento**, clique em **Guardar**.

    ![Clique em Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Mover uma base de dados fora de um conjunto de dados flexível

1. Na pá **agrupamento de configurar** , selecione a base de dados ou a bases de dados para remover.

    ![bases de dados de lista](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Clique em **Remover de agrupamento**.

    ![bases de dados de lista](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    O **conjunto de configurar** pá lista agora a base de dados que selecionou para ser removido, com o seu estado definido como **pendente**.

    ![pré-visualização da base de dados adição e remoção](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Em **Configurar pá de agrupamento**, clique em **Guardar**.

    ![Clique em Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Alterar definições de desempenho de um conjunto de dados

Como monitorizar a utilização de recursos de um conjunto de dados, poderá descobrir que alguns ajustes são necessárias. Talvez o agrupamento de necessita de uma alteração no desempenho ou armazenamento dos limites. Possivelmente que pretende alterar as definições de base de dados no conjunto. Pode alterar a configuração do conjunto de qualquer momento para obter o melhor equilíbrio de desempenho e custo. Consulte o artigo [quando a um agrupamento de base de dados flexível deve ser utilizado?](sql-database-elastic-pool-guidance.md) para obter mais informações.

**Para alterar os limites eDTUs ou armazenamento por agrupamento e eDTUs por bases de dados:**

1. Abra o pá de **agrupamento de configurar** .

    Em **definições do agrupamento de base de dados flexível**, utilize o controlo de deslize para alterar as definições do agrupamento.

    ![Utilização de recursos do conjunto de dados flexível](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Quando a definição é alterada, a visualização mostra o custo mensal estimado da alteração.

    ![Atualizar um agrupamento e novo custo mensal](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Criar e gerir tarefas flexível

Tarefas de flexível permitem-lhe executar scripts de Transact-SQL contra qualquer número de bases de dados no conjunto. Pode criar novas tarefas ou gerir tarefas existentes utilizando o portal.

![Criar e gerir tarefas flexível][5]


Antes de utilizar tarefas, instalar componentes de tarefas flexível e forneça as suas credenciais. Para mais informações, consulte o artigo [Descrição geral de tarefas da base de dados flexível](sql-database-elastic-jobs-overview.md).

Consulte o artigo [dimensionamento saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md): utilizar ferramentas de base de dados flexível para fora de escala, mover os dados, de consulta ou crie transações.



## <a name="additional-resources"></a>Recursos adicionais

- [Agrupamento de flexível de base de dados SQL](sql-database-elastic-pool.md)
- [Criar um agrupamento de base de dados flexível com o portal](sql-database-elastic-pool-create-csharp.md)
- [Criar um agrupamento de base de dados flexível com PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Criar um agrupamento de base de dados flexível com c#](sql-database-elastic-pool-create-csharp.md)
- [Considerações sobre preço e desempenho para agrupamentos de base de dados flexível](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
