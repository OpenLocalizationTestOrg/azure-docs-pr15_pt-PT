<properties
    pageTitle="Criar uma réplica da máquinas virtuais de VMware para Azure utilizando recuperação de Site com o Azure automatização DSC | Microsoft Azure"
    description="Descreve como utilizar o Azure automatização DSC para implementar automaticamente o serviço de mobilidade de recuperação de Site do Azure e Azure agente para virtual/físico máquinas Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Criar uma réplica da máquinas virtuais de VMware para Azure utilizando a recuperação de sites com DSC de automatização do Azure

No conjunto de aplicações de gestão de operações, fornecemos-lhe com uma cópia de segurança abrangente e a solução de recuperação de falhas que pode utilizar como parte do seu plano de continuidade do negócio.

Vamos iniciado este viagem juntamente com o Hyper-V utilizando réplica Hyper-V. Mas Expandimos para suportar uma configuração heterogénea porque clientes tem múltiplas plataformas e hypervisors no respetivos nuvens.

Se estiver a executar VMware cargas de trabalho e/ou servidores físicos hoje, um servidor de gestão executa todos os componentes de recuperação de sites do Azure no seu ambiente processe a replicação de comunicação e dados com Azure, quando Azure é o destino.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Implementar o serviço de mobilidade de recuperação do Site através da utilização DSC de automatização
Vamos começar ao efetuar uma divisão rápida do que significa este servidor de gestão.

O servidor de gestão é executado várias funções de servidor. Uma das seguintes funções é de *configuração*, que coordenadas comunicação e gere os processos de replicação e recuperação de dados.

Além disso, a função de *processo* age como um gateway replicação. Esta função recebe dados de replicação de máquinas origem protegida, optimiza-o com colocação em cache, compressão e encriptação e, em seguida, envia-o para uma conta de armazenamento Azure. Uma das funções para a função de processo é também transmitir a instalação do serviço de mobilidade para máquinas protegidas e executar a deteção automática de VMware VMs.

Se existir uma reposição de recurso a partir do Azure, a função de *destino mestra* processará os dados de replicação como parte desta operação.

Para as máquinas protegidas, podemos dependem do *serviço de mobilidade*. Este componente é implementado para cada máquina (VMware VM ou servidor físico) que pretende criar uma réplica para Azure. -Captura escritas de dados no computador e encaminha-los para o servidor de gestão (função processo).

Quando estiver a trabalhar com continuidade do negócio, é importante compreender a sua das cargas de trabalho, sua infraestrutura e os componentes envolvidos. Em seguida, pode cumprir os requisitos para o seu objectivo de tempo de recuperação (RTO) e objectivo de ponto de recuperação (RPO). Neste contexto, o serviço de mobilidade é fundamental para assegurar que o seu das cargas de trabalho estão protegidas conforme esperado.

Então como pode, uma forma otimizada, certifique-se de que tem uma configuração protegida fiável com a ajuda a partir de alguns componentes do conjunto de aplicações de gestão de operações?

Este artigo fornece um exemplo de como pode utilizar Azure automatização pretendido estado configuração (DSC), juntamente com recuperação de Site, para se certificar de que:

- O serviço de mobilidade e agente do Azure VM são implementados as máquinas com o Windows que pretende proteger.
- O serviço de mobilidade e agente do Azure VM estão sempre a executar quando Azure é alvo replicação.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório para armazenar a configuração necessária
- Um repositório para armazenar a frase de acesso necessário para registar com o servidor de gestão

 > [AZURE.NOTE] É gerado uma frase de acesso exclusivo para cada management server. Se implementar os servidores de gestão de várias, tem de se certificar de que a frase de acesso correto está armazenado no ficheiro passphrase.txt.

