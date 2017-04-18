<properties
    pageTitle="Tornar a unidade d: de uma VM um disco dados | Microsoft Azure"
    description="Descreve como alterar letras de unidade para uma VM do Windows para que possa utilizar na unidade d: como uma unidade de dados."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilizar na unidade d: como uma unidade de dados numa VM do Windows 

Se a aplicação necessitar de utilizar a unidade D para armazenar dados, siga estas instruções para utilizar uma letra de unidade diferente para o disco temporário. Nunca utilize o disco temporário para armazenar os dados que precisa para manter.

Se redimensionar ou **Parar (Deallocate)** uma máquina virtual, este poderá accionar posicionamento da máquina virtual para um novo hipervisor. Um evento de manutenção planeada ou não planeado poderá também accionar este posicionamento. Neste cenário, o disco temporário será reatribuído até à primeira letra de unidade disponível. Se tiver uma aplicação que exige na unidade d: especificamente, terá de seguir estes passos para temporariamente mover a Pagefile, anexar um novo disco de dados e atribuí-lo a letra D e, em seguida, voltar a Pagefile para a unidade temporária. Depois de concluído, Azure não terão voltar a d: se a VM move o cursor para um hipervisor diferente.

Para mais informações sobre como o Azure utiliza o disco temporário, consulte o artigo [Compreender a unidade temporária em máquinas virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Anexe o disco de dados

Em primeiro lugar, terá de anexar o disco de dados para a máquina virtual. 

- Utilizar o portal, consulte o artigo [como anexar um disco de dados no portal do Azure](virtual-machines-windows-attach-disk-portal.md)
- Utilizar o portal clássico, consulte o artigo [como anexar um disco de dados para uma máquina de virtual do Windows](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporariamente Pagefile para unidade C

1. Ligar à máquina virtual. 

2. Botão direito do rato no menu **Iniciar** e selecione **sistema**.

3. No menu do lado esquerdo, selecione **Definições avançadas do sistema**.

4. Na secção de **Desempenho** , selecione **Definições**.

5. Selecione o separador **Avançadas** .

5. Na secção de **Memória Virtual** , selecione **Alterar**.

6. Selecione a unidade **C** e, em seguida, clique em **Tamanho gerido pelo sistema** e, em seguida, clique em **Definir**.

7. Selecione a unidade **D** e, em seguida, clique em **nenhum ficheiro de paginação** e, em seguida, clique em **Definir**.

8. Clique em aplicar. Irá obter um aviso de que o computador tem de ser reiniciado para as alterações terem efeito.

9. Reinicie o computador virtual.




## <a name="change-the-drive-letters"></a>Alterar as letras de unidade 

1. Assim que a VM reinicia, inicie sessão novamente para a VM.

2. Clique no menu **Iniciar** e escreva **Diskmgmt** e prima Enter. Gestão de discos será iniciado.

3. Com o botão direito em **D**, a unidade de armazenamento temporário e selecione **Alterar letra e unidade caminhos**.

4. Em letra da unidade, selecione a unidade **G** e, em seguida, clique em **OK**. 

5. Com o botão direito no disco dados e selecione **Alterar letra e unidade caminhos**.

6. Em letra da unidade, selecione a unidade **D** e, em seguida, clique em **OK**. 

7. Com o botão direito no **G**, a unidade de armazenamento temporário e selecione **Alterar unidade de letra e caminhos**.

8. Em letra da unidade, selecione a unidade **E** e, em seguida, clique em **OK**. 

> [AZURE.NOTE] Se a sua VM tiver outras discos ou unidades, utilize o mesmo método para reatribuir as letras de unidade dos outros discos e unidades. Pretender que a configuração do disco estar:  
>- C: disco SO  
>- D: dados disco  
>- E: disco temporário



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Mover Pagefile novamente para a unidade de armazenamento temporário 

1. Botão direito do rato no menu **Iniciar** e selecione **sistema**

2. No menu do lado esquerdo, selecione **Definições avançadas do sistema**.

3. Na secção de **Desempenho** , selecione **Definições**.

4. Selecione o separador **Avançadas** .

5. Na secção de **Memória Virtual** , selecione **Alterar**.

6. Selecione a unidade de sistema operativo **C** e clique em **nenhum ficheiro de paginação** e, em seguida, clique em **Definir**.

7. Selecione a unidade de armazenamento temporário **"e"** e, em seguida, clique em **Tamanho gerido pelo sistema** e, em seguida, clique em **Definir**.

8. Clique em **Aplicar**. Irá obter um aviso de que o computador tem de ser reiniciado para as alterações terem efeito.

9. Reinicie o computador virtual.




## <a name="next-steps"></a>Próximos passos
- Pode aumentar o armazenamento disponível para o seu computador virtual ao [como anexar um disco de dados adicionais](virtual-machines-windows-attach-disk-portal.md).



