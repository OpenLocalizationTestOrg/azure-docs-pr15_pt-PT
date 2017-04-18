<properties
    pageTitle="Remover servidores e desativar a proteção contra | Microsoft Azure" 
    description="Este artigo descreve como para anular o registo de servidores do cofre recuperação de sites e para desativar a proteção contra para máquinas virtuais e servidores físicos." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar a proteção contra

O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md)

## <a name="overview"></a>Descrição geral

Este artigo descreve como anular o registo de servidores do cofre a recuperação de sites e como desativar a proteção contra para máquinas virtuais protegido por recuperação de sites. 

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-vmm-server"></a>Anular o registo de um servidor VMM

Anular o registo de um servidor VMM a partir do cofre eliminando o servidor no separador **servidores** no portal do Azure recuperação de sites. Tenha em atenção que:

-  **Servidor ligado VMM**: Recomendamos anular o registo do servidor VMM quando que está ligado ao Azure. Este procedimento assegura que as definições de servidor no local VMM e os servidores VMM associados (VMM para servidores que contêm nuvens são mapeados para nuvens no servidor que pretende eliminar) são desorganizadas; corretamente. Recomendamos que só remover um servidor não ligado se existir um problema com a conectividade permanente.
- **Servidor de VMM desligado**: se o servidor VMM não está ligado ao eliminá-lo terá executar um script manualmente para executar a limpeza do. O script está disponível na [Galeria de Microsoft](http://aka.ms/asr-cleanup-script-vmm). Tenha em atenção o ID do VMM do servidor para poder concluir o processo de limpeza manual.
- **Servidor VMM cluster**: Se precisar de um servidor VMM que é implementado num cluster de anular o registo, efetue o seguinte procedimento:

    - Se o servidor do ligado, elimine o servidor VMM ligado no separador **servidores** . Para desinstalar o fornecedor no servidor, inicie sessão em todos os nós de cluster e desinstalar a partir do painel de controlo. Execute o script de limpeza referenciado na secção anterior em todos os nós passivas cluster para eliminar entradas de registo.
    - Se o servidor não está ligado terá de executar o script de limpeza em todos os nós de cluster.

### <a name="unregister-an-unconnected-vmm-server"></a>Anular o registo de um servidor VMM desligado

No servidor VMM que pretende remover:

1. Anular o registo de servidor VMM a partir do portal Azure.
2. No servidor VMM, transfira o script de limpeza.
3. Abra o PowerShell com a executar como opção do administrador para alterar a política de execução para o âmbito predefinido (LocalMachine).
4. Siga as instruções no script. 

Em servidores VMM que tenham nuvens são emparelhados com nuvens no servidor que está a remover:

1. Executar o script de limpeza e siga os passos 2 a 4.
2. Especificar o ID de VMM para o servidor de VMM que foi registado. 
3. Este script removerá as informações de registo para o servidor de VMM e nuvem emparelhar informações.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Anular o registo de um servidor de Hyper-V num site Hyper-V

Quando Azure recuperação de Site for implementada para proteger máquinas virtuais localizadas num servidor Hyper-V num site Hyper-V (com o servidor não VMM), pode anular o registo de um servidor de Hyper-V a partir do cofre da seguinte forma:

1. Desative a proteção contra para máquinas virtuais localizada no servidor Hyper-V.
2. No separador **servidores** no portal do Azure recuperação de sites, selecione o servidor > eliminar. O servidor não tem de estar ligado à Azure quando fazê-lo.
3. Execute o seguinte script limpar as definições do servidor e anular o registo do Cofre de palavras. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Deixar de proteger uma máquina de virtual Hyper-V

Se pretender deixar de proteger uma máquina de virtual Hyper-V terá de remover a proteção do mesmo. Cálculo consoante a forma como remover a proteção por poderá ter de limpar as definições de proteção manualmente no computador. 

### <a name="remove-protection"></a>Remover a proteção por

1. No separador **máquinas virtuais** das propriedades de nuvem, selecione a máquina virtual > **Remover**.
2. Na página **Confirmar a remoção de máquina de Virtual** tem duas opções:

    - **Desativar a proteção contra**— se ativar e guardar esta opção máquina virtual já não será protegida por recuperação de sites. Definições de proteção para a máquina virtual vai ser limpo automaticamente.
    - **Remover do cofre**— se selecionar esta opção máquina virtual será removida apenas o Cofre de recuperação de sites. Definições de proteção no local para a máquina virtual não serão afetadas. Terá de limpar o manualmente as definições para remover as definições de proteção e remova a máquina virtual da subscrição Azure e remova as definições de proteção que terá de limpá-los manualmente utilizando as instruções abaixo.

Se selecionar para eliminar a máquina virtual e os seus discos rígido serão removidos desde a localização de destino.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Limpar o definições de proteção manualmente (entre VMM sites)

Se tiver seleccionado **Parar de gerir a máquina virtual**, limpe manualmente as definições:

1. No servidor principal executar este script a partir da consola VMM de limpar as definições de máquina virtual principal. Na consola do VMM clique no botão do PowerShell para abrir a consola VMM PowerShell. Substitua o nome do seu máquina virtual SQLVM1.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. No servidor VMM secundário executar este script de limpar as definições de máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. No servidor VMM secundário, depois de executar o script atualizar as máquinas virtuais no servidor de anfitrião Hyper-V para que a máquina virtual secundária novamente obtém detectada na consola do VMM.
4. Os passos acima irão limpar o replicação definições único VMM servidor. Se pretender remover a replicação de máquina virtual para a máquina virtual. Terá de efetuar os passos em ambos os a página principal e secundário abaixo máquinas virtuais. Executar a abaixo script para remover a replicação e substituir SQLVM1 com o nome do seu máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Limpar o definições de proteção manualmente (entre sites VMM no local e Azure)

1. Do servidor de origem VMM executar este script de limpar as definições de máquina virtual principal:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Os passos acima irão limpar o replicação definições único VMM servidor. Depois de ter removido a replicação a partir do servidor VMM Certifique-se para remover a replicação para a máquina virtual em execução no servidor com este script anfitrião Hyper-V. Substitua o nome do seu máquina virtual e host01.contoso.com com o nome do servidor de anfitrião Hyper-V SQLVM1.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Limpar o definições de proteção manualmente (entre sites Hyper-V e Azure)

1. No Hyper-V anfitrião, servidor de origem para remover a replicação para a máquina virtual utilize este script. Substitua o nome do seu máquina virtual SQLVM1.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Deixar de proteger uma máquina de virtual VMware ou um servidor físico

Se pretender deixar de proteger uma máquina de virtual VMware ou um servidor físico terá de remover a proteção do mesmo. Cálculo consoante a forma como remover a proteção por poderá ter de limpar as definições de proteção manualmente no computador. 

### <a name="remove-protection"></a>Remover a proteção por

1. No separador **máquinas virtuais** das propriedades de nuvem, selecione a máquina virtual > **Remover**.
2. Na **Remover Máquina Virtual** Selecione uma das opções:

    - **Desativar a proteção contra (Utilize para redimensionamento de desagregação e o volume de recuperação)**, verá apenas e ativar esta opção se tiver:
        - **Resized o volume de máquina virtual**— quando a redimensiona um volume máquina virtual informações compõem um Estado de crítica. Se tal acontecer Selecione esta opção. -Desativa proteção mantendo os pontos de recuperação no Azure. Quando reactivar proteção para o computador os dados para o volume redimensionado serão transferidos para Azure.
        - **Executar uma activação pós-falha**— depois do seu ambiente testámos-se ao executar uma activação pós-falha de máquinas de virtuais VMware no local ou servidores físicos para Azure, selecione esta opção para começar a proteger novamente máquinas virtuais no local. Esta opção desativa cada máquina virtual e, em seguida, terá de voltar a proteção para os mesmos. Tenha em atenção que:
            - Desativar a máquina virtual com esta definição não afeta a máquina de virtual réplica no Azure.
            - Não desinstalar o serviço de mobilidade de máquina virtual.
    
    - **Desativar a proteção contra**— se ativar e guardar esta opção na máquina já não será protegida por recuperação de sites. Definições de proteção para o computador vai ser limpo automaticamente.
    - **Remover do cofre**— se selecionar esta opção computador será removido apenas o Cofre de recuperação de sites. Definições de proteção no local para o computador não serão afetadas. Para remover as definições no computador e para remover a máquina virtual do Azure subscrição e terá de limpar as definições ao desinstalar o serviço de mobilidade.
    
        ![Remover as opções](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















