<properties
    pageTitle="Resolver problemas de eliminar contas do Azure armazenamento, contentores ou VHDs numa implementação clássica | Microsoft Azure"
    description="Resolver problemas de eliminar contas do Azure armazenamento, contentores ou VHDs numa implementação clássica"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Resolver problemas de eliminar contas do Azure armazenamento, contentores ou VHDs numa implementação clássica

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Poderá receber erros ao tentar eliminar a conta de armazenamento Azure, contentor ou VHD na do [Azure portal](https://portal.azure.com/) ou o [Azure portal clássica](https://manage.windowsazure.com/). Problemas que podem ser causados por seguintes circunstâncias:

-   Quando elimina uma VM, o disco e VHD não são automaticamente eliminados. Que poderá ser o motivo para falha de eliminação de conta de armazenamento. Vamos não elimina o disco para que possa utilizar o disco para montagem VM outra.

-   Ainda é uma locação num disco ou o blob que está associado o disco.

Se o seu problema Azure não é dirigido neste artigo, visite os fóruns Azure no [MSDN e a pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema destes fóruns ou ao @AzureSupport no Twitter. Além disso, pode preencher um pedido de suporte Azure ao selecionar a **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomas

A secção seguinte lista os erros comuns que poderá receber ao tentar eliminar as contas do Azure armazenamento, contentores ou VHDs.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Cenário 1: Não é possível eliminar uma conta de armazenamento

Quando navegar para a conta de armazenamento no [Azure portal](https://portal.azure.com/) ou [portal clássica Azure](https://manage.windowsazure.com/) e selecionar, **Eliminar**, poderá ver a seguinte mensagem de erro:

*Conta de armazenamento StorageAccountName contém VM imagens. Certifique-se de que estas imagens VM são removidas antes de o eliminar esta conta de armazenamento.*

Também poderá ver este erro:

**No Azure portal**:

*Ocorreu uma falha ao eliminar a conta de armazenamento < vm armazenamento conta nome--->. Não é possível eliminar a conta de armazenamento < vm armazenamento conta nome--->: ' conta de armazenamento < vm armazenamento conta nome---> tem algumas imagem (NS) ativa e/ou discos. Certifique-se de que estes imagem (NS) e/ou discos são removidos antes de o eliminar esta conta de armazenamento.'.*

**No Azure clássica portal**:

*Conta de armazenamento < vm armazenamento conta nome---> tem algumas imagem (NS) ativa e/ou discos, por exemplo, xxxxxxxxx-xxxxxxxxx-the-209490240936090599. Certifique-se de que estes imagem (NS) e/ou discos são removidos antes de o eliminar esta conta de armazenamento.*

Ou

**No Azure portal**:

Conta de armazenamento *< vm armazenamento conta nome---> tem 1 recipiente quais têm uma imagem ativa e/ou artefactos de disco. Certifique-se de que essas artefactos são removidos do repositório de imagem antes de o eliminar esta conta de armazenamento*.

**No Azure clássica portal**:

Conta de armazenamento de falha ao submeter *< vm armazenamento conta nome---> tem 1 recipiente quais têm uma imagem ativa e/ou artefactos de disco. Certifique-se de que essas artefactos são removidos do repositório de imagem antes de o eliminar esta conta de armazenamento. Quando tentar eliminar uma conta de armazenamento e existem discos ainda ativos associados, verá uma mensagem a informar existem discos ativos que precisam de ser eliminada*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Cenário 2: Não é possível eliminar um contentor

Quando tenta eliminar o contentor de armazenamento, poderá ver a seguinte mensagem de erro:

*Falha ao eliminar o contentor de armazenamento <container name>. Erro: ' momento não existe uma locação no contentor e foi especificado nenhum ID de locação financeira no pedido de*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Cenário 3: Não é possível eliminar um VHD

Depois de eliminar uma VM e, em seguida, tentar eliminar os blobs para os VHDs associados, poderá receber a seguinte mensagem:

*Ocorreu uma falha ao eliminar blob ' caminho/XXXXXX-XXXXXX-SO-1447379084699.vhd'. Erro: ' momento não existe uma locação no blob e nenhum ID locação financeira foi especificada no pedido.*

## <a name="solution"></a>Solução
Para resolver os problemas mais comuns, experimente o seguinte método:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Passo 1: Eliminar quaisquer discos SO que estão a impedir a eliminação da conta de armazenamento, contentor ou VHD

1. Mudar para o [portal clássica Azure](https://manage.windowsazure.com/).
2. Selecione **máquina VIRTUAL** > **discos**.

    ![Imagem de discos em máquinas virtuais no Azure portal clássica.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Localize os discos que estão associados a conta de armazenamento, contentor ou VHD que pretende eliminar. Ao selecionar a localização do disco, irá encontrar a conta de armazenamento associado, contentor ou VHD.

    ![Imagem que mostra informações sobre a localização para discos no Azure portal clássico](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Confirme que não VM está listado no campo **anexados a** dos discos e, em seguida, elimine os discos.

    > [AZURE.NOTE] Se um disco estiver ligado a uma VM, não poderá eliminá-la. Discos são desligados modo assíncrono a partir de uma VM eliminada. Pode demorar alguns minutos depois da VM é eliminada para este campo limpar.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Passo 2: Eliminar quaisquer imagens VM que estão a impedir a eliminação da conta de armazenamento ou contentor

1. Mudar para o [portal clássica Azure](https://manage.windowsazure.com/).
2. Selecione **máquina VIRTUAL** > **imagens**e, em seguida, elimine as imagens que estão associadas a conta de armazenamento, contentor ou VHD.

    Após esta ação, experimente eliminar a conta de armazenamento, contentor ou VHD novamente.

> [AZURE.WARNING] Certifique-se de que agregar nada que pretende guardar antes de eliminar a conta. Não é possível restaurar uma conta de armazenamento eliminado ou obter qualquer conteúdo que continha antes da eliminação. Isto também se aplica para qualquer recursos na conta: Quando elimina um VHD, blob, tabela, fila ou ficheiro, será eliminado permanentemente. Certifique-se de que o recurso não é utilizado.

## <a name="about-the-stopped-deallocated-status"></a>Sobre o estado de parado (desatribuído)

VMs que foram criados no modelo de implementação clássica e que tenham sido retidas terão o estado de **parado (desatribuído)** no [portal do Azure](https://portal.azure.com/) ou [Azure portal clássica](https://manage.windowsazure.com/).

**Portal clássica azure**:

![Parado (Deallocated) Estado para VMs no Azure portal.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure portal**:

![Parado (desatribuído) Estado para VMs no Azure portal clássico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Um Estado "Parado (desatribuído)" liberta os recursos do computador, como o CPU, memória e rede. Discos, no entanto, são ainda mantidos para que pode rapidamente criar novamente a VM se for necessário. Estes discos são criados na parte superior de VHDs, que são cópias pelo armazenamento Azure. A conta de armazenamento tem estes VHDs e os discos ter concessões nesses VHDs.

## <a name="next-steps"></a>Próximos passos

- [Eliminar uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
- [Como para inserir uma quebra locação bloqueada de armazenamento de Blobs do Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
