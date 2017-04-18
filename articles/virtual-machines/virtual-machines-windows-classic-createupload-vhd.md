<properties
    pageTitle="Criar e carregar uma imagem VM através do Powershell | Microsoft Azure"
    description="Saiba como criar e carregar uma imagem Windows Server GRG (VHD) utilizando o modelo de implementação clássica e Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Criar e carregar um VHD de servidor do Windows para o Azure

Este artigo mostra-lhe como carregar a sua própria imagem VM GRG como um disco rígido virtual (VHD) para utilizá-lo para criar máquinas virtuais. Para obter mais detalhes sobre discos e VHDs no Microsoft Azure, consulte o artigo [sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Também pode [carregar](virtual-machines-windows-upload-image.md) uma máquina de virtual utilizando o modelo de Gestor de recursos. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem:

- **Azure uma subscrição** - se não tiver uma, pode [Abrir uma conta Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - tiver o módulo do Microsoft Azure PowerShell instalado e configurado para utilizar a sua subscrição. 

- **A . Ficheiro VHD** - suportada armazenados num ficheiro. vhd e anexado a uma máquina virtual de sistema operativo do Windows. Verifique se as funções de servidor a executar no VHD são suportadas pelo mesmo. Para mais informações, consulte o artigo [Suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.IMPORTANT] O formato VHDX não é suportado no Microsoft Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o [cmdlet VHD converter](http://technet.microsoft.com/library/hh848454.aspx). Para obter detalhes, consulte o artigo este [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Passo 1: Preparação VHD 

Antes de carregar o VHD para Azure, necessita de ser generalized utilizando a ferramenta de Sysprep. Isto prepara VHD para ser utilizado como uma imagem. Para obter detalhes sobre Sysprep, consulte o artigo [como utilizar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). Criar cópias de segurança a VM antes de executar Sysprep.

A partir de máquina virtual que foi instalado o sistema operativo, conclua o seguinte procedimento:

1. Inicie sessão no sistema operativo.

2. Abra uma janela de linha de comandos como administrador. Alterar o directório para **%WINDIR%\system32\sysprep.**e, em seguida, execute `sysprep.exe`.

    ![Abra uma janela de linha de comandos](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  É apresentada a caixa de diálogo da **Ferramenta de preparação do sistema** .

    ![Iniciar Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Na **Ferramenta de preparação do sistema**, selecione **Sistema de caixa de experiência (OOBE)** e certifique-se de que **Generalize** está selecionada.

5.  Nas **Opções de encerramento**, selecione **encerramento**.

6.  Clique em **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Passo 2: Criar uma conta de armazenamento e um contentor

É necessária uma conta de armazenamento no Azure, pelo que tem um local para carregar o ficheiro. vhd. Este passo mostra-lhe como criar uma conta ou obter a informação de que precisa de uma conta existente. Substituir as variáveis &lsaquo; parênteses &rsaquo; pelas suas próprias informações.

1. Início de sessão

        Add-AzureAccount

1. Configure a sua subscrição do Azure.

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Crie uma nova conta de armazenamento. O nome da conta de armazenamento deve ser único, 3-24 carateres. O nome pode ser qualquer combinação de letras e números. Também precisa de especificar uma localização como "Oriente-nos"
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Predefinir a nova conta de armazenamento.
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Crie um novo contentor.

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>Passo 3: Carregar o ficheiro. vhd

Utilize a [Adicionar AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) para carregar o VHD.

A partir da janela do Azure PowerShell que utilizou no passo anterior, escreva o seguinte comando e substituir as variáveis &lsaquo; parênteses &rsaquo; pelas suas próprias informações.

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Passo 4: Adicionar a imagem à sua lista de imagens personalizadas

Utilize o cmdlet [AzureVMImage adicionar](https://msdn.microsoft.com/library/mt589167.aspx) para adicionar a imagem para a lista de imagens personalizadas.

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>Próximos passos

Pode agora [criar uma VM personalizada](virtual-machines-windows-classic-createportal.md) utilizando a imagem carregada.

