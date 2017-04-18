<properties
    pageTitle="Proteger os servidores para Azure utilizando o Azure PowerShell com o Gestor de recursos do Azure | Microsoft Azure"
    description="Automatizar proteção dos servidores que Azure com a recuperação de Site do Azure utilizando o PowerShell e Gestor de recursos do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Criar uma réplica entre máquinas de virtuais Hyper-V no local e Azure através do PowerShell e Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Gestor de recursos](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portal clássico](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Descrição geral

Azure recuperação de sites contribui para a sua estratégia de recuperação empresas continuidade e falhas por orquestrar replicação, activação pós-falha e recuperação de máquinas virtuais um número de cenários de implementação. Para obter uma lista completa de cenários de implementação, consulte o artigo [Descrição geral de recuperação de Site do Azure](site-recovery-overview.md).

Azure PowerShell é um módulo que fornece cmdlets para gerir Azure através do Windows PowerShell. Pode trabalhar com dois tipos de módulos: módulo Azure perfil ou o módulo Azure o Gestor de recursos.

Site recuperação os cmdlets do PowerShell, disponível com o Azure PowerShell para o Gestor de recursos do Azure, ajudam a proteger e recuperar os seus servidores no Azure.

Este artigo descreve como utilizar o Windows PowerShell, juntamente com o Gestor de recursos do Azure, para implementar recuperação de Site para configurar e orquestrar proteção de servidor para Azure. O exemplo utilizado neste artigo mostra-lhe como proteger, falhar ao longo e recuperar máquinas virtuais num sistema anfitrião Hyper-V para Azure, utilizando o Azure PowerShell com o Gestor de recursos do Azure.

> [AZURE.NOTE] Os cmdlets do PowerShell de recuperação de Site permitem-lhe configurar as seguintes opções atualmente: um site do Gestor de Máquina Virtual para outro, um site de Gestor de Máquina Virtual para Azure e um site de Hyper-V para Azure.

