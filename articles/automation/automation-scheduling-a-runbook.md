<properties 
   pageTitle="Agendamento de um livro de execuções no Azure automatização | Microsoft Azure"
   description="Descreve como criar uma agenda no Azure automatização, para que possa começar automaticamente um livro de execuções num momento específico ou numa agenda periódica."
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
   ms.date="08/05/2016"
   ms.author="bwren" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendamento de um livro de execuções na automatização do Azure

Para agendar um livro de execuções no Azure automatização para iniciar a uma hora especificada, ligá-lo a uma ou mais agendas. Uma agenda pode ser configurada para executar apenas uma vez ou uma ocorra novamente por hora ou diariamente agenda para runbooks no portal clássico do Azure e para runbooks no portal do Azure, pode para além disso agendá-los para semanal, mensal, determinados dias da semana ou dias do mês ou dia do mês em particular.  Um livro de execuções pode ser ligado a várias agendas e uma agenda pode ter vários runbooks associado à mesma.


## <a name="creating-a-schedule"></a>Criar uma agenda

Pode criar uma nova agenda para runbooks no portal do Azure, no portal do clássico ou com o Windows PowerShell. Também tem a opção de criar uma nova agenda quando cria um livro de execuções uma ligação para uma agenda utilizando o portal de clássico ou o Azure Azure.

>[AZURE.NOTE] Ao associar uma agenda de um livro de execuções, automatização armazena versões atuais dos módulos na sua conta e ligações-los para que agenda.  Isto significa que se tinha um módulo com a versão 1.0 na sua conta quando tiver criado uma agenda e, em seguida, atualize o módulo para a versão 2.0, a agenda continua a utilizar 1.0.  Para poder utilizar a versão actualizada módulo, terá de criar uma nova agenda. 

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Para criar uma nova agenda no portal do Azure clássico

1. No portal do Azure clássico, selecione automatização e, em seguida, em seguida, selecione o nome de uma conta de automatização.
1. Selecione o separador de **recursos** .
1. Na parte inferior da janela, clique em **Adicionar definição**.
1. Clique em **Adicionar agenda**.
1. Escreva um **nome** e, opcionalmente, uma **Descrição** para a nova agenda de schedule.your será executado **Uma vez**, **horários**, **diária**, **semanal**ou **mensal**.
1. Especifique uma **Hora de início** e outras opções, dependendo do tipo de agenda que selecionou.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure

1. No portal do Azure, da sua conta de automatização, clique no mosaico de **activos** para abrir o pá de **recursos** .
2. Clique no mosaico de **agendas** para abrir o pá **agendas** .
3. Clique em **Adicionar uma agenda** no topo da pá.
4. No pá **nova agenda** , escreva um **nome** e, opcionalmente, uma **Descrição** para a nova agenda.
5. Selecione se a agenda será executada uma vez ou uma agenda periódica ao selecionar **uma vez** ou **Periodicidade**.  Se selecionar **uma vez por** especificar uma **hora de início** e, em seguida, clique em **Criar**.  Se selecionar **Periodicidade**, especifique uma **hora de início** e a frequência para com que frequência com que pretende que o livro de execuções para repetir - por **hora**, **o dia**, **semana**ou **mês**.  Se selecionar **semana** ou **mês** a partir da lista pendente, a **opção periodicidade** será apresentada a pá e relativamente a seleção, será apresentada a **opção periodicidade** pá e pode selecionar o dia da semana, se tiver selecionado **semana**.  Se tiver seleccionado o **mês**, pode escolher por **dias da semana** ou determinados dias do mês no calendário e por fim, faça que pretende executá-la no último dia do mês ou não e, em seguida, clique em **OK**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para criar uma nova agenda com o Windows PowerShell

