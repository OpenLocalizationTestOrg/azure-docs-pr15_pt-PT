<properties
    pageTitle="Resolver problemas de erros quando elimina contas do Azure armazenamento, contentores ou VHDs numa implementação Gestor de recursos | Microsoft Azure"
    description="Resolver problemas de erros quando elimina contas do Azure armazenamento, contentores ou VHDs numa implementação Gestor de recursos"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="genli"/>

# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Resolver problemas de erros quando elimina contas do Azure armazenamento, contentores ou VHDs numa implementação Gestor de recursos

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Poderá receber erros ao tentar eliminar uma conta de armazenamento Azure, contentor ou disco rígido virtual (VHD) no [portal do Azure](https://portal.azure.com). Este artigo fornece orientações resolução de problemas para ajudar a resolver o problema numa implementação do Azure o Gestor de recursos.

Se este artigo não resolver o problema de Azure, visite os fóruns Azure no [MSDN e pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema destes fóruns ou ao @AzureSupport no Twitter. Além disso, pode preencher um pedido de suporte Azure ao selecionar a **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomas

### <a name="scenario-1"></a>Cenário 1

Quando tenta eliminar um VHD com uma conta de armazenamento de uma implementação do Gestor de recursos, recebe a seguinte mensagem de erro:

**Falha ao eliminar blob 'vhds/BlobName.vhd'. Erro: Não existe atualmente uma locação no blob e nenhum ID locação financeira foi especificada no pedido.**

Este problema pode ocorrer porque uma máquina de virtual (VM) tem uma locação no VHD que está a tentar eliminar.

### <a name="scenario-2"></a>Cenário 2

Quando tenta eliminar um contentor de uma conta de armazenamento de uma implementação do Gestor de recursos, recebe a seguinte mensagem de erro:

**Falha ao eliminar armazenamento contentor 'vhds'. Erro: Não existe atualmente uma locação no contentor e nenhum ID locação financeira foi especificada no pedido.**

Este problema pode ocorrer porque o contentor tem um VHD que está bloqueado no estado de locação financeira.

### <a name="scenario-3"></a>Cenário 3

Quando tenta eliminar uma conta de armazenamento de uma implementação do Gestor de recursos, recebe a seguinte mensagem de erro:

**Ocorreu uma falha ao eliminar a conta de armazenamento 'StorageAccountName'. Erro: A conta de armazenamento não pode ser eliminada devido ao respetivos artefactos a ser utilizado.**

Este problema pode ocorrer porque a conta de armazenamento contém um VHD que se encontra no Estado locação financeira.

## <a name="solution"></a>Solução

Para resolver estes problemas, tem de identificar o VHD que está a causar o erro e a VM associada. Em seguida, desanexar VHD a partir da VM (para discos de dados) ou eliminar a VM que está a utilizar o VHD (para discos SO). Isto remove a locação VHD e permite-lhe a ser eliminada.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Passo 1: Identificar o problema VHD e a VM associada


1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **concentrador** , selecione **todos os recursos**. Vá para a conta de armazenamento que pretende eliminar e, em seguida, selecione **Blobs** > **vhds**.

    ![Captura de ecrã do portal do, com a conta de armazenamento e o contentor de "vhds" realçada](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

3. Verifique as propriedades de cada VHD no contentor. Localize o VHD que se encontra no estado **Leased** . Em seguida, determine que VM está a utilizar o VHD. Normalmente, pode determinar que VM detém o VHD ao selecionar o nome do VHD:

    - Discos SO seguem geralmente este Convenção de nomenclatura: VMNameYYYYMMDDHHMMSS.vhd
    - Discos dados seguem geralmente este Convenção de nomenclatura: VMName-AAAAMMDD-HHMMSS.vhd

    ![Captura de ecrã das informações do contentor no portal do, com o nome da VM, o estado de locação financeira de "Bloqueado" e o estado de locação financeira de "Leased" realçada](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Passo 2: Remover a locação a partir do VHD

Para eliminar a VM que está a utilizar o VHD (para discos SO):

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  No menu **concentrador** , selecione **máquinas virtuais**.
3.  Selecione a VM que detém uma locação no VHD.
4.  Certifique-se de que nada ativamente está a utilizar máquina virtual, e que já não necessita de máquina virtual.
5.  Na parte superior da pá **Detalhes VM** , selecione **Eliminar**e, em seguida, clique em **Sim** para confirmar.
6.  A VM deve ser eliminada, mas o VHD deve ser mantido. No entanto, o VHD já não deve ter uma locação no mesmo. Poderá demorar alguns minutos para arrendar a ser lançadas. Para verificar que foi publicada a locação, vá para **todos os recursos** > **Nome da conta de armazenamento** > **Blobs** > **vhds**. No painel de **Propriedades de BLOBs** , o valor de **Estado de locação financeira** deve ser **Unlocked**.

Desanexar VHD a partir da VM que está a utilizar (para discos de dados):

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  No menu **concentrador** , selecione **máquinas virtuais**.
3.  Selecione a VM que detém uma locação no VHD.
4.  Selecione **discos** pá os **Detalhes VM** .
5.  Seleccione o disco de dados que contém uma locação no VHD. Pode determinar que está anexado VHD no disco, verificando o URL do VHD.
6.  Determine com tenha a certeza de que nada ativamente está a utilizar o disco de dados.
7.  Clique em **Desanexar** pá os **Detalhes do disco** .
8.  Agora deverá ser desligado no disco do VM, e o VHD já não deve ter uma locação no mesmo. Poderá demorar alguns minutos para arrendar a ser lançadas. Para verificar que foi lançada a locação financeira, vá para **todos os recursos** > **Nome da conta de armazenamento** > **Blobs** > **vhds**. No painel de **Propriedades de BLOBs** , o valor de **Estado de locação financeira** deve ser **Unlocked**.

## <a name="what-is-a-lease"></a>O que é uma locação?

Uma locação é um cadeado que pode ser utilizado para controlar o acesso a um blob (por exemplo, um VHD). Quando um blob é dedicado, apenas os proprietários da locação podem aceder o blob. É importante uma locação pelos seguintes motivos:

-   Impede a danos nos dados se os proprietários de vários tentarem escrever à parte da mesma do blob ao mesmo tempo.
-   Impede o blob a ser eliminado, se ativamente algo é utilizá-lo (por exemplo, VM).
-   Impede a conta de armazenamento a ser eliminado, se ativamente algo é utilizá-lo (por exemplo, VM).



## <a name="next-steps"></a>Próximos passos

- [Eliminar uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
- [Como para inserir uma quebra locação bloqueada de armazenamento de Blobs do Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
