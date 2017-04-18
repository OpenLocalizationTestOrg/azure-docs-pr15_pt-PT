<properties
    pageTitle="Instalar o tendência de segurança abrangente Micro numa VM | Microsoft Azure"
    description="Este artigo descreve como instalar e configurar a segurança de tendência Micro uma VM criada com o modelo clássico de implementação do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar tendência Micro abrangente segurança como um serviço numa VM do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artigo mostra-lhe como instalar e configurar tendência Micro abrangente segurança como um serviço numa nova ou existente máquina virtual (VM) com o Windows Server. Segurança abrangente como um serviço inclui proteção antimalware, uma firewall, um sistema de prevenção de intrusos e integridade monitorização.

O cliente é instalado como uma extensão de segurança através do agente de VM. Numa nova máquina virtual, instale o agente VM juntamente com o agente de segurança abrangente. Num computador virtual existente que não tem o agente VM, tem de transferir e instalá-lo em primeiro lugar. Este artigo abrange as duas situações.

Se tiver uma subscrição existente a partir do Micro tendência para uma solução no local, pode utilizá-lo para ajudar a proteger o seu máquinas virtuais Azure. Se não tiver a um cliente ainda, pode inscrever-se para uma subscrição de avaliação. Para mais informações sobre esta solução, consulte o blogue da Micro tendência no [Microsoft Azure VM agente extensão para abrangente segurança](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalar o abrangente agente de segurança numa nova VM

O [portal clássica Azure](http://manage.windowsazure.com) permite-lhe instalar o agente de VM e a extensão de segurança de tendência Micro ao utilizar a opção **a partir da Galeria** para criar a máquina virtual. Se estiver a criar uma única máquina de virtual, utilizando o portal é uma forma fácil de adicionar proteção a partir do Micro de tendência.

Esta opção **a partir da Galeria** abre um assistente que o ajuda a configurar a máquina virtual. Utilize a última página do Assistente para instalar o agente de VM e a extensão de segurança de tendência Micro. Para obter instruções gerais, consulte o artigo [criar uma máquina virtual a executar o Windows no portal do Azure clássico](virtual-machines-windows-classic-tutorial.md). Quando recebe para a última página do assistente, faça o seguinte:

1.  Em **VM agente**, selecione **Instalar agente VM**.

2.  Em **Extensões de segurança**, selecione **Tendência Micro abrangente agente de segurança**.

    ![Instale o agente VM e o agente de segurança abrangente](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Clique na marca de verificação para criar a máquina virtual.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalar o abrangente agente de segurança numa VM existente

Para instalar o agente numa VM existente, precisa do seguinte:

- O módulo Azure PowerShell, a versão 0.8.2 ou mais recente, instalado no seu computador local. Pode verificar a versão do PowerShell Azure que tenha instalado utilizando o **azure Get-módulo | versão Formatar tabela** comando. Para obter instruções e uma ligação para a versão mais recente, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Inicie sessão na sua subscrição Azure através de `Add-AzureAccount`.

- O agente de VM instalado no computador virtual destino.

Em primeiro lugar, certifique-se de que o agente VM já está instalado. Introduza o nome do serviço na nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos numa linha de comandos do Azure PowerShell nível de administrador. Substituir tudo dentro de aspas, incluindo o < e > carateres.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se não sabe o serviço na nuvem e o nome de máquina virtual, execute **Get-AzureVM** para apresentar essas informações para todas as máquinas virtuais na sua subscrição atual.

Se o comando de **anfitrião de escrita** devolve **Verdadeiro**, o agente VM está instalado. Se devolve **Falso**, consulte as instruções e uma ligação para a transferência do blogue do Azure publicar [VM agente e extensões - parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o agente VM estiver instalado, execute estes comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Próximos passos

Bastam alguns minutos para que o agente começar a executar quando estiver instalado. Após esta ação, tem de ativar abrangente de segurança na máquina virtual para que pode ser gerido por um Gestor de segurança abrangente. Consulte o seguinte para obter instruções adicionais:

- Artigo do tendência sobre esta solução, [Instant-On nuvem segurança do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Um [exemplo de script do Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
- [Instruções](http://go.microsoft.com/fwlink/?LinkId=404099) para a amostra

## <a name="additional-resources"></a>Recursos adicionais

[Como iniciar sessão para uma máquina de virtual com o Windows Server]

[Funcionalidades e extensões de VM Azure]


<!--Link references-->
[Como iniciar sessão para uma máquina de virtual com o Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Funcionalidades e extensões de VM Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
