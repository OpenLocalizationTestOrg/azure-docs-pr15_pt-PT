<properties
    pageTitle="Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o PowerShell"
    description="Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artigo fornece instruções para o arquivo a base de dados do Azure SQL para um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (armazenado no armazenamento de Blobs do Azure) através do PowerShell.

Quando precisar de criar um arquivo de uma base de dados do Azure SQL, pode exportar o esquema da base de dados e os dados para um ficheiro BACPAC. Um ficheiro BACPAC é simplesmente um ficheiro ZIP com uma extensão de .bacpac. Um ficheiro BACPAC mais tarde pode ser armazenado em armazenamento de Blobs do Azure ou em armazenamento local numa localização no local. Também pode ser importado novamente para a base de dados do SQL Azure ou para uma instalação do SQL Server no local.

**Considerações sobre**

- Para um arquivo ser consistentes, tem de se certificar que sem escrever atividade está a ocorrer durante a exportação ou que esteja a exportar a partir de uma [cópia consistente](sql-database-copy.md) da base de dados Azure SQL.
- O tamanho máximo de um ficheiro BACPAC arquivado ao armazenamento de Blobs do Azure é 200 GB. Para arquivar um ficheiro BACPAC maior para o armazenamento local, utilize o utilitário de linha de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com Visual Studio e do SQL Server. Também pode [Transferir](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente das ferramentas de dados do SQL Server para obter este utilitário.
- Arquivar ao armazenamento de Azure premium utilizando um ficheiro BACPAC não é suportada.
- Se a operação de exportação exceder 20 horas, pode ser cancelada. Para aumentar o desempenho durante a exportação, pode:
 - Aumente temporariamente o seu nível de serviço.
 - Cessar todos ler e escrever atividade durante a exportação.
 - Utilize um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos sobre todas as tabelas grandes. Sem índices agrupados, uma exportação poderá falhar se demora mais de 6 a 12 horas. Isto acontece porque o serviço de exportação necessitam de ser uma pesquisa da tabela para tentar exportar a tabela inteira. É uma boa forma de saber se as suas tabelas estão otimizadas para exportar executar **DBCC SHOW_STATISTICS** e certifique-se de que o *RANGE_HI_KEY* não é nulo e o valor tem boa distribuição. Para obter detalhes, consulte o artigo [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] Não BACPACs destinam-se para ser utilizada para cópia de segurança e restaurar operações. Base de dados SQL Azure cria automaticamente cópias de segurança para cada base de dados do utilizador. Para obter detalhes, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md).

Para concluir este artigo, precisa do seguinte:

- Uma subscrição do Azure.
- Uma base de dados do Azure SQL.
- Uma [conta de armazenamento do Windows Azure padrão](../storage/storage-create-storage-account.md), com um contentor blob para armazenar a BACPAC no armazenamento padrão.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]




## <a name="export-your-database"></a>Exportar a sua base de dados

[Novo-AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx) cmdlet submeta um pedido de base de dados de exportação para o serviço. Dependendo do tamanho da base de dados, a operação de exportação poderá demorar algum tempo para concluir.

> [AZURE.IMPORTANT] Para garantir a um ficheiro BACPAC consistente, deve primeiro, [crie uma cópia da base de dados](sql-database-copy-powershell.md)e, em seguida, exporte a cópia da base de dados.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorizar o progresso da operação de exportação

Depois de executar o [AzureRmSqlDatabaseExport novo] (https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx), pode verificar o estado do pedido de executando [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx). A executar este imediatamente após o pedido normalmente devolve **Estado: em curso**. Quando vir **Estado: foi concluída com êxito** a exportação está concluída.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Exemplo de base de dados do SQL de exportação

O exemplo seguinte exporta base de dados SQL existente para um BACPAC e, em seguida, mostra como verificar o estado da operação de exportação.

Para executar o exemplo, existem algumas variáveis que precisa para substituir os valores específicos para a sua conta de base de dados e armazenamento. No [portal do Azure](https://portal.azure.com), navegue para a sua conta de armazenamento para obter o nome da conta de armazenamento, o nome do contentor blob e valor de chave. Pode localizar a chave ao clicar em **teclas de acesso** no seu pá de conta de armazenamento.

Substituir o seguinte `VARIABLE-VALUES` com valores para os recursos do Azure específicos. O nome da base de dados é a base de dados existente que pretende exportar.



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="next-steps"></a>Próximos passos

- Para saber como importar uma base de dados do Azure SQL utilizando o Powershell, consulte o artigo [Importar um BACPAC através do PowerShell](sql-database-import-powershell.md).


## <a name="additional-resources"></a>Recursos adicionais

- [Novo AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)