<properties 
   pageTitle="Políticas de cópia de segurança do Gestor de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como utilizar o Gestor de instantâneo StorSimple MMC snap-in para criar e gerir as políticas de cópia de segurança que controlam cópias de segurança agendadas."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Utilizar o Gestor de instantâneo StorSimple para criar e gerir políticas de cópia de segurança

## <a name="overview"></a>Descrição geral

Uma política de cópia de segurança cria uma agenda de cópia de segurança dos dados de volume localmente ou na nuvem. Quando cria uma política de cópia de segurança, também pode especificar uma política de retenção. (Pode manter um máximo de 64 instantâneos.) Para obter mais informações sobre as políticas de cópia de segurança, consulte o artigo [tipos de cópia de segurança](storsimple-what-is-snapshot-manager.md#backup-type) no [StorSimple 8000 série: uma solução de nuvem híbrido](storsimple-overview.md).

Este tutorial explica como:

- Criar uma política de cópia de segurança 
- Editar uma política de cópia de segurança 
- Eliminar uma política de cópia de segurança 

## <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança

Utilize o procedimento seguinte para criar uma nova política de cópia de segurança.

#### <a name="to-create-a-backup-policy"></a>Para criar uma política de cópia de segurança

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , com o botão direito **Políticas de cópia de segurança**e clique em **Criar a política de cópia de segurança**.

    ![Criar uma política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    É apresentada a caixa de diálogo **criar uma política** . 

    ![Criar uma política de-separador Geral](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. No separador **Geral** , preencha as seguintes informações:

   1. Na caixa de texto **nome** , escreva um nome para a política.

   2. Na caixa de texto **grupo Volume** , escreva o nome do grupo de volume associado à política.

   3. Selecione **instantâneo Local** ou **na nuvem instantâneo**.

   4. Selecione o número de instantâneos para reter. Se selecionar **todas as**, 64 instantâneos serão retidos (o máximo). 

4. Clique no separador **agenda** .

    ![Criar uma política de-separador Agenda](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. No separador **agenda** , preencha as seguintes informações: 

   1. Clique na caixa de verificação **Ativar** para agendar a próxima cópia de segurança.

   2. Em **Definições**, selecione **uma vez**, **diária**, **semanal**ou **mensal**. 

   3. Na caixa de texto **Iniciar** , clique no ícone de calendário e selecione uma data de início.

   4. Em **Definições avançadas**, pode definir agendas de repetição opcionais e uma data de fim.

   5. Clique em **OK**.

Depois de criar uma política de cópia de segurança, as seguintes informações é apresentada no painel de **resultados** :

- **Nome** – o nome da política de cópia de segurança.

- **Tipo de** – instantâneo local ou instantâneo na nuvem.

- **Grupo de volume** – o grupo de volume associado a política.

- **Retenção** – o número de instantâneos retido; o valor máximo é 64.

- **Criado** – a data em que esta política foi criada.

- **Enabled** – se a política está atualmente na prática: **Verdadeiro** indica que está em vigor; **Falso** indica que não está em vigor. 

## <a name="edit-a-backup-policy"></a>Editar uma política de cópia de segurança

Utilize o seguinte procedimento para editar uma política de cópia de segurança existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar uma política de cópia de segurança

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , clique no nó de **Políticas de cópia de segurança** . Todas as políticas de cópia de segurança aparecem no painel de **resultados** . 

3. Botão direito do rato na política que pretende editar e, em seguida, clique em **Editar**. 

    ![Editar uma política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. Quando a janela de **criar uma política** é apresentada, introduza as suas alterações e, em seguida, clique em **OK**. 

## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança

Utilize o seguinte procedimento para eliminar uma política de cópia de segurança.

#### <a name="to-delete-a-backup-policy"></a>Para eliminar uma política de cópia de segurança

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , clique no nó de **Políticas de cópia de segurança** . Todas as políticas de cópia de segurança aparecem no painel de **resultados** . 

3. Botão direito do rato a política de cópia de segurança que pretende eliminar e, em seguida, clique em **Eliminar**.

4. Quando for apresentada a mensagem de confirmação, clique em **Sim**.

    ![Eliminar a confirmação de política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Próximos passos

- Saiba como utilizar o [Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como utilizar o [Gestor de instantâneo StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md).
