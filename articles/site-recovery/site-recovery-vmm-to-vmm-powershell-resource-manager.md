<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para um site VMM secundário através do PowerShell (Gestor de recurso) | Microsoft Azure"
    description="Descreve como implementar o Azure recuperação de Site para orquestrar replicação, activação e recuperação do Hyper-V VMs no nuvens VMM para um site VMM secundário através do PowerShell (Gestor de recurso)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para um site VMM secundário através do PowerShell (Gestor de recurso)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestor de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bem-vindo ao Azure Site recuperação! Utilize este artigo se pretender criar uma réplica no local Hyper-V virtual máquinas gerido no nuvens sistema Center Máquina Virtual Gestor (VMM) para um site secundário. 

Este artigo mostra-lhe como utilizar o PowerShell para automatizar tarefas comuns que necessitar de efetuar quando configurar a recuperação de sites do Azure a replicação Hyper-V máquinas virtuais nuvens System Center VMM para nuvens System Center VMM no site secundário.

O artigo inclui pré-requisitos para o cenário e mostra-lhe 

- Como configurar um cofre de serviços de recuperação
- Instalar o fornecedor de recuperação de Site do Azure no servidor VMM de origem e no servidor VMM de destino
- Registe-se o servidor (es) VMM no Cofre
- Configure a política de replicação para a nuvem VMM. As definições de replicação na política serão aplicadas a todas as máquinas virtuais protegidas 
- Active a protecção para as máquinas virtuais. 
- Teste a activação pós-falha de VMs individualmente ou como parte de um plano de recuperação para se certificar de que tudo está a funcionar como esperado.
- Execute uma planeado ou um não planeado activação pós-falha de VMs individualmente ou como parte de um plano de recuperação para se certificar de que tudo está a funcionar como esperado.

Se encontrar problemas de configuração de neste cenário, publique as suas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação. Este artigo abrange o modelo de implementação do Gestor de recursos.



## <a name="on-premises-prerequisites"></a>Pré-requisitos de no local

Eis o que vai precisar nos sites principais e secundários no local para implementar este cenário:

