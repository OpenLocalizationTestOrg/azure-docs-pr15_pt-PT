<properties
    pageTitle="Restaurar chave cofre chave e o segredo para VMs encriptadas utilizando Azure cópia de segurança | Microsoft Azure"
    description="Saiba como restaurar a chave de chave cofre e secreta na cópia de segurança do Azure através do PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar chave cofre chave e o segredo para VMs encriptadas utilizando cópia de segurança do Azure
Este artigo fala sobre a utilização de cópia de segurança do Azure VM para executar restauro de encriptada Azure VMs, se a sua chave e a palavra-passe não existam na chave cofre. Também podem ser utilizados estes passos se pretender manter uma cópia em separado da chave (chave de encriptação de chave) e o segredo (chave de encriptação BitLocker) para a VM restaurada.

## <a name="pre-requisites"></a>Pré-requisitos

1. **Cópia de segurança encriptada VMs** - encriptados VMs Azure ter sido cópia de segurança utilizando Azure cópia de segurança. Consulte o artigo [Gerir a cópia de segurança e restauro dos VMs Azure através do PowerShell](backup-azure-vms-automation.md) para obter detalhes sobre como fazer cópia de segurança encriptada Azure VMs.

2. **Configurar o Azure chave cofre** – Certifique-se de que chave cofre ao qual teclas e segredos têm de ser restaurados já está a apresentar. Consulte o artigo [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md) para obter detalhes sobre a gestão de chave cofre.

## <a name="setup-recovery-services-vault"></a>Cofre de serviços de recuperação de configuração 
Utilize os passos seguintes para iniciar sessão no PowerShell e definir recuperação serviços cofre contexto

### <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell 

Inicie sessão na conta Azure utilizando o cmdlet seguinte

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Conjunto de recuperação serviços cofre contexto

Quando tem sessão iniciada, utilize o cmdlet seguinte para obter a lista de subscrições disponíveis

```
PS C:\> Get-AzureRmSubscription
```

Selecione a subscrição na qual os recursos estão disponíveis

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Definir o contexto de cofre utilizando Cofre de serviços de recuperação onde a cópia de segurança foi ativada para VMs encriptadas

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obter o ponto de recuperação 

Selecione o contentor no cofre que representa encriptada máquina virtual Azure

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Utilizar este contentor, regressar item para a máquina virtual correspondente para cima

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obter uma matriz de pontos de recuperação para o item selecionado cópia de segurança a rp variável

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurar máquina virtual encriptada
Utilize os seguintes passos para restaurar VM encriptada, sua chave e a palavra-passe.

### <a name="restore-key"></a>Restaurar chave

A matriz $rp acima, é ordenada pela ordem inversa de tempo com o ponto de recuperação mais recente no índice de 0. Por exemplo: $rp [0] seleciona o ponto de recuperação mais recente.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Depois deste cmdlet é executado com êxito, é gerado um ficheiro de BLOBs na pasta especificada no computador onde é executado. Este ficheiro blob representa encriptados chave num formato encriptado.

Restaure chave anterior ao Cofre de palavras chave utilizando o cmdlet seguinte. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurar o segredo

Restaurar secreta de dados a partir do ponto de recuperação obtido acima

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
O texto antes de vault.azure.net representa o nome do cofre chave original. O texto depois de segredos / representa nome secreta. 

Obtenha o nome secreta e o valor do resultado do cmdlet executar acima, no caso que pretende utilizar o mesmo nome secreto. Em outros casos, devem ser atualizados $secretname abaixo para utilizar o novo nome secreto. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Configurar etiquetas para o segredo, caso VM tem de ser restaurada, assim. Para a etiqueta DiskEncryptionKeyFileName, valor deverão conter nome do segredo que planeia utilizar. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Valor de DiskEncryptionKeyFileName é igual ao nome secreta obtido acima. Valor para DiskEncryptionKeyEncryptionKeyURL pode ser obtido do cofre chave depois de restaurar as teclas novamente e utilizar o cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)   

Definir a secreta para trás para o Cofre chave

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurar máquina virtual
Os cmdlets do PowerShell acima ajudá-lo a restaurar chave e secreta anterior ao Cofre de palavras chave, se tiver uma cópia encriptada VM utilizando cópia de segurança do Azure VM. Depois de restaurá-las, consulte o artigo [Gerir a cópia de segurança e restauro dos VMs Azure através do PowerShell](backup-azure-vms-automation.md) para restaurar VMs encriptadas.
