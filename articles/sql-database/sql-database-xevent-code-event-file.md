<properties 
    pageTitle="Código de XEvent evento ficheiro de base de dados SQL | Microsoft Azure" 
    description="Fornece PowerShell e Transact-SQL para uma amostra de código de duas fases que demonstrará o destino de ficheiro de eventos num evento adicional na base de dados do SQL Azure. Armazenamento Azure é uma parte obrigatória deste cenário." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino do ficheiro de evento para eventos adicional na base de dados SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Um exemplo de código completo que pretende para uma forma eficaz de informações de um evento adicional captura e relatório.


Microsoft SQL Server, o [ficheiro de eventos destino](http://msdn.microsoft.com/library/ff878115.aspx) é utilizada para armazenar os resultados de evento para um ficheiro de disco rígido local. Mas esses ficheiros não estão disponíveis para a base de dados do SQL Azure. Em vez disso, utilizamos o serviço de armazenamento do Windows Azure para suportar o destino de ficheiros do evento.


Este tópico apresenta uma amostra de código de duas fases:


- PowerShell, para criar um contentor de armazenamento do Windows Azure na nuvem.

- O Transact-SQL:
 - Para atribuir o contentor de armazenamento do Windows Azure a um alvo de ficheiro de eventos.
 - Para criar e iniciar a sessão de evento etc.


## <a name="prerequisites"></a>Pré-requisitos


- Uma conta Azure e subscrição. Pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Quaisquer bases de dados que pode criar uma tabela no.
 - Opcionalmente, pode [criar uma base de dados de demonstração sobre **AdventureWorksLT** ](sql-database-get-started.md) em minutos.


- SQL Server Management Studio (ssms.exe), idealmente a versão mais recente mensal atualização. Pode transferir o ssms.exe mais recente a partir de:
 - Tópico intitulado [Transferir o SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Uma ligação direta para a transferência.](http://go.microsoft.com/fwlink/?linkid=616025)


- Tem de ter o [Azure PowerShell módulos](http://go.microsoft.com/?linkid=9811175) instalado.
 - Os módulos fornecem comandos, como - **AzureStorageAccount de novo**.


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Código de PowerShell contentor de armazenamento do Azure


Este PowerShell é fase 1 do código de duas fases de exemplo.

Inicia o script com comandos de limpar o depois de executar um possível anterior e é rerunnable.



1. Cole o script do PowerShell no editor de texto simples como Notepad.exe e guarde o script como um ficheiro com a extensão **. ps1**.

2. Inicie o ISE do PowerShell como administrador.

3. Na linha de comandos, escreva<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e, em seguida, prima Enter.

4. No ISE do PowerShell, abra o ficheiro **. ps1** . Execute o script.

5. O script inicia pela primeira vez numa nova janela na qual pode iniciar sessão no Azure.
 - Se executar novamente o script sem interromper a sessão, tem a opção conveniente dos comentários executa o comando **Adicionar AzureAccount** .


![Ise do PowerShell, com o módulo Azure instalado, pronto para ser executado script.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Tome nota dos alguns valores com nome que o script do PowerShell imprime quando termina. Tem de editar esses valores para o script Transact-SQL que se segue como fase 2.


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Código de Transact-SQL que utiliza o contentor de armazenamento do Azure


- Na fase 1 deste exemplo de código, executou um script PowerShell para criar um contentor de armazenamento do Windows Azure.
- Seguinte na fase 2, o seguinte script Transact-SQL tem de utilizar o contentor.


Inicia o script com comandos de limpar o depois de executar um possível anterior e é rerunnable.


O script do PowerShell impressos alguns valores com nome-terminada. Tem de editar o script Transact-SQL para utilizar esses valores. Encontre **TODO** o script Transact-SQL para localizar os editar pontos.


1. Abra o SQL Server Management Studio (ssms.exe).

2. Ligar a sua base de dados da base de dados do Azure SQL.

3. Clique para abrir um novo painel de consulta.

4. Cole o seguinte script Transact-SQL no painel de consulta.

5. Localizar todas as **TODO** o script e efetue as edições adequadas.

6. Guardar e, em seguida, execute o script.


&nbsp;


> [AZURE.WARNING] O valor da chave SA gerado pelo script do PowerShell anterior poderá começar com um '? " (ponto de interrogação). Quando utiliza a chave de SA no script T-SQL a seguir, tem de *remover os espaços à esquerda '?'*. Caso contrário, os esforços de poderão estar bloqueados pelo segurança.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Se o destino falhar anexar ao executar, deve parar e reinicie a sessão de evento:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>Saída


Quando concluir o script Transact-SQL, clique numa célula abaixo do cabeçalho de coluna **event_data_XML** . Um **<event>** elemento é apresentado que mostra uma instrução UPDATE.

Eis um **<event>** elemento que foi criado durante o ensaio:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


O script Transact-SQL precedente utilizada a função seguinte do sistema para ler o event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


Obter uma explicação sobre as opções avançadas para a visualização de dados a partir de eventos adicional está disponível em:

- [Avançadas de visualização de dados de destino a partir de eventos expandidos](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Converter o código de exemplo para executar no SQL Server


Suponha que pretendia executar o exemplo de Transact-SQL anterior no Microsoft SQL Server.


- Para simplificar, que seria que pretende substituir completamente utilização do contentor de armazenamento do Windows Azure com um ficheiro simple como **C:\myeventdata.xel**. O ficheiro seria escrito na unidade de disco rígido local do computador que aloja do SQL Server.


- Não tem qualquer tipo de instruções Transact-SQL para **criar o modelo global de chave** e **Criar CREDENCIAIS**.


- A instrução de **Evento criar sessão** , na sua cláusula **Adicionar destino** , o utilizador seria substitui o valor de Http atribuído efetuadas **filename =** por uma cadeia de caminho completo como **C:\myfile.xel**.
 - Não precisa de estar envolvida nenhuma conta de armazenamento do Windows Azure.


## <a name="more-information"></a>Obter mais informações


Para obter mais informações sobre contas e contentores no serviço de armazenamento do Windows Azure, consulte:

- [Como utilizar o armazenamento de Blobs do .NET](../storage/storage-dotnet-how-to-use-blobs.md)
- [Atribuição de nomes e referenciar contentores, Blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Trabalhar com o contentor de raiz](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lição 1: Criar uma política de acesso armazenado e uma assinatura de acesso partilhado num contentor Azure](http://msdn.microsoft.com/library/dn466430.aspx)
    - [Lição 2: Criar uma credencial de SQL Server utilizando uma assinatura de acesso partilhado](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

