<properties
    pageTitle="Criar um conjunto de disponibilidade VM | Microsoft Azure"
    description="Saiba como criar uma disponibilidade definido para máquinas virtuais utilizando o Azure portal ou PowerShell utilizando o modelo de implementação do Gestor de recursos."
    keywords="conjunto de disponibilidade"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade 

Quando tiver definido como através do portal, se pretender que o seu VM para fazer parte de uma disponibilidade, tem de criar a disponibilidade definida pela primeira vez.

Para mais informações sobre como criar e utilizar conjuntos de disponibilidade, consulte o artigo [Gerir a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Utilizar o portal para criar uma conjunto antes de criar o seu VMs de disponibilidade

1. No menu do centro, clique em **Procurar** e selecione **conjuntos de disponibilidade**.

2. Na **disponibilidade define pá**, clique em **Adicionar**.

    ![Captura de ecrã que mostra o botão Adicionar para criar uma nova disponibilidade definir.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. No pá **Definir criar disponibilidade** , preencha as informações para o conjunto.

    ![Captura de ecrã que mostra as informações que necessitar de introduzir para criar o conjunto de disponibilidade.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nome** - o nome deve ser constituídas por números, letras, períodos, sublinhado e traços de carateres de 1 80. O primeiro caráter tem de ser uma letra ou número. O último caráter tem de ser uma letra, o número ou o caráter de sublinhado.
    - **Domínios falhas** - domínios falhas definem o grupo de máquinas virtuais que partilham um parâmetro de origem e de rede power comuns. Por predefinição, os VMs separados em vários domínios de falhas até três e podem ser alterados para entre 1 e 3.
    - **Atualizar domínios** - atualização cinco domínios são atribuídos por predefinição e isso pode ser definidas entre 1 e 20. Atualização domínios indicam grupos de máquinas virtuais e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. Por exemplo, se vamos especificar cinco atualizar domínios, quando mais de cinco máquinas virtuais estão configuradas dentro de um conjunto único de disponibilidade, a máquina virtual sexta vão ser colocados para o mesmo domínio de actualização de como a primeira máquina virtual, o sétimo no mesmo UD como a segunda máquina virtual e assim sucessivamente. A ordem do ser reiniciado pode não sequencial, mas apenas uma atualização domínio será reiniciado uma vez.
    - **Subscrição** - selecione a subscrição para utilizar se tiver mais do que um.
    - **Grupo de recursos** - selecione um grupo de recursos existente ao clicar na seta e selecionar um grupo de recursos a partir da lista pendente para baixo. Também pode criar um novo grupo de recursos ao escrever um nome. O nome pode conter qualquer um dos seguintes carateres: letras, números, períodos, travessões, sublinhado e abertura ou parêntese de fecho. O nome não é possível terminar durante um período. Todas as VMs no grupo disponibilidade tem de ser criados no mesmo grupo de recursos, como o conjunto de disponibilidade.
    - **Localização** - selecionar uma localização a partir do menu pendente.

4. Quando terminar introduzir as informações, clique em **Criar**. Assim que tiver sido criado o grupo de disponibilidade, pode vê-lo na lista depois de atualizar o portal.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Utilizar o portal para criar uma máquina virtual e disponibilidade de definir ao mesmo tempo

Se estiver a criar uma nova VM através do portal, também pode criar uma nova disponibilidade definida para a VM enquanto cria a primeira VM no conjunto.

![Captura de ecrã que mostra o processo para criar uma nova disponibilidade definir enquanto cria a VM.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Adicionar uma nova VM a um conjunto de disponibilidade existente

Para cada adicional VM que criar que deve pertencer no conjunto, certifique-se de que criá-lo no mesmo **grupo de recursos** e, em seguida, selecione a disponibilidade existente definir no passo 3. 

![Captura de ecrã a mostrar como selecionar uma existente disponibilidade configurar para utilizar o seu VM.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Utilizar o PowerShell para criar um conjunto de disponibilidade

Este exemplo cria uma disponibilidade definir no grupo de recursos **RMResGroup** na localização **Dos e.u.a. Ocidental** . Isto tem de ser executadas antes de criar a primeira VM que será no conjunto.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Para mais informações, consulte o artigo [AzureRmAvailabilitySet novo](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Resolução de problemas

- Quando cria uma VM, se o conjunto de disponibilidade que pretende não estiver na lista pendente no portal do poderá ter criado-lo num grupo de recursos diferentes. Se não souber o grupo de recursos para a sua disponibilidade definir, aceda ao menu concentrador e clique em Procurar > disponibilidade define para ver uma lista dos seus conjuntos de disponibilidade e que grupos de recursos pertencem.


## <a name="next-steps"></a>Próximos passos

Adicione armazenamento adicional para sua VM adicionando um adicional [disco de dados](virtual-machines-windows-attach-disk-portal.md).