Pode utilizar o cmdlet [AzureAutomationSchedule novo](http://msdn.microsoft.com/library/azure/dn690271.aspx) para criar uma nova agenda no Azure automatização para runbooks clássico ou [Novo AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet para runbooks no portal do Azure. Tem de especificar a hora de início para a agenda e a frequência deve executar.

Os comandos de exemplo seguintes mostram como criar uma nova agenda que é executada cada dia na 3:30 PM começando no 20 de Janeiro de 2015 um cmdlet de gestão de serviço do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

Os comandos de exemplo seguinte mostra como criar uma agenda para o dia 15 e 30 de cada mês utilizando um cmdlet do Gestor de recursos do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
    

## <a name="linking-a-schedule-to-a-runbook"></a>Ligar uma agenda a um livro de execuções

Um livro de execuções pode ser ligado a várias agendas e uma agenda pode ter vários runbooks associado à mesma. Se um livro de execuções tiver parâmetros, pode fornecer valores para os mesmos. Tem de fornecer valores para qualquer parâmetros obrigatórios e poderá fornecer valores para os parâmetros opcionais.  Estes valores serão utilizadas sempre que o livro de execuções é iniciado por esta agenda.  Pode anexar o livro de execuções mesmo a outra agenda e especificar valores de parâmetros diferente.


### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Para ligar uma agenda para um livro de execuções com portal clássico do Azure

1. No portal do Azure clássico, selecione **Automatização** e, em seguida, em seguida, clique no nome de uma conta de automatização.
2. Selecione o separador **Runbooks** .
3. Clique no nome do livro de execuções agendar.
4. Clique no separador **agenda** .
5. Se o livro de execuções não está atualmente ligado a uma agenda, em seguida, ser-lhe será dada a opção para **ligação para uma nova agenda de** ou **ligar a uma agenda existente**.  Se o livro de execuções atualmente estiver ligado a uma agenda, clique em **ligação** na parte inferior da janela para aceder a estas opções.
6. Se o livro de execuções tiver parâmetros, ser-lhe-á pedido para os respetivos valores.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para criar uma agenda de uma ligação para um livro de execuções com o portal do Azure

1. No portal do Azure, da sua conta de automatização, clique no mosaico **Runbooks** para abrir o pá **Runbooks** .
2. Clique no nome do livro de execuções agendar.
3. Se o livro de execuções não está ligado atualmente para uma agenda, em seguida, ser-lhe será dada a opção para criar uma nova agenda de ou ligar a uma agenda existente.  
4. Se o livro de execuções tiver parâmetros, pode selecionar a opção **Modificar executar definições (predefinido: Azure)** e o pá **parâmetros** é apresentada onde pode introduzir as informações em conformidade.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para criar uma agenda de uma ligação para um livro de execuções com o Windows PowerShell

Pode utilizar o [Registo AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para criar uma agenda de uma ligação a um livro de execuções clássico ou o cmdlet [Register AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) para runbooks no portal do Azure.  Pode especificar valores para os parâmetros do livro de execuções com o parâmetro de parâmetros. Consulte o artigo [Iniciar um livro de execuções no Azure automatização](automation-starting-a-runbook.md) para obter mais informações sobre como especificar valores de parâmetros.

Os comandos de exemplo seguintes mostram como associar uma agenda utilizando um cmdlet de gestão de serviço do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

Os comandos de exemplo seguintes mostram como associar uma agenda para um livro de execuções utilizando um cmdlet do Gestor de recursos do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## <a name="disabling-a-schedule"></a>Desativar uma agenda

Quando desativar uma agenda, qualquer runbooks associado à mesma já não será executado no mesmo esquema. Pode desativar uma agenda ou definir uma hora de expiração para agendas com frequência quando de criá-las manualmente. Quando o tempo de expiração é atingido, será desativada na agenda.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Para desativar uma agenda a partir do Azure portal clássico

Pode desativar uma agenda no portal do Azure clássica a partir da página de detalhes da agenda para a agenda.

1. No portal do Azure clássico, selecione automatização e, em seguida, em seguida, clique no nome de uma conta de automatização.
1. Selecione o separador de recursos.
1. Clique no nome de uma agenda para abrir a sua página de detalhes.
2. Alterar **ativado** para **não**.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desativar uma agenda a partir do portal do Azure

1. No portal do Azure, da sua conta de automatização, clique no mosaico de **activos** para abrir o pá de **recursos** .
2. Clique no mosaico de **agendas** para abrir o pá **agendas** .
2. Clique no nome de uma agenda para abrir o separador Detalhes.
3. Alterar **ativado** para **não**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para desativar uma agenda com o Windows PowerShell

Pode utilizar o cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para alterar as propriedades de uma agenda existente para um livro de execuções clássico ou o cmdlet [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) para runbooks no portal do Azure. Para desativar a agenda, especifique **Falso** para o parâmetro **IsEnabled** .

Os comandos de exemplo seguintes mostram como desativar uma agenda utilizando o cmdlet de gestão de serviço do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

Os comandos de exemplo seguintes mostram como desativar uma agenda para um livro de execuções utilizando um cmdlet do Gestor de recursos do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre como trabalhar com esquemas, consulte o artigo [Ativos de agenda na automatização do Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- Para começar a trabalhar com runbooks no Azure automatização, consulte o artigo [Iniciar um livro de execuções na automatização do Azure](automation-starting-a-runbook.md) 