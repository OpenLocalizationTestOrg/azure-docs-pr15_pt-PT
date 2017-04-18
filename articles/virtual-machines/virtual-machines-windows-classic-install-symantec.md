<properties
    pageTitle="Instalar o Symantec Endpoint Protection numa VM | Microsoft Azure"
    description="Saiba como instalar e configurar a extensão de segurança Symantec Endpoint Protection uma VM Azure nova ou existente criados com o modelo de implementação clássica."
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

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artigo mostra-lhe como instalar e configurar o cliente de Symantec Endpoint Protection numa máquina virtual existente (VM) com o Windows Server. Este é o cliente completo, que inclui a serviços como o vírus e spyware proteção, firewall e prevenção de intrusos. O cliente é instalado como uma extensão de segurança ao utilizar o agente VM.

Se tiver uma subscrição existente a partir do Symantec para uma solução no local, pode utilizá-lo para proteger a sua máquinas virtuais Azure. Se não tiver a um cliente ainda, pode inscrever-se para uma subscrição de avaliação. Para mais informações sobre esta solução, consulte o artigo [Symantec Endpoint Protection plataforma Azure da Microsoft][Symantec]. Esta página também tem ligações para informações de licenciamento e instruções para instalar o cliente se já for um cliente Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar o Symantec Endpoint Protection numa VM existente

Antes de começar, precisa do seguinte:

- O módulo Azure PowerShell, a versão 0.8.2 ou posterior, no seu computador de trabalho. Pode verificar a versão do Azure PowerShell, que tem instalada com o **azure Get-módulo | versão Formatar tabela** comando. Para obter instruções e uma ligação para a versão mais recente, consulte o artigo [como instalar e configurar o Azure PowerShell][PS]. Inicie sessão na sua subscrição Azure através de `Add-AzureAccount`.

- O agente de VM em execução no Azure Virtual Machine.

Em primeiro lugar, certifique-se de que o agente VM já está instalado no computador virtual. Introduza o nome do serviço na nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos numa linha de comandos do Azure PowerShell nível de administrador. Substituir tudo dentro de aspas, incluindo o < e > carateres.

> [AZURE.TIP] Se não sabe o serviço na nuvem e os nomes de máquina virtual, execute **Get-AzureVM** para uma lista de nomes para todas as máquinas virtuais na sua subscrição atual.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se o comando de **escrita anfitrião** apresenta **Verdadeiro**, o agente VM está instalado. Se for apresentada **Falso**, consulte as instruções e uma ligação para a transferência do blogue do Azure publicar [VM agente e extensões - parte 2][Agent].

Se o agente VM estiver instalado, execute estes comandos para instalar o agente Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Para verificar que a extensão de segurança Symantec ter sido instalada e está atualizada:

1.  Inicie sessão no computador virtual. Para obter instruções, consulte o artigo [como iniciar sessão numa máquina Virtual a executar o Windows Server][Logon].
2.  Para o Windows Server 2008 R2, clique em **Iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou Windows Server 2012 R2, a partir do ecrã Iniciar, escreva **Symantec**e, em seguida, clique em **Symantec Endpoint Protection**.
3.  No separador **Estado** da janela do **Estado Symantec Endpoint Protection** , aplicar atualizações ou reiniciar caso seja necessário.

## <a name="additional-resources"></a>Recursos adicionais

[Como iniciar sessão numa máquina de Virtual com o Windows Server][Logon]

[Funcionalidades e extensões Azure VM][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493