- Windows Management Framework (WMF) 5.0 instalado nos computadores que pretende ativar proteção (um requisito para automatização DSC)

 > [AZURE.NOTE] Se pretender utilizar máquinas DSC para Windows que tenham WMF 4.0 instalado, consulte a secção [Utilizar DSC nos ambientes desligados](#Use DSC in disconnected environments).

O serviço de mobilidade pode ser instalado através da linha de comandos e aceita vários argumentos. Razão pela qual tem de ter os binários (depois de extrai-los a partir de configuração do seu) e armazená-los num local onde pode recuperá-las utilizando uma configuração DSC.

## <a name="step-1-extract-binaries"></a>Passo 1: Binários extrair

1. Para extrair os ficheiros que precisa para esta configuração, navegue para o seguinte directório no seu servidor de gestão:

    **\Microsoft azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    Nesta pasta, deverá ver um ficheiro MSI com o nome:

    **Microsoft ASR_UA_version_Windows_GA_date_Release.exe**

    Utilize o seguinte comando para extrair o instalador do:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Selecione todos os ficheiros e enviá-las para uma pasta comprimida (em formato zip).

Tem agora binários de que precisa para automatizar a configuração do serviço de mobilidade utilizando DSC de automatização.

### <a name="passphrase"></a>Frase de acesso

Em seguida, tem de determinar onde pretende colocar esta pasta compactada. Pode utilizar uma conta de armazenamento Azure, conforme mostrado posterior, para armazenar a frase de acesso que precisa para a configuração. O agente, em seguida, irá registe-se com o servidor de gestão como parte do processo.

A frase de acesso que recebeu quando implementado o servidor de gestão de pode ser guardada num ficheiro de texto como passphrase.txt.

Coloca a pasta (compactada) e a frase de acesso num contentor dedicado na conta de armazenamento Azure.

![Localização da pasta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se preferir manter estes ficheiros numa partilha na sua rede, pode fazê-lo. Só precisa de garantir que o recurso DSC que vai estar a utilizar mais tarde tem acesso e pode aceder a configuração e frase de acesso.

## <a name="step-2-create-the-dsc-configuration"></a>Passo 2: Criar a configuração de DSC

A configuração depende WMF 5.0. Para o computador aplicar a configuração através de automatização DSC com êxito, WMF 5.0 tem de estar presentes.

O ambiente utiliza a configuração de DSC exemplo seguinte:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
A configuração será faça o seguinte:

- As variáveis irão indicar a configuração de onde pretende obter os binários para o serviço de mobilidade e o agente de Azure VM, onde pode obter a frase de acesso e para onde pretende armazenar a saída.
- A configuração irá importar o recurso xPSDesiredStateConfiguration DSC, para que pode utilizar `xRemoteFile` para transferir os ficheiros a partir do repositório.
- A configuração irá criar um diretório onde pretende armazenar os binários.
- O recurso de arquivo irá extrair os ficheiros a partir da pasta compactada.
- O pacote de instalação recurso instalará o serviço de mobilidade a partir do UNIFIEDAGENT. Installer EXE com os argumentos específicos. (As variáveis que construir os argumentos têm de ser alterados para refletir o seu ambiente.)
- O pacote AzureAgent recurso instalará o agente de Azure VM, é recomendado em cada VM que é executada no Azure. O agente do Azure VM também torna possível adicionar extensões a VM após activação pós-falha.
- O recurso de serviço ou recursos irão garantir que os serviços de mobilidade relacionados e os serviços Azure estão sempre em execução.

Guarde a configuração como **ASRMobilityService**.

>[AZURE.NOTE] Lembre-se substituir o CSIP na configuração para refletir o servidor de gestão real, para que o agente será ligado corretamente e irá utilizar a correta frase de acesso.

## <a name="step-3-upload-to-automation-dsc"></a>Passo 3: Carregar a automatização DSC

Uma vez que a configuração de DSC que efetuou irá importar um módulo de recursos necessário DSC (xPSDesiredStateConfiguration), tem de importar esse módulo na automatização antes de carregar a configuração de DSC.

Inicie sessão na sua conta de automatização, navegue até à **activos** > **módulos**e clique em **Procurar Galeria**.

Aqui pode procurar o módulo e importá-lo à sua conta.

![Módulo de importação](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Quando terminar de isto, vá para o seu computador onde tem os módulos de Gestor de recursos do Azure instalados e continuar para importar a configuração de DSC recentemente criada.

### <a name="import-cmdlets"></a>Cmdlets de importação

PowerShell, inicie sessão na sua subscrição do Azure. Modificar os cmdlets, para refletir o seu ambiente e capturar as suas informações de conta de automatização numa variável:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Carregar a configuração para automatização DSC utilizando o cmdlet seguinte:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilar a configuração na DSC de automatização

Em seguida, tem de compilação a configuração na DSC de automatização, para que possa começar registar nós à mesma. Atingir que ao executar o cmdlet seguinte:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Isto pode demorar alguns minutos, uma vez que que mostra basicamente está a implementar a configuração para o serviço de solicitação DSC alojado.

Depois de compilar a configuração, pode obter as informações da tarefa utilizando o PowerShell (Get-AzureRmAutomationDscCompilationJob) ou através de do [portal do Azure](https://portal.azure.com/).

![Obter tarefa](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Tem agora com êxito publicada e carregado sua configuração DSC para DSC de automatização.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Passo 4: Máquinas incorporadas para DSC de automatização
>[AZURE.NOTE] Uma dos pré-requisitos para concluir este cenário é que o seu máquinas com o Windows são atualizadas com a versão mais recente do WMF. Pode transferir e instalar a versão correta para a sua plataforma a partir do [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=50395).

Agora irá criar um metaconfig para DSC será aplicada a sua nós. Para ter êxito com o seguinte, é necessário obter o URL de ponto final e a chave primária para a sua conta de automatização selecionada no Azure. Pode encontrar estas valores em **teclas** pá a **todas as definições** da conta de automatização.

![Valores chave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Neste exemplo, tem de um servidor físico do Windows Server 2012 R2 que pretende proteger através de recuperação de sites.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Verificar a existência de quaisquer pendentes operações de mudar o nome de ficheiro no registo

Antes de começar associar o servidor o ponto final de automatização DSC, recomendamos que consulte de quaisquer pendentes operações de mudar o nome de ficheiro no registo. Estes podem proibi-a configuração de acabamento devido a um reinício pendente.

Execute o cmdlet seguinte para confirmar que não existe nenhuma reinicialização pendentes no servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Se esta opção mostra vazia, está OK para continuar. Caso contrário, deverá resolver este problema ao reiniciar o servidor durante uma janela de manutenção.

Para aplicar a configuração no servidor, comece a integrada Scripting ambiente (ISe do PowerShell) e execute o seguinte script. Esta é, essencialmente, uma configuração local do DSC que vai indicar o motor de Gestor de configuração do Local para registe-se com o serviço de automatização DSC e obter da configuração específica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Esta configuração irão provocar o motor de Gestor de configuração do Local para se registar DSC de automatização. -Lo também irá determinar como deve operar o motor, o deve fazer se existir um desvio de configuração (ApplyAndAutoCorrect) e como deve continuar com a configuração se é necessário reiniciar.

Depois de executar este script, deverá começar o nó registar com DSC de automatização.

![Registo de nó em curso](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se voltar ao portal do Azure, pode ver que o nó recentemente registado tem agora apareciam no portal.

![Nó registada no portal](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

No servidor, pode executar o seguinte cmdlet do PowerShell para confirmar que o nó foi registado corretamente:

```powershell
Get-DscLocalConfigurationManager
```

Após a configuração foi movida e aplicada ao servidor, pode verificar esta situação ao executar o cmdlet seguinte:

```powershell
Get-DscConfigurationStatus
```

O resultado mostra que o servidor com êxito tem movido sua configuração:

![Saída](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Além disso, a configuração do serviço de mobilidade tem os suas próprias registo que pode ser encontrado em \ProgramData\ASRSetupLogs *unidade do sistema*.

Já está. Tem agora com êxito implementado e registou o serviço de mobilidade no computador que pretende proteger através de recuperação de sites. DSC irá Certifique-se de que os serviços necessários estão sempre em execução.

![Implementação com êxito](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Depois do servidor de gestão Deteta a implementação com êxito, pode configurar a protecção e activar a replicação no computador utilizando recuperação de sites.

## <a name="use-dsc-in-disconnected-environments"></a>Utilizar DSC em ambientes desligados

Se o seu máquinas não estão ligadas à Internet, ainda pode depender DSC para implementar e configurar o serviço de mobilidade nas cargas de trabalho que pretende proteger.

Pode criar uma instância o seu servidor de solicitação DSC no seu ambiente para fornecer, essencialmente, as mesmas capacidades que obtém de automatização DSC. Isto é, os clientes puxa a configuração (depois de está registado) para o ponto final DSC. No entanto, outra opção é transmitir manualmente a configuração de DSC para sua máquinas, local ou remotamente.

Tenha em atenção que neste exemplo, não existe um parâmetro adicional para o nome do computador. Os ficheiros remotos agora estão localizados numa partilha remota que deve ser acessível pelas máquinas que pretende proteger. O fim do script enacts a configuração e, em seguida, inicia para aplicar a configuração de DSC ao computador de destino.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que o módulo do PowerShell xPSDesiredStateConfiguration está instalado. Para máquinas com o Windows onde WMF 5.0 está instalado, pode instalar o módulo xPSDesiredStateConfiguration executando o seguinte cmdlet nos computadores destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Também pode transferir e guarde o módulo, caso necessite de distribuí-lo para máquinas com o Windows que tenham WMF 4.0. Execute este cmdlet num computador onde está presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Também para WMF 4.0, certifique-se de que está instalado o [Windows 8.1 atualizar KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) nos computadores.

A seguinte configuração pode ser enviada para máquinas com o Windows que tenham WMF 5.0 e WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Se pretender criar uma instância o seu servidor de solicitação DSC na sua rede para imitar as capacidades de que pode obter a partir de automatização DSC empresarial, consulte o artigo [configurar o servidor DSC web solicitação](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcional: Implementar uma configuração DSC utilizando um modelo de Gestor de recursos do Azure

Este artigo tem aborda como pode criar sua própria configuração DSC automaticamente implementar o serviço de mobilidade e de agente de VM Azure – e certifique-se de que estão a ser executados nos computadores que pretende proteger. Também temos um modelo de Gestor de recursos do Azure que irá implementar esta configuração DSC a uma conta de automatização do Azure nova ou existente. O modelo irá utilizar parâmetros de entrada para criar os elementos de automatização que irão conter as variáveis para o seu ambiente.

Depois de implementar o modelo, pode simplesmente consultar para o passo 4 neste guia para incorporada nos computadores.

O modelo será faça o seguinte:

1. Utilizar uma conta de automatização existente ou crie um novo
2. Tome parâmetros de entrada para:
    - ASRRemoteFile – a localização onde armazenou a configuração do serviço de mobilidade
    - ASRPassphrase – a localização onde armazenou o ficheiro passphrase.txt
    - ASRCSEndpoint – o endereço IP do seu servidor de gestão
3. Importar o módulo do PowerShell xPSDesiredStateConfiguration
4. Criar e compilar a configuração de DSC

Os passos anteriores provocará na ordem correta, para que possa começar a ativação seu máquinas para protecção.

O modelo, com instruções para a implementação, encontra-se no [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Implemente o modelo utilizando o PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Próximos passos

Depois de implementar os agentes de serviço de mobilidade, pode [Ativar a replicação](site-recovery-vmware-to-azure.md#step-6-replicate-applications) para as máquinas virtuais.
