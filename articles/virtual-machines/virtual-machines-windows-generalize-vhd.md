<properties
    pageTitle="Generalizar VHD com Windows | Microsoft Azure"
    description="Saiba como utilizar Sysprep generalizar uma VM do Windows para utilizar com o modelo de implementação do Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizar uma máquina de virtual do Windows utilizando o Sysprep

Esta secção mostra-lhe como generalizar virtual máquina com o Windows para ser utilizado como uma imagem. Sysprep remove todas as informações da conta pessoal, entre outras coisas e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre Sysprep, consulte o artigo [como utilizar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Certifique-se as funções de servidor a executar no computador são suportadas pelo mesmo. Para obter mais informações, consulte o artigo [Suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Se estiver a executar o Sysprep antes de carregar a sua VHD para Azure pela primeira vez, certifique-se de que tem [preparada a VM](virtual-machines-windows-prepare-for-upload-vhd-image.md) antes de executar Sysprep. 

1. Inicie sessão no computador Windows virtual.

2. Abra a janela de linha de comandos como administrador. Alterar o directório para **%WINDIR%\system32\sysprep.**e, em seguida, execute `sysprep.exe`.

3. Na caixa de diálogo **Ferramenta de preparação do sistema** , selecione **sistema introduza Out of Box experiência (OOBE)**e certifique-se de que a caixa de verificação **Generalize** está selecionada.

4. Nas **Opções de encerramento**, selecione **encerramento**.

5. Clique em **OK**.

    ![Iniciar Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Quando concluir Sysprep, encerra máquina virtual. 

## <a name="next-steps"></a>Próximos passos

- Se a VM está no local, pode agora [carregar o VHD para Azure](virtual-machines-windows-upload-image.md).
- Se já estiver a VM no Azure, pode agora [criar uma imagem a partir da VM GRG](virtual-machines-windows-capture-image.md).