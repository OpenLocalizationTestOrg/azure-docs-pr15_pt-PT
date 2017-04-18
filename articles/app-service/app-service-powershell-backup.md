<properties
    pageTitle="Utilizar o PowerShell para fazer cópia de segurança e restaurar as aplicações de serviço de aplicação"
    description="Saiba como utilizar o PowerShell para fazer cópia de segurança e restaurar uma aplicação do serviço de aplicação do Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Utilizar o PowerShell para fazer cópia de segurança e restaurar as aplicações de serviço de aplicação

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [REST API](../app-service-web/websites-csm-backup.md)

Saiba como utilizar o PowerShell do Azure para fazer cópia de segurança e restaurar a [aplicação de serviço de aplicações](https://azure.microsoft.com/services/app-service/web/). Para mais informações sobre web app cópias de segurança, incluindo os requisitos e restrições, consulte o artigo [Criar cópia de segurança de uma aplicação web na aplicação de serviço de Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o PowerShell para gerir as cópias de segurança de aplicação, é necessário o seguinte procedimento:

- **Um URL de SA** que permite a leitura e escrita acesso a um contentor de armazenamento do Windows Azure. Consulte o artigo [Compreender o modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md) para obter uma explicação de URLs SA. Consulte o artigo [Utilizar o PowerShell Azure com armazenamento do Windows Azure](../storage/storage-powershell-guide-full.md) para obter exemplos da gestão de armazenamento do Windows Azure através do PowerShell.
- **Uma cadeia de ligação de base de dados** se pretender agregar uma base de dados juntamente com a sua aplicação web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Como gerar um URL de SA para utilizar com os cmdlets de cópia de segurança de aplicação web
Um URL de SA pode ser gerado com o PowerShell. Eis um exemplo de como gerar uma que podem ser utilizados com os cmdlets de outros fabricantes referidos neste artigo.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Instalar o Azure PowerShell 1.3.2 ou superior

Consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-install-configure.md) para obter instruções sobre como instalar e utilizar o Azure PowerShell.

## <a name="create-a-backup"></a>Criar uma cópia de segurança

Utilize o cmdlet AzureRmWebAppBackup novo para criar uma cópia de segurança de uma aplicação web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Esta ação cria uma cópia de segurança com um nome gerado automaticamente. Se pretender para fornecer um nome para a cópia de segurança, utilize o parâmetro opcional NomeCópiaSegurança.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Para incluir uma base de dados da cópia de segurança, primeiro crie uma definição de cópia de segurança da base de dados utilizando o cmdlet AzureRmWebAppDatabaseBackupSetting novo, em seguida, fornecer essa definição no parâmetro de bases de dados do cmdlet AzureRmWebAppBackup novo. O parâmetro de bases de dados aceita uma matriz de definições de base de dados, permitindo-lhe criar uma cópia de segurança da base de dados mais do que uma.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Obter cópias de segurança

O cmdlet Get-AzureRmWebAppBackupList devolve uma matriz de todas as cópias de segurança para uma aplicação web. Tem de fornecer o nome do web app e ao seu grupo de recursos.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Para obter uma cópia de segurança específica, utilize o cmdlet Get-AzureRmWebAppBackup.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Também pode encaminhar um objecto da aplicação web em qualquer um dos cmdlets de gestão de cópia de segurança para maior conveniência.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Agendar cópias de segurança automáticas

Pode agendar cópias de segurança para ocorrer automaticamente um intervalo especificado. Para configurar uma agenda de cópia de segurança, utilize o cmdlet AzureRmWebAppBackupConfiguration editar. Este cmdlet leva-o até vários parâmetros:

- **Nome** - o nome da aplicação web.
- **ResourceGroupName** - o nome do grupo de recursos que contém a aplicação web.
- **Ranhura** - opcional. O nome da ranhura de aplicação web.
- **StorageAccountUrl** - o de SA de URL para o contentor de armazenamento do Windows Azure utilizado para armazenar as cópias de segurança.
- **FrequencyInterval** - um valor numérico para o número de vezes que devem ser feitas as cópias de segurança. Tem de ser um número inteiro positivo.
- **FrequencyUnit** - unidade de tempo de número de vezes que devem ser feitas as cópias de segurança. Opções estão hora e o dia.
- **RetentionPeriodInDays** - quantos dias cópias de segurança automáticas devem ser guardadas antes de a ser automaticamente eliminados.
- **Hora de início** - opcional. A hora quando as cópias de segurança automáticas deverão começar. Cópias de segurança começa imediatamente se esta é nula. Tem de ser uma data/hora.
- **Bases de dados** - opcional. Uma matriz de DatabaseBackupSettings para as bases de dados para cópia de segurança.
- **KeepAtLeastOneBackup** - opcional mudado parâmetro. Fornecer neste se sempre deve ser mantida numa cópia de segurança da conta de armazenamento, independentemente de como antigo é.

Segue-se um exemplo de como utilizar este cmdlet.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Para obter o atual da agenda de cópia de segurança, utilize o cmdlet Get-AzureRmWebAppBackupConfiguration. Isto pode ser útil para modificar uma agenda que já tenha sido configurada.

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>Restaurar uma aplicação web a partir de uma cópia de segurança

Para restaurar uma aplicação web a partir de uma cópia de segurança, utilize o cmdlet AzureRmWebAppBackup restaurar. A forma mais fácil utilizar este cmdlet é pipe num objeto cópia de segurança obtidos a partir do cmdlet Get-AzureRmWebAppBackup ou cmdlet Get-AzureRmWebAppBackupList.

Assim que tiver um objeto de cópia de segurança, pode encaminhá-lo para o cmdlet AzureRmWebAppBackup restaurar. Especifique o parâmetro de substituir para indicar que pretende substituir o conteúdo da sua aplicação web com o conteúdo da cópia de segurança. Se a cópia de segurança contém bases de dados, essas bases de dados são restaurados também.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

Segue-se um exemplo de como utilizar o AzureRmWebAppBackup restaurar ao especificar todos os parâmetros.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Eliminar uma cópia de segurança

Para eliminar uma cópia de segurança, utilize o cmdlet AzureRmWebAppBackup remover. Esta ação remove a cópia de segurança da sua conta de armazenamento. Especifique o nome da aplicação, o seu grupo de recursos e o ID da cópia de segurança que pretende eliminar.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Também pode encaminhar um objeto de cópia de segurança para o cmdlet remover AzureRmWebAppBackup para eliminá-la.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
