<properties 
   pageTitle="Subordinado runbooks no Azure automatização | Microsoft Azure"
   description="Descreve os diferentes métodos para iniciar um livro de execuções no Azure automatização a partir de outro livro de execuções e partilhar informações entre elas."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Subordinado runbooks na automatização do Azure

É aconselhável no Azure automatização para escrever reutilizável, modular runbooks com uma função descontínuos que pode ser utilizada pelo outras runbooks. Um livro de execuções principal ligarem frequentemente um ou mais runbooks subordinado para efetuar a funcionalidade necessária. Existem duas formas para ligar a um livro de execuções subordinado e cada tem diferenças distintas que deve compreender para que possa determinar qual será melhor para os seus cenários diferentes.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um livro de execuções subordinado utilizando execução inline

Para invocar uma inline livro execuções a partir de outro livro de execuções, utilize o nome do livro de execuções e forneça os valores para os respetivos parâmetros exatamente como utilizaria uma actividade ou um cmdlet.  Todos os runbooks na mesma conta de automatização estão disponíveis para todos os outros a ser utilizado em desta forma. Livro de execuções principal irá aguardar que o livro de execuções subordinado concluir antes de passar para a linha seguinte e qualquer saída é devolvida diretamente ao principal.

Quando invocar uma livro execuções inline, esta é executada na mesma tarefa de livro de execuções principal. Não será nenhuma indicação no histórico de tarefa do livro de execuções do subordinado que executou o-lo. Quaisquer exceções e qualquer sequência de saída do livro de execuções subordinado será associados com a principal. Isto resulta num menos tarefas e os torna mais fácil para controlar e resolver problemas de uma vez que quaisquer exceções iniciadas ao livro de execuções subordinado e qualquer um dos seu saída da cadeia estão associadas à tarefa de elemento principal.

Quando um livro de execuções é publicado, qualquer runbooks subordinado que ele chama já deve ser publicado. Isto acontece porque Azure automatização constrói uma associação com qualquer runbooks subordinado quando um livro de execuções é compilado. Se que não sejam, o livro de execuções principal serão apresentados para publicar corretamente, mas irá gerar uma exceção quando for iniciado. Se tal acontecer, pode publicar novamente o livro de execuções principal para poder corretamente fazer referência a runbooks subordinado. Não tem de voltar a publicar o livro de execuções principal se qualquer um da runbooks subordinado são alteradas porque a associação será já foram criada.

