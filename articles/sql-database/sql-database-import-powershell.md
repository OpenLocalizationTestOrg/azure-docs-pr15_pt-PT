<properties
    pageTitle="Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL utilizando o PowerShell | Microsoft Azure"
    description="Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL utilizando o PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL utilizando o PowerShell

**Única base de dados**

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artigo fornece instruções para criar uma base de dados do Azure SQL importar um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) com o PowerShell.

A base de dados é criada a partir de um ficheiro BACPAC (.bacpac) importado a partir de um contentor de Blobs do armazenamento do Windows Azure. Se não tiver um ficheiro BACPAC no armazenamento do Windows Azure, consulte o artigo [arquivo uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o PowerShell](sql-database-export-powershell.md). Se já tiver um ficheiro BACPAC que não está no armazenamento do Windows Azure, [utilizar AzCopy para facilmente carregá-lo à sua conta de armazenamento do Windows Azure](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Base de dados SQL Azure automaticamente cria e mantém cópias de segurança para cada utilizador da base de dados que pode restaurar. Para obter detalhes, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md).


Para importar uma base de dados do SQL, precisa do seguinte:

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure simplesmente clique **Versão de avaliação gratuita** na parte superior desta página e, em seguida, regresse para concluir deste artigo.
- Um ficheiro BACPAC da base de dados que pretende importar. O BACPAC tem de estar no contentor de BLOBs [conta de armazenamento do Windows Azure](../storage/storage-create-storage-account.md) .



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Configurar as variáveis para o seu ambiente

Existem algumas variáveis onde precisa para substituir os valores de exemplo com os valores específicos para a base de dados e a sua conta de armazenamento.

O nome do servidor deve ser um servidor que execute atualmente na subscrição selecionada no passo anterior. Deverá ser o servidor que pretende que a base de dados ser criada em. Importar uma base de dados diretamente para um conjunto de dados flexível não é suportada. Mas pode importar primeiro para uma única base de dados e, em seguida, mova a base de dados num conjunto de dados.

O nome da base de dados é o nome que pretende para a nova base de dados.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


As seguintes variáveis são da conta de armazenamento onde se encontra o seu BACPAC. No [portal do Azure](https://portal.azure.com), navegue para a sua conta de armazenamento para aceder estes valores. Pode localizar a chave primária acesso ao clicar em **todas as definições** e, em seguida, **as teclas** de pá da sua conta de armazenamento.

O nome de BLOBs é o nome de um ficheiro BACPAC existente ao qual pretende criar a base de dados a partir de. Tem de incluir a extensão de .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Executar a [Get-credencial] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) cmdlet abre uma janela pedir para o seu nome de utilizador e palavra-passe. Introduza o início de sessão do administrador e a palavra-passe para o servidor de base de dados SQL ($ServerName dos acima) e não o nome de utilizador e palavra-passe para a sua conta Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importar a base de dados

Este comando submeta um pedido de base de dados de importar para o serviço. Dependendo do tamanho da base de dados, a operação de importação poderá demorar algum tempo para concluir.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Monitorizar o progresso da operação de

Depois de executar o [AzureRmSqlDatabaseImport novo] (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), pode verificar o estado do pedido de ao executar o [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Script de importação do PowerShell de base de dados SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre como ligar e uma base de dados importados do SQL da consulta, consulte o artigo [ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL exemplo](sql-database-connect-query-ssms.md)
