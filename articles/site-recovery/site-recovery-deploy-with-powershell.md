<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM utilizando recuperação de sites do Azure e PowerShell | Microsoft Azure"
    description="Saiba como para automatizar a replicação de Hyper-V máquinas virtuais nuvens VMM utilizando recuperação de sites e PowerShell."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para Azure através do Powershell - clássico

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestor de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Descrição geral

Azure recuperação de sites contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais um número de cenários de implementação. Para obter uma lista completa de cenários de implementação consulte o artigo [Descrição geral de recuperação de Site do Azure](site-recovery-overview.md).

Este artigo mostra-lhe como utilizar o PowerShell para automatizar tarefas comuns que necessitar de efetuar ao configurar Azure recuperação de Site para criar uma réplica Hyper-V máquinas virtuais nuvens System Center VMM ao armazenamento Azure.

O artigo inclui pré-requisitos para o cenário e mostra-lhe como configurar o Cofre recuperação de sites, instale o fornecedor de recuperação de Site do Azure do servidor de origem VMM, registar o servidor no cofre, adicionar uma conta de armazenamento Azure, instalar o agente de serviços de recuperação do Azure em servidores de anfitriões Hyper-V, configurar definições de proteção para nuvens VMM que serão aplicados a todas as máquinas virtuais protegidas e, em seguida, ativar proteção para esses máquinas virtuais. Terminar a configuração testando a activação pós-falha para se certificar de que tudo está a funcionar como esperado.

Se encontrar problemas de configuração de neste cenário, publique as suas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos e clássica](../resource-manager-deployment-model.md). Este artigo aborda a utilizar o modelo de implementação clássica.



## <a name="before-you-start"></a>Antes de começar

Certifique-se de que tem estes pré-requisitos num local:

### <a name="azure-prerequisites"></a>Pré-requisitos Azure

