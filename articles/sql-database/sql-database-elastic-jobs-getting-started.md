<properties
    pageTitle="Começar a trabalhar com tarefas de base de dados flexível"
    description="como utilizar tarefas de base de dados flexível"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Começar a trabalhar com tarefas de base de dados flexível

Flexível trabalhos de base de dados (pré-visualização) para a base de dados do Azure SQL permite-lhe fiabilidade executa scripts de T-SQL que aparecem em várias bases de dados enquanto automaticamente a repetir e fornecer garantias eventual conclusão. Para mais informações sobre a funcionalidade de tarefa flexível da base de dados, consulte o artigo a [página de descrição geral da funcionalidade](sql-database-elastic-jobs-overview.md).

Este tópico expande o exemplo que se encontram na [introdução com ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md). Quando concluído, irá: Saiba como criar e gerir tarefas de gerir um grupo de bases de dados relacionados. Não é necessário utilizar as ferramentas de escala flexível forma a tirar partido dos benefícios dos trabalhos flexível.

## <a name="prerequisites"></a>Pré-requisitos

Transferir e executar [introdução com amostra de ferramentas da base de dados flexível](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar um shard Gestor de mapa com a aplicação de exemplo

Aqui irá criar um mapa de shard Gestor juntamente com várias shards, seguido de inserção de dados para os shards. Se já tiver shards configurado com dados sharded, pode ignorar os seguintes passos e deslocar-se para a secção seguinte.

1. Criar e executar a aplicação de exemplo **introdução com ferramentas de base de dados flexível** . Siga os passos até ao passo 7 na secção de [Transferir e executa a aplicação de exemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). No final do passo 7, irá ver a linha de comandos seguinte:

    ![linha de comandos][1]

2.  Na janela de comandos, escreva "1" e prima **Enter**. Cria a shard Gestor de mapa e adiciona duas shards ao servidor. Em seguida, escreva "3" e prima **Enter**; Repita esta ação quatro vezes. Este procedimento insere linhas de dados de exemplo no seu shards.

3.  O [Portal do Azure](https://portal.azure.com) deve ser apresentado três novas bases de dados no seu servidor v12:

    ![Confirmação do Visual Studio][2]

    Neste momento, podemos irá criar uma coleção de base de dados personalizados que reflita a todas as bases de dados no mapa do shard. Isto irá permitir-nos criar e executar uma tarefa que adicionar uma nova tabela ao longo de shards.

Aqui faria normalmente criamos um alvo de mapa shard, utilizando o cmdlet **AzureSqlJobTarget novo** . A base de dados do Gestor de mapa shard tem de estar definida como um alvo de base de dados e, em seguida, o mapa shard específico for especificado como um alvo. Em vez disso, vamos enumerar todas as bases de dados no servidor e adicionar as bases de dados para a nova coleção de personalizado, à exceção dos base de dados principal.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Cria uma coleção de personalizado e adiciona todas as bases de dados no servidor para o destino de coleções de sites personalizada, à exceção dos modelo global.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script T-SQL para execução entre as bases de dados

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Criar a tarefa para executar um script ao longo do grupo personalizado das bases de dados

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Executar o trabalho 

O seguinte script do PowerShell pode ser utilizado para executar uma tarefa existente:

Atualize a seguinte variável para refletir o nome da tarefa pretendido para ter executado:

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Obter o estado de execução de uma única tarefa

Utilize o cmdlet **Get-AzureSqlJobExecution** mesmo com o parâmetro **IncludeChildren** para ver o estado de execuções do projecto subordinado, nomeadamente o estado de específico para cada execução tarefa cada orientadas pelo trabalho na base de dados.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>Ver o estado de entre vários execuções do projecto

O cmdlet **Get-AzureSqlJobExecution** tem vários parâmetros opcionais que podem ser utilizados para apresentar várias execuções de tarefa, filtradas pelos parâmetros fornecidos. A seguinte demonstra a algumas das formas possíveis para utilizar AzureSqlJobExecution obter:

Obter todas as execuções de tarefa de nível superior activa:

    Get-AzureSqlJobExecution

Obter todas as execuções do projecto de nível superior, incluindo execuções tarefa inativa:

    Get-AzureSqlJobExecution -IncludeInactive

Obter todas as execuções do projecto subordinado de um ID de execução de tarefa fornecido, incluindo execuções tarefa inativa:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Obter todas as execuções do projecto criadas com uma agenda / combinação, incluindo tarefas Inativas da tarefa:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Obter todas as tarefas de filtragem de um mapa de shard especificado, incluindo Inativas tarefas:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Obter todas as tarefas de filtragem de uma coleção de personalizado especificada, incluindo Inativas tarefas:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Obter a lista de execuções de tarefas do projecto dentro de execução de uma tarefa específica:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Obter detalhes sobre o trabalho tarefa execução:

O seguinte script do PowerShell pode ser utilizado para ver os detalhes de execução de tarefa uma tarefa, que é particularmente útil quando depurar falhas de execução.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Obter falhas dentro de execuções de tarefas do projecto

O objeto JobTaskExecution inclui uma propriedade para o ciclo de vida da tarefa, juntamente com uma propriedade de mensagem. Se uma tarefa tarefa a execução falhou, a propriedade do ciclo de vida irá estar definida como *falhou* e a propriedade de mensagem irá estar definida para a mensagem de exceção resultante e respectiva pilha. Se uma tarefa não teve êxito, é importante ver os detalhes de tarefas do projecto que não teve êxito para uma determinada tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>Aguardar a execução de um projecto concluir

O seguinte script do PowerShell pode ser utilizado para aguardar uma tarefa de projecto concluir:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizado

Flexível trabalhos de base de dados suporta a criação de políticas de execução personalizados que podem ser aplicadas ao iniciar tarefas.
  
Políticas de execução atualmente permitem que define:

* Nome: Identificador para a política de execução.
* Tempo limite do trabalho: Tempo Total antes de uma tarefa será cancelada pelas flexível tarefas de base de dados.
* Intervalo de repetição inicial: Intervalo a aguardar antes de repetir primeiro.
* Intervalo de repetição máximo: Remate dos intervalos de repetir a utilizar.
* Repetir intervalo duplicar coeficiente: Coeficiente utilizado para calcular o intervalo seguinte entre tentativas.  É utilizada a seguinte fórmula: (intervalo inicial de repetir) * Math.pow ((intervalo duplicar coeficiente), (número de tentativas) - 2). 
* Máximas tentativas: O número máximo de repetir tenta executar dentro de uma tarefa.

A política predefinida para execução utiliza os seguintes valores:

* Nome: Política de execução predefinido
* Tempo limite do trabalho: 1 semana
* Intervalo de repetição inicial: 100 milissegundos
* Intervalo de repetição máximo: 30 minutos
* Volte a tentar coeficiente de intervalo: 2
* Máximas tentativas: 2.147.483.647

Crie a política de execução pretendido:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Actualizar uma política de execução personalizado

Atualize a política de execução pretendido para atualizar:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Cancelar uma tarefa

Tarefas de base de dados flexível suporta pedidos de cancelamento de tarefas.  Se flexível trabalhos de base de dados Deteta um pedido de cancelamento para uma tarefa atualmente a ser executada, tentará para parar a tarefa.

Existem duas formas diferentes que flexível trabalhos de base de dados pode executar um aviso de cancelamento:

1. Cancelar atualmente em execução tarefas: se é detetado um aviso de cancelamento enquanto uma tarefa estiver atualmente em execução, um aviso de cancelamento será tentado dentro o aspecto atualmente execução da tarefa.  Por exemplo: se existir uma consulta longa atualmente a ser executada quando um aviso de cancelamento é tentado, haverá uma tentativa para cancelar a consulta.
2. Cancelar tarefa tentativas: Se um aviso de cancelamento é detetado pelo controlo threads antes de uma tarefa é iniciada para execução, controlo threads irão evitar iniciar a tarefa e declarar o pedido como cancelada.

Se for solicitado um aviso de cancelamento de tarefa para um projecto principal, o pedido de cancelamento será respeitado para a tarefa de principal e para todas as suas tarefas subordinadas de.
 
Para submeter um pedido de cancelamento, utilize o cmdlet **AzureSqlJobExecution parar** e defina o parâmetro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Eliminar uma tarefa por nome e histórico da tarefa

Tarefas de base de dados flexível suporta a eliminação assíncrona das tarefas. Uma tarefa pode ser marcada para eliminação e o sistema irá eliminar a tarefa e todos os seu histórico da tarefa depois de tem concluído todas as execuções do projecto para a tarefa. O sistema não será automaticamente Cancelar execuções do projecto ativo.  

Em vez disso, parar AzureSqlJobExecution deve ser chamado para cancelar execuções do projecto ativo.

Acionar eliminação de tarefa, utilize o cmdlet **Remover AzureSqlJob** e defina o parâmetro **NomeTarefa** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Criar um alvo de base de dados personalizados
Destinos de base de dados personalizada podem ser definidos na trabalhos de base de dados flexível que podem ser utilizados para execução diretamente ou para inclusão dentro de um grupo de base de dados personalizados. Uma vez que **agrupamentos de base de dados flexível** ainda não são diretamente suportadas através da API do PowerShell, basta criar um alvo de base de dados personalizada e de destino de coleção de base de dados personalizados que engloba todas as bases de dados no conjunto de.

Defina as seguintes variáveis para refletir as informações de base de dados pretendido:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Criar um alvo de coleção de base de dados personalizados
Um alvo de coleção de base de dados personalizados pode ser definido para permitir a execução de vários destinos de base de dados definido. Quando um grupo de base de dados estiver criado, bases de dados podem ser associados ao destino colecção personalizada.

Defina as seguintes variáveis para refletir a configuração de destino pretendido colecção personalizada:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Adicionar bases de dados para uma base de dados personalizados coleções de sites de destino

Destinos de base de dados podem ser associados destinos de coleção de base de dados personalizados para criar um grupo de bases de dados. Sempre que é criada uma tarefa que destina-se um alvo de coleção de base de dados personalizados, será expandida para as bases de dados associados ao grupo no momento da execução de destino.

Adicione a base de dados pretendido para uma coleção específica personalizada:

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Reveja as bases de dados dentro de um alvo de coleção de base de dados personalizados

Utilize o cmdlet **Get-AzureSqlJobTarget** para obter as bases de dados subordinado dentro de um alvo de coleção de base de dados personalizados. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar uma tarefa para executar um script através de um alvo de coleção de base de dados personalizados

Utilize o cmdlet **AzureSqlJob novo** para criar uma tarefa de um grupo de bases de dados definido por um alvo de coleção de base de dados personalizados. Os trabalhos de base de dados flexível irão expandir a tarefa para vários subordinado trabalhos cada correspondente a uma base de dados associado o destino da coleção de base de dados personalizados e certifique-se de que o script é executado em relação a cada base de dados. Novamente, é importante que os scripts são idempotent para ser e são para o número de tentativas.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Recolha de dados através de bases de dados

**Tarefas de base de dados flexível** suporta a executar uma consulta ao longo de um grupo de bases de dados e envia os resultados a tabela de uma base de dados especificada. A tabela que pode ser consultada após o facto de para ver os resultados da consulta de cada base de dados. Este procedimento fornece um mecanismos assíncrono para executar uma consulta em muitas bases de dados. Caso de falha como um das bases de dados a ser temporariamente indisponível é processado automaticamente através do número de tentativas.

A tabela de destino especificado será criada automaticamente se-ainda não existir, correspondência o esquema do conjunto de resultados devolvidos. Se a execução de um script devolve vários conjuntos de resultados, trabalhos de base de dados flexível enviará apenas a primeira parte à tabela de destino fornecido.

O seguinte script do PowerShell pode ser utilizado para executar um script recolher os seus resultados numa tabela especificada. Este script assume que foi criado um script T-SQL que exporta um conjunto único resultado e um alvo de coleção de base de dados personalizados foi criado.

Defina o seguinte procedimento para refletir o script pretendido, credenciais e destino de execução:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Criar e iniciar uma tarefa para cenários de recolha de dados
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Criar uma agenda de execução de tarefas utilizando um acionador de tarefa

O seguinte script do PowerShell pode ser utilizado para criar uma agenda periódica. Este script utiliza um intervalo de um minuto, mas novo AzureSqlJobSchedule também suporta - DayInterval, - HourInterval, - MonthInterval e - WeekInterval parâmetros. Esquemas que executar apenas uma vez podem ser criados por prisma - vez.

Crie uma nova agenda:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Criar um accionador de tarefa para que uma tarefa executada numa agenda de tempo

Acionar uma tarefa pode ser definida para que uma tarefa executada de acordo com uma agenda de tempo. O seguinte script do PowerShell pode ser utilizado para criar um acionador de tarefa.

Defina as seguintes variáveis para corresponder à agenda e na tarefa pretendida:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Remover uma associação agendada para parar de tarefa a partir da execução dentro do prazo

Para interromper a execução de tarefa periódica através de um acionador de tarefa, o accionador tarefa pode ser removido. Remova um acionador de tarefa para parar de uma tarefa a partir da ser executada de acordo com uma agenda utilizando o cmdlet **AzureSqlJobTrigger remover** .

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importar os resultados da consulta de base de dados flexível para o Excel

 Pode importar os resultados a partir de uma consulta para um ficheiro do Excel.

1. Inicie o Excel 2013.
2.  Navegue até ao Friso **dados** .
3.  Clique em **De outras origens** e clique em **Do SQL Server**.

    ![Importar do Excel a partir de outras origens][5]
4.  No **Assistente de ligação de dados** , escreva as credenciais de utilizador início de sessão e nome de servidor. Em seguida, clique em **seguinte**.
5.  Na caixa de diálogo **Selecione a base de dados que contém os dados que pretende**, selecione a base de dados **ElasticDBQuery** .
6.  Selecione a tabela **compradores** na vista de lista e clique em **seguinte**. Em seguida, clique em **Concluir**.
7.  No formulário de **Importar dados** , em **Selecione como pretende ver estes dados no seu livro**, selecione a **tabela** e clique em **OK**.

Todas as linhas de tabela **clientes** , armazenada na shards diferentes povoar folha do Excel.

## <a name="next-steps"></a>Próximos passos
Agora pode utilizar funções de dados do Excel. Utilize a cadeia de ligação com o seu nome de servidor, nome da base de dados e as credenciais para ligar as ferramentas de integração de BI e dados para a base de dados de consulta flexível. Certifique-se de que é suportado do SQL Server como uma origem de dados de uma ferramenta. Referir-se a base de dados de consulta flexível e tabelas externas tal como faria com qualquer outra base de dados do SQL Server e tabelas do SQL Server que teria se ligam com a ferramenta.

### <a name="cost"></a>Custo
Não existe nenhuma gratuitamente para utilizar a funcionalidade de consulta de base de dados flexível. No entanto, neste momento esta funcionalidade só está disponível em bases de dados de premium como um ponto final, mas os shards podem ser de qualquer camada de serviços.

Para obter informações sobre os preços consulte [Detalhes de preços de base de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
