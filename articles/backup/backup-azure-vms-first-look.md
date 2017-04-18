<properties
    pageTitle="Apresentação: Proteger Azure VMs com uma cópia de segurança Cofre | Microsoft Azure"
    description="Protege Azure VMs com Cofre de cópia de segurança. Tutorial explica criar cofre, registe-se VMs, criar política e proteger VMs no Azure."
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/15/2016"
    ms.author="markgal; jimpark"/>


# <a name="first-look-backing-up-azure-virtual-machines"></a>Primeiro olhar: cópias de segurança máquinas virtuais Azure

> [AZURE.SELECTOR]
- [Proteger VMs com cofre recuperação serviços](backup-azure-vms-first-look-arm.md)
- [Proteger Azure VMs com uma cópia de segurança Cofre](backup-azure-vms-first-look.md)

Neste tutorial leva-o através dos passos para criar cópias de segurança de uma máquina de virtuais Azure (VM) para uma cópia de segurança cofre no Azure. Este artigo descreve o modelo clássico ou modelo de implementação do Gestor do serviço, de cópias de segurança VMs. Se estiver interessado em cópias de uma VM para um cofre de serviços de recuperação que pertence a um grupo de recursos, consulte o artigo [primeiro olhar: proteger VMs com um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md). Para concluir com êxito neste tutorial, tem de existir estes pré-requisitos:

