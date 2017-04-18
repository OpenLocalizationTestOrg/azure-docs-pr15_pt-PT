<properties
    pageTitle="Repor a palavra-passe ou a configuração de ambiente de trabalho remoto numa VM Windows | Microsoft Azure"
    description="Saiba como repor uma palavra-passe da conta ou serviços de ambiente de trabalho remoto numa VM Windows utilizando o Azure portal ou Azure PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Como repor o serviço de ambiente de trabalho remoto ou a sua palavra-passe de início de sessão numa VM do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se não conseguir ligar a uma máquina de virtual do Windows (VM), pode repor a palavra-passe de administrador local ou repor a configuração de ambiente de trabalho remoto do serviço. Pode utilizar o portal do Azure ou a extensão de acesso de VM no Azure PowerShell para repor a palavra-passe. Se estiver a utilizar o PowerShell, certifique-se de que tem o módulo mais recente do PowerShell instalado no seu computador de trabalho e tiver sessão iniciada à sua subscrição do Azure. Para obter passos detalhados, saiba [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] Pode verificar a versão do PowerShell que tenha instalado utilizando`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Windows VMs no modelo de implementação do Gestor de recursos

### <a name="azure-portal"></a>Portal do Azure
Selecione o seu VM ao clicar em **Procurar** > **máquinas virtuais** > *máquina virtual Windows* > **todas as definições de** > **Repor palavra-passe**. É apresentada a pá de reposição de palavra-passe:

![Página de reposição de palavra-passe](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Introduza o nome de utilizador e uma palavra-passe nova, em seguida, clique em **Guardar**. Experimente ligar à sua VM novamente.

### <a name="vmaccess-extension-and-powershell"></a>Extensão de VMAccess e PowerShell

Certifique-se de tem o Azure PowerShell 1.0 ou posterior instalado e que tem sessão iniciada numa conta utilizando o `Login-AzureRmAccount` cmdlet.

#### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**

Pode repor o nome de utilizador ou palavra-passe de administrador utilizando o comando PowerShell [AzureRmVMAccessExtension conjunto](https://msdn.microsoft.com/library/mt619447.aspx) .

Crie o administrador local as credenciais de conta utilizando o seguinte comando:

    $cred=Get-Credential

Se escrever um nome diferente que a conta atual, o seguinte comando de extensão VMAccess muda o nome de conta de administrador local, atribui a palavra-passe a essa conta e emite um evento de terminar a sessão de ambiente de trabalho remoto. Se a conta de administrador local estiver desactivada, a extensão de VMAccess a ativar.

Utilize a extensão de acesso VM para definir as credenciais novas da seguinte forma:

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Substituir `myRG`, `myVM`, `myVMAccess`e a localização com valores relevantes para a configuração.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Repor a configuração de ambiente de trabalho remoto do serviço**

Pode repor o acesso remoto para sua VM utilizando [Conjunto AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [Conjunto AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), da seguinte forma. (Substituir o `myRG`, `myVM`, `myVMAccess` e a localização com os seus próprios valores.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Ou:<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Ambos os comandos adicionam um novo agente de acesso VM com nome para a máquina virtual. Em qualquer ponto, uma VM pode ter apenas um única VM agente de acesso. Para definir o acesso VM propriedades do agente com êxito, remover o agente de acesso definido anteriormente utilizando um `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. Começar a partir do Azure PowerShell versão 1.2.2, pode evitar este passo ao utilizar `Set-AzureRmVMExtension` com um `-ForceRerun` opção. Quando utilizar `-ForceRerun`, certifique-se a utilizar o mesmo nome para o agente de acesso VM como conjunto, o comando anterior.

Se ainda não é possível ligar remotamente ao seu máquina virtual, consulte o artigo mais passos a experimentar em [ligações de resolver problemas de ambiente de trabalho remoto para uma máquina de virtual baseados no Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Windows VMs no modelo de implementação clássico

### <a name="azure-portal"></a>Portal do Azure

Para máquinas virtuais criadas utilizando o modelo clássico de implementação, pode utilizar o [Azure portal](https://portal.azure.com) para repor o serviço de ambiente de trabalho remoto. Clique em: **Procurar** > **máquinas virtuais (clássico)** > *máquina virtual Windows* > **Repor remota...**. É apresentada a página seguinte.

![Repor a página de configuração de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Também pode tentar repor o nome e palavra-passe da conta de administrador local. Clique em: **Procurar** > **máquinas virtuais (clássico)** > *máquina virtual Windows* > **todas as definições de** > **Repor palavra-passe**. É apresentada a página seguinte.

![Página de reposição de palavra-passe](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Depois de introduzir o novo nome de utilizador e palavra-passe, clique em **Guardar**.

### <a name="vmaccess-extension-and-powershell"></a>Extensão de VMAccess e PowerShell

Certifique-se de que o agente VM é instalado no computador virtual. A extensão de VMAccess não têm de ser instalados antes de poder utilizar, desde que o agente VM está disponível. Certifique-se de que o agente VM já está instalado utilizando o comando seguinte. (Substitua "myCloudService" e "myVM" aos nomes do seu serviço de nuvem e seu VM, respetivamente. Pode obter os seguintes nomes executando `Get-AzureVM` sem quaisquer parâmetros.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Se o comando de **escrita anfitrião** apresenta **Verdadeiro**, o agente VM está instalado. Se for apresentada **Falso**, consulte as instruções e uma ligação para a transferência do [agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure blogue.

Se criou a máquina virtual utilizando o portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` devolve **True**. Caso contrário, pode defini-lo ao utilizar este comando:

    $vm.GetInstance().ProvisionGuestAgent = $true

Este comando impede o erro seguinte quando estiver a executar o comando **Definir AzureVMExtension** nos próximos passos: "Aprovisionar convidado agente deve ser ativado no objeto VM antes de configurar a extensão do Access IaaS VM."

#### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**

Criar uma credencial de iniciar sessão com o nome de conta de administrador local atual e uma nova palavra-passe e, em seguida, execute o `Set-AzureVMAccessExtension` da seguinte forma.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Se escrever um nome diferente que a conta atual, a extensão VMAccess muda o nome de conta de administrador local, atribui a palavra-passe a essa conta e problemas de ambiente de trabalho remoto terminar sessão. Se a conta de administrador local estiver desactivada, a extensão de VMAccess a ativar.

Estes comandos também repor a configuração de ambiente de trabalho remoto do serviço.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Repor a configuração de ambiente de trabalho remoto do serviço**

Para repor a configuração do serviço de ambiente de trabalho remoto, execute o seguinte comando:

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

A extensão VMAccess executa duas comandos na máquina virtual:

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando permite o grupo de Firewall do Windows incorporado que permite que o tráfego de ambiente de trabalho remoto entrada, que utiliza a porta TCP 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando define o fDenyTSConnections o valor de registo para 0, permitindo-ligações de ambiente de trabalho remoto.


## <a name="next-steps"></a>Próximos passos

Se não responder a extensão de acesso do Azure VM e não conseguir para repor a palavra-passe, pode [Repor o Windows palavra-passe local offline](virtual-machines-windows-reset-local-password-without-agent.md). Este método é um processo mais avançado e requer que ligar o disco rígido virtual da VM problemático para outro VM. Siga os passos documentados neste artigo pela primeira vez e tentar apenas o método de reposição de palavra-passe offline como um último recurso.

[Funcionalidades e extensões VM Azure](virtual-machines-windows-extensions-features.md)

[Ligar a uma máquina virtual Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Resolver problemas de ligações de ambiente de trabalho remoto para uma máquina de virtual baseados no Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md)