**Pré-requisitos** | **Detalhes** 
--- | ---
**VMM** | Recomendamos que implementar um servidor VMM no site principal e um servidor VMM no site secundário.<br/><br/> Também pode [criar uma réplica entre nuvens num único servidor VMM](site-recovery-single-vmm.md). Para fazer isto terá de nuvens, pelo menos, duas configurados no servidor de VMM.<br/><br/> Os servidores de VMM devem estar em execução, pelo menos, sistema Centro 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor VMM tem de ter um ou mais nuvens configurados e todas as nuvens têm de ter o perfil da capacidade de Hyper-V definir. <br/><br/>Nuvens tem de conter um ou mais grupos de anfitrião VMM.<br/><br/>Saiba mais sobre a configuração de nuvens VMM na [configurar o ferro nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [Tutorial: criar nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Os servidores VMM devem ter acesso à internet. 
**Hyper-V** | Os servidores de Hyper-V devem estar em execução, pelo menos, Windows Server 2012, com a função Hyper-V e tem instalado as atualizações mais recentes.<br/><br/> Um servidor de Hyper-V deve conter um ou mais VMs.<br/><br/>  Os servidores de anfitrião Hyper-V devem estar localizados em grupos de anfitrião em nuvens VMM principais e secundários.<br/><br/> Se estiver a executar o Hyper-V num cluster no Windows Server 2012 R2 deve instalar [Atualizar 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver a executar o Hyper-V num cluster no Windows Server 2012 nota que corretor cluster não é automaticamente criada se tiver um cluster de baseados em endereços IP estático. Terá de configurar o corretor de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Fornecedor** | Durante a implementação de recuperação de sites que instalar o fornecedor de recuperação de Site do Azure nos servidores VMM. O fornecedor de comunica com a recuperação do Site através da 443 HTTPS para orquestrar replicação. Ocorre replicação de dados entre os servidores de Hyper-V principais e secundários através da rede local ou de uma ligação VPN.<br/><br/> O fornecedor executado no servidor do VMM precisa de aceder a estes URLs: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Além disso, permitir a comunicação de firewall dos servidores VMM para os [intervalos IP do Centro de dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir que o protocolo HTTPS (443).

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Mapas de mapeamento de rede entre redes VMM VM nos servidores VMM principais e secundários para:

- Coloque ideal réplica VMs em secundários anfitriões de Hyper-V após activação pós-falha.
- Ligar réplica VMs a redes VM adequados.
- Se não configurar réplica de mapeamento de rede que VMS não estar ligados a quaisquer rede após activação pós-falha.
- Se pretender configurar a rede durante a recuperação de Site de mapeamento implementação aqui é o que precisa:

    - Certifique-se de que VMs do servidor de origem Hyper-V anfitrião estão ligadas a uma rede VMM VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem.
    - Certifique-se de que a nuvem secundária que irá utilizar para a recuperação tem uma rede VM correspondente configurado. Que rede VM deve ser ligado a uma rede lógica que está associada a nuvem secundária.


Saiba mais sobre como configurar redes VMM a abaixo artigos

- [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Como configurar VM redes e gateways no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento de rede.

###<a name="powershell-prerequisites"></a>Pré-requisitos de PowerShell
Certifique-se de que tem o Azure PowerShell pronta a ser enviada. Se já estiver a utilizar o PowerShell, terá de atualizar para a versão 0.8.10 ou posterior. Para obter informações sobre como configurar PowerShell, consulte o [Guia para instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Assim que tiver configurar e configurado o PowerShell, pode ver todos os cmdlets disponíveis para o serviço [aqui](https://msdn.microsoft.com/library/dn850420.aspx). 

Para saber mais sobre sugestões que o podem ajudar a que utilizar os cmdlets, tal como valores de parâmetros, entradas e saídas são normalmente processadas no Azure PowerShell, consulte o [Guia de introdução ao Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Passo 1: Definir a subscrição 

1. A partir do Azure powershell, iniciar sessão na sua conta Azure: utilizar os seguintes cmdlets
 
        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred 
    

2. Obter uma lista das suas subscrições. Isto irá também listar o subscriptionIDs para cada uma das subscrições. Nota para baixo subscriptionID da subscrição na qual pretende criar a recuperação ter serviços Cofre    

        Get-AzureRmSubscription 

3. Definir a subscrição na qual o Cofre de serviços de recuperação é criada por mencionar o ID da subscrição

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Passo 2: Criar um cofre de serviços de recuperação 

1. Criar um grupo de recursos do Gestor de recursos do Azure se ainda não tiver um

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Criar um novo cofre de serviços de recuperação e guarde o objeto de Cofre de recuperação automática criado numa variável (será utilizada mais tarde). Também pode obter a criação de objeto Cofre de recuperação automática post utilizando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto de recuperação serviços Cofre

1.  Se tiver um cofre já criou, execute o abaixo comando para obter o cofre.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Definir o contexto de cofre ao executar a abaixo comando.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault



## <a name="step-4-install-the-azure-site-recovery-provider"></a>Passo 4: Instale o fornecedor de recuperação de Azure Site

1.  No computador VMM, crie um diretório, executando o seguinte comando:
    
        New-Item c:\ASR -type directory
        
2.  Extrair os ficheiros utilizando o fornecedor transferido, executando o seguinte comando
    
        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q

    
3.  Instale o fornecedor utilizando os seguintes comandos:
    
        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Aguarde que a instalação concluir.
    
4.  Registe o servidor no cofre utilizando o seguinte comando:
    
        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-and-associate-a-replication-policy"></a>Passo 5: Criar e associar uma política de replicação

1.  Crie uma política de replicação Hyper-V 2012 R2 executando o seguinte comando:

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] Nuvem VMM pode conter anfitriões Hyper-V com diferentes versões do Windows Server (tal como mencionado nos pré-requisitos Hyper-V), mas a política de replicação é a versão do SO específica. Se tiver anfitriões diferentes em execução no versões diferentes do sistema operativo, em seguida, crie políticas de replicação separada para cada tipo de versão do SO. Para por ex: Se tiver cinco anfitriões em execução no Windows os servidores de 2012 e três no Windows Server 2012 R2, crie duas políticas de replicação – um para cada tipo de versões do sistema operativo.

2.  Obter a proteção primária contentores (primária VMM nuvem) e recuperação proteção (recuperação VMM nuvem) ao executar os seguintes comandos:
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Obter a política que criou no passo 1 utilizando o nome amigável da política

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Inicie a associação do contentor proteção (VMM nuvem) com a política de replicação:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Aguarde que a tarefa de associação de política concluir. Pode verificar se a tarefa estiver concluída, utilizando o fragmento de PowerShell que se segue.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Depois da tarefa já terminou processamento, execute o seguinte comando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Para verificar a conclusão da operação de, siga os passos no [Monitor de atividade](#monitor).

## <a name="step-5-configure-network-mapping"></a>Passo 5: Configurar mapeamento de rede

1. O primeiro comando obtém os servidores para o Cofre Azure recuperação de Site atual. O comando armazena os servidores de recuperação de Site do Microsoft Azure na variável de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. A abaixo comandos obter a rede de recuperação de site para o servidor de VMM de origem e o servidor VMM de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] O servidor de VMM origem pode ser a primeira parte ou a segunda opção na matriz servidores. Verifique os nomes dos servidores VMM e obter as redes corretamente


4. O cmdlet final cria um mapeamento entre a rede principal e a rede de recuperação. O cmdlet Especifica a rede principal, como o primeiro elemento do $PrimaryNetworks e da rede de recuperação como o primeiro elemento de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Passo 6: Configurar mapeamento de armazenamento

1. A abaixo comando obtém a lista de classificações de armazenamento para $storageclassifications variável.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. A abaixo comandos obter a classificação de origem em $SourceClassificaion classificação de variável e de destino para $TargetClassification variável. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] Classificações de origem e destino podem ser qualquer elemento na matriz. Referir-se para o resultado da abaixo comando para calcular o índice de origem e destino classificações na matriz $storageclassifications. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object - propriedade FriendlyName, Id | Formatar tabela


3. A abaixo cmdlet cria um mapeamento entre a classificação de origem e a classificação de destino. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Passo 7: Ativar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, pode ativar a proteção por para máquinas virtuais na nuvem. 


  1. Para permitir proteção, execute o seguinte comando para obter o contentor de proteção:
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Obter a entidade de proteção (VM) ao executar o seguinte comando:
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Active a replicação para a VM executando o seguinte comando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Testar a sua implementação

Para testar a sua implementação pode executar um teste activação pós-falha para uma única máquina virtual, ou criar um plano de recuperação que consiste várias máquinas virtuais e executa um teste activação pós-falha para o plano. Teste activação pós-falha simula seu mecanismo activação e recuperação numa rede isolada. 

> [AZURE.NOTE] Pode criar um plano de recuperação para a sua aplicação no Azure portal.

Para verificar a conclusão da operação de, siga os passos no [Monitor de atividade](#monitor).


### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

1.  Executar a abaixo cmdlets, para obter o seu VMs para a rede VM ao qual pretende testar activação pós-falha.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Realizar um ensaio activação pós-falha de uma VM efetuando o seguinte procedimento:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Realizar um ensaio activação pós-falha de um plano de recuperação efetuando o seguinte procedimento:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Executar uma activação planeada pós-falha

1. Efetue activação pós-falha planeada de uma VM ao efetuar o seguinte:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Efectue uma planeada activação pós-falha de um plano de recuperação efetuando o seguinte procedimento:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Executar uma activação não planeado pós-falha

1. Efectue uma activação pós-falha não planeado de uma VM efetuando o seguinte procedimento:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2. Efetue uma não planeado activação pós-falha de um plano de recuperação ao efetuar o seguinte:
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
## <a name=monitor></a>Monitor de atividade

Utilize os comandos seguintes para monitorizar a atividade. Tenha em atenção que têm de esperar entre tarefas para a transformação concluir.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Próximos passos

[Leia mais](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre a recuperação de Site Azure com os cmdlets do PowerShell do Azure Gestor de recursos.

