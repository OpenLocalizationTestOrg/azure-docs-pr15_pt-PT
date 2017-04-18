<properties
    pageTitle="Configurar sempre no grupo de disponibilidade no Azure VM com PowerShell"
    description="Neste tutorial utiliza recursos criados com o modelo clássico de implementação e utiliza o PowerShell para criar uma sempre no grupo de disponibilidade no Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Configurar sempre no grupo de disponibilidade no Azure VM com PowerShell

> [AZURE.SELECTOR]
- [Gestor de recursos: modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gestor de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clássico: IU](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure máquinas virtuais (VMs) pode ajudar os administradores a base de dados para implementar o inferior o custo de um elevada disponibilidade do sistema do SQL Server. Este tutorial mostra-lhe como implementar um grupo de disponibilidade com o SQL Server sempre no fim-a-ponto dentro de um ambiente do Azure. No fim do tutorial, irá consistir a solução do SQL Server sempre no Azure os seguintes elementos:

- Uma rede virtual que contém vários sub-redes, incluindo um front-end e uma sub-rede back-end

- Controlador de domínio com um domínio do Active Directory (AD)

- Duas SQL Server VMs implementado à sub-rede back-end e aderido ao domínio de AD

- Um cluster WSFC 3 nó com o modelo de quórum maioria de nós

- Um grupo de disponibilidade com duas réplicas síncrono consolidação de uma base de dados de disponibilidade

Este cenário é escolhido para sua simplificar, não para sua eficácia de custo ou outros fatores no Azure. Por exemplo, pode minimizar o número de VMs para um grupo de disponibilidade de duas réplica para poder guardar num cluster de horas no Azure utilizando o controlador de domínio como o fé para partilhar o ficheiro quórum num cluster de WSFC nós de 2. Este método reduz a contagem VM por um partir da configuração acima.

Neste tutorial destina-se para mostrar-lhe os passos necessários para configurar a solução descrita acima sem ter de elaboração dos detalhes de cada passo. Por conseguinte, em vez de mostrando-lhe os passos de configuração de interface gráfica, utiliza PowerShell scripting para orientá-lo rapidamente através de cada passo. Assume que o seguinte procedimento:

- Já tem uma conta Azure com a subscrição de máquina virtual.

- Instalou os [cmdlets do Azure PowerShell](../powershell-install-configure.md).

- Já tem uma compreensão sólida sempre no disponibilidade dos grupos de para soluções no local. Para mais informações, consulte o artigo [Sempre no grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Ligar à sua subscrição do Azure e crie a rede Virtual

1. Numa janela do PowerShell no seu computador local, importar o módulo Azure, transferir um ficheiro de definições de publicação para o seu computador e ligar a sua sessão de PowerShell à sua subscrição do Azure importando as definições de publicação transferidas.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    O comando **Get-AzurePublishgSettingsFile** gera automaticamente um de gestão de certificado com Azure transferências-lo para o seu computador. Num browser será aberto automaticamente e lhe for pedido para introduzir as credenciais da conta Microsoft para a sua subscrição Azure. O ficheiro transferido **.publishsettings** contém todas as informações necessárias para gerir a sua subscrição do Azure. Depois de guardar este ficheiro para um diretório local, importá-lo utilizando o comando **Importar AzurePublishSettingsFile** .

    >[AZURE.NOTE] O ficheiro publishsettings contém as suas credenciais (não codificadas) que são utilizadas para administrar a sua subscrições Azure e serviços. O procedimento recomendado de segurança este ficheiro é armazená-lo temporariamente fora da sua directórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, elimine-o quando a importação estiver concluída. Acesso ao ficheiro de publishsettings obter um utilizador malicioso pode editar, criar e eliminar os seus serviços Azure.

1. Defina uma série de variáveis que irá utilizar para criar a sua infraestrutura de TI nuvem.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Tenha em atenção o seguinte procedimento para se certificar de que os seus comandos serão bem sucedida mais tarde para:

    - Variáveis **$storageAccountName** e **$dcServiceName** tem de ser exclusivos porque são utilizadas para identificar o cloud armazenamento conta e na nuvem servidor, respetivamente, na internet.

    - Nomes especificados para variáveis **$affinityGroupName** e **$virtualNetworkName** estão configurados no documento de configuração de rede virtual que irá utilizar mais tarde.

    - **$sqlImageName** Especifica o nome atualizado da imagem VM que contém o SQL Server 2012 Service Pack 1 Enterprise Edition.

    - Para simplificar, **Contoso! 000** é a mesma palavra-passe utilizada em toda a iniciação inteira.

1. Crie um grupo de afinidade.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Crie uma rede virtual ao importar um ficheiro de configuração.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    O ficheiro de configuração contém o documento XML seguinte. Em resumo, especifica uma rede virtual denominado **ContosoNET** no grupo afinidade denominado **ContosoAG**e tem de espaço de endereços **10.10.0.0/16** e tem duas sub-redes, **10.10.1.0/24** e **10.10.2.0/24**, que são sub-rede parte da frente e de trás sub-rede, respetivamente. A parte da frente sub-rede é onde pode colocar aplicações de cliente como o Microsoft SharePoint e a sub-rede anterior é onde irá colocar a VMs de servidor de SQL. Se alterar as variáveis **$affinityGroupName** e **$virtualNetworkName** anterior, também tem de alterar os nomes correspondentes abaixo.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. Crie uma conta de armazenamento associado ao grupo afinidade criado e defini-lo como a conta de armazenamento atual na sua subscrição.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Crie o servidor do Centro de dados no novo conjunto de serviço e disponibilidade de nuvem.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Esta série de comandos encaminhados efetue as seguintes coisas:

    - **Novo AzureVMConfig** cria uma configuração VM.

    - **Adicionar AzureProvisioningConfig** dá os parâmetros de configuração de um programa autónomo Windows server.

    - **Adicionar AzureDataDisk** adiciona o disco de dados que irá utilizar para armazenar dados do Active Directory, com a opção definida como nenhum de colocação em cache.

    - **Novo AzureVM** cria um novo serviço de nuvem e cria a nova VM do Azure no novo serviço em nuvem.

1. Aguarde que a nova VM ser totalmente aprovisionado e transferir o ficheiro de ambiente de trabalho remoto para o seu diretório de trabalho. Uma vez que a nova VM do Azure demora muito tempo a aprovisionar, do enquanto Repetir continuamente continua consultar a nova VM até estar pronto para ser utilizado.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

O servidor do Centro de dados agora está aprovisionado com êxito. Em seguida, irá configurar o domínio do Active Directory neste servidor do Centro de dados. Deixe a janela do PowerShell aberta no seu computador local. Irá utilizá-la novamente mais tarde para criar os dois VMs de servidor de SQL.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio

1. Ligar ao servidor CC iniciando o ficheiro de ambiente de trabalho remoto. Utilizar o administrador de máquina AzureAdmin de nome de utilizador e palavra-passe **Contoso! 000**, que especificou quando criar a nova VM.

1. Abra uma janela do PowerShell no modo de administrador.

1. Execute o seguinte **DCPROMO. EXE** comando para configurar o domínio **corp.contoso.com** , com os directórios de dados na unidade M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Assim que o comando for concluída, a VM reinicia automaticamente.

1. Ligar-se para o servidor de CC novamente ao iniciar o ficheiro de ambiente de trabalho remoto. Desta vez, inicie sessão como **CORP\Administrator**.

1. Abra uma janela do PowerShell no modo de administrador e importar o módulo do Active Directory PowerShell utilizando o seguinte comando:

        Import-Module ActiveDirectory

1. Execute os seguintes comandos para adicionar três utilizadores para o domínio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** é utilizada para configurar tudo relacionadas com instâncias de serviço do SQL Server, WSFC cluster e o grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são utilizados como as contas de serviço do SQL Server para as duas VMs de servidor de SQL.

1. Em seguida, execute os seguintes comandos para dar **CORP\Install** as permissões para criar objectos do computador no domínio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    GUID especificado acima é o GUID para o tipo de objeto do computador. A conta **CORP\Install** tem a permissão de **Ler todas as propriedades** e **Criar objectos de computador** para criar os objetos ativo direta para o cluster WSFC. A permissão de **Ler todas as propriedades** já é dado a CORP\Install por predefinição, pelo que não necessita de conceder-lhe explicitamente. Para mais informações sobre permissões necessárias para criar o cluster WSFC, consulte o artigo [Guia do activação pós-falha Cluster passo a passo: Configurar contas no Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Agora que tiver terminado a configuração do Active Directory e os objetos de utilizador, irá criar duas SQL Server VMs e associá-los neste domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs de servidor SQL

1. Continue a utilizar a janela de PowerShell que esteja aberta no seu computador local. Defina as seguintes variáveis adicionais:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    O endereço IP **10.10.0.4** é normalmente atribuída para a primeira VM criar na sub-rede **10.10.0.0/16** da sua rede virtual Azure. Deve verificar que este é o endereço do seu servidor de centro de dados executando **IPCONFIG**.

1. Execute os seguintes comandos encaminhados para criar a primeira VM no cluster WSFC, com o nome **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Tenha em atenção o seguinte relativamente ao comando acima:

    - **Novo AzureVMConfig** cria uma configuração de VM com o nome do conjunto de disponibilidade pretendida. Os VMs subsequentes serão criados com o mesmo nome do conjunto de disponibilidade para que estes são associadas ao mesmo conjunto de disponibilidade.

    - **Adicionar AzureProvisioningConfig** associa a VM para o domínio do Active Directory que criou.

    - **Conjunto AzureSubnet** coloca a VM na sub-rede anterior.

    - **Novo AzureVM** cria um novo serviço de nuvem e cria a nova VM do Azure no novo serviço em nuvem. O parâmetro de **DnsSettings** Especifica que o servidor de DNS para os servidores de novo serviço em nuvem tem o IP endereço **10.10.0.4**, que é o endereço IP do servidor de centro de dados. Este parâmetro é necessário para ativar os novos VMs no serviço de nuvem aderir ao domínio do Active Directory com êxito. Sem este parâmetro, tem de definir manualmente as definições de IPv4 na sua VM para utilizar o servidor do Centro de dados como o servidor DNS primário depois da VM está aprovisionada e, em seguida, aderir a VM ao domínio do Active Directory.

1. Execute os seguintes comandos encaminhados para criar VMs de servidor SQL, com o nome **ContosoSQL1** e **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Tenha em atenção o seguinte relativamente aos comandos acima:

    - **Novo AzureVMConfig** utiliza o mesmo nome do conjunto de disponibilidade como o servidor do Centro de dados e utiliza a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na Galeria de máquina virtual. Também define o disco do sistema operativo a leitura de colocação em cache apenas (sem escrita em cache). É recomendado que migrar os ficheiros de base de dados para um disco de dados separado anexar a VM e configurá-lo com sem leitura ou escrita em cache. No entanto, o melhor seguinte é remover a cache de escrita no disco do sistema operativo, uma vez que não é possível remover colocação em cache no disco do sistema operativo.

    - **Adicionar AzureProvisioningConfig** associa a VM para o domínio do Active Directory que criou.

    - **Conjunto AzureSubnet** coloca a VM na sub-rede anterior.

    - **Adicionar AzureEndpoint** Adiciona pontos finais de acesso para que as aplicações de cliente podem aceder a estes instâncias de serviços do SQL Server na internet. Portas diferentes são atribuídas a ContosoSQL1 e ContosoSQL2.

    - **Novo AzureVM** cria a nova VM de servidor do SQL no serviço de nuvem do mesmo como ContosoQuorum. Tem de colocar os VMs no serviço na nuvem mesmo se pretende que sejam no mesmo conjunto de disponibilidade.

1. Aguarde para cada VM ser totalmente aprovisionado e transferir o ficheiro de ambiente de trabalho remoto para o seu diretório de trabalho. O para a ligação percorre as três VMs novos e executa os comandos entre parênteses curvos nível superiores para cada um deles.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Agora configurações para o VMs de servidor SQL e em execução, mas são instalados com o SQL Server com as opções predefinidas.

## <a name="initialize-the-wsfc-cluster-vms"></a>Iniciar os VMs Cluster WSFC

Nesta secção, tem de modificar os servidores de três que irá utilizar no WSFC cluster e a instalação do SQL Server. Especificamente:

- (Todos os servidores) Tem de instalar a funcionalidade **Clusters activação pós-falha** .

- (Todos os servidores) Tem de adicionar **CORP\Install** como o **administrador**do computador.

- (ContosoSQL1 e ContosoSQL2 apenas) Tem de adicionar **CORP\Install** como uma função **sysadmin** na base de dados predefinido.

- (ContosoSQL1 e ContosoSQL2 apenas) É necessário adicionar **NT\Sistema** como um início de sessão com as seguintes permissões:

    - Alterar qualquer grupo de disponibilidade

    - Ligar SQL

    - Ver o estado do servidor

- (ContosoSQL1 e ContosoSQL2 apenas) O protocolo **TCP** já está activado a VM do SQL Server. No entanto, ainda precisar de abrir a firewall para acesso remoto do SQL Server.

Agora, está pronto para começar. Começando por **ContosoQuorum**, siga os passos abaixo:

1. Ligar a **ContosoQuorum** iniciando os ficheiros de ambiente de trabalho remotos. Utilizar o administrador de máquina **AzureAdmin** de nome de utilizador e palavra-passe **Contoso! 000**, que especificou quando criar os VMs.

1. Certifique-se de que os computadores tem sido associados com êxito ao **corp.contoso.com**.

1. Aguarde que a instalação do SQL Server concluir as tarefas de inicialização automatizado antes de continuar a executar.

1. Abra uma janela do PowerShell no modo de administrador.

1. Instale a funcionalidade clusters de activação pós-falha do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Adicione **CORP\Install** como administrador local.

        net localgroup administrators "CORP\Install" /Add

1. Termine ContosoQuorum sessão. Terminar com este servidor agora.

        logoff.exe

Em seguida, iniciar **ContosoSQL1** e **ContosoSQL2**. Siga os passos abaixo, que são idênticos para ambos os VMs de servidor de SQL.

1. Ligar a de duas VMs de servidor do SQL por iniciar os ficheiros de ambiente de trabalho remotos. Utilizar o administrador de máquina **AzureAdmin** de nome de utilizador e palavra-passe **Contoso! 000**, que especificou quando criar os VMs.

1. Certifique-se de que os computadores tem sido associados com êxito ao **corp.contoso.com**.

1. Aguarde que a instalação do SQL Server concluir as tarefas de inicialização automatizado antes de continuar a executar.

1. Abra uma janela do PowerShell no modo de administrador.

1. Instale a funcionalidade clusters de activação pós-falha do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Adicionar **CORP\Install** como administrador local

        net localgroup administrators "CORP\Install" /Add

1. Importe o fornecedor de PowerShell do SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Adicione **CORP\Install** como a função sysadmin para a instância do SQL Server predefinida.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Adicionar **NT\Sistema** como um início de sessão com as três permissões descrito acima.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Abra a firewall para acesso remoto do SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Termine ambas as VMs sessão.

        logoff.exe

Por fim, está pronto para configurar o grupo de disponibilidade. Irá utilizar o fornecedor do SQL Server PowerShell para realizar todo o trabalho no **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade

1. Ligar a **ContosoSQL1** novamente iniciando os ficheiros de ambiente de trabalho remotos. Em vez de início de sessão utilizando a conta de computador, inicie sessão com **CORP\Install**.

1. Abra uma janela do PowerShell no modo de administrador.

1. Defina as seguintes variáveis:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. Importar o fornecedor do PowerShell do SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Altere a conta de serviço do SQL Server para ContosoSQL1 para CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Altere a conta de serviço do SQL Server para ContosoSQL2 para CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Transferir **CreateAzureFailoverCluster.ps1** a partir de [Criar Cluster de WSFC para sempre no disponibilidade grupos no Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o local directório de trabalho. Irá utilizar este script para o ajudar a criar um cluster WSFC funcional. Para obter informações importantes sobre como WSFC interage com a rede Azure, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

1. Mudar para o seu diretório de trabalho e criar WSFC cluster com o script transferido.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Ative sempre no grupos de disponibilidade para instâncias do SQL Server predefinido no **ContosoSQL1** e **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Criar um diretório de cópia de segurança e conceder permissões para as contas de serviço do SQL Server. Irá utilizar este diretório para preparar a base de dados disponibilidade a réplica secundária.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Criar uma base de dados no **ContosoSQL1** denominado **MyDB1**, levar uma cópia de segurança completa e uma cópia de segurança do registo e restaurá-las no **ContosoSQL2** com a opção **Com NORECOVERY** .

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Crie a disponibilidade pontos finais de grupo na VMs de servidor SQL e defina as permissões adequadas nos pontos finais.

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Crie réplicas disponibilidade.

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. Por fim, crie o grupo de disponibilidade e aderir a réplica secundária ao grupo de disponibilidade.

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>Próximos passos
Tiver agora com êxito implementado SQL Server sempre no através da criação de um grupo de disponibilidade no Azure. Para configurar uma escuta para este grupo de disponibilidade, consulte o artigo [Configurar uma escuta ILB para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Para obter outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
