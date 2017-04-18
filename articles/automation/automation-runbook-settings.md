<properties 
   pageTitle="Definições de livro execuções"
   description="Descreve as definições de configuração de um livro de execuções no Azure automatização e como alterá-los com o Portal de gestão do Azure e o Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Definições de livro execuções

Cada livro de execuções no Azure automatização tem várias definições que ajudá-lo para ser verificada e para alterar o comportamento de registo. Cada uma destas definições é descrita abaixo seguido de procedimentos sobre como modificá-las.

## <a name="settings"></a>Definições

### <a name="name-and-description"></a>Nome e descrição

Não é possível alterar o nome de um livro de execuções após ter sido criada. A descrição opcional e pode ter até 512 carateres.

### <a name="tags"></a>Etiquetas

Etiquetas permitem-lhe atribuir palavras particulares e expressões para ajudar a identificar um livro de execuções. Por exemplo, quando submete um livro de execuções na [Galeria de livro execuções](https://msdn.microsoft.com/library/dn781422.aspx), especifique as etiquetas específicas para identificar as categorias de livro de execuções deve ser indicado no. Pode especificar várias etiquetas para um livro de execuções, separando-los por vírgulas.

### <a name="logging"></a>Funcionalidade de registo

Por predefinição, verboso e o progresso de registos não são escritos histórico da tarefa. Pode alterar as definições de um determinado livro de execuções para iniciar sessão estes registos. Para mais informações sobre estes registos, consulte [mensagens e de saída do livro execuções](https://msdn.microsoft.com/library/dn879148.aspx).

## <a name="changing-runbook-settings"></a>Alterar as definições de livro execuções

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Alterar as definições de livro execuções com o Portal de gestão do Azure

Pode alterar as definições para um livro de execuções no Portal de gestão do Azure a partir da página **Configurar** para o livro de execuções.

1. No Portal de gestão do Azure, selecione **Automatização** e, em seguida, em seguida, clique no nome de uma conta de automatização.
1. Selecione o separador **Runbooks** .
1. Clique no nome de um livro de execuções.
1. Selecione o separador de **Configurar** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Alterar as definições de livro execuções com o Windows PowerShell

Pode utilizar o cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) para alterar as definições de um livro de execuções. Se pretende especificar várias etiquetas, quer pode fornecer uma matriz ou de uma única cadeia com valores delimitado por vírgulas para o parâmetro de etiquetas. Pode obter as etiquetas com a [Obter AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx)atuais.

Os comandos de exemplo seguintes mostram como configurar as propriedades de um livro de execuções. Este exemplo adiciona três etiquetas para as etiquetas existentes e especifica que devem ser registados verbosas registos.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Artigos relacionados
- [Livro execuções saída e mensagens](../automation-runbook-output-and-messages) 
- [Criar ou importar um livro de execuções](https://msdn.microsoft.com/library/dn643637.aspx) 