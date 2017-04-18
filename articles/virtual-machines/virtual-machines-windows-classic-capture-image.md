<properties
    pageTitle="Capture uma imagem de um VM do Windows Azure | Microsoft Azure"
    description="Capture uma imagem de uma máquina de virtual do Azure Windows criada com o modelo de implementação clássica."
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capture uma imagem de uma máquina de virtual do Azure Windows criada com o modelo de implementação clássica.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para obter informações de modelo de Gestor de recursos, consulte o artigo [criar uma cópia Windows VM em execução no Azure](virtual-machines-windows-vhd-copy.md).


Este artigo mostra-lhe como capturar uma máquina virtual Azure a executar o Windows para utilizá-la como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco de sistema operativo e quaisquer discos de dados que estão anexados à máquina virtual. -Não inclui as configurações de rede, pelo que terá de configurar as quando cria as outras máquinas virtuais que utilizam a imagem.

Azure armazena a imagem nas **Minhas imagens**. Este é o mesmo lugar onde estão armazenadas os imagens que carregou. Para obter detalhes sobre as imagens, consulte o artigo [sobre as imagens para máquinas virtuais](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin"></a>Antes de começar##

Estes passos partem do pressuposto de que já criou uma máquina virtual Azure e configurado o sistema operativo, incluindo quaisquer discos de dados a anexar. Se ainda não o tenha feito ainda, consulte estas instruções:

- [Criar uma máquina virtual a partir de uma imagem](virtual-machines-windows-classic-createportal.md)
- [Como anexar um disco de dados a uma máquina virtual](virtual-machines-windows-classic-attach-disk.md)
- Certifique-se que as funções de servidor são suportadas com Sysprep. Para mais informações, consulte o artigo [Suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Este processo elimina a máquina virtual original depois é capturado. 

Antes de uma imagem de uma máquina virtual Azure caputuring, recomenda-se o destino virtual machine ser cópias de segurança. Azure máquinas virtuais pode ser cópia de segurança utilizando Azure cópia de segurança. Para obter detalhes, consulte o artigo [criar uma cópia de segurança máquinas virtuais Azure](../backup/backup-azure-vms.md). Outras soluções estão disponíveis a partir de parceiros certificados. Para saber o que está disponível neste momento, procure o Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Capturar a máquina virtual

1. No [portal clássica Azure](http://manage.windowsazure.com), **ligar-se** para a máquina virtual. Para obter instruções, consulte o artigo [como iniciar sessão numa máquina de virtual com o Windows Server] [].

2.  Abra uma janela de linha de comandos como administrador.

3.  Alterar o directório para `%windir%\system32\sysprep`, e, em seguida, executar sysprep.exe.

4.  É apresentada a caixa de diálogo da **Ferramenta de preparação do sistema** . Faça o seguinte:

    - Em **Ação de limpeza do sistema**, selecione **sistema introduza Out of Box experiência (OOBE)** e certifique-se de que **Generalize** está selecionada. Para obter mais informações sobre como utilizar Sysprep, consulte o artigo [como utilizar Sysprep: uma introdução][].

    - Nas **Opções de encerramento**, selecione **encerramento**.

    - Clique em **OK**.

    ![Executar o Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep encerra máquina virtual, que altera o estado da máquina virtual no portal do Azure clássica **parado**.

8.  No portal do Azure clássico, clique em **máquinas virtuais** e selecione a máquina virtual que pretende capturar.

9.  Na barra de comandos, clique em **captura**.

    ![Capturar máquina virtual](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    É apresentada a caixa de diálogo **capturar a Máquina Virtual** .

10. Em **Nome da imagem**, escreva um nome para a nova imagem.

11. Antes de adicionar uma imagem do Windows Server ao seu conjunto de imagens personalizadas, tem de ser generalized executando o Sysprep conforme indicado nos passos anteriores. Clique em **tem a executar o Sysprep na máquina virtual** para indicar que fez.

12. Clique na marca de verificação para capturar a imagem. A nova imagem está agora disponível em **imagens**.

    ![Captura de ecrã com êxito](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Próximos passos

A imagem está pronta para ser utilizado para criar máquinas virtuais. Para fazer isto, crie uma máquina virtual utilizando o item de menu **Da Galeria** e selecionar a imagem que acabou de criar. Para obter instruções, consulte o artigo [criar uma máquina de virtual a partir de uma imagem](virtual-machines-windows-classic-createportal.md).



[Como iniciar sessão numa máquina de virtual com o Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Como utilizar Sysprep: uma introdução]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
