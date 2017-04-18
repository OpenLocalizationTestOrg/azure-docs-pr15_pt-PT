<properties 
   pageTitle="Tipos de livro execuções automatização Azure"
   description="Descreve os diferentes tipos de runbooks que pode utilizar no Azure automatização e considerações que deverá ter em consideração quando para determinar que tipo deve para utilizar. "
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
   ms.author="bwren" />

# <a name="azure-automation-runbook-types"></a>Tipos de livro execuções automatização Azure

Automatização Azure suporta quatro tipos de runbooks são brevemente descritas na seguinte tabela.  As secções abaixo fornecem mais informações sobre cada tipo incluindo considerações sobre quando utilizar cada um.


| Tipo |  Descrição |
|:---|:---|
| [Gráficos](#graphical-runbooks) | Com base no Windows PowerShell e criado e editado completamente no editor de gráfico no Azure portal. | 
| [Gráficos fluxo de trabalho do PowerShell](#graphical-runbooks) | Com base no fluxo de trabalho do Windows PowerShell e criados e editados completamente no editor de gráfico no Azure portal. 
| [PowerShell](#powershell-runbooks) | Livro de execuções de texto com base em script do Windows PowerShell.
| [Fluxo de trabalho do PowerShell](#powershell-workflow-runbooks) | Livro de execuções de texto com base no fluxo de trabalho do Windows PowerShell. |


## <a name="graphical-runbooks"></a>Runbooks gráficas

[Gráficos](automation-runbook-types.md#graphical-runbooks) e gráficos fluxo de trabalho do PowerShell runbooks são criados e editados com o editor de gráfico no portal do Azure.  Pode exportá-los para um ficheiro e, em seguida, importá-los para outra conta de automatização, mas não é possível criar ou edite-os com outra ferramenta.  Runbooks gráficas gerar código do PowerShell, mas não pode ver ou modificar o código diretamente. Não pode ser convertido runbooks gráficas para um dos [formatos de texto](automation-runbook-types.md), nem pode ser convertido um livro de execuções do texto para formato de gráfico. Runbooks gráficas podem ser convertidos para gráficas fluxo de trabalho do PowerShell runbooks durante a importação e vice versa.

### <a name="advantages"></a>Vantagens

- Crie runbooks com dados de conhecimento mínimos do [PowerShell](automation-powershell-workflow.md).
- Representa visualmente os processos de gestão.
- Inclua outros runbooks como subordinado runbooks para criar fluxos de trabalho alto nível.


### <a name="limitations"></a>Limitações

- Não pode editar o livro de execuções fora do Azure portal.
- Poderá necessitar de uma atividade de código que contém o código do PowerShell para realizar lógica complexa.
- Não é possível visualizar ou editar diretamente o código de PowerShell que é criado pelo estruturador fluxo de trabalho. Tenha em atenção que pode ver o código que cria no outras atividades de código.


## <a name="powershell-runbooks"></a>PowerShell runbooks

PowerShell runbooks são baseados no Windows PowerShell.  Editar o código do livro de execuções utilizando o editor de texto no portal do Azure diretamente.  Também pode utilizar qualquer editor de texto offline e, em seguida, [Importar livro de execuções](http://msdn.microsoft.com/library/azure/dn643637.aspx) para automatização Azure.

### <a name="advantages"></a>Vantagens

- Implemente a todos os lógica complexa com código de PowerShell sem a complexidade de adicional de fluxo de trabalho do PowerShell. 
- Livro execuções mais depressa que runbooks gráficos ou fluxo de trabalho do PowerShell inicia uma vez que não necessita de ser compilada antes de executar.

### <a name="limitations"></a>Limitações

- Tem de estar familiarizado com os scripts de PowerShell.
- Não pode utilizar o [Processamento paralelo](automation-powershell-workflow.md#parallel-processing) executar várias acções em paralelo.
- Não é possível utilizar [os pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o livro de execuções em caso de erro.
- Fluxo de trabalho do PowerShell runbooks e runbooks gráfica só podem ser incluídos como subordinado runbooks utilizando o cmdlet iniciar AzureAutomationRunbook que cria uma nova tarefa.

### <a name="known-issues"></a>Problemas conhecidos
Seguem-se atual problemas conhecidos do PowerShell runbooks.

- PowerShell runbooks não é possível não consegue obter um não encriptado [elementos variável](automation-variables.md) com um valor nulo.
- PowerShell runbooks não consegue obter um [variável ativos](automation-variables.md) com *~* no nome.
- Processo de obter um ciclo num PowerShell livro execuções poderão falhar após cerca 80 iterações. 
- Um livro de execuções do PowerShell poderá falhar se tentativas de escrever uma grande quantidade de dados para a sequência de saída ao mesmo tempo.   Normalmente, pode contornar este problema ao exportar apenas as informações que necessárias ao trabalhar com objetos de grandes dimensões.  Por exemplo, em vez de exportar algo parecido com *Get-Process*, pode exportar apenas os campos obrigatórios com *Get-Process | Selecione nomeprocesso, CPU*.

## <a name="powershell-workflow-runbooks"></a>Fluxo de trabalho do PowerShell runbooks

Fluxo de trabalho do PowerShell runbooks são runbooks de texto com base no [Fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md).  Editar o código do livro de execuções utilizando o editor de texto no portal do Azure diretamente.  Também pode utilizar qualquer editor de texto offline e, em seguida, [Importar livro de execuções](http://msdn.microsoft.com/library/azure/dn643637.aspx) para automatização Azure.

### <a name="advantages"></a>Vantagens

- Implemente a todos os lógica complexa com o código de fluxo de trabalho do PowerShell.
- Utilize [os pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o livro de execuções em caso de erro.
- Utilize o [Processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias acções em paralelo.
- Pode incluir outros runbooks gráficas e fluxo de trabalho do PowerShell runbooks como subordinado runbooks para criar fluxos de trabalho nível alto.


### <a name="limitations"></a>Limitações

- Autor tem de estar familiarizado com PowerShell fluxo de trabalho.
- Livro execuções tem lidar com a complexidade adicional de fluxo de trabalho do PowerShell como [serialização objetos](automation-powershell-workflow.md#code-changes).
- Livro execuções demora mais tempo a iniciar PowerShell runbooks uma vez que precisa de ser compilada antes de executar.
- PowerShell runbooks apenas podem ser incluídos como subordinado runbooks utilizando o cmdlet iniciar AzureAutomationRunbook que cria uma nova tarefa.


## <a name="considerations"></a>Considerações sobre

Deverá tomar em consideração as seguintes considerações adicionais quando para determinar que tipo deve para utilizar para um determinado livro de execuções.

- Não pode converter runbooks gráficos do tipo textual ou vice versa.
- Existem limitações utilizando runbooks de diferentes tipos como um livro de execuções subordinado.  Para mais informações, consulte [subordinado runbooks no Azure automatização](automation-child-runbooks.md) .

  
## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a criação de livro de execuções gráfico, consulte o artigo [gráficos cocriação na automatização do Azure](automation-graphical-authoring-intro.md)
- Para compreender as diferenças entre PowerShell e PowerShell fluxos de trabalho para runbooks, consulte o artigo [Formação fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md)
- Para obter mais informações sobre como criar ou importar um livro de execuções, consulte o artigo [criar ou importar um livro de execuções](automation-creating-importing-runbook.md)



