<properties 
    pageTitle="Editar runbooks textual na automatização do Azure"
    description="Este artigo fornece procedimentos diferentes para trabalhar com runbooks PowerShell e fluxo de trabalho do PowerShell no Azure automatização utilizando o editor de textual."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Editar runbooks textual na automatização do Azure

O editor textual no Azure automatização pode ser utilizado para editar [PowerShell runbooks](automation-runbook-types.md#powershell-runbooks) e [runbooks PowerShell fluxo de trabalho](automation-runbook-types.md#powershell-workflow-runbooks). Este tenha as funcionalidades típicas de outros editores de código como o intellisense e codificação de cores com funcionalidades especiais adicionais para ajudá-lo a aceder a recursos comuns a runbooks.  Este artigo fornece os passos detalhados para desempenhar diferentes funções com este editor.

O editor de textual inclui uma funcionalidade para inserir o código para atividades, recursos e subordinado runbooks para um livro de execuções. Em vez de escrever no código de si, pode selecionar a partir de uma lista dos recursos disponíveis e ter o código adequado inserido no livro de execuções.

Cada livro de execuções no Azure automatização tem duas versões, rascunho e publicados. Editar a versão de rascunho do livro de execuções e, em seguida, publicá-lo para que possa ser executado. A versão publicada não pode ser editada. Para obter mais informações, consulte a [publicar um livro de execuções](automation-creating-importing-runbook.md#publishing-a-runbook) .

Para trabalhar com [Gráficos Runbooks](automation-runbook-types.md#graphical-runbooks), consulte o artigo [gráficos cocriação na automatização Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um livro de execuções com o portal do Azure

Utilize o seguinte procedimento para abrir um livro de execuções para edição no editor de textual.

1. No portal do Azure, selecione a sua conta de automatização.
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Clique no nome do livro de execuções que pretende editar e, em seguida, clique no botão **Editar** .
6. Execute a edição necessários.
7. Clique em **Guardar** quando as edições estiverem concluídas.
8. Se pretender que a versão mais recente do rascunho do livro de execuções para ser publicado, clique em **Publicar** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Para inserir um cmdlet para um livro de execuções

2. Na tela do editor de textual, posicione o cursor onde pretende colocar o cmdlet.
3. Expanda o nó **Cmdlets** no controlo de biblioteca. 
3. Expanda o módulo que contém o cmdlet que pretende utilizar.
4. Clique com botão direito do cmdlet para inserir e selecione **Adicionar ao tela**.  Se o cmdlet tiver mais do que um parâmetro definido, o conjunto predefinido será adicionado.  Também pode expandir o cmdlet para selecionar um conjunto diferente de parâmetro.
4. O código para o cmdlet é inserido com a sua lista completa dos parâmetros.
5. Fornece um valor adequado em vez do tipo de dados rodeado por chavetas <> para quaisquer parâmetros necessários.  Remova quaisquer parâmetros que não precisa.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir código para um livro de execuções subordinado um livro de execuções

2. Na tela do editor de textual, posicione o cursor onde pretende colocar o código para o [livro de execuções subordinado](automation-child-runbooks.md).
3. Expanda o nó **Runbooks** no controlo de biblioteca. 
3. Botão direito do rato, clique em livro de execuções para inserir e selecione **Adicionar ao tela**.
4. O código do livro de execuções subordinado é inserido com qualquer marcadores de posição para quaisquer parâmetros de livro execuções.
5. Substitua os marcadores de posição com valores adequados para cada parâmetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para inserir um ativo para um livro de execuções

2. Na tela do editor de textual, posicione o cursor onde pretende colocar o código do livro de execuções subordinado.
3. Expanda o nó de **activos** no controlo de biblioteca. 
4. Expanda o nó do tipo de elementos que pretende.
3. Botão direito do rato, clique em ativo para inserir e selecione **Adicionar ao tela**.  Para os [elementos de variáveis](automation-variables.md), selecione **Adicionar "obter variável de" a tela** ou **Adicionar "Definir variável de" a tela** dependendo se pretende obter ou definir a variável.
4. O código para o elemento é inserido no livro de execuções.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um livro de execuções com o portal do Azure

Utilize o seguinte procedimento para abrir um livro de execuções para edição no editor de textual.

1. No portal do Azure, selecione **Automatização** e, em seguida, em seguida, clique no nome de uma conta de automatização.
2. Selecione o separador **Runbooks** .
3. Clique no nome do livro de execuções que pretende editar e, em seguida, selecione o separador de **autor** .
5. Clique no botão **Editar** na parte inferior do ecrã.
6. Execute a edição necessários.
7. Clique em **Guardar** quando as edições estiverem concluídas.
8. Se pretender que a versão mais recente do rascunho do livro de execuções para ser publicado, clique em **Publicar** .

### <a name="to-insert-an-activity-into-a-runbook"></a>Para inserir uma atividade de um livro de execuções

1. Na tela do editor de textual, posicione o cursor onde pretende colocar a atividade.
1. Na parte inferior do ecrã, clique em **Inserir** e, em seguida, **atividade**.
1. Na coluna a **Integração do módulo** , selecione o módulo que contém a atividade.
1. No painel de **atividade** , selecione uma atividade.
1. Na coluna da **Descrição** , tenha em atenção a descrição da atividade. Opcionalmente, pode clicar em ver detalhadas ajuda para iniciar a ajuda para a atividade no browser.
1. Clique na seta à direita.  Se a atividade tiver parâmetros, serão apresentados às suas informações.
1. Clique no botão de verificação.  Código para executar a atividade vai ser inserido no livro de execuções.
1. Se a atividade requer parâmetros, fornece um valor em vez do tipo de dados rodeado por chavetas <> adequado.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir código para um livro de execuções subordinado um livro de execuções

1. Na tela do editor de textual, posicione o cursor onde pretende colocar o [livro de execuções subordinado](automation-child-runbooks.md).
2. Na parte inferior do ecrã, clique em **Inserir** e, em seguida, **livro execuções**.
3. Selecione o livro de execuções para inserir a partir da coluna central e clique na seta à direita.
4. Se o livro de execuções tiver parâmetros, serão apresentados às suas informações.
5. Clique no botão de verificação.  Código para executar o livro de execuções selecionado vai ser inserido no livro de execuções atual.
7. Se o livro de execuções requer parâmetros, fornece um valor em vez do tipo de dados rodeado por chavetas <> adequado.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para inserir um ativo para um livro de execuções

1. Na tela do editor de textual, posicione o cursor onde pretende colocar a atividade para obter o elemento.
1. Na parte inferior do ecrã, clique em **Inserir** e, em seguida, **definição**.
1. Na coluna **Ação definição** , selecione a ação que pretende.
1. Selecione os ativos disponíveis a coluna central.
1. Clique no botão de verificação.  Código para obter ou definir elemento vai ser inserido no livro de execuções.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar um livro de execuções do Azure automatização através do Windows PowerShell

Para editar um livro de execuções com o Windows PowerShell, utilize o editor da sua preferência e guardá-lo para um ficheiro. ps1. Pode utilizar o cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) para obter o conteúdo do livro execuções e, em seguida, [Definir AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) cmdlet para substituir o livro de execuções rascunho existente pelo modificados.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para obter os conteúdos de um livro de execuções através do Windows PowerShell

Os comandos de exemplo seguintes mostram como obter o script para um livro de execuções e guardá-lo para um ficheiro de script. Neste exemplo, a versão de rascunho serem recuperada. Também é possível obter a versão publicada do livro de execuções apesar desta versão não pode ser alterada.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para alterar o conteúdo de um livro de execuções através do Windows PowerShell

Os seguintes comandos de exemplo mostram como substituir o conteúdo existente de um livro de execuções com os conteúdos de um ficheiro de script. Tenha em atenção que este é o mesmo procedimento de exemplo, tal como [Importar um livro de execuções a partir de um ficheiro de script com o Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Artigos relacionados

- [Criar ou importar um livro de execuções na automatização do Azure](automation-creating-importing-runbook.md)
- [Home page do PowerShell fluxo de trabalho](automation-powershell-workflow.md)
- [Gráficos de cocriação na automatização do Azure](automation-graphical-authoring-intro.md)
- [Certificados](automation-certificates.md)
- [Ligações](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Agendas](automation-schedules.md)
- [Variáveis](automation-variables.md)