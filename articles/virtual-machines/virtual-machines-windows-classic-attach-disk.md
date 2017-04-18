<properties
    pageTitle="Anexar um disco a uma VM | Microsoft Azure"
    description="Anexar um disco de dados para uma máquina de virtual Windows criada com o modelo clássico de implementação e inicialização-lo."
    services="virtual-machines-windows, storage"
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
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Anexar um disco de dados para uma máquina de virtual Windows criada com o modelo clássico de implementação

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se pretender utilizar o portal de novo, consulte o artigo [como anexar um disco de dados para uma VM do Windows no portal do Azure](virtual-machines-windows-attach-disk-portal.md).

Se precisar de um disco de dados adicionais, pode anexar um disco vazio ou um disco existente com os dados a uma VM. Em ambos os casos, os discos são ficheiros. vhd que residem numa conta de armazenamento Azure. No caso de um novo disco, depois de anexar o disco, também terá a inicialização-lo para que fique pronto para ser utilizado por uma VM do Windows.

Para obter mais detalhes sobre discos, consulte o artigo [sobre discos e VHDs para máquinas virtuais](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Iniciar o disco

1. Ligar à máquina virtual. Para obter instruções, consulte o artigo [como iniciar sessão para uma máquina de virtual executar o Windows Server][logon].

2. Depois de iniciar sessão para a máquina virtual, abra o **Gestor de servidor**. No painel esquerdo, selecione o **ficheiro e serviços de armazenamento**.

    ![Abrir o Gestor de servidor](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Expandir o menu e selecione **discos**.

4. A secção de **discos** listas discos. Na maioria dos casos, terá disco 0, o disco 1 e 2 do disco. Disco 0 é o sistema operativo, disco 1 é o disco temporário e disco 2 é o disco de dados que anexou para a VM. O disco de dados novo irá listar a partição como **desconhecida**. Botão direito do rato no disco e selecione **Iniciar**.

5.  É notificado que todos os dados serão apagados quando o disco está inicializado. Clique em **Sim** para confirmar o aviso e iniciar o disco. Uma vez concluída, o partiçao irá estar listado como **GPT**. Com o botão direito do disco novamente e selecione **Novo Volume**.

6.  Conclua o assistente utilizando os valores predefinidos. Quando o assistente concluir o processo, a secção de **Volumes** listas o novo volume. O disco está agora online e pronto para armazenar dados.

    ![Volume inicializado com êxito](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] O tamanho da VM determina quantos discos pode anexar à mesma. Para obter detalhes, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Recursos adicionais

[Como desanexar um disco a partir de uma máquina de virtual do Windows](virtual-machines-windows-classic-detach-disk.md)

[Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
