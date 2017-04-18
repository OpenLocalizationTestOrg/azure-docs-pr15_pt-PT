<properties 
    pageTitle="Monitorizar e gerir a fábrica de dados do Azure tubagens" 
    description="Saiba como utilizar a monitorização e gestão de aplicação para monitorizar e gerir fábricas dados Azure e tubagens." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="spelluru"/>

# <a name="monitor-and-manage-azure-data-factory-pipelines-using-new-monitoring-and-management-app"></a>Monitorizar e gerir a fábrica de dados do Azure tubagens utilizando novas monitorização e gestão de aplicação
> [AZURE.SELECTOR]
- [Utilizar o PowerShell do Azure Portal/Azure](data-factory-monitor-manage-pipelines.md)
- [Utilizar a monitorização e gestão de aplicação](data-factory-monitor-manage-app.md)

Este artigo descreve como monitorizar, gerir e depurar seu tubagens e criar alertas para receber uma notificação no falhas utilizando a **monitorização e gestão de aplicação**. Também pode ver o vídeo seguinte para saber como utilizar a monitorização e gestão de aplicação.
   

> [AZURE.VIDEO azure-data-factory-monitoring-and-managing-big-data-piplines]
      
## <a name="launching-the-monitoring-and-management-app-a"></a>Iniciar a monitorização e gestão App um
Para iniciar o Monitor e gestão de aplicação, clique em mosaico de **monitorização e gerir** na pá a **Fábrica de dados** para a fábrica de dados.

![Mosaico na página de base de dados fábrica de monitorização](./media/data-factory-monitor-manage-app/MonitoringAppTile.png) 

Deverá visualizar que a monitorização e gestão de aplicação iniciada no separador/janela em separado.  