Os parâmetros de um livro de execuções subordinado denominado inline podem ser qualquer tipo de dados, incluindo objectos complexos, e não existe sem [serialização JSON](automation-starting-a-runbook.md#runbook-parameters) existe ao iniciar o livro de execuções utilizando o Portal de gestão do Azure ou com o cmdlet AzureRmAutomationRunbook iniciar.


### <a name="runbook-types"></a>Tipos de livro execuções

Quais os tipos de podem ligar a si:

- Um [livro de execuções do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks gráfica](automation-runbook-types.md#graphical-runbooks) podem ligar a cada outras inline (ambos são PowerShell com base).
- Um [livro de execuções do PowerShell fluxo de trabalho](automation-runbook-types.md#powershell-workflow-runbooks) e de fluxo de trabalho de PowerShell gráficas runbooks pode ligar a cada outras inline (ambos são fluxo de trabalho do PowerShell com base)
- Os tipos de PowerShell e os tipos de fluxo de trabalho do PowerShell não é possível chamar inline entre si e tem de utilizar AzureRmAutomationRunbook iniciar.
    
Quando publicar relevante ordem:

- A ordem de publicar das runbooks questões apenas para runbooks PowerShell e fluxo de trabalho gráficas PowerShell.


Ao ligar para um gráficos ou fluxo de trabalho do PowerShell subordinado livro execuções utilizando execução inline, utilizar apenas o nome do livro de execuções.  Ao ligar para um livro de execuções do PowerShell subordinado, tem de precedido o respetivo nome com *.\\ * Para especificar que o script está localizado no diretório local. 

### <a name="example"></a>Exemplo

O exemplo seguinte invoca um livro de teste execuções subordinado aceita três parâmetros de um objeto complexo, um número inteiro e um valor booleano. O resultado do livro de execuções subordinado está atribuído a uma variável.  Neste caso, o livro de execuções subordinado é um livro de execuções do PowerShell fluxo de trabalho

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Segue-se o mesmo exemplo utilizar um livro de execuções do PowerShell como subordinados.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciar um livro de execuções subordinado utilizando cmdlet

Pode utilizar o cmdlet [AzureRmAutomationRunbook de início](https://msdn.microsoft.com/library/mt603661.aspx) para começar a um livro de execuções conforme descrito [para iniciar um livro de execuções com o Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Existem dois modos de utilização para este cmdlet.  No modo de um, o cmdlet devolve o id da tarefa assim que a tarefa subordinada é criada para o livro de execuções subordinado.  No outro modo, que permitem ao especificar o **-esperar** parâmetro, o cmdlet irá esperar até que a criança tarefa acabamentos e irá devolver o resultado do livro de execuções subordinado.

A tarefa a partir de um livro de execuções subordinado iniciado com um cmdlet será executada numa tarefa em separado a partir do livro de execuções principal. Isto resulta num mais tarefas que invocar inline o livro execuções e torna-as mais difícil controlar. Principal pode começar a vários subordinado runbooks modo assíncrono sem aguardar que cada um deles concluir. Para esse mesmo tipo de execução paralela chamar o inline runbooks subordinado, livro de execuções principal seria necessário utilizar a [palavra-chave paralelo](automation-powershell-workflow.md#parallel-processing).

Parâmetros para um livro de execuções subordinado iniciado com um cmdlet são fornecidos como um hashtable conforme descrito no [Livro execuções parâmetros](automation-starting-a-runbook.md#runbook-parameters). Podem ser utilizados apenas tipos de dados simples. Se o livro de execuções tiver um parâmetro com um tipo de dados complexos, em seguida,-tem de ser chamado inline.

### <a name="example"></a>Exemplo

O exemplo seguinte inicia um livro de execuções subordinado com parâmetros e, em seguida, aguarda-lo concluir a utilizar o AzureRmAutomationRunbook início-esperar parâmetro. Uma vez concluída, o resultado é recolhido a partir do livro de execuções subordinado.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação dos métodos para ligar a um livro de execuções subordinado

A tabela seguinte resume as diferenças entre os dois métodos para ligar a um livro de execuções a partir de outro livro de execuções.

| | Inline| Cmdlet|
|:---|:---|:---|
|Tarefa|Subordinado runbooks executar na mesma tarefa como principal.|É criada uma tarefa em separado para o livro de execuções subordinado.|
|Execução|Livro de execuções principal aguarda livro de execuções subordinado concluir antes de continuar.|Livro de execuções principal continua imediatamente após o livro de execuções subordinado for iniciado *ou* livro principal execuções espera para a tarefa subordinada concluir.|
|Saída|Livro de execuções principal pode obter diretamente saída a partir do livro de execuções subordinado.|Livro de execuções principal deve obter a saída de subordinados livro execuções tarefa *ou* livro de execuções do principal diretamente pode obter saída na partir do livro de execuções subordinado.|
|Parâmetros|Valores para os parâmetros de livro execuções subordinado são especificados separadamente e podem utilizar qualquer tipo de dados.|Valores para os parâmetros de livro execuções subordinado tem combinados numa única hashtable e podem incluir apenas simples, matriz e tipos de dados de objeto que serialização de JSON aproveitar.|
|Conta de automatização|Livro de execuções principal só pode utilizar o livro de execuções subordinado na mesma conta de automatização.|Livro de execuções principal pode utilizar o livro de execuções subordinado a partir de qualquer conta automatização da mesma subscrição do Azure e até mesmo uma subscrição diferente se tiver uma ligação à mesma.|
|Publicação|Livro de execuções subordinado deve ser publicado antes de livro de execuções principal é publicado.|Livro de execuções subordinado deve ser publicado a qualquer momento antes de livro de execuções principal é iniciado.|

## <a name="next-steps"></a>Próximos passos

- [Iniciar um livro de execuções no Azure automatização](automation-starting-a-runbook.md)
- [Saída do livro execuções e mensagens de automatização do Azure](automation-runbook-output-and-messages.md)
