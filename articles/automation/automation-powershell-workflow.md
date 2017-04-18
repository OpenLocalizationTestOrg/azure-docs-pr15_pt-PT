<properties 
   pageTitle="Fluxo de trabalho de aprendizagem PowerShell"
   description="Este artigo destina-se como uma lição rápida para autores de familiarizado com o PowerShell para compreender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell específicas."
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

# <a name="learning-windows-powershell-workflow"></a>Fluxo de trabalho do aprendizagem Windows PowerShell

Runbooks no Azure automatização são implementados como fluxos de trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo utilizador.  Este artigo destina-se para os utilizadores já familiarizados com o PowerShell e explica brevemente conceitos que requerem se estiver a converter um script PowerShell para um fluxo de trabalho do PowerShell para utilização num livro de execuções.  

Um fluxo de trabalho é uma sequência de passos programados, ligadas que efetuar tarefas de execução longa ou exigem a coordenação dos vários passos através de vários dispositivos ou nós gerida. As vantagens de um fluxo de trabalho ao longo de um script normal incluem a capacidade em simultâneo, efetue uma ação contra vários dispositivos e a capacidade de recuperar automaticamente a partir de falhas. Um fluxo de trabalho do Windows PowerShell é um script do Windows PowerShell que tira partido Windows Workflow Foundation. Enquanto o fluxo de trabalho está escrito com sintaxe do Windows PowerShell e iniciado pelo Windows PowerShell, esta é processada ao Windows Workflow Foundation.

