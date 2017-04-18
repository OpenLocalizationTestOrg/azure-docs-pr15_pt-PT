<properties
    pageTitle="Criar uma VM no portal do clássico | Microsoft Azure"
    description="Crie uma máquina de virtual do Windows no portal do Azure clássico."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Criar uma máquina de virtual a executar o Windows no portal do Azure clássico

> [AZURE.SELECTOR]
- [Azure portal clássico](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Implementação de clássica](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de implementação do Gestor de recursos](virtual-machines-windows-hero-tutorial.md) utilizando o **novo portal Azure**. 

Este tutorial mostra-lhe como criar uma máquina virtual Azure (VM) a executar o Windows no portal do Azure clássico. Vamos utilizar uma imagem do Windows Server como exemplo, mas que é de apenas uma das imagens muitos que Azure oferece. Tenha em atenção que as opções de imagem variam consoante a sua subscrição. Por exemplo, imagens de ambiente de trabalho do Windows poderão estar disponíveis para subscritores do MSDN.

Esta secção mostra-lhe como utilizar a opção **a partir da Galeria** no portal do Azure clássico para criar a máquina virtual. Esta opção fornece opções de configuração mais do que a opção de **Criação rápida** . Por exemplo, se pretender aderir a uma máquina virtual a uma rede virtual, terá de utilizar a opção **a partir da Galeria** .

Também pode criar VMs utilizando as [suas próprias imagens](virtual-machines-windows-classic-createupload-vhd.md). Para saber mais sobre este e outros métodos, consulte [diferentes formas de criar uma máquina de virtual do Windows](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Vídeo guiadas

Segue-se instruções deste tutorial.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Criar a máquina virtual

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar uma VM utilizando o modelo de implementação do Gestor de recursos](virtual-machines-windows-hero-tutorial.md) no novo portal do Azure. 

- Inicie sessão no computador virtual. Para obter instruções, consulte o artigo [Iniciar sessão para uma máquina de virtual com o Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Anexe um disco para armazenar dados. Pode anexar discos vazios e discos que contêm dados. Para obter instruções, consulte a [anexar um disco de dados para uma máquina de virtual Windows criado com o modelo de implementação clássica](virtual-machines-windows-classic-attach-disk.md).
