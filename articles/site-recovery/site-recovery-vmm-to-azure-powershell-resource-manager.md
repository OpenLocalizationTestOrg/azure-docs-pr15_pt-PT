<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM utilizando recuperação de sites do Azure e PowerShell (Gestor de recurso) | Microsoft Azure"
    description="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM utilizando recuperação de sites do Azure e PowerShell"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para Azure utilizando o PowerShell e Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestor de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Descrição geral

Azure recuperação de sites contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais um número de cenários de implementação. Para obter uma lista completa de cenários de implementação consulte o artigo [Descrição geral de recuperação de Site do Azure](site-recovery-overview.md).

Este artigo mostra-lhe como utilizar o PowerShell para automatizar tarefas comuns que necessitar de efetuar ao configurar Azure recuperação de Site para criar uma réplica Hyper-V máquinas virtuais nuvens System Center VMM ao armazenamento Azure.

O artigo inclui pré-requisitos para o cenário e mostra-lhe

- Como configurar um cofre de serviços de recuperação
- Instalar o fornecedor de recuperação de Site do Azure do servidor de origem VMM
- Registar o servidor no cofre, adicionar uma conta de armazenamento Azure
- Instalar o agente de serviços de recuperação do Azure em servidores de anfitriões Hyper-V
- Configurar definições de proteção para nuvens VMM, que irão ser aplicadas a todas as máquinas virtuais protegidas
- Ative proteção para esses máquinas virtuais.
- Teste a falha para se certificar de que tudo está a funcionar como esperado.

Se encontrar problemas de configuração de neste cenário, publique as suas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos e clássica](../resource-manager-deployment-model.md). Este artigo aborda a utilizar o modelo de implementação do Gestor de recursos.

## <a name="before-you-start"></a>Antes de começar

Certifique-se de que tem estes pré-requisitos num local:

### <a name="azure-prerequisites"></a>Pré-requisitos Azure