![Monitorização e gestão de aplicação](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [AZURE.NOTE] Se vir que o browser está retido na "Autorizar...", desativar/desmarque definição **bloquear cookies de terceiros e dados do site** (ou) mantê-lo ativado e criar uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar a aplicação novamente.


Se não vir windows atividade na lista na parte inferior, clique no botão **Atualizar** na barra de ferramentas para atualizar a lista. Além disso, defina os valores certos para os filtros de **hora de início** e **hora de fim** .  


## <a name="understanding-the-monitoring-and-management-app"></a>Noções sobre a monitorização e gestão de aplicação
Existem três separadores (**Explorer de recursos**, **Monitorização de vistas**e **alertas**) à esquerda e no primeiro separador (Explorer de recurso) está seleccionado por predefinição. 

### <a name="resource-explorer"></a>Explorador de recursos
Verá o seguinte procedimento: 

- Recursos do Explorador **vista em árvore** no painel esquerdo.
- **Vista de diagrama** na parte superior.
- Lista de **Atividade Windows** na parte inferior no painel do meio.
- **Propriedades**/separadores de**Atividade janela Explorador** no painel direito. 

No Explorador do recurso, consulte todos os recursos (tubagens, conjuntos de dados, serviços ligados) a fábrica de dados numa vista de árvore. Quando selecionar um objeto no Explorador do recurso, repare o seguinte procedimento: 

- entidade fábrica de dados associada é realçada na vista de diagrama.
- associadas windows atividade (clique [aqui](data-factory-scheduling-and-execution.md) para saber mais sobre windows atividade) estão realçadas na lista de atividade Windows na parte inferior.  
- Propriedades do objeto selecionado na janela de propriedades no painel direito. 
- Definição de JSON do objeto selecionado se aplicável. Por exemplo: um serviço ligado ou um conjunto de dados ou uma tubagem. 

![Explorador de recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o artigo [agendar e execução](data-factory-scheduling-and-execution.md) artigo para obter informações detalhadas conceptuais sobre a janela de atividade. 

### <a name="diagram-view"></a>Vista de diagrama
A vista de diagrama de uma fábrica dados fornece uma única painel da Lupa para monitorizar e gerir a fábrica de dados e os seus activos. Quando seleciona uma entidade fábrica de dados (conjunto de dados/pipeline) na vista de diagrama, repare o seguinte procedimento:
 
- a entidade de fábrica do mesmo de dados está selecionada na vista de árvore
- actividade associada windows estão realçadas na lista de atividade Windows.
- Propriedades do objeto selecionado na janela de propriedades

Quando a tubagem está ativada (não no Estado em pausa), é apresentada com uma linha verde. 

![Em curso em execução](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Repare a que não existem três botões de comando para a tubagem na vista de diagrama. Pode utilizar o segundo botão para colocar em pausa as em curso. Interromper não terminar as atividades atualmente em execução e informá-los, avance para conclusão. Terceiro botão interrompe a tubagem e termina a sua executar atividades existentes. Primeiro botão é retomada as em curso. Quando o seu pipeline estiver em pausa, repare que a alteração de cor para pipeline de mosaico da seguinte forma.

![Colocar em pausa/currículo no mosaico](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Pode seleções múltiplas duas ou mais tubagens (utilizando CTRL) e utilize os botões da barra de comando para colocar em pausa/currículo tubagens vários cada vez.

![Colocar em pausa/currículo na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

Pode ver todas as atividades no pipeline de ao clicar no mosaico de tubagem e, clicar em **Abrir em curso**.

![Abrir menu em curso](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na vista de tubagem aberta, pode ver todas as atividades no pipeline de. Neste exemplo, existe apenas uma atividade: copiar atividade. Para voltar para a vista anterior, clique em nome de fábrica de dados no menu de trilho na parte superior.

![Em curso aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Na vista de tubagem, quando clica num conjunto de dados de saída ou quando coloca o rato sobre o conjunto de dados de saída, verá o Windows de atividade de pop-up para esse conjunto de dados.

![Menu de contexto de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Pode clicar numa janela de atividade para ver os detalhes para a mesma na janela da **propriedade** no painel direito. 

![Propriedades da janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel direito, mude para o separador **Atividade janela Explorador** para ver mais detalhes.

![Atividade janela Explorador](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png) 

Consulte também **resolvido variáveis** para cada execução de atividade tentativa na secção **tentativas** . 

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Mudar para o separador de **Script** para ver a definição de script JSON para o objeto selecionado.   

![Separador script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Pode ver windows atividade em três locais:

- Atividade Windows pop-up na vista de diagrama (painel do meio).
- Atividade janela do Explorer, no painel direito.
- Lista de atividade Windows no painel inferior.

No Windows de atividade de pop-up e atividade janela do Explorer, pode deslocar-se para a semana anterior e utilizar as setas para a esquerda e à direita na próxima semana.

![Atividade janela do Explorer esquerda/direita setas](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da vista de diagrama, consulte botões para ampliar, reduzir, Zoom para ajustar, Zoom a 100%, bloquear esquema. Botão esquema bloquear o impede de mover acidentalmente tabelas e tubagens na vista de diagrama e está Ativada por predefinição. Pode desativar a opção e mover entidades no diagrama. Quando ativá-la, pode utilizar o último botão para posicionar automaticamente tabelas e tubagens. Também pode ampliar / zoom com a roda do rato.

![Comandos de Zoom de vista de diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### <a name="activity-windows-list"></a>Lista de atividade do Windows
A lista de windows atividade na parte inferior do painel do meio apresenta todas as janelas de atividade para o conjunto de dados seleccionada no Explorador do recurso ou vista de diagrama. Por predefinição, a lista é a ordem descendente, o que significa que está a ver a janela de atividade mais recente na parte superior. 

![Lista de atividade do Windows](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista não atualizar automaticamente, pelo que o botão Atualizar na barra de ferramentas para atualizar manualmente.  


As janelas de atividade podem ser de uma das seguintes Estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">Em espera</td><td>ScheduleTime</td><td>Não tem vir a hora para a janela de atividade executar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de cluster não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas a executar o windows outras atividade.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Atividade está em pausa e não é possível executar as janelas de atividade até retomá-lo.</td>
</tr>
<tr>
<td>Repetir</td><td>Execução de atividade é repetida.</td>
</tr>
<tr>
<td>Validação</td><td>Validação ainda não tem iniciadas.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>A aguardar a validação de ser tentada novamente.</td>
</tr>
<tr>
<tr
<td rowspan="2">Em curso</td><td>Validar</td><td>Validação de progresso.</td>
</tr>
<td></td>
<td>A janela de atividade está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Ocorreu uma falha</td><td>Tempo limite excedido</td><td>Execução demorou mais tempo do que é permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>Cancelado por ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>Falha na validação.</td>
</tr>
<tr>
<td></td><td>Ocorreu uma falha ao gerar e/ou validar a janela de atividade.</td>
</tr>
<td>Pronto</td><td></td><td>A janela de atividade está pronta para o consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td></td><td>A janela de atividade não está a ser processada.</td>
</tr>
<tr>
<td>Nenhum</td><td></td><td>Janela de atividade que utilizado existir com o estado de diferentes, mas tiver sido reposta.</td>
</tr>
</table>


Quando clica numa janela de atividade na lista, consulte detalhes sobre a mesma na janela do **Explorador do Windows atividade** ou **Propriedades** à direita.

![Atividade janela Explorador](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualizar o windows de atividade  
Os detalhes não são automaticamente actualizados, para utilizar o **Atualizar** botão (segunda) na barra de comando para atualizar manualmente a lista de atividade do windows.  
 

### <a name="properties-window"></a>Janela de propriedades
É apresentada a janela Propriedades no painel mais à direita da aplicação de monitorização e gestão. 

![Janela de propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Apresenta as propriedades do item que selecionou no Explorador de recursos (vista de árvore) (ou) diagrama vista (ou) lista de atividade do windows. 

### <a name="activity-window-explorer"></a>Atividade janela Explorador

A janela do **Explorador da janela de atividade** é apresentada no painel mais à direita da monitorização e gestão de aplicação. Apresenta detalhes sobre a janela de atividade que selecionou no Windows de atividade de pop-up ou na lista de atividade Windows. 

![Atividade janela Explorador](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Pode mudar para outra janela de atividade ao clicar na mesma na vista de calendário na parte superior. Também pode utilizar a **seta para a esquerda**/botões de**seta para a direita** na parte superior para ver janelas de atividade a partir do anterior/semana seguinte.

Pode utilizar os botões da barra de ferramentas no painel inferior para o **execute novamente** a janela de atividade ou **Atualizar** os detalhes no painel de. 

### <a name="script"></a>Script 
Pode utilizar o separador de **Script** para ver a definição de JSON da entidade fábrica de dados selecionada (serviço ligadas, conjunto de dados e em curso). 

![Separador script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="using-system-views"></a>Utilizar vistas de sistema
A monitorização e gestão de aplicação inclui pré-concebidos vistas do sistema (**windows de atividade recentes**, **Falha na atividade windows**, **Windows atividade em curso**) permite-lhe ver janelas de atividade recentes/falhou/em curso para a fábrica de dados. 

Mudar para o separador de **Monitorização de vistas** à esquerda ao clicar na mesma. 

![Separador de vistas de monitorização](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, existem três vistas do sistema suportadas. Selecione uma opção para ver recentes atividade windows (ou) atividade falha windows (ou) windows em curso atividade na lista de atividade Windows (na parte inferior do painel do meio). 

Quando seleciona a opção **janelas de atividade recentes** , vê todas as janelas de atividade recentes a ordem descendente de **tentativa de última hora**. 

Pode utilizar a vista de **Falha na atividade windows** para ver todas as janelas de atividade falha na lista. Selecione uma janela de atividade falha na lista para ver detalhes sobre ela na **Propriedades** janela (ou) **Atividade janela Explorer**. Também pode transferir quaisquer registos para uma janela de atividade falha. 


## <a name="sorting-and-filtering-activity-windows"></a>Ordenar e filtrar windows de atividade
Altere as definições de **hora de início** e **hora de fim** na barra de comandos para o windows de atividade de filtro. Depois de alterar a hora de início e hora de fim, clique no botão junto a hora de fim para atualizar a lista de atividade Windows.

![Início e de fim](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Atualmente, todas as horas estão no formato de UTC na monitorização e gestão de aplicação. 

Na **lista de atividade Windows**, clique no nome de uma coluna (por exemplo: Estado). 

![Menu de atividade de coluna de lista do Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Pode fazer o seguinte:

- Ordenar pela ordem ascendente.
- Ordenar pela ordem descendente.
- Filtrar por um ou mais valores (preparado, em espera, etc.)

Quando especificar um filtro numa coluna, verá o botão de filtro com capacidade para essa coluna indicar que os valores na coluna são valores filtrados. 

![Filtrar na coluna da lista de atividade Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Pode utilizar a mesma janela de pop-up para limpar filtros. Para limpar todos os filtros para a lista de atividade do windows, clique no botão Limpar filtro na barra de comandos. 

![Limpar todos os filtros na lista de atividade Windows](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## <a name="performing-batch-actions"></a>Realizar ações batch

### <a name="rerun-selected-activity-windows"></a>Volte a executar o windows actividade seleccionada
Selecione uma janela de atividade, clique na seta para baixo para o primeiro botão da barra de comando e selecione **voltar a executar** / **novamente com o montante no pipeline**. Quando seleciona a opção **novamente com o montante no pipeline** , volta a executar todas as janelas de atividade montante também. 
    ![Volte a executar uma janela de atividade](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também pode selecionar várias janelas de atividade na lista e volte a executar ao mesmo tempo. Poderá pretender filtrar windows atividade com o estado (por exemplo: **falhou**) e, em seguida, volte a executar as janelas de atividade falha depois de corrigir o problema que faz com que as janelas de atividade para falhar. Consulte a secção seguinte para obter detalhes sobre a filtragem windows atividade na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Colocar em pausa/currículo vários tubagens
Pode seleções múltiplas duas ou mais tubagens (utilizando CTRL) e utilize os botões da barra de comando (realçados no rectângulo vermelho na seguinte imagem) para colocar em pausa/currículo-los uma vez.

![Suspender/currículo na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="creating-alerts"></a>Criar alertas 
A página alertas permite-lhe criar um alerta, ver/editar/eliminar alertas de existentes. Pode também desativar/activar um alerta. Para ver a página de alertas, clique no separador alertas.

![Separador alertas](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Para criar um alerta

1. Clique no **Alerta de adicionar** para adicionar um alerta. Irá ver a página de detalhes. 

    ![Criar alertas - página de detalhes](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Especifique o **nome** e **Descrição** para o alerta e clique em **seguinte**. Deverá ver a página de **filtros** .

    ![Criar alertas - página de filtros](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

2. Selecione o **evento**, **Estado**e **subestado** (opcional) no qual pretende que o serviço de dados fábrica para alertá-lo e clique em **seguinte**. Deverá ver a página de **destinatários** .

    ![Criar alertas - página de destinatários](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. Selecione a opção de **administradores de subscrição de correio eletrónico** e/ou introduza **correio electrónico do administrador adicionais**e clique em **Concluir**. Deverá ver o alerta na lista. 
    
    ![Lista de alertas](./media/data-factory-monitor-manage-app/AlertsList.png)

Na lista de alertas, utilize os botões associados a alerta para editar/eliminar/desativar/ativar um alerta. 

### <a name="eventstatussubstatus"></a>Estado/evento/subestado
A tabela seguinte fornece a lista de eventos disponíveis e Estados (e subestados).

Nome do evento | Estado | Estado de sub
-------------- | ------ | ----------
Executar a introdução de atividade | Iniciado | Iniciar
Executar terminada de atividade | Foi concluída com êxito | Foi concluída com êxito 
Executar terminada de atividade | Ocorreu uma falha| Atribuição de recursos falha<br/><br/>Falha de execução<br/><br/>Excedeu<br/><br/>Falha de validação<br/><br/>Abandonadas
A pedido HDI Cluster criar introdução | Iniciado | &nbsp; |
Cluster a pedido HDI criado com êxito | Foi concluída com êxito | &nbsp; |
Cluster a pedido HDI eliminado | Foi concluída com êxito | &nbsp; |
### <a name="to-editdeletedisable-an-alert"></a>Para editar/eliminar/desativar um alerta


![Botões de alertas](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 


