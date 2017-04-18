<properties
    pageTitle="Criar ou importar um livro de execuções na automatização do Azure"
    description="Este artigo descreve como criar um novo livro de execuções no Azure automatização ou importar um partir de um ficheiro."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Criar ou importar um livro de execuções na automatização do Azure

Pode adicionar um livro de execuções para Azure automatização, quer [criar um novo](#creating-a-new-runbook) ou através da importação de um livro de execuções existente a partir de um ficheiro ou a partir da [Galeria de livro execuções](automation-runbook-gallery.md). Este artigo fornece informações sobre como criar e importar runbooks de um ficheiro.  Pode obter todos os detalhes sobre como aceder ao módulos no [livro execuções e módulo galerias de automatização do Azure](automation-runbook-gallery.md)e runbooks de Comunidade.

## <a name="creating-a-new-runbook"></a>Criar um novo livro de execuções

Pode criar um novo livro de execuções no Azure automatização utilizando um do Azure portais ou o Windows PowerShell. Assim que o livro de execuções ter sido criado, pode editá-lo utilizando as informações no [Fluxo de trabalho de PowerShell de aprendizagem](automation-powershell-workflow.md) e [gráficos a cocriação na automatização Azure](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Para criar um novo livro de execuções do Azure automatização com o portal do Azure clássico

Só pode trabalhar com o [fluxo de trabalho do PowerShell runbooks](automation-runbook-types.md#powershell-workflow-runbooks) no portal do Azure.

1. No portal do Azure clássico, clique em **Novo**, **Serviços de aplicação**, **Automatização**, **livro execuções**, **Criação rápida**.
2. Introduza as informações necessárias e, em seguida, clique em **Criar**. O nome do livro execuções tem de iniciar por uma letra e pode ter letras, números, sublinhado e travessões.
3. Se pretende editar o livro de execuções agora, em seguida, clique em **Editar o livro de execuções**. Caso contrário, clique em **OK**.
4. O novo livro de execuções irão aparecer no separador **Runbooks** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para criar um novo livro de execuções do Azure automatização com o portal do Azure

1. No portal do Azure, abre a sua conta de automatização.
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um livro de execuções** e, em seguida, **criar um novo livro de execuções**.
2. Escreva um **nome** para o livro de execuções e selecione o [tipo](automation-runbook-types.md). O nome do livro execuções tem de iniciar por uma letra e pode ter letras, números, sublinhado e travessões.
3. Clique em **Criar** para criar o livro de execuções e abra o editor.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para criar um novo livro de execuções do Azure automatização com o Windows PowerShell

Pode utilizar o cmdlet [AzureRmAutomationRunbook novo](https://msdn.microsoft.com/library/mt619376.aspx) para criar um [livro de execuções do PowerShell fluxo de trabalho](automation-runbook-types.md#powershell-workflow-runbooks)de vazia. Pode especificar o parâmetro **nome** para criar um livro de execuções branco que pode editar mais tarde ou pode especificar o parâmetro de **caminho** para importar um ficheiro de livro execuções. O parâmetro de **tipo** também deve ser incluído para especificar uma das quatro tipos de livro execuções.

Os comandos de exemplo seguintes mostram como criar um novo livro de execuções vazio.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importar um livro de execuções a partir de um ficheiro para automatização do Azure

Pode criar um novo livro de execuções no Azure automatização importando um script PowerShell ou PowerShell fluxo de trabalho (. ps1 extensão) ou um livro de execuções exportado gráfico (.graphrunbook).  Tem de especificar o [tipo de livro execuções](automation-runbook-types.md) que será criada a partir tendo em conta as seguintes considerações importar.

- Um ficheiro de .graphrunbook apenas pode ser importado para um novo [livro de execuções gráfico](automation-runbook-types.md#graphical-runbooks)e gráficos runbooks só podem ser criados a partir de um ficheiro de .graphrunbook.
- Um ficheiro. ps1 que contenha um fluxo de trabalho do PowerShell só pode ser importado para um [livro de execuções do PowerShell fluxo de trabalho](automation-runbook-types.md#powershell-workflow-runbooks).  Se o ficheiro contém vários fluxos de trabalho do PowerShell, a importação irá falhar. Tem de guardar cada fluxo de trabalho no seu próprio ficheiro e importar cada separadamente.
- Um ficheiro. ps1 que não contém um fluxo de trabalho pode ser importado de um [livro de execuções do PowerShell](automation-runbook-types.md#powershell-runbooks) ou um [livro de execuções do PowerShell fluxo de trabalho](automation-runbook-types.md#powershell-workflow-runbooks).  Se a mesma é importada para um livro de execuções do fluxo de trabalho do PowerShell, em seguida, será convertido para um fluxo de trabalho e comentários serão incluídos no livro de execuções especificando as alterações efetuadas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Para importar um livro de execuções a partir de um ficheiro com o portal do Azure clássico
Pode utilizar o seguinte procedimento para importar um ficheiro de script para automatização Azure.  Tenha em atenção que apenas pode importar um ficheiro. ps1 para um livro de execuções do fluxo de trabalho do PowerShell utilizando este portal.  Tem de utilizar o portal do Azure para outros tipos de.

1. No portal de gestão do Azure, selecione **Automatização** e, em seguida, selecione uma conta de automatização.
2. Clique em **Importar**.
3. Clique em **Procurar o ficheiro** e localize o ficheiro de script para importar.
4. Se pretende editar o livro de execuções agora, em seguida, clique em **Editar o livro de execuções**. Caso contrário, clique em OK.
5. Livro de execuções novo aparecerá no separador **Runbooks** para a conta de automatização.
6. Tem de [publicar o livro de execuções](#publishing-a-runbook) antes de que pode executá-la.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar um livro de execuções a partir de um ficheiro com o portal do Azure
Pode utilizar o seguinte procedimento para importar um ficheiro de script para automatização Azure.  

>[AZURE.NOTE] Tenha em atenção que apenas pode importar um ficheiro. ps1 para um livro de execuções do PowerShell fluxo de trabalho através do portal.

1. No portal do Azure, abre a sua conta de automatização.
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um livro de execuções** e, em seguida, **Importar**.
4. Clique em **ficheiro de livro execuções** para selecionar o ficheiro a importar
2. Se o campo **nome** estiver ativado, em seguida, tem a opção para alterá-lo.  O nome do livro execuções tem de iniciar por uma letra e pode ter letras, números, sublinhado e travessões.
3. O [tipo de livro execuções](automation-runbook-types.md) será selecionada automaticamente, mas pode alterar o tipo de depois de efetuar as restrições aplicáveis para a conta. 
3. Livro de execuções novo irão aparecer na lista de runbooks para a conta de automatização.
4. Tem de [publicar o livro de execuções](#publishing-a-runbook) antes de que pode executá-la.

>[AZURE.NOTE] Depois de importar um livro de execuções gráfico ou um livro de execuções de fluxo de trabalho de PowerShell gráfico, tem a opção para converter o outro tipo se pretendia. Não pode converter textual.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar um livro de execuções a partir de um ficheiro de script com o Windows PowerShell

Pode utilizar o cmdlet [AzureRMAutomationRunbook importação](https://msdn.microsoft.com/library/mt603735.aspx) para importar um ficheiro de script como um rascunho livro de execuções do PowerShell fluxo de trabalho. Se o livro de execuções já existir, a importação irá falhar, a menos que utilize o *-Forçar* parâmetro. 

Os comandos de exemplo seguintes mostram como importar um ficheiro de script para um livro de execuções.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicar um livro de execuções

Quando criar ou importar um novo livro de execuções, tem de publicá-lo antes de poder executá-lo.  Cada livro de execuções na automatização tem um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado e pode ser editada apenas a versão de rascunho. A versão publicada não é afetada por quaisquer alterações para a versão de rascunho. Quando deve ser disponibilizada a versão de rascunho, em seguida, publicar que substitui a versão publicada com a versão de rascunho.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Para publicar um livro de execuções através do portal Azure clássico

1. Abra o livro de execuções no portal do Azure clássica.
1. Na parte superior do ecrã, clique em **autor**.
1. Na parte inferior do ecrã, clique em **Publicar** e, em seguida, **Sim** para a mensagem de confirmação.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Para publicar um livro de execuções através do portal Azure

1. Abra o livro de execuções no portal do Azure.
1. Clique no botão **Editar** .
1. Clique no botão **Publicar** e, em seguida, **Sim** para a mensagem de confirmação.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Para publicar um livro de execuções através do Windows PowerShell

Pode utilizar o cmdlet [AzureRmAutomationRunbook publicar](https://msdn.microsoft.com/library/mt603705.aspx) para publicar um livro de execuções com o Windows PowerShell. Os comandos de exemplo seguintes mostram como publicar um livro de execuções do exemplo.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Próximos passos
- Para saber mais sobre como beneficiar de livro execuções e Galeria de módulo do PowerShell, consulte o artigo [livro execuções e módulo galerias de automatização do Azure](automation-runbook-gallery.md)
- Para saber mais sobre a edição runbooks PowerShell e fluxo de trabalho do PowerShell com um editor de textual, consulte o artigo [runbooks textual de edição no Azure automatização](automation-edit-textual-runbook.md)
- Para saber mais sobre a criação de livro de execuções gráfico, consulte o artigo [gráficos cocriação na automatização do Azure](automation-graphical-authoring-intro.md)