- Terá de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Terá de uma conta de armazenamento Azure para armazenar dados replicados. A conta tem de replicação geo ativada. Deverá ser na mesma região como cofre recuperação de sites do Azure e ser associadas a mesma subscrição. [Saiba mais sobre o armazenamento Azure](../storage/storage-introduction.md).
- Irá precisar para se certificar de que máquinas virtuais que pretende proteger está em conformidade com a [Pré-requisitos de máquina virtual Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="vmm-prerequisites"></a>Pré-requisitos de VMM
- Terá de servidor VMM em execução no sistema de centro de 2012 R2.
- Terá de pelo menos uma nuvem no servidor VMM que pretende proteger. Deverá conter na nuvem:
    - Um ou mais grupos de anfitrião VMM.
    - Uma ou mais servidores de anfitrião de Hyper-V ou clusters de cada grupo de anfitrião.
    - Máquinas virtuais um ou mais no servidor de origem Hyper-V.

### <a name="hyper-v-prerequisites"></a>Pré-requisitos de Hyper-V

- Os servidores de anfitrião Hyper-V, pelo menos, tem de executar o **Windows Server 2012** com a função Hyper-V ou **Microsoft Hyper-V Server 2012** e tem instalado as atualizações mais recentes.
- Se estiver a executar Hyper-V numa nota cluster esse corretor cluster não é automaticamente criada se tiver um cluster de baseados em endereços IP estático. Terá de configurar o corretor de cluster manualmente. Para efetuar esta ação, no Gestor de servidor > Gestor de clusters activação pós-falha, ligar ao cluster, clique em **Configurar a função** e selecione **Hyper-V réplica corretor** no ecrã do Assistente de elevada disponibilidade **Selecione função** .
- Qualquer servidor de anfitrião de Hyper-V ou cluster para o qual pretende gerir proteção deve ser incluído numa nuvem VMM.

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Quando proteger máquinas virtuais nos mapas de mapeamento de rede Azure entre redes VM no servidor VMM de origem e destino Azure redes para ativar o seguinte:

- Todos os computadores que falharem ao longo na mesma rede podem ligar umas às outras, independentemente que plano de recuperação estiverem na.
- Se um gateway de rede está configurada no destino rede Azure, máquinas virtuais pode ligar a outras máquinas virtuais no local.
- Se não configurar rede mapeamento apenas máquinas virtuais que falhar ao longo no plano de recuperação mesmo poderão ligar umas às outras depois activação pós-falha ao Azure.

Se pretende implementar o mapeamento de rede terá o seguinte procedimento:

- As máquinas virtuais que pretende proteger do servidor de origem VMM deve estar ligadas a uma rede VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem.
- Uma rede Azure à qual pode ligar replicadas máquinas virtuais após activação pós-falha. Irá Selecione esta rede no momento da activação pós-falha. A rede deve estar na mesma região como a sua subscrição de recuperação de sites do Azure.
- [Saiba mais](site-recovery-network-mapping.md) sobre mapeamento de rede:

###<a name="powershell-prerequisites"></a>Pré-requisitos de PowerShell
Certifique-se de que tem o Azure PowerShell pronta a ser enviada. Se já estiver a utilizar o PowerShell, terá de atualizar para a versão 0.8.10 ou posterior. Para obter informações sobre como configurar PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Assim que tiver configurar e configurado o PowerShell, pode ver todos os cmdlets disponíveis para o serviço [aqui](https://msdn.microsoft.com/library/dn850420.aspx).

Para saber mais sobre sugestões que o podem ajudar a que utilizar os cmdlets, tal como valores de parâmetros, entradas e saídas são normalmente processadas no Azure PowerShell, consulte o artigo [Introdução ao Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Passo 1: Definir a subscrição

No PowerShell, execute estes cmdlets:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Substitua os elementos dentro do "< >" informações específicas.

## <a name="step-2-create-a-site-recovery-vault"></a>Passo 2: Criar um cofre de recuperação de sites

Na PowerShell, substitua os elementos dentro do "< >" informações específicas e executar estes comandos:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Passo 3: Gerar uma chave de registo do Cofre

Gere uma chave de registo no cofre. Depois de transferir o fornecedor de recuperação de Site do Azure e instalá-lo no servidor VMM, irá utilizar esta chave para registar o servidor VMM no cofre.

1.  Obtenha o ficheiro de definição do cofre e definir o contexto de:

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Defina o contexto de cofre executando os seguintes comandos:

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Passo 4: Instale o fornecedor de recuperação de Azure Site

1.  No computador VMM, crie um diretório, executando o seguinte comando:

    ```

    pushd C:\ASR\

    ```

2.  Extrair os ficheiros utilizando o fornecedor transferido, executando o seguinte comando

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Instale o fornecedor utilizando os seguintes comandos:

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Aguarde que a instalação concluir.

4.  Registe o servidor no cofre utilizando o seguinte comando:

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Passo 5: Criar uma conta de armazenamento Azure

Se não tiver uma conta de armazenamento Azure, crie uma conta com capacidade de replicação de geo executando o seguinte comando:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Tenha em atenção que a conta de armazenamento tem ser na mesma região como o serviço de recuperação de sites do Azure e associado à subscrição mesmo.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Passo 6: Instalar o agente de serviços de recuperação Azure

A partir do portal do Azure, instale o agente de serviços de recuperação do Azure num servidor de anfitrião cada Hyper-V localizado nas nuvens VMM que pretende proteger.

Execute o seguinte comando em todos os anfitriões VMM:

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Passo 7: Configure nuvem definições de proteção

1.  Crie um perfil de proteção de nuvem para Azure executando o seguinte comando:

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Obtenha um contentor de proteção executando os seguintes comandos:

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Inicie a associação do contentor proteção com a nuvem:

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Depois da tarefa for concluída, execute o seguinte comando:


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Depois da tarefa já terminou processamento, execute o seguinte comando:


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



Para verificar a conclusão da operação de, siga os passos no [Monitor de atividade](#monitor).

## <a name="step-8-configure-network-mapping"></a>Passo 8: Configurar mapeamento de rede
Antes de começar mapeamento de rede Certifique-se de que máquinas virtuais do servidor de origem VMM estão ligadas a uma rede VM. Além disso, crie um ou mais redes virtuais Azure. Tenha em atenção que múltiplas redes VM podem ser mapeadas para uma única rede Azure.

Tenha em atenção que, se a rede de destino tem várias sub-redes e uma desses sub-redes tem o mesmo nome como sub-rede no qual se encontra a máquina virtual da origem, em seguida, a máquina de virtual réplica será ligada nessa sub-rede de destino depois activação pós-falha. Se não for uma sub-rede de destino com um nome correspondente, a máquina virtual será ligada para a primeira sub-rede na rede.

O primeiro comando obtém os servidores para o Cofre Azure recuperação de Site atual. O comando armazena os servidores de recuperação de Site do Microsoft Azure na variável de matriz $Servers.

    $Servers = Get-AzureSiteRecoveryServer


O comando segundo obtém a rede de recuperação de site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

O comando terceiro recebe as suas subscrições Azure utilizando o cmdlet Get-AzureSubscription e, em seguida, armazena esse valor na variável $Subscriptions.

    $Subscriptions = Get-AzureSubscription



O comando quarto obtém redes virtuais Azure utilizando o cmdlet Get-AzureVNetSite e, em seguida, esse valor na variável $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



O cmdlet final cria um mapeamento entre a rede principal e a rede de máquina virtual Azure. O cmdlet Especifica a rede principal, como o primeiro elemento de $Networks. O cmdlet Especifica uma rede de máquina virtual como o primeiro elemento de $AzureVmNetworks utilizando o respetivo ID. O comando inclui o seu ID de subscrição Azure.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Passo 9: Ativar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, pode ativar a proteção por para máquinas virtuais na nuvem. Tenha em atenção o seguinte procedimento:

Máquinas virtuais tem de cumprir [Pré-requisitos de máquina virtual Azure](site-recovery-best-practices.md#virtual-machines).

Para ativar a proteção do sistema operativo e o sistema operativo propriedades do disco tem de estar definidas para a máquina virtual. Quando cria uma máquina virtual no VMM através de um modelo de máquina virtual pode definir a propriedade. Também pode definir estas propriedades para máquinas virtuais existentes nos separadores **gerais** e de **Configuração do Hardware** das propriedades máquina virtual. Se não definir estas propriedades no VMM poderá configurá-los no portal do Azure recuperação de sites.



1.  Para permitir proteção, execute o seguinte comando para obter o contentor de proteção:

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Obter a entidade de proteção (VM) ao executar o seguinte comando:


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Active DR para a VM executando o seguinte comando:



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Testar a sua implementação

Para testar a sua implementação pode executar um teste activação pós-falha para uma única máquina virtual, ou criar um plano de recuperação que consiste várias máquinas virtuais e executa um teste activação pós-falha para o plano. Teste activação pós-falha simula seu mecanismo activação e recuperação numa rede isolada. Tenha em atenção que:

- Se pretende ligar a máquina virtual no Azure utilizando o ambiente de trabalho remoto após a activação pós-falha, ative ligação de ambiente de trabalho remoto na máquina virtual antes de executar o teste activação pós-falha.
- Depois de falha na ligação, que irá utilizar um endereço IP público para ligar à máquina virtual no Azure utilizando o ambiente de trabalho remoto. Se quiser fazer isto, certifique-se de que não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.

Para verificar a conclusão da operação de, siga os passos no [Monitor de atividade](#monitor).

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Criar um ficheiro. XML como um modelo para o seu plano de recuperação a utilizar os dados abaixo e, em seguida, guarde-o como "C:\RPTemplatePath.xml".
2. Altere o nó RecoveryPlan Id, nome, PrimaryServerId e SecondaryServerId.
3. Altere o nó ProtectionEntity PrimaryProtectionEntityId (vmid a partir do VMM).
4. Pode adicionar mais VMs ao adicionar mais ProtectionEntity nós.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Preencha os dados no modelo:


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Crie o RecoveryPlan:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

1.  Obter o objeto RecoveryPlan executando o seguinte comando:

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Inicie o teste activação pós-falha ao executar o seguinte comando:


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[Leia mais](https://msdn.microsoft.com/library/dn850420.aspx) sobre os cmdlets do PowerShell de recuperação do Azure Site. </a>.