Para obter detalhes completos sobre tópicos neste artigo, consulte o artigo [Introdução ao fluxo de trabalho do Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Tipos de livro execuções

Existem três tipos de livro de execuções no Azure automatização, *Fluxo de trabalho do PowerShell*, *PowerShell* e *gráficos*.  Definir o tipo de livro execuções quando criar o livro de execuções e não pode converter um livro de execuções o outro tipo assim que estiver foi criada.

Runbooks de fluxo de trabalho do PowerShell e PowerShell runbooks estão para utilizadores que preferem trabalhar diretamente com o código do PowerShell ou a utilizar o editor de textual no Azure automatização ou um editor offline como ISE do PowerShell. Deve compreender as informações neste artigo, se estiver a criar um livro de execuções do PowerShell fluxo de trabalho. 

Runbooks gráficas permitem-lhe criar um livro de execuções utilizando as atividades e os cmdlets do mesmo, mas utilizando uma interface gráfica que oculta a complexidade do fluxo de trabalho PowerShell subjacente.  Conceitos neste artigo, tais como os pontos de verificação e execução paralela ainda se aplicam a runbooks gráficas, mas que não terá de se preocupar a sintaxe da detalhadas. 

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho

O primeiro passo para converter um script PowerShell para um fluxo de trabalho do PowerShell é colocá-lo com a palavra-chave de **fluxo de trabalho** .  Um fluxo de trabalho é iniciado com a palavra-chave de **fluxo de trabalho** seguida do corpo de script entre chavetas. O nome do fluxo de trabalho segue a palavra-chave de **fluxo de trabalho** , conforme apresentado na seguinte sintaxe. 

    Workflow Test-Workflow
    {
       <Commands>
    }

O nome do fluxo de trabalho tem de corresponder ao nome do livro de execuções de automatização. Se está a ser importado o livro de execuções, o nome deve corresponder ao nome do fluxo de trabalho e tem de terminar. ps1.

Para adicionar parâmetros ao fluxo de trabalho, utilize a palavra-chave **Param** , tal como faria para um script. 

## <a name="code-changes"></a>Alterações de código

Código de fluxo de trabalho do PowerShell parece quase idêntico ao código de script do PowerShell, à exceção algumas alterações significativas.  As seguintes secções descrevem as alterações que precisa para fazer um script do PowerShell para o mesmo seja executado num fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma actividade é uma tarefa específica num fluxo de trabalho. Tal como um script é composto por uma ou mais comandos, um fluxo de trabalho é composto por uma ou mais atividades que realizada numa sequência. Fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades quando executar um fluxo de trabalho. Quando especificar um destes cmdlets no seu livro de execuções, a atividade correspondente na realidade é executada por Windows Workflow Foundation. Para esses cmdlets sem uma atividade de correspondente, o fluxo de trabalho do Windows PowerShell é executado automaticamente o cmdlet dentro de uma actividade [InlineScript](#inlinescript) . Existe um conjunto de cmdlets que são excluídos e não podem ser utilizados num fluxo de trabalho, a menos que incluem explicitamente num bloco de InlineScript. Para obter mais detalhes sobre estes conceitos, consulte o artigo [Utilizar atividades em Script fluxos de trabalho](http://technet.microsoft.com/library/jj574194.aspx).

Atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns para configurar o seu funcionamento. Para obter detalhes sobre o fluxo de trabalho parâmetros comuns, consulte o artigo [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais

Não pode utilizar parâmetros posicionais com atividades e os cmdlets num fluxo de trabalho.  Todos os significa que esta está que tem de utilizar os nomes dos parâmetros.

Por exemplo, considere o seguinte código que obtém todos os serviços em execução.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Se tentar executar este código mesmo num fluxo de trabalho, obterá uma mensagem como "conjunto de parâmetro não pode ser resolvido utilizando parâmetros com nome especificado."  Para corrigir este problema, basta de fornecer o nome do parâmetro tal como o seguinte.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objetos

Objetos em fluxos de trabalho são serialização.  Isto significa que as respectivas propriedades ainda estão disponíveis, mas não os seus métodos.  Por exemplo, considere o seguinte código de PowerShell que deixa de um serviço utilizando o método de parar do objeto serviço.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Se tentar executar esta num fluxo de trabalho, obterá um erro dizer "invocação de método não é suportada num fluxo de trabalho do Windows PowerShell".  

Uma das opções é moldar estas duas linhas de código num bloco caso em que $Service [InlineScript](#InlineScript) seria um objeto de serviço dentro do bloco. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

Outra opção é utilizar o cmdlet outro que efetua a mesma funcionalidade que o método, se disponível.  No nosso exemplo, caso do cmdlet do serviço de parar fornece a mesma funcionalidade que o método de parar e que pode utilizar o seguinte procedimento para um fluxo de trabalho.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

A atividade de **InlineScript** é útil quando que precisa para executar uma ou mais comandos como script PowerShell tradicional em vez de fluxo de trabalho do PowerShell.  Enquanto comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, comandos num bloco de InlineScript são processados pelo Windows PowerShell. 

InlineScript utiliza da sintaxe mostrada abaixo.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Pode devolver resultados a partir de um InlineScript atribuindo a saída de uma variável. O exemplo seguinte deixa de um serviço e, em seguida, exporta o nome do serviço.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Pode passar valores para um bloco de InlineScript, mas tem de utilizar **$Using** modificadora de âmbito.  O exemplo seguinte é idêntico do exemplo anterior, exceto a que o nome do serviço é fornecido por uma variável. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Enquanto InlineScript atividades poderão ser críticas em determinados fluxos de trabalho, estes não suportam construções de fluxo de trabalho e só devem ser utilizados quando for necessário pelos seguintes motivos:

- Não é possível utilizar [os pontos de verificação](#Checkpoints) dentro de um bloco de InlineScript. Se ocorre uma falha de dentro do bloco, tem retomá-lo desde o início do bloco.
- Não é possível utilizar [execução paralela](#parallel-execution) dentro de uma InlineScriptBlock.
- InlineScript afeta escalabilidade do fluxo de trabalho, uma vez que contém a sessão do Windows PowerShell para todo o comprimento do bloco InlineScript.

Para obter mais detalhes sobre como utilizar InlineScript, consulte [a executar o Windows PowerShell comandos num fluxo de trabalho](http://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Processamento paralelo

Uma vantagem dos fluxos de trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente tal como acontece com um script típico. 

Pode utilizar a palavra-chave **paralelas** para criar um bloco de script com vários comandos que serão executada em simultâneo. Esta opção utiliza da sintaxe mostrada abaixo. Neste caso, Activity1 e Activity2 serão iniciado ao mesmo tempo. Activity3 só será iniciada após tem concluído Activity1 e Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por exemplo, considere os seguintes comandos do PowerShell copiar vários ficheiros para uma rede de destino.  Estes comandos são executados sequencialmente, para que um ficheiro tem de concluir a copiar antes do seguinte é iniciado.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

O fluxo de trabalho seguinte é executado estes comandos mesmo em paralelo para que todos os começam copiar ao mesmo tempo.  Apenas depois de serem todos os totalmente copiado é conclusão apresentada a mensagem.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Pode utilizar o **ForEach-paralelas** construção de comandos do processo para cada item numa coleção de em simultâneo. Os itens na coleção de são processados em paralelo enquanto os comandos do bloco de script a ser executada sequencialmente. Esta opção utiliza da sintaxe mostrada abaixo. Neste caso, Activity1 será iniciado ao mesmo tempo para todos os itens na coleção de. Para cada item, Activity2 será iniciado após Activity1 estar concluída. Activity3 só será iniciada após Activity1 e Activity2 tem concluído para todos os itens.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

O exemplo seguinte é semelhante ao exemplo anterior copiar os ficheiros em paralelo.  Neste caso, é apresentada uma mensagem para cada ficheiro depois de os copiar.  Apenas depois de serem todos os totalmente copiado é conclusão final apresentada a mensagem.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  Não recomendamos que execute subordinado runbooks em paralelo, uma vez que este foi demonstrado apresente resultados não fiáveis.  O resultado do livro de subordinados execuções, por vezes, não irá aparecer e definições no livro de execuções um subordinado podem afetar a outros runbooks subordinado paralelas 


## <a name="checkpoints"></a>Pontos de verificação

Um *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual de variáveis e qualquer saída gerada a esse ponto. Se um fluxo de trabalho termina erro ou se está suspensa, em seguida, da próxima vez que é executada será iniciado a partir do seu ponto de verificação último em vez do início da worfklow.  Pode definir um ponto de verificação num fluxo de trabalho com a atividade de **Fluxo de trabalho de ponto de verificação** .

No seguinte exemplo de código, uma exceção ocorre após Activity2 a causar o fluxo de trabalho para o fim. Quando o fluxo de trabalho é executado novamente, inicia executando Activity2 desde que este foi imediatamente após o último ponto de verificação Definir.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Deverá definir os pontos de verificação num fluxo de trabalho depois de atividades que poderão estar sujeito a exceção e não devem ser repetida se o fluxo de trabalho é retomado. Por exemplo, o seu fluxo de trabalho pode criar uma máquina virtual. Pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, em seguida, os comandos seriam ser repetidos se o fluxo de trabalho for iniciado novamente. Se o o worfklow falha após a criação ser bem sucedida, em seguida, a máquina virtual não será criada novamente quando o fluxo de trabalho é retomado.

O exemplo seguinte copia vários ficheiros para uma localização de rede e define um ponto de verificação após cada ficheiro.  Se a localização de rede é perdida, em seguida, o fluxo de trabalho terminará erro.  Quando for iniciado novamente, será retomada no último ponto de verificação que significa que apenas os ficheiros que tiverem sido copiados serão ignorados.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Uma vez que as credenciais de nome de utilizador não são permanentes após ligar para a atividade de [Fluxo de trabalho suspender](https://technet.microsoft.com/library/jj733586.aspx) ou depois do último ponto de verificação, tem de definir as credenciais para null e, em seguida, obtê-los novamente a partir da loja de elementos depois de **Fluxo de trabalho suspender** ou ponto de verificação chama-se.  Caso contrário, poderá receber a seguinte mensagem de erro: não é possível retomar *a tarefa de fluxo de trabalho, quer porque dados persistente não foi possível ser totalmente guardados ou guardados dados persistente foi danificados. Tem de reiniciar o fluxo de trabalho.*

O mesmo código seguinte demonstra como lidar isto no seu runbooks PowerShell fluxo de trabalho.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Isto não é necessário se são autenticar utilizando uma conta de executar como configurada com um serviço principal.  

Para mais informações sobre os pontos de verificação, consulte o artigo [Adicionar pontos de verificação para um fluxo de trabalho de Script](http://technet.microsoft.com/library/jj574114.aspx).


## <a name="next-steps"></a>Próximos passos

- Para começar a trabalhar com runbooks de fluxo de trabalho do PowerShell, consulte o artigo [meu livro de execuções de fluxo de trabalho de PowerShell primeiro](automation-first-runbook-textual.md) 