- Ter criado uma VM na sua subscrição do Azure.
- A VM tem conectividade ao Azure endereços IP públicos. Para obter informações adicionais, consulte [a conectividade da rede](./backup-azure-vms-prepare.md#network-connectivity).

Para agregar uma VM, existem cinco passos principais:  

![um passo](./media/backup-azure-vms-first-look/step-one.png) identificar um cofre de cópia de segurança existente ou criar uma cópia de segurança cofre. <br/>
![passo dois](./media/backup-azure-vms-first-look/step-two.png) utilizar o portal do Azure clássico para detetar e registar as máquinas virtuais. <br/>
![passo três](./media/backup-azure-vms-first-look/step-three.png) instalar o agente VM. <br/>
![passo quatro](./media/backup-azure-vms-first-look/step-four.png) criar a política para proteger as máquinas virtuais. <br/>
![passo cinco](./media/backup-azure-vms-first-look/step-five.png) executar a cópia de segurança.

![Vista de alto nível do processo de cópia de segurança VM](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

>[AZURE.NOTE] Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Gestor de recursos e clássica](../resource-manager-deployment-model.md). Neste tutorial é para utilização com os VMs que podem ser criados no portal do Azure clássica. O serviço de cópia de segurança do Azure suporta VMs com base no Gestor de recursos. Para obter detalhes sobre cópias VMs para cofre recuperação serviços, consulte o artigo [veja primeiro: proteger VMs com um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md).



## <a name="step-1---create-a-backup-vault-for-a-vm"></a>Passo 1 - criar uma cópia de segurança cofre para uma VM

Uma cópia de segurança Cofre é uma entidade que armazena todas as cópias de segurança e pontos de recuperação que foram criados ao longo do tempo. O cópia de segurança cofre também contém as políticas de cópia de segurança que são aplicadas a máquinas virtuais a ser cópias de segurança.

1. Inicie sessão no [portal Azure clássica](http://manage.windowsazure.com/).

2. No canto inferior esquerdo do Azure portal, clique em **Novo**

    ![Clique em novo menu](./media/backup-azure-vms-first-look/new-button.png)

3. No Assistente de criação rápida, clique em **Serviços de dados** > **Serviços de recuperação** > **Cofre de cópia de segurança** > **Criação rápida**.

    ![Criar cópia de segurança Cofre](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    O assistente pede-lhe o **nome** e a **região**. Se administrar mais do que uma subscrição, é apresentada uma caixa de diálogo para escolher a subscrição.

4. Para o **nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição Azure.

5. Na **região**, selecione a região geográfica para o cofre. O cofre, **tem** de ser na mesma região como as máquinas virtuais protege.

    Se não souber a região em que existe a VM, fechar este assistente e clique em **máquinas virtuais** na lista de serviços Azure. A coluna localização fornece o nome da região. Se tiver máquinas virtuais em várias regiões, crie uma cópia de segurança cofre em cada região.

6. Se existir sem diálogo **subscrição** no assistente, avance para o próximo passo. Se trabalha com múltiplas subscrições, selecione uma subscrição para associar o novo cofre cópia de segurança.

    ![Criar a notificação de alerta do Cofre](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

7. Clique em **Criar cofre**. Pode demorar algum tempo para o Cofre cópia de segurança criação de. Monitorize as notificações de estado na parte inferior do portal.

    ![Criar a notificação de alerta do Cofre](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Uma mensagem confirma que o Cofre foi criado com êxito. Estiver listado na página de **Serviços de recuperação** como **ativo**.

    ![Criar a notificação de alerta do Cofre](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

8. Na lista de cofres na página de **Serviços de recuperação** , selecione o cofre que criou para iniciar a página de **Início rápido** .

    ![Lista de cofres cópia de segurança](./media/backup-azure-vms-first-look/active-vault-demo.png)

9. Na página de **Início rápido** , clique em **Configurar** para abrir a opção de replicação de armazenamento.
    ![Lista de cofres cópia de segurança](./media/backup-azure-vms-first-look/configure-storage.png)

10. A opção de **replicação de armazenamento** , selecione a opção de replicação para o cofre.

    ![Lista de cofres cópia de segurança](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Por predefinição, o seu Cofre tem armazenamento geo redundante. Selecione armazenamento geo redundante se esta é a cópia de segurança principal. Selecione armazenamento localmente redundante se pretender que uma opção de menos dispendiosa que não se adequarem como resistente. Leia mais sobre as opções de armazenamento geo redundantes e localmente redundantes na [Descrição geral de replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md).

Depois de escolher a opção de armazenamento para o cofre, está pronto para associar a VM cofre. Para começar a associação, descobrir e registar as máquinas virtuais Azure.

## <a name="step-2---discover-and-register-azure-virtual-machines"></a>Passo 2 - descobrir e máquinas virtuais do Azure registe-se
Antes de registar a VM com cofre, execute o processo de deteção para identificar qualquer VMs novos. Isto devolve uma lista de máquinas virtuais da subscrição, juntamente com informações adicionais, como o nome do serviço na nuvem e a região.

1. Inicie sessão no [portal Azure clássico](http://manage.windowsazure.com/)

2. No portal do Azure clássico, clique em **Serviços de recuperação** para abrir a lista de serviços de recuperação cofres.
    ![Selecione a carga de trabalho](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. Na lista de cofres, selecione o cofre para agregar uma VM.

    Quando selecionar cofre, abre-se na página de **Início rápido**

4. No menu do cofre, clique em **Itens registado**.

    ![Selecione a carga de trabalho](./media/backup-azure-vms-first-look/configure-registered-items.png)

5. A partir do menu **tipo** , selecione **Azure Virtual Machine**.

    ![Selecione a carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)

6. Clique em **DISCOVER** na parte inferior da página.
    ![Descobrir o botão](./media/backup-azure-vms/discover-button-only.png)

    O processo de deteção poderá demorar alguns minutos, enquanto as máquinas virtuais estão a ser distribuídos num quadro. Existe uma notificação na parte inferior do ecrã que permite-lhe saber se está a ser executado o processo.

    ![Descobrir o VMs](./media/backup-azure-vms/discovering-vms.png)

    As alterações de notificação quando o processo estiver concluída.

    ![Deteção concluída](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Clique em **Registe-se** na parte inferior da página.
    ![Registe-se de botão](./media/backup-azure-vms-first-look/register-icon.png)

8. No menu de atalho **Registar itens** , selecione as máquinas virtuais ao qual pretende registar.

    >[AZURE.TIP] Várias máquinas virtuais pode estar registado junto ao mesmo tempo.

    É criada uma tarefa para cada máquina virtual que selecionou.

9. Clique em **Vista de tarefa** na notificação para ir para a página **tarefas** .

    ![Registe-se a tarefa](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também é apresentada na lista de itens registados, juntamente com o estado da operação de registo.

    ![Registar o estado de 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o estado é alterado para refletir o estado *registado* .

    ![Estado de registo 2](./media/backup-azure-vms/register-status02.png)

## <a name="step-3---install-the-vm-agent-on-the-virtual-machine"></a>Passo 3 - instalar o agente de VM na máquina virtual

O agente de VM Azure tem de estar instalado no computador virtual Azure para a extensão de cópia de segurança trabalhar. Se a VM foi criada a partir da Galeria Azure, o agente VM já está presente na VM. Pode avançar para [proteger a sua VMs](backup-azure-vms-first-look.md#step-4-protect-azure-virtual-machines).

Se o seu VM migrado a partir de um centro de dados no local, a VM provavelmente não possui o VM Agent instalado. Tem de instalar o agente VM na máquina virtual antes de prosseguir para proteger a VM. Para obter passos detalhados sobre como instalar o agente VM, consulte a [secção de VM agente do artigo VMs de cópia de segurança](backup-azure-vms-prepare.md#vm-agent).


## <a name="step-4---create-the-backup-policy"></a>Passo 4 - criar a política de cópia de segurança
Antes de acionar a tarefa de cópia de segurança inicial, defina agendar quando são disponibilizados instantâneos cópia de segurança. A agenda quando forem tomados instantâneos cópia de segurança e o comprimento do tempo esses instantâneos são mantidas, é a política de cópia de segurança. As informações de retenção são baseadas num esquema de cópia de segurança rotação adquiridos-pai-filho.

1. Navegue para a cópia de segurança do Cofre de palavras em **Serviços de recuperação** no portal do Azure clássica e clique em **Itens registado**.
2. Selecione **máquina de Virtual Azure** a partir do menu pendente.

    ![Selecione a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)

3. Clique em **proteger** na parte inferior da página.
    ![Clique em proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    O **Assistente de proteger itens** aparece e lista *apenas* os máquinas virtuais que estão registados e não protegidos.

    ![Configurar a protecção de escala](./media/backup-azure-vms/protect-at-scale.png)

4. Selecione as máquinas virtuais que pretende proteger.

    Se existirem duas ou mais máquinas virtuais com o mesmo nome, utilize o serviço de nuvem para distinguir entre as máquinas virtuais.

5. No menu de **proteção de configurar** seleccionar uma política existente ou criar uma nova política para proteger as máquinas virtuais que identificou.

    Novos cofres de cópia de segurança tenham uma política de predefinido associada cofre. Esta política assume diária instantâneo cada noite e o instantâneo diário é mantido para 30 dias. Cada política de cópia de segurança pode ter várias máquinas virtuais associadas. No entanto, a máquina virtual só pode ser associada uma política de cada vez.

    ![Proteger com nova política](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Uma política de cópia de segurança inclui um esquema de retenção para as cópias de segurança agendadas. Se selecionar uma política de cópia de segurança existente, não será possível modificar as opções de retenção no próximo passo.

6. Num **Intervalo de retenção** defina o âmbito diário, semanal, mensal e anual para os pontos de cópia de segurança específicos.

    ![Máquina virtual cópias de segurança com ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    Política de retenção indica o comprimento do tempo para armazenar uma cópia de segurança. Pode especificar políticas de retenção diferente com base em quando a cópia de segurança é disponibilizada.

7. Clique em **tarefas** para ver a lista de tarefas **Proteção de configurar** .

    ![Configurar a tarefa de proteção](./media/backup-azure-vms/protect-configureprotection.png)

    Agora que já estabelecidos a política, vá para o passo seguinte e execute a cópia de segurança inicial.

## <a name="step-5---initial-backup"></a>Passo 5 - cópia de segurança inicial

Assim que uma máquina virtual foi protegida com uma política, pode ver essa relação no separador **Itens protegida** . Até que a cópia de segurança inicial ocorre, o **Estado de proteção** mostra como **protegido - (pendente cópia de segurança inicial)**. Por predefinição, a primeira cópia de segurança agendada é a *cópia de segurança inicial*.

![Cópia de segurança pendentes](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para iniciar a cópia de segurança inicial agora:

1. Na página **Itens protegido** , clique em **Cópia de segurança agora** na parte inferior da página.
    ![Agora o ícone de cópia de segurança](./media/backup-azure-vms-first-look/backup-now-icon.png)

    O serviço de cópia de segurança do Azure cria uma tarefa de cópia de segurança para a operação de cópia de segurança inicial.

2. Clique no separador **tarefas** para ver a lista de tarefas.

    ![Cópia de segurança em curso](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Quando cópia de segurança inicial estiver concluída, o estado da máquina virtual no separador **Itens protegida** é *protegida*.

    ![Máquina virtual cópias de segurança com ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] Cópias de segurança máquinas virtuais são um processo local. Não é possível agregar máquinas virtuais a partir de uma região para uma cópia de segurança cofre noutra região. Sim, para cada região Azure que tenha VMs que precisam de ser cópias de segurança, pelo menos uma cofre cópia de segurança deve ser criada na região.

## <a name="next-steps"></a>Próximos passos
Agora que criou com êxito cópias uma VM, existem vários passos que podem ser de interesse. O passo mais lógico é familiarizar-se com a restaurar dados para uma VM. No entanto, existem tarefas de gestão de que irão ajudá-lo a compreender como manter os seus dados seguros e minimizar custos.

- [Gerir e monitorizar o seu máquinas virtuais](backup-azure-manage-vms.md)
- [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
- [Resolução de problemas de orientação](backup-azure-vms-troubleshoot.md)


## <a name="questions"></a>Perguntas?
Se tiver dúvidas, ou se existir qualquer funcionalidade que pretende ver incluídas, [envie-nos comentários](http://aka.ms/azurebackup_feedback).