Não tem de ser um especialista o PowerShell para utilizar este artigo, mas é necessário compreender os conceitos básicos, tais como módulos, cmdlets e sessões. Para mais informações sobre o Windows PowerShell, consulte o artigo [Introdução ao Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Também pode ler mais sobre como [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Parceiros da Microsoft que fazem parte do programa do fornecedor de solução da nuvem (CSP) podem configurar e gerir a proteção dos servidores aos seus clientes para respetivos CSP as subscrições aos seus clientes (inquilino subscrições).

## <a name="before-you-start"></a>Antes de começar

Certifique-se de que tem estes pré-requisitos num local:

- Uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, pode obter informações sobre [preços do Gestor de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Para informações sobre este lançamento e como instalar, consulte o artigo [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- Os módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) . Pode obter as versões mais recentes destes módulos a partir da [Galeria de PowerShell](https://www.powershellgallery.com/)

Este artigo ilustra como utilizar o Azure Powershell com o Gestor de recursos do Azure para configurar e gerir a proteção dos servidores. O exemplo utilizado neste artigo mostra-lhe como pode proteger uma máquina de virtual em execução num sistema anfitrião Hyper-V, para Azure. Os pré-requisitos seguintes são específicos para este exemplo. Para um conjunto mais abrangente de requisitos para os vários cenários de recuperação de sites, consulte a documentação sobre a esse cenário.

- Um anfitrião Hyper-V a executar o Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 que contém uma ou mais máquinas virtuais.
- Os servidores de Hyper-V ligado à Internet, diretamente ou através de um proxy.
- As máquinas virtuais que pretende proteger deve em conformidade com [a pré-requisitos de Máquina Virtual](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Passo 1: Inicie sessão na sua conta Azure


1. Abrir uma consola do PowerShell e execute este comando para iniciar sessão na sua conta Azure. O cmdlet mostra uma página web que irá pedir-lhe para as suas credenciais de conta.

        Login-AzureRmAccount

    Em alternativa, também pode incluir as credenciais da conta como um parâmetro para a `Login-AzureRmAccount` cmdlet, utilizando o `-Credential` parâmetro.

    Se estiver parceiro CSP trabalhar em nome de um inquilino, especifique o cliente como um inquilino, utilizando o nome de domínio principal tenantID ou inquilino.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Uma conta pode ter várias subscrições, pelo que deve associar a subscrição que pretende utilizar com a conta.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Certifique-se de que a sua subscrição está registada para utilizar os fornecedores de Azure para serviços de recuperação e recuperação de Site, utilizando os seguintes comandos:

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    Em saída destes comandos de, se o **RegistrationState** está definido para **registado**, pode avançar para o passo 2. Caso contrário, deverá registar o fornecedor em falta na sua subscrição.

    Para registar o fornecedor do Azure recuperação de sites e serviços de recuperação, execute os seguintes comandos:

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Verificar se os fornecedores de registado com êxito ao utilizar os seguintes comandos: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` e `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Passo 2: Configurar o serviços de recuperação Cofre

1. Crie um grupo de recursos do Gestor de recursos do Azure na qual irá criar cofre ou utilizar um grupo de recursos existente. Pode criar um novo grupo de recursos utilizando o seguinte comando:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    em que a variável de $ResourceGroupName contém o nome do grupo de recursos que pretende criar e a variável de $Geo contém a região Azure na qual pretende criar o grupo de recursos (por exemplo, "Brasil Sul").

    Pode obter uma lista de grupos de recursos na sua subscrição, utilizando o `Get-AzureRmResourceGroup` cmdlet.

2. Crie um novo cofre de serviços de recuperação do Azure da seguinte forma:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Poderá obter uma lista de cofres existentes utilizando o `Get-AzureRmRecoveryServicesVault` cmdlet.

> [AZURE.NOTE] Se pretender executar operações em cofres de recuperação de Site criados utilizando o portal clássico ou o módulo Azure Service gestão PowerShell, pode obter uma lista dessas cofres utilizando o `Get-AzureRmSiteRecoveryVault` cmdlet. Deve criar uma nova Cofre de serviços de recuperação para todas as operações de novas. Os cofres de recuperação de sites que criou anteriormente são suportados, mas não tem as funcionalidades mais recentes.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto de Cofre de serviços de recuperação

1.  Defina o contexto de cofre executando o seguinte comando:

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Passo 4: Criar um site de Hyper-V e gerar uma nova chave de registo do cofre para o site.

1. Crie um novo site de Hyper-V da seguinte forma:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Este cmdlet inicia uma tarefa de recuperação de sites para criar o site e devolve um objecto de tarefa de recuperação de sites. Aguarde que a tarefa concluir e certifique-se de que a tarefa foi concluída com êxito.

    Pode obter o objeto de tarefa e, portanto verificar o estado atual da tarefa, utilizando o cmdlet Get-AzureRmSiteRecoveryJob.
2. Gerar e transferir uma chave de registo para o site, da seguinte forma:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copie a chave transferida ao anfitrião do Hyper-V. Tem a chave para registar o anfitrião do Hyper-V para o site.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Passo 5: Instalar o fornecedor de recuperação de sites do Azure e agente de serviços de recuperação do Azure no seu anfitrião de Hyper-V

1. Transferir o Windows installer para a versão mais recente do fornecedor da [Microsoft](https://aka.ms/downloaddra).

2. Executar o installer no seu anfitrião de Hyper-V e no final da instalação do continue para o passo de registo.

3. Quando lhe for pedido, forneça a chave de registo do site transferido e registo completo do anfitrião do Hyper-V para o site.

4. Certifique-se de que o anfitrião do Hyper-V está registado para o site utilizando o seguinte comando:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Passo 6: Criar uma política de replicação e associá-la com o contentor de proteção

1. Crie uma política de replicação da seguinte forma:

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Verificar a tarefa devolvida para se certificar de que a criação de política de replicação é concluída com êxito.

    >[AZURE.IMPORTANT] A conta de armazenamento especificada deve ser na mesma região Azure como Cofre de serviços de recuperação e deve ter geo-replicação ativado.
    >
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Windows Azure (clássico), activação pós-falha das máquinas protegidas recuperar a máquina para Azure IaaS (clássico).
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Windows Azure (Gestor de recursos do Azure), activação pós-falha das máquinas protegidas recuperar a máquina para Azure IaaS (Gestor de recursos do Azure).

2. Obter o contentor de proteção correspondente para o site, da seguinte forma:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Inicie a associação do contentor proteção com a política de replicação, da seguinte forma:

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Aguarde que a tarefa de associação concluir e certifique-se de que concluída com êxito.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Passo 7: Ativar proteção para máquinas virtuais

1. Obter a entidade de proteção correspondente para a VM que pretende proteger, da seguinte forma:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Começar a proteger máquina virtual, da seguinte forma:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] A conta de armazenamento especificada deve ser na mesma região Azure como Cofre de serviços de recuperação e deve ter geo-replicação ativado.
    >
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Windows Azure (clássico), activação pós-falha das máquinas protegidas recuperar a máquina para Azure IaaS (clássico).
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Windows Azure (Gestor de recursos do Azure), activação pós-falha das máquinas protegidas recuperar a máquina para Azure IaaS (Gestor de recursos do Azure).

    > Se a VM que está a proteger tiver mais de um disco anexado ao mesmo, especifique o disco do sistema operativo, utilizando o parâmetro *OSDiskName* .

3. Aguarde para as máquinas virtuais alcançar um estado protegido após a replicação inicial. Isto pode demorar algum tempo, dependendo de fatores como a quantidade de dados ser replicadas e a largura de banda montante disponível para Azure. Após a VM chegar a um estado protegido, o estado da tarefa e StateDescription são atualizadas da seguinte forma.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Atualize as propriedades de recuperação, tais como o tamanho da função memória virtual e a rede Azure para anexar rede interface cartões a máquina virtual de relativamente a activação pós-falha.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Passo 8: Executar um teste activação pós-falha

1. Execute uma tarefa de activação pós-falha de teste, da seguinte forma:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Certifique-se de que o teste VM é criada no Azure. (A tarefa de activação pós-falha de teste será suspensa, depois de criar o teste VM no Azure. A tarefa estiver concluída por limpar os perturbaçġes criadas após retomar a tarefa, tal como ilustrado no próximo passo.)

3. Conclua a falha na ligação teste, da seguinte forma:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Próximos passos

[Leia mais](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre a recuperação de Site Azure com os cmdlets do PowerShell do Azure Gestor de recursos.