- Terá de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Se não tiver uma, comece com uma [conta gratuita](https://azure.microsoft.com/free). Além disso, pode ler sobre o [Gestor de recuperação de Site do Azure preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Tem uma subscrição CSP se estiver a experimentar a replicação para um cenário de subscrição CSP. Saiba mais sobre o programa de CSP [a inscrição no programa do CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Terá de uma conta de armazenamento (Gestor de recurso) Azure v2 para armazenar dados replicados para Azure. A conta tem de replicação geo ativada. Deve ser na mesma região como o serviço de recuperação de sites do Azure e ser associadas a mesma subscrição ou se a subscrição CSP. Para saber mais sobre como configurar Azure armazenamento, consulte o artigo [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md) para referência.
- Irá precisar para se certificar de que máquinas virtuais que pretende proteger está em conformidade com a [Pré-requisitos de máquina virtual Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Atualmente, só VM nível operações são possíveis através do Powershell. Suporte para operações de nível de plano de recuperação será feito mais rapidamente.  Por agora, está limitado a efetuar falhas disponíveis, a renovação apenas numa granularidade «protegida VM» e não num nível de recuperação planear.

### <a name="vmm-prerequisites"></a>Pré-requisitos de VMM
- Terá de servidor VMM em execução no sistema de centro de 2012 R2.
- Qualquer servidor VMM que contém máquinas virtuais que pretende proteger tem de executar o fornecedor de recuperação de Site do Azure. Isto é instalado durante a implementação do Azure recuperação de sites.
- Terá de pelo menos uma nuvem no servidor VMM que pretende proteger. Deverá conter na nuvem:
    - Um ou mais grupos de anfitrião VMM.
    - Uma ou mais servidores de anfitrião de Hyper-V ou clusters de cada grupo de anfitrião.
    - Máquinas virtuais um ou mais no servidor de origem Hyper-V.
- Saiba mais sobre a configuração de nuvens VMM:
    - Leia mais sobre como privada nuvens recortada VMM no [que há de novo na nuvem privado com o sistema Centro 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e em [VMM 2012 e as nuvens](http://go.microsoft.com/fwlink/?LinkId=324956).
    - Saiba mais sobre como [configurar o ferro nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Depois dos elementos do ferro nuvem estão no local, saiba mais sobre a criação de nuvens privados na [criação de uma nuvem privada no VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) e no [Tutorial: criar nuvens privadas com System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Pré-requisitos de Hyper-V

- Os servidores de anfitrião Hyper-V, pelo menos, tem de executar o **Windows Server 2012** com a função Hyper-V ou **Microsoft Hyper-V Server 2012** e tem instalado as atualizações mais recentes.
- Se estiver a executar Hyper-V numa nota cluster esse corretor cluster não é automaticamente criada se tiver um cluster de baseados em endereços IP estático. Terá de configurar o corretor de cluster manualmente. Para
- Para obter instruções, consulte [como configurar o corretor de réplica Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Qualquer servidor de anfitrião de Hyper-V ou cluster para o qual pretende gerir proteção deve ser incluído numa nuvem VMM.

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Quando proteger máquinas virtuais no Azure, os mapas de mapeamento de rede máquina virtual redes de mensagens no servidor VMM de origem e destino Azure redes para ativar o seguinte:

- Todos os computadores que falharem ao longo na mesma rede podem ligar umas às outras, independentemente que plano de recuperação estiverem na.
- Se um gateway de rede está configurada no destino rede Azure, máquinas virtuais pode ligar a outras máquinas virtuais no local.
- Se não configurar mapeamento de rede, apenas as máquinas virtuais que falhar ao longo no plano de recuperação mesmo poderão ligar umas às outras depois de falhas com sobre para Azure.

Se pretende implementar o mapeamento de rede terá o seguinte procedimento:

- As máquinas virtuais que pretende proteger do servidor de origem VMM deve estar ligadas a uma rede VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem.
- Uma rede Azure à qual pode ligar replicadas máquinas virtuais após falhas com sobre. Irá Selecione esta rede no momento da falhas com sobre. A rede deve estar na mesma região como a sua subscrição de recuperação de sites do Azure.

Obter mais informações sobre mapeamento de rede no

- [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Como configurar VM redes e gateways no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Como configurar e monitorizar redes virtuais no Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


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

1. Criar um grupo de recursos no Gestor de recursos do Azure se ainda não tiver um

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Criar um novo cofre de serviços de recuperação e guarde o objeto de Cofre de recuperação automática criado numa variável (será utilizada mais tarde). Também pode obter a criação de objeto Cofre de recuperação automática post utilizando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto de recuperação serviços Cofre

1.  Definir o contexto de cofre ao executar a abaixo comando.

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


## <a name="step-5-create-an-azure-storage-account"></a>Passo 5: Criar uma conta de armazenamento Azure

1. Se não tiver uma conta de armazenamento Azure, crie uma conta de replicação geo ativada no mesmo geo como cofre executando o seguinte comando:

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Tenha em atenção que a conta de armazenamento tem ser na mesma região como o serviço de recuperação de sites do Azure e associado à subscrição mesmo.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Passo 6: Instalar o agente de serviços de recuperação Azure

1. Transfira o agente de serviços de recuperação do Azure na [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e instalá-lo num servidor de anfitrião cada Hyper-V localizado nas nuvens VMM que pretende proteger.

2. Execute o seguinte comando em todos os anfitriões VMM:

    marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Passo 7: Configure nuvem definições de proteção

1.  Crie uma política de replicação para Azure executando o seguinte comando:


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Obtenha um contentor de proteção executando os seguintes comandos:

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Obter os detalhes da política para uma variável de utilizar a tarefa que foi criada e mencionar o nome da política amigável:

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Inicie a associação do contentor proteção com a política de replicação:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Depois da tarefa for concluída, execute o seguinte comando:

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Depois da tarefa já terminou processamento, execute o seguinte comando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Para verificar a conclusão da operação de, siga os passos no [Monitor de atividade](#monitor).

## <a name="step-8-configure-network-mapping"></a>Passo 8: Configurar mapeamento de rede

Antes de começar mapeamento de rede Certifique-se de que máquinas virtuais do servidor de origem VMM estão ligadas a uma rede VM. Além disso, crie um ou mais redes virtuais Azure.

Saiba mais sobre como criar uma rede virtual utilizar Gestor de recursos do Azure e PowerShell, [criar uma rede virtual com uma ligação VPN de site para o site com o Gestor de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Tenha em atenção que múltiplas redes de Máquina Virtual podem ser mapeadas para uma única rede Azure. Se a rede de destino tem várias sub-redes e uma desses sub-redes tem o mesmo nome como sub-rede no qual está localizada a máquina virtual da origem, em seguida, a máquina de virtual réplica será ligada nessa sub-rede de destino após falhas com sobre. Se não existirem sem sub-rede destino com um nome correspondente, a máquina virtual será ligada para a primeira sub-rede na rede.

1. O primeiro comando obtém os servidores para o Cofre Azure recuperação de Site atual. O comando armazena os servidores de recuperação de Site do Microsoft Azure na variável de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. O comando segundo obtém a rede de recuperação de site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. O comando terceiro obtém redes virtuais Azure e, em seguida, esse valor na variável $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. O cmdlet final cria um mapeamento entre a rede principal e a rede de máquina virtual Azure. O cmdlet Especifica a rede principal, como o primeiro elemento de $Networks. O cmdlet Especifica uma rede de máquina virtual como o primeiro elemento de $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Passo 9: Ativar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, pode ativar a proteção por para máquinas virtuais na nuvem.

 Tenha em atenção o seguinte procedimento:

 - Máquinas virtuais tem de cumprir requisitos Azure. Confirme o seguinte na [Pré-requisitos e suporte](site-recovery-best-practices.md) o guia de planeamento.

 - Para ativar a proteção, o sistema operativo e o sistema operativo propriedades do disco tem de estar definidas para a máquina virtual. Quando cria uma máquina virtual no VMM através de um modelo de máquina virtual pode definir a propriedade. Também pode definir estas propriedades para máquinas virtuais existentes nos separadores **gerais** e de **Configuração do Hardware** das propriedades máquina virtual. Se não definir estas propriedades no VMM poderá configurá-los no portal do Azure recuperação de sites.


  1. Para permitir proteção, execute o seguinte comando para obter o contentor de proteção:

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Obter a entidade de proteção (VM) ao executar o seguinte comando:

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Active DR para a VM executando o seguinte comando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Testar a sua implementação

Para testar a sua implementação pode executar um teste falhas com sobre para uma única máquina virtual, ou criar um plano de recuperação que consiste várias máquinas virtuais e executa um teste falhas com sobre para o plano. Teste falhas com sobre simula seu mecanismo falhas com sobre e recuperação numa rede isolada. Tenha em atenção que:

- Se pretende ligar a máquina virtual no Azure após falhas com sobre utilizar o ambiente de trabalho remoto, ative ligação de ambiente de trabalho remoto na máquina virtual antes de executar o teste activação pós-falha.
- Após falhas com sobre, que irá utilizar um endereço IP público para ligar à máquina virtual no Azure utilizando o ambiente de trabalho remoto. Se quiser fazer isto, certifique-se de que não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.

Para verificar a conclusão da operação de, siga os passos no [Monitor de atividade](#monitor).


### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

1.  Inicie o teste activação pós-falha ao executar o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Executar uma activação planeada pós-falha

1. Inicie o activação planeada pós-falha ao executar o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Executar uma activação não planeado pós-falha

1. Inicie o planeado activação pós-falha ao executar o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


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
