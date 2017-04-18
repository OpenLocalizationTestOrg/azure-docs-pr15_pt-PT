<properties 
   pageTitle="Gestor de instantâneo StorSimple e volumes | Microsoft Azure"
   description="Descreve como utilizar o snap-in MMC do Gestor de instantâneo StorSimple para ver e gerir volumes de e para configurar as cópias de segurança."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Utilizar o Gestor de instantâneo StorSimple para ver e gerir volumes

## <a name="overview"></a>Descrição geral

Pode utilizar o nó do Gestor de instantâneo StorSimple **Volumes** (no painel de **âmbito** ) para selecionar volumes e visualizar informações sobre os mesmos. Os volumes são apresentados como unidades que correspondem aos volumes instalados pelo anfitrião. O nó **Volumes** mostra volumes locais e tipos de volume que são suportados pelo StorSimple, incluindo volumes descobertos através da utilização de iSCSI e um dispositivo. 

Para obter mais informações sobre os volumes suportados, aceda ao [suporte para múltiplos tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volume no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O nó **Volumes** também lhe permite analisar novamente ou eliminar volumes após StorSimple instantâneo Gestor detetá-los. 

Neste tutorial explica como pode montagem, inicialização e formatar volumes-las e, em seguida, utilize o Gestor de instantâneo StorSimple para:

- Ver informações sobre os volumes 
- Eliminar volumes
- Voltar a analisar volumes 
- Configurar um volume básico e -criar cópias de segurança
- Configurar um espelhado dinâmico e -criar cópias de segurança

>[AZURE.NOTE] Todas as ações de nó **Volume** também estão disponíveis no painel de **ações** .
 
## <a name="mount-volumes"></a>Montagem volumes

Utilize o seguinte procedimento para montagem, iniciar e formatar volumes de StorSimple. Este procedimento utiliza gestão do disco, um utilitário de sistema para gerir discos rígido e correspondente volumes ou partições. Para mais informações sobre a gestão de disco, aceda a [Gestão de disco](https://technet.microsoft.com/library/cc770943.aspx) no Web site da Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Para volumes de montagem

1. No seu computador anfitrião, inicie o iniciador de iSCSI da Microsoft.

2. Fornecer um dos endereços IP interface como o portal de destino ou endereço IP de deteção e ligue para o dispositivo. Depois do dispositivo está ligado, os volumes será acessíveis ao seu sistema do Windows. Para mais informações sobre como utilizar o iniciador de iSCSI da Microsoft, aceda à secção "Ligar a um dispositivo de destino iSCSI" no [iniciador iSCSI instalar e configurar o Microsoft][1].

3. Utilize qualquer uma das seguintes opções para iniciar a gestão de discos:

    - Escreva Diskmgmt na caixa **Executar** .

    - Iniciar o Gestor de servidor, expanda o nó de **armazenamento** e, em seguida, selecione **Gestão do disco**.

    - Iniciar as **Ferramentas administrativas**, expanda o nó de **Gestão do computador** e, em seguida, selecione **Gestão do disco**. 

    >[AZURE.NOTE] Tem de utilizar privilégios de administrador para executar a gestão de disco.
 
4. Siga os volumes online:

   1. Em gestão de disco, com o botão direito qualquer volume marcado **Offline**.

   2. Clique em **reativar disco**. O disco deve ser marcado como **Online** depois do disco está reactivar.

5. Iniciar o volume (s):

   1. Os volumes de descoberta de contexto.

   2. No menu, selecione **Iniciar disco**.

   3. Na caixa de diálogo de **Inicialização disco** , selecione os discos ao qual pretende iniciar e, em seguida, clique em **OK**.

6. Formatar os volumes simples:

   1. Botão direito do rato num volume que pretende formatar.

   2. No menu, selecione **Novo Volume simples**.

   3. Utilize o Assistente de novo Volume simples para formatar o volume:

      - Especifique o tamanho do volume.
      - Fornece uma letra de unidade.
      - Selecione o sistema de ficheiros NTFS.
      - Especifique um tamanho de unidade de atribuição de 64 KB.
      - Efectue uma formatação rápida.

7. Formatar partição com várias volumes. Para obter instruções, aceda à secção, "Partições Volumes e" na [Implementar a gestão do disco](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Ver informações sobre os volumes

Utilize o procedimento seguinte para ver informações sobre local e volumes de Azure StorSimple.

#### <a name="to-view-volume-information"></a>Para ver informações de volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , clique no nó **Volumes** . Uma lista de volumes locais e montadas, incluindo todos os volumes de Azure StorSimple, é apresentada no painel de **resultados** . As colunas no painel de **resultados** são configuráveis. (Clique com o botão direito do rato em **Volumes** , selecione **vista**e, em seguida, selecione **Adicionar/remover colunas**.)

    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Coluna de resultados | Descrição 
    :--------------|:-------------
    Nome           | A coluna **nome** contém a letra da unidade atribuída a cada descoberta volume.
    Dispositivo         | A coluna de **dispositivo** contém o endereço IP do dispositivo ligado ao computador do anfitrião.
    Nome de Volume do dispositivo | Na coluna **Nome de Volume do dispositivo** contém o nome do volume dispositivo a que pertence o volume seleccionado. Este é o nome de volume definido no portal do Azure clássico para esse volume específico.
    Caminhos de acesso   | A coluna de **Caminhos de acesso** apresenta o caminho de acesso para o volume. Este é a unidade letra ou um ponto no qual o volume está acessível no computador anfitrião.
 
## <a name="delete-a-volume"></a>Eliminar um volume

Utilize o seguinte procedimento para eliminar um volume a partir do Gestor de StorSimple instantâneo.

>[AZURE.NOTE] Não pode eliminar um volume se fizer parte de qualquer grupo de volume. (A opção Eliminar não está disponível para volumes que são membros de um grupo de volume.) Tem de eliminar o grupo de todo o volume para eliminar o volume.


#### <a name="to-delete-a-volume"></a>Para eliminar um volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , clique no nó **Volumes** . 

3. No painel de **resultados** , com o botão direito do volume que pretende eliminar.

4. No menu, clique em **Eliminar**. 

    ![Eliminar um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. É apresentada a caixa de diálogo **Eliminar Volume** . Escreva **Confirmar** na caixa de texto e, em seguida, clique em **OK**.

6. Por predefinição, o Gestor de instantâneo StorSimple cópias de um volume antes de o eliminar. Este precauções podem protegê-lo a partir de perda de dados se a eliminação foi não intencional. Gestor de instantâneo de StorSimple apresenta uma mensagem de progresso **Instantâneo automática** enquanto uma cópia de segurança o volume. 

    ![Mensagem de instantâneo automáticas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Voltar a analisar volumes

Utilize o seguinte procedimento para voltar a analisar os volumes ligados ao StorSimple instantâneo gestor.

#### <a name="to-rescan-the-volumes"></a>Para voltar a analisar os volumes

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , **Volumes**com o botão direito e, em seguida, clique em **voltar a analisar volumes**.

    ![Voltar a analisar volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Este procedimento sincroniza a lista de volume com o Gestor de instantâneo StorSimple. Quaisquer alterações, tais como novos volumes ou eliminados volumes, serão refletidas nos resultados de.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e agregar um volume básico

Utilize o seguinte procedimento para configurar uma cópia de segurança de um volume básico e, em seguida, iniciar uma cópia de segurança imediatamente ou criar uma política para cópias de segurança agendadas.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que o computador de dispositivo e anfitrião StorSimple estão corretamente configurados. Para obter mais informações, consulte [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough-u2.md).

- Instalar e configurar o Gestor de instantâneo StorSimple. Para obter mais informações, aceda ao [Gestor de instantâneo StorSimple implementar](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar a cópia de segurança de um volume básico

1. Crie um volume básico no dispositivo StorSimple.

2. Montagem, iniciar e formatar o volume, tal como descrito no [volumes de montagem](#mount-volumes). 

3. Clique no ícone de Gestor de instantâneo StorSimple no ambiente de trabalho. É apresentada a janela do Gestor de instantâneo StorSimple. 

4. No painel de **âmbito** , clique com o botão direito do rato em **Volumes** e, em seguida, selecione **voltar a analisar volumes**. Quando a digitalização estiver concluída, uma lista de volumes deverão aparecer no painel de **resultados** . 

5. No painel de **resultados** , com o botão direito do volume e, em seguida, selecione **Criar grupo de Volume**. 

    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. Na caixa de diálogo **Criar grupo Volume** , escreva um nome para o grupo de volume, atribua-lhe volumes e, em seguida, clique em **OK**.

7. No painel de **âmbito** , expanda o nó de **Grupos de Volume** . O novo grupo de volume deverá aparecer no nó de **Grupos de Volume** . 

8. Botão direito do rato no nome do grupo volume.

    - Para iniciar uma tarefa de cópia de segurança interativa (a pedido), clique em **Tomar cópia de segurança**. 

    - Para agendar uma cópia de segurança automática, clique em **Criar cópia de segurança política**. Na página **Geral** , selecione um grupo de volume a partir da lista. Na página **agenda** , introduza os detalhes da agenda. Quando tiver terminado, clique em **OK**. 

9. Para confirmar que iniciou a tarefa de cópia de segurança, expanda o nó de **tarefas** no painel de **âmbito** e, em seguida, clique no nó **em execução** . É apresentada a lista de tarefas atualmente em execução no painel de **resultados** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar e agregar um espelhado dinâmico

Conclua os passos seguintes para configurar a cópia de segurança de um espelhado dinâmico:

- Passo 1: Utilizar gestão de discos para criar um espelhado dinâmico. 

- Passo 2: Utilizar StorSimple instantâneo o gestor para configurar cópia de segurança.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que o computador de dispositivo e anfitrião StorSimple estão corretamente configurados. Para obter mais informações, consulte [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough-u2.md).

- Instalar e configurar o Gestor de instantâneo StorSimple. Para obter mais informações, aceda ao [Gestor de instantâneo StorSimple implementar](storsimple-snapshot-manager-deployment.md).

- Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **disco 1** e **2 de disco**). 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passo 1: Utilizar a gestão de discos para criar um espelhado dinâmico

Gestão de discos é um utilitário de sistema para gerir discos rígido e os volumes ou a partições que contêm. Para mais informações sobre a gestão de disco, aceda a [Gestão de disco](https://technet.microsoft.com/library/cc770943.aspx) no Web site da Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um espelhado dinâmico

1. Utilize qualquer uma das seguintes opções para iniciar a gestão de discos: 

   - Abrir a caixa **Executar** , escreva **Diskmgmt**e prima Enter.

   - Iniciar o Gestor de servidor, expanda o nó de **armazenamento** e, em seguida, selecione **Gestão do disco**. 

   - Iniciar as **Ferramentas administrativas**, expanda o nó de **Gestão do computador** e, em seguida, selecione **Gestão do disco**. 

2. Certifique-se de que tem dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **2 de disco**.) 

3. Na janela de gestão do disco, na coluna à direita do painel inferior, com o botão direito **no disco 1** e selecione **Novo Volume espelhado**. 

    ![Novo espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. Na página do assistente **Novo Volume espelhado** , clique em **seguinte**.

5. Na página **Selecionar discos** , selecione **disco 2** no painel de **selecionado** , clique em **Adicionar**e, em seguida, clique em **seguinte**. 

6. Na página **atribuir letra da unidade ou caminho** , aceite as predefinições e, em seguida, clique em **seguinte**. 

7. Na página **Format Volume** , na caixa **Tamanho da unidade de atribuição** , selecione **64 mil**. Selecione a caixa de verificação **executar uma formatação rápida** e, em seguida, clique em **seguinte**. 

8. Na página **Concluir o novo Volume espelhado** , reveja as definições e, em seguida, clique em **Concluir**. 

9. É apresentada uma mensagem a indicar que o disco básico será convertido num disco dinâmico. Clique em **Sim**.

    ![Mensagem de conversão de disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. Em gestão de disco, certifique-se de que o disco 1 e 2 de disco são apresentadas como volumes espelhados dinâmicos. (**Dinâmica** deverá aparecer na coluna Estado e, deverá alterar a cor da barra capacidade para vermelho, indicando um espelhado.) 

    ![Disco gestão espelhado discos dinâmicos](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passo 2: Utilizar StorSimple o Gestor de instantâneo para configurar a cópia de segurança

Utilize o seguinte procedimento para configurar um espelhado dinâmico e iniciar uma cópia de segurança imediatamente ou criar uma política para cópias de segurança agendadas.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar a cópia de segurança de um espelhado dinâmico

1. Clique no ícone de Gestor de instantâneo StorSimple no ambiente de trabalho. É apresentada a janela do Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , clique com o botão direito do rato em **Volumes** e selecione **voltar a analisar volumes**. Quando a digitalização estiver concluída, uma lista de volumes deverão aparecer no painel de **resultados** . O volume espelhado dinâmico é listado como um único volume. 

3. No painel de **resultados** , o volume espelhado dinâmico com o botão direito e, em seguida, clique em **Criar grupo de Volume**. 

4. Na caixa de diálogo **Criar grupo Volume** , escreva um nome para o grupo de volume, atribuir o volume espelhado dinâmico a este grupo e, em seguida, clique em **OK**. 

5. No painel de **âmbito** , expanda o nó de **Grupos de Volume** . O novo grupo de volume deverá aparecer no nó de **Grupos de Volume** . 

6. Botão direito do rato no nome do grupo volume. 

    - Para iniciar uma tarefa de cópia de segurança interativa (a pedido), clique em **Tomar cópia de segurança**. 

    - Para agendar uma cópia de segurança automática, clique em **Criar cópia de segurança política**. Na página **Geral** , selecione o grupo de volume a partir da lista. Na página **agenda** , introduza os detalhes da agenda. Quando tiver terminado, clique em **OK**. 

7. Pode monitorizar a tarefa de cópia de segurança que seja executado. No painel de **âmbito** , expanda o nó de **tarefas** e, em seguida, clique **em execução**, detalhes de tarefa aparecem no painel de **resultados** . Quando tiver terminada a tarefa de cópia de segurança, os detalhes são transferidos para a lista de tarefas do **último de 24** horas. 

## <a name="next-steps"></a>Próximos passos

- Saiba como utilizar o [Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [utilizar o Gestor de instantâneo StorSimple para criar e gerir grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
