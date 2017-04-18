<properties
   pageTitle="Cmdlets do PowerShell para armazém de dados do SQL Azure"
   description="Localize os cmdlets do PowerShell superiores para armazém de dados SQL Azure incluindo como interromper e retomar uma base de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Os cmdlets do PowerShell e os REST APIs para armazém de dados SQL

Muitas tarefas de administração do armazém de dados SQL podem ser geridas utilizando os cmdlets do Azure PowerShell ou REST APIs.  Abaixo estão alguns exemplos de como utilizar os comandos do PowerShell para automatizar tarefas comuns no seu armazém de dados SQL.  Para obter alguns exemplos de resto boas, consulte o artigo [Gerir escalabilidade com restantes][].

> [AZURE.NOTE]  Para poder utilizar o Azure PowerShell com armazém de dados do SQL, terá de Azure PowerShell versão 1.0.3 ou superior.  Pode verificar a sua versão executando **módulo Get - ListAvailable-nome Azure**.  Pode ser instalada a versão mais recente do [Microsoft Web plataforma Installer][].  Para mais informações sobre como instalar a versão mais recente, consulte o artigo [como instalar e configurar o Azure PowerShell][].

## <a name="get-started-with-azure-powershell-cmdlets"></a>Começar a trabalhar com os cmdlets do PowerShell do Azure

1. Abra o Windows PowerShell. 
2. Na linha de comandos do PowerShell, execute estes comandos para iniciar sessão para o Gestor de recursos do Azure e selecione a sua subscrição.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemplo de armazém de dados SQL colocar em pausa

Coloque o cursor uma base de dados com o nome "Database02" alojado num servidor com o nome "Server01."  O servidor está num grupo de recursos Azure denominada "ResourceGroup1." 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, neste exemplo tubos o objeto obtido [AzureRmSqlDatabase suspender][].  Como resultado, a base de dados está em pausa. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Iniciar o exemplo de armazém de dados SQL

Retomar a operação de uma base de dados com o nome "Database02" alojado num servidor com o nome "Server01." O servidor está contido num grupo de recursos com o nome "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo obtém uma base de dados denominada "Database02" a partir de um servidor com o nome "Server01" que estão contidas num grupo de recursos com o nome "ResourceGroup1." -Tubos o objeto obtido [AzureRmSqlDatabase currículo][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Note que, se o servidor está foo.database.windows.net, utilize "foo" como nomedoservidor - os cmdlets do PowerShell.

## <a name="frequently-used-powershell-cmdlets"></a>Cmdlets do PowerShell utilizados frequentemente

Estes cmdlets do PowerShell com frequência são utilizados com armazém de dados do SQL Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Novo AzureRmSqlDatabase][]
- [Remover AzureRmSqlDatabase][]
- [Restaurar AzureRmSqlDatabase][] 
- [Currículo AzureRmSqlDatabase][]
- [Selecione AzureRmSubscription][]
- [Definir AzureRmSqlDatabase][]
- [AzureRmSqlDatabase suspender][]

## <a name="next-steps"></a>Próximos passos
Para obter mais exemplos de PowerShell, consulte:

- [Criar um armazém de dados SQL através do PowerShell][]
- [Restauro de base de dados][]

Para obter uma lista de todas as tarefas que podem ser automatizados com o PowerShell, consulte [Cmdlets de base de dados do SQL Azure][].  Para obter uma lista de tarefas que podem ser automatizados com resto, consulte o artigo [operações para bases de dados do SQL Azure][].

<!--Image references-->

<!--Article references-->
[Como instalar e configurar PowerShell do Azure]: ./powershell-install-configure.md
[Criar um armazém de dados SQL através do PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restauro de base de dados]: ./sql-data-warehouse-restore-database-powershell.md
[Gerir escalabilidade com restantes]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Cmdlets de base de dados do Azure SQL]: https://msdn.microsoft.com/library/mt574084.aspx
[Operações de bases de dados Azure SQL]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Novo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remover AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restaurar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Currículo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Selecione AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Definir AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[AzureRmSqlDatabase suspender]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web plataforma Installer]: https://aka.ms/webpi-azps
