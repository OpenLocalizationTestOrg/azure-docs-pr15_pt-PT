<properties
    pageTitle="Configurar integração do Azure cofre chave para o SQL Server no Azure VMs (clássico)"
    description="Saiba como para automatizar a configuração de encriptação do SQL Server para utilização com o Azure chave cofre. Este tópico explica como utilizar o Azure chave cofre integração com do SQL Server em máquinas virtuais criar no modelo de implementação clássica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Configurar integração do Azure cofre chave para o SQL Server no Azure VMs (clássico)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clássico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Descrição geral
Existem várias funcionalidades de encriptação de SQL Server, como a [encriptação de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [coluna encriptação nível (limpar)](https://msdn.microsoft.com/library/ms173744.aspx)e [encriptação de cópia de segurança](https://msdn.microsoft.com/library/dn449489.aspx). Estes formulários de encriptação necessitam que gerir e armazenar as chaves de criptografia que utiliza para encriptação. O serviço do Azure chave cofre (AKV) foi concebido para melhorar a segurança e gestão destas teclas numa localização segura e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite SQL Server para utilizar estas teclas do Azure chave cofre.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Se estiver a executar o SQL Server com máquinas no local, existem [passos que pode seguir para aceder ao Azure chave cofre a partir do seu computador do SQL Server no local](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server no Azure VMs, pode poupar tempo ao utilizar a funcionalidade de *Integração do Azure chave cofre* . Com alguns Azure os cmdlets do PowerShell para ativar esta funcionalidade, pode automatizar a configuração necessária para uma VM SQL aceder a sua chave cofre.

Quando esta funcionalidade está ativada,-lo automaticamente instala o SQL Server Connector, configura o fornecedor EKM para aceder ao Cofre de chave do Azure e cria a credencial a permitem-lhe aceder ao seu cofre. Se os passos na documentação anteriormente mencionadas no local que visualizou, pode ver que esta funcionalidade automatiza os passos 2 e 3. É a única coisa que ainda precisa para fazer manualmente criar as teclas e chave cofre. A partir daí, o todo o programa de configuração da sua VM SQL é automática. Assim que esta funcionalidade terminar este programa de configuração, pode executar declarações de T-SQL para começar a encriptar o seu bases de dados ou cópias de segurança como faria normalmente.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar a integração de AKV
Utilizar o PowerShell para configurar a integração do Azure chave cofre. As secções seguintes fornecem uma descrição geral dos parâmetros necessários e, em seguida, um exemplo de script PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão de IaaS do SQL Server

Em primeiro lugar, [instale a extensão de IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreender os parâmetros de entrada
A tabela seguinte lista os parâmetros necessários para executar o script do PowerShell na secção seguinte.

|Parâmetro|Descrição|Exemplo|
|---|---|---|
|**$akvURL**|**O URL do cofre chave**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nome Principal de serviço**|"fde2b411 33 + d 5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Secreta Principal de serviço**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM ="|
|**$credName**|**Nome de credenciais**: integração AKV cria uma credencial dentro do SQL Server, permitindo a VM ter acesso ao Cofre de palavras chave. Selecione um nome para esta credencial.|"mycred1"|
|**$vmName**|**Nome da máquina virtual**: O nome de uma VM SQL criado anteriormente.|"myvmname"|
|**$serviceName**|**Nome do serviço**: nome do serviço na nuvem que está associado a VM de SQL.|"mycloudservicename"|

### <a name="enable-akv-integration-with-powershell"></a>Ativar a integração do AKV com PowerShell
O cmdlet **Novo AzureVMSqlServerKeyVaultCredentialConfig** cria um objeto de configuração para a funcionalidade de integração do Azure chave cofre. O **Conjunto AzureVMSqlServerExtension** configura este integração com o parâmetro **KeyVaultCredentialSettings** . Os passos seguintes mostram como utilizar estes comandos.

1. No Azure PowerShell, configure primeiro os parâmetros de entrada com seus valores específicos, tal como descrito nas secções anteriores deste tópico. O script seguinte é um exemplo.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  Em seguida, utilize o seguinte script para configurar e ativar a integração do AKV.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão de agente de IaaS SQL irão atualizar a VM SQL com esta configuração nova.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
