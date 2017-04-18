<properties 
    pageTitle="Criar e gerir tarefas de base de dados flexível através do PowerShell" 
    description="PowerShell utilizado para gerir conjuntos de dados da base de dados do Azure SQL" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Criar e gerir tarefas de base de dados flexível uma base de dados SQL através do PowerShell (pré-visualização)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



API do PowerShell para **trabalhos de base de dados flexível** (na pré-visualização), permitem-lhe definir um grupo de contra a qual vai executar scripts de bases de dados. Este artigo mostra como criar e gerir **tarefas de base de dados flexível** utilizando os cmdlets do PowerShell. Consulte o artigo [Descrição geral de tarefas flexível](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Para uma avaliação gratuita, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).
* Um conjunto de bases de dados criadas com as ferramentas de base de dados flexível. Consulte o artigo [Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Para obter informações detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
* **Tarefas de base de dados flexível** Pacote do PowerShell: consulte o artigo [tarefas instalar flexível da base de dados](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Selecione a sua subscrição do Azure

Para selecionar a subscrição tem o Id (**-SubscriptionId**) da subscrição ou a subscrição nome (**-SubscriptionName**). Se tiver múltiplas subscrições que pode executar o cmdlet **Get-AzureRmSubscription** e copie as informações de subscrição pretendido a partir do conjunto de resultados. Assim que tiver as informações de subscrição, execute o seguinte commandlet para predefinir esta subscrição, nomeadamente de destino para criar e gerir tarefas:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

O [Ise do PowerShell](https://technet.microsoft.com/library/dd315244.aspx) é recomendado para utilização desenvolver e executar scripts de PowerShell contra as tarefas de base de dados flexível.

## <a name="elastic-database-jobs-objects"></a>Objetos de tarefas da base de dados flexível

A tabela seguinte lista saída todos os objetos tipos de **tarefas da base de dados flexível** juntamente com a sua descrição e relevantes PowerShell APIs.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descrição</th>
    <th>APIs do PowerShell relacionados</th>
  </tr>
  <tr>
    <td>Credenciais</td>
    <td>Nome de utilizador e palavra-passe para utilizar quando se liga ao bases de dados para execução dos scripts ou da aplicação do DACPACs. <p>A palavra-passe é encriptada antes de enviar para e armazenar na base de dados flexível trabalhos de base de dados.  A palavra-passe é desencriptar pelo serviço flexível trabalhos de base de dados através de credenciais criado e carregados a partir do script de instalação.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Novo AzureSqlJobCredential</p><p>Definir AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script de Transact-SQL para ser utilizado para execução bases de dados.  O script deve ser criado para ser idempotent uma vez que o serviço vai repetir a execução do script relativamente a falhas.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Novo AzureSqlJobContent</p>
    <p>Definir AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplicação dados camadas </a> pacote para ser aplicada em bases de dados.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Novo AzureSqlJobContent</p>
    <p>Definir AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Destino de base de dados</td>
    <td>Base de dados e nome de servidor apontar para uma base de dados do SQL Azure.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Destino de mapa shard</td>
    <td>Combinação de um alvo de base de dados e uma credencial a ser utilizada para determinar informações armazenadas dentro de um mapa de shard flexível da base de dados.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    <p>Definir AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de coleções de sites personalizada</td>
    <td>Grupo definido das bases de dados para utilizar constituem de execução.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de subordinados da colecção personalizada</td>
    <td>Destino de base de dados que é referenciado a partir de uma colecção personalizada.</td>
    <td>
    <p>AzureSqlJobChildTarget adicionar</p>
    <p>Remover AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Tarefa</td>
    <td>
    <p>Definição de parâmetros de uma tarefa que podem ser utilizados para acionar execução ou para satisfazer uma agenda.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Novo AzureSqlJob</p>
    <p>Definir AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Execução de tarefas</td>
    <td>
    <p>Contentor de tarefas necessárias para satisfazer em executar um script ou aplicar uma DACPAC a um destino utilizando as credenciais para ligações de base de dados com falhas processados de acordo com uma política de execução.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Iniciar AzureSqlJobExecution</p>
    <p>Parar AzureSqlJobExecution</p>
    <p>Aguarde AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Execução de tarefas do projecto</td>
    <td>
    <p>Única unidade de trabalho para satisfazer uma tarefa.</p>
    <p>Se uma tarefa de projecto com êxito não é possível executar, a mensagem de exceção resultante será registada e uma nova tarefa tarefa correspondente será criada e executada em conformidade com a política de execução especificado.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Iniciar AzureSqlJobExecution</p>
    <p>Parar AzureSqlJobExecution</p>
    <p>Aguarde AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Política de execução de tarefa</td>
    <td>
    <p>Controlos de cargo tempos limite de execução, limites de repetição e os intervalos entre tentativas.</p>
    <p>Tarefas de base de dados flexível inclui uma política de execução de tarefa predefinida que causar essencialmente infinitas repetições de tarefa de falhas de trabalho com duplicar exponencial de intervalos de entre cada repetir.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Novo AzureSqlJobExecutionPolicy</p>
    <p>Definir AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Agenda</td>
    <td>
    <p>Tempo com base especificação para a execução para realizar-se num intervalo de periódica ou uma única vez.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Novo AzureSqlJobSchedule</p>
    <p>Definir AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Tarefa accionadores</td>
    <td>
    <p>Um mapeamento entre uma tarefa e uma agenda de execução de tarefas acionador de acordo com a agenda.</p>
    </td>
    <td>
    <p>Novo AzureSqlJobTrigger</p>
    <p>Remover AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Tarefas de base de dados flexível suportadas tipos de grupo
A tarefa executa scripts Transact-SQL (T-SQL) ou da aplicação do DACPACs ao longo de um grupo de bases de dados. Quando uma tarefa é apresentada ao ser executada ao longo de um grupo de bases de dados, a tarefa "expande" a para tarefas subordinadas onde cada executa a execução do pedida uma única na base de dados no grupo. 
 
Existem dois tipos de grupos que pode criar: 

* Grupo de [Mapa de shard](sql-database-elastic-scale-shard-map-management.md) : quando uma tarefa é submetida para um mapa de shard de destino, a tarefa de consultas mapa shard para determinar o seu conjunto atual de shards e, em seguida, criar subordinados trabalhos para cada shard no mapa do shard.
* Grupo de coleções de sites personalizado: um personalizado definido conjunto de bases de dados. Quando uma tarefa destina-se uma colecção personalizada, cria subordinado trabalhos para cada base de dados atualmente na coleção de personalizado.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Para definir a base de dados flexível as tarefas de ligação

Uma ligação tem de ser definidas para as tarefas de *base de dados de controlo* antes de utilizar as tarefas APIs. A executar este cmdlet accionadores uma janela de credenciais para pop-up a solicitar o nome de utilizador e palavra-passe criada ao instalar o trabalhos de base de dados flexível. Todos os exemplos fornecidos dentro neste tópico partem do pressuposto de que já tiver sido executado neste primeiro passo.

Abra uma ligação para as tarefas de base de dados flexível:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenciais encriptadas dentro das tarefas da base de dados flexível

Credenciais base de dados podem ser inseridas para a *base de dados de controlo* de tarefas com a sua palavra-passe encriptada. É necessário armazenar credenciais para ativar a ser executada tarde, tarefas (utilizando as agendas de tarefa).
 
Encriptação funciona através de um certificado criado como parte do script de instalação. O script de instalação cria e carrega o certificado para o serviço de nuvem Azure pela desencriptar de palavras-passe encriptadas armazenadas. O serviço de nuvem Azure armazena mais tarde a chave pública dentro de tarefas *base de dados de controlo* que permite a interface de API do PowerShell ou Azure clássica Portal encriptar uma palavra-passe fornecida sem que requerem o certificado de estar instalado localmente.
 
As palavras-passe de credenciais são encriptados e seguro a partir de utilizadores com acesso só de leitura para objectos de tarefas da base de dados flexível. Mas é possível para um utilizador malicioso com acesso de leitura / escrita a objetos flexível trabalhos de base de dados extrair uma palavra-passe. Credenciais foram concebidas para ser reutilizado através de execuções do projecto. Credenciais são transmitidas a bases de dados de destino quando estabelecer ligações. Não existem atualmente restrições a bases de dados de destino utilizadas para cada credencial, o utilizador malicioso adicionaria um alvo de base de dados para uma base de dados em controlo do utilizador malicioso. O utilizador subsequentemente foi possível iniciar uma tarefa de filtragem desta base de dados para obter a palavra-passe da credencial.

Melhores práticas de segurança para trabalhos de base de dados flexível incluem:

* Limitar a utilização das APIs a indivíduos fidedignas.
* Credenciais devem ter o mínimo de privilégios necessário para executar a tarefa de trabalho.  Obter mais informações podem ser vistas dentro neste artigo do MSDN do SQL Server [autorização e permissões](https://msdn.microsoft.com/library/bb669084.aspx) .

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Para criar uma credencial encriptada para execução de tarefas ao longo de bases de dados

Para criar uma nova credencial encriptada, o [**cmdlet Get-credencial**](https://technet.microsoft.com/library/hh849815.aspx) solicita-lhe um nome de utilizador e palavra-passe que pode ser passado para o [**Novo AzureSqlJobCredential cmdlet**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Para atualizar as credenciais

Quando alterar palavras-passe, utilize o [**cmdlet Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) e defina o parâmetro **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Para definir um alvo de mapa de shard flexível da base de dados

Para executar uma tarefa de todas as bases de dados num conjunto de shard (criada utilizando [a biblioteca do cliente da base de dados flexível](sql-database-elastic-database-client-library.md)), utilize um mapa de shard como o destino da base de dados. Este exemplo requer uma aplicação sharded criada com a biblioteca do cliente flexível da base de dados. Consulte o artigo [introdução com amostra de ferramentas da base de dados flexível](sql-database-elastic-scale-get-started.md).

A base de dados do Gestor de mapa shard tem de estar definida como um alvo de base de dados e, em seguida, o mapa shard específico tem de ser especificado como um destino.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script T-SQL para execução entre as bases de dados

Quando criar scripts de T-SQL para execução, é vivamente recomendado para construir que sejam [idempotent](https://en.wikipedia.org/wiki/Idempotence) e flexível contra falhas. Tarefas de base de dados flexível vai repetir execução de um script sempre execução ocorrerá uma falha de, independentemente da classificação da falha.

Utilize o [**cmdlet AzureSqlJobContent novo**](https://msdn.microsoft.com/library/mt346085.aspx) para criar e guardar um script para execução e configurar os parâmetros **- ContentName** e **- CommandText** .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Criar um novo script a partir de um ficheiro
Se o script T-SQL estiver definido dentro de um ficheiro, utilize esta opção para importar o script:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Para atualizar um script T-SQL para execução em bases de dados  

Este script do PowerShell atualiza o texto do comando T-SQL para um script existente.

Defina as seguintes variáveis para refletir a definição de script pretendido para ser definir:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Para atualizar a definição para um script existente

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Para criar uma tarefa para executar um script através de um mapa de shard

Este script do PowerShell inicia uma tarefa para a execução de um script através de cada shard num mapa shard escala flexível.

Defina as seguintes variáveis para refletir o script pretendida e destino:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Executar a uma tarefa 

Este script do PowerShell executa uma tarefa existente:

Atualize a seguinte variável para refletir o nome da tarefa pretendido para ter executado:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Para obter o estado de execução de um único trabalho

Utilize o [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) e defina o parâmetro de **JobExecutionId** para ver o estado de execução de tarefas.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utilize o cmdlet **Get-AzureSqlJobExecution** mesmo com o parâmetro **IncludeChildren** para ver o estado de execuções do projecto subordinado, nomeadamente o estado de específico para cada execução tarefa cada orientadas pelo trabalho na base de dados.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Para ver o estado de entre vários execuções do projecto

O [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) tem vários parâmetros opcionais que podem ser utilizados para apresentar várias execuções de tarefa, filtradas pelos parâmetros fornecidos. A seguinte demonstra a algumas das formas possíveis para utilizar AzureSqlJobExecution obter:

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Para obter falhas dentro de execuções de tarefas do projecto

O **objeto JobTaskExecution** inclui uma propriedade de ciclo de vida da tarefa, juntamente com uma propriedade de mensagem. Se uma tarefa tarefa a execução falhou, a propriedade do ciclo de vida irá estar definida como *falhou* e a propriedade de mensagem irá estar definida para a mensagem de exceção resultante e respectiva pilha. Se uma tarefa não teve êxito, é importante ver os detalhes de tarefas do projecto que não teve êxito para uma determinada tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>De aguardar a execução de um projecto concluir

O seguinte script do PowerShell pode ser utilizado para aguardar uma tarefa de projecto concluir:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizado

Flexível trabalhos de base de dados suporta a criação de políticas de execução personalizado que podem ser aplicadas ao iniciar tarefas.
  
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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Cancelar tarefas atualmente em execução: se é detetado um aviso de cancelamento enquanto uma tarefa estiver atualmente em execução, um aviso de cancelamento será tentado dentro o aspecto atualmente execução da tarefa.  Por exemplo: se existir uma consulta longa atualmente a ser executada quando um aviso de cancelamento é tentado, haverá uma tentativa para cancelar a consulta.
2. Cancelar o número de tentativas de tarefa: se um aviso de cancelamento é detetado pelo controlo threads antes de uma tarefa é iniciada para execução, o controlo threads serão evitar iniciar a tarefa e declarar o pedido como cancelada.

Se for solicitado um aviso de cancelamento de tarefa para um projecto principal, o pedido de cancelamento será respeitado para a tarefa de principal e para todas as suas tarefas subordinadas de.
 
Para submeter um pedido de cancelamento, utilize o [**cmdlet AzureSqlJobExecution parar**](https://msdn.microsoft.com/library/mt346053.aspx) e defina o parâmetro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Para eliminar uma tarefa e histórico da tarefa forma assíncrona

Tarefas de base de dados flexível suporta a eliminação assíncrona das tarefas. Uma tarefa pode ser marcada para eliminação e o sistema irá eliminar a tarefa e todos os seu histórico da tarefa depois de tem concluído todas as execuções do projecto para a tarefa. O sistema não será automaticamente Cancelar execuções do projecto ativo.  

Invocar [**Parar AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) para cancelar execuções do projecto ativo.

Acionar eliminação de tarefa, utilize o [**cmdlet remover AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) e defina o parâmetro **NomeTarefa** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>Para criar um alvo de base de dados personalizados

Pode definir destinos de base de dados personalizados para execução direta ou para inclusão dentro de um grupo de base de dados personalizados. Por exemplo, uma vez que **agrupamentos de base de dados flexível** ainda não são diretamente suportadas utilizando o PowerShell APIs, pode criar um alvo de base de dados personalizada e de destino de coleção de base de dados personalizados que engloba todas as bases de dados no conjunto de.

Defina as seguintes variáveis para refletir as informações de base de dados pretendido:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Para criar um alvo de coleção de base de dados personalizados

Utilize o cmdlet [**AzureSqlJobTarget novo**](https://msdn.microsoft.com/library/mt346077.aspx) para definir um alvo de coleção de base de dados personalizados para permitir a execução vários destinos de base de dados definido. Depois de criar um grupo de base de dados, bases de dados podem ser associados com o destino de coleções de sites personalizada.

Defina as seguintes variáveis para refletir a configuração de destino pretendido colecção personalizada:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Para adicionar bases de dados para uma base de dados personalizados coleções de sites de destino

Para adicionar uma base de dados para uma coleção específica personalizada, utilize o cmdlet [**AzureSqlJobChildTarget adicionar**](https://msdn.microsoft.comlibrary/mt346064.aspx) .

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Reveja as bases de dados dentro de um alvo de coleção de base de dados personalizados

Utilize o cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para obter as bases de dados subordinado dentro de um alvo de coleção de base de dados personalizados. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar uma tarefa para executar um script através de um alvo de coleção de base de dados personalizados

Utilize o cmdlet [**AzureSqlJob novo**](https://msdn.microsoft.com/library/mt346078.aspx) para criar uma tarefa de um grupo de bases de dados definido por um alvo de coleção de base de dados personalizados. Os trabalhos de base de dados flexível irão expandir a tarefa para vários subordinado trabalhos cada correspondente a uma base de dados associado o destino da coleção de base de dados personalizados e certifique-se de que o script é executado em relação a cada base de dados. Novamente, é importante que os scripts são idempotent para ser e são para o número de tentativas.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Recolha de dados através de bases de dados

Pode utilizar uma tarefa para executar uma consulta ao longo de um grupo de bases de dados e enviar os resultados para uma tabela específica. A tabela que pode ser consultada após o facto de para ver os resultados da consulta de cada base de dados. Este procedimento fornece um método assíncrono para executar uma consulta em muitas bases de dados. Tentativas falhadas são processadas automaticamente através do número de tentativas.

A tabela de destino especificado será criada automaticamente se-ainda não existir. A nova tabela corresponde o esquema do conjunto de resultados devolvidos. Se um script devolve vários conjuntos de resultados, trabalhos de base de dados flexível enviar apenas o primeiro à tabela de destino.

O seguinte script do PowerShell executa um script e recolhe seus resultados numa tabela especificada. Este script assume que foi criado um script T-SQL que exporta um conjunto único resultado e que foi criado um alvo de coleção de base de dados personalizados.

Este script utiliza o cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Defina os parâmetros de script, credenciais e destino de execução:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Para criar e iniciar uma tarefa para cenários de recolha de dados

Este script utiliza o cmdlet [**AzureSqlJobExecution iniciar**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Agendar um acionador de execução de tarefa

O seguinte script do PowerShell pode ser utilizado para criar uma agenda periódica. Este script utiliza um intervalo de minuto, mas [**Novo AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) também suporta - DayInterval, - HourInterval, - MonthInterval e - WeekInterval parâmetros. Esquemas que executar apenas uma vez podem ser criados por prisma - vez.

Crie uma nova agenda:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Para acionar uma tarefa executada numa agenda de tempo

Acionar uma tarefa pode ser definida para que uma tarefa executada de acordo com uma agenda de tempo. O seguinte script do PowerShell pode ser utilizado para criar um acionador de tarefa.

Utilize o [Novo AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) e definir as seguintes variáveis para corresponder à agenda e na tarefa pretendida:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Para remover uma associação agendada para parar de tarefa a partir da execução dentro do prazo

Para interromper a execução de tarefa periódica através de um acionador de tarefa, o accionador tarefa pode ser removido. Remova um acionador de tarefa para parar de uma tarefa a partir da ser executada de acordo com uma agenda utilizando o [**cmdlet AzureSqlJobTrigger remover**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Obter accionadores tarefa dependente de uma agenda de tempo

O seguinte script do PowerShell pode ser utilizado para obter e apresentar os accionadores tarefa registados para uma agenda de tempo específico.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Para obter Accionadores da tarefa associada a uma tarefa 

Utilize [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) para obter e apresentar agendas que contém uma tarefa registada.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para criar uma aplicação de camada de dados (DACPAC) para execução em bases de dados

Para criar um DACPAC, consulte o artigo [as aplicações camadas de dados](https://msdn.microsoft.com/library/ee210546.aspx). Para implementar um DACPAC, utilize o [cmdlet AzureSqlJobContent novo](https://msdn.microsoft.com/library/mt346085.aspx). O DACPAC deve ser acessível para o serviço. Recomenda-se para carregar um DACPAC criado para o armazenamento do Windows Azure e criar uma [Assinatura de acesso partilhados](../storage/storage-dotnet-shared-access-signature-part-1.md) para o DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para atualizar uma aplicação de camada de dados (DACPAC) para execução em bases de dados

DACPACs existentes registados dentro flexível de base de dados de tarefas podem ser atualizados para apontarem para o novo URIs. Utilize o [**cmdlet Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) para atualizar o URI DACPAC numa DACPAC registado existente:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Para criar uma tarefa para aplicar uma aplicação de camada de dados (DACPAC) através de bases de dados

Depois de ter sido criada uma DACPAC dentro flexível trabalhos de base de dados, pode ser criada uma tarefa para aplicar a DACPAC ao longo de um grupo de bases de dados. O seguinte script do PowerShell pode ser utilizado para criar uma tarefa DACPAC através de um conjunto personalizado de bases de dados:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
