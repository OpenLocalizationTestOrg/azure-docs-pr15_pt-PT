<properties 
   pageTitle="Grupos de volume do Gestor de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como utilizar o Gestor de instantâneo StorSimple MMC snap-in para criar e gerir grupos de volume."
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

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Utilizar o Gestor de instantâneo StorSimple para criar e gerir grupos de volume

## <a name="overview"></a>Descrição geral

Pode utilizar o nó do **Volume grupos** no painel de **âmbito** para atribuir volumes a grupos de volume, ver informações sobre um grupo de volume, agendar cópias de segurança e editar grupos de volume. 

Os grupos de volume são conjuntos de dados de volumes relacionados utilizados para assegurar que cópias de segurança são consistentes de aplicação. Para obter mais informações, consulte [os Volumes e grupos de volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [integração com o serviço de cópia sombra Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT] 
>
> * Todos os volumes num grupo de volume tem de vir de um fornecedor de serviço de nuvem único.
> 
> * Quando configurar grupos de volume, não misture volumes partilhados de cluster (CSVs) e que não sejam CSVs no mesmo grupo de volume. Gestor de instantâneo StorSimple não suporta uma mistura de CSVs e não CSVs no limite da mesma.
 
![Nó de grupos de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nó de grupos de Volume do Gestor de instantâneo StorSimple** 

Neste tutorial explica como pode utilizar o Gestor de instantâneo StorSimple para:

- Ver informações sobre os grupos de volume 
- Criar um grupo de volume
- Agregar um grupo de volume
- Editar um grupo de volume
- Eliminar um grupo de volume

Todas estas ações também estão disponíveis no painel de **ações** .
 
## <a name="view-volume-groups"></a>Ver grupos de volume

Se clicar no nó de **Grupos de Volume** , o painel de **resultados** mostra as seguintes informações sobre cada grupo de volume, consoante as seleções de coluna que efetua. (As colunas no painel de **resultados** são configuráveis. Botão direito do rato o nó **Volumes** , selecione **vista**e, em seguida, selecione **Adicionar/remover colunas**.)

Coluna de resultados | Descrição 
:--------------|:------------ 
Nome           | Na coluna **nome** contém o nome do grupo de volume.
Aplicação    | A coluna de **aplicações** mostra o número de escritores VSS atualmente instalado e em execução no anfitrião do Windows.
Selecionada       | A coluna de **seleccionado** mostra o número de volumes contidos no grupo volume. Um zero (0) indica que não existe uma aplicação é associada com os volumes no grupo volume.
Importado       | A coluna **importadas** mostra o número de volumes importados. Esta coluna quando definida como **Verdadeiro**, indica que um grupo de volume foi importado a partir do portal clássico do Azure e não foi criado no StorSimple instantâneo Manager.
 
>[AZURE.NOTE] Grupos de volume do Gestor de instantâneo StorSimple também são apresentados no separador **Políticas de cópia de segurança** no portal do Azure clássico.
 
## <a name="create-a-volume-group"></a>Criar um grupo de volume

Utilize o seguinte procedimento para criar um grupo de volume.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , com o botão direito **Grupos de Volume**e, em seguida, clique em **Criar grupo de Volume**. 

    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    É apresentada a caixa de diálogo **criar um grupo de volume** . 

    ![Criar uma caixa de diálogo do grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Introduza as seguintes informações: 

    1. Na caixa **nome** , escreva um nome exclusivo para o novo grupo de volume. 

    2. Na caixa de **aplicações** , selecione aplicações associadas os volumes que adicionar ao grupo de volume. 

        As listas de caixa de **aplicações** apenas nessas aplicações que utilizam volumes de StorSimple e têm escritores VSS ativadas para os mesmos. Um gravador de VSS só é activado se todos os volumes que o gravador de está em mente são StorSimple volumes. Se a caixa de aplicações estiver vazia, em seguida, sem aplicações que utilizam volumes de Azure StorSimple e tem suportadas escritores VSS instaladas. (Atualmente, Azure StorSimple suporta Microsoft Exchange e o SQL Server.) Para mais informações sobre escritores VSS, consulte o artigo [integração com o serviço de cópia sombra Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Se selecionar uma aplicação, todos os volumes associados são selecionados automaticamente. Por outro lado, se selecionar volumes associados a uma aplicação específica, a aplicação é selecionada automaticamente na caixa de **aplicações** . 

    3. Na caixa **Volumes de** , selecione volumes de StorSimple para adicionar ao grupo de volume. 

      - Pode incluir volumes com a partições únicos ou múltiplos. (Vários volumes de partição podem ser discos dinâmicos ou discos básicos com múltiplas partições.) Um volume que contém a partições múltiplas é tratado como uma única unidade. Consequentemente, se adicionar apenas uma das partições a um grupo de volume, todas as outras partições são automaticamente adicionadas a esse grupo volume ao mesmo tempo. Depois de adicionar um partição volume vários a um grupo de volume, a vários partição volume continua a ser tratado como uma única unidade.

      - Pode criar grupos de volume vazia atribuindo não quaisquer volumes às mesmas. 

      - Não misture volumes partilhados de cluster (CSVs) e que não sejam CSVs no mesmo grupo de volume. Gestor de instantâneo StorSimple não suporta uma mistura de volumes CSV e que não sejam CSV no limite da mesma. 

4. Clique em **OK** para guardar o grupo de volume.

## <a name="back-up-a-volume-group"></a>Agregar um grupo de volume

Utilize o seguinte procedimento para criar cópias de um grupo de volume.

#### <a name="to-back-up-a-volume-group"></a>Para criar cópias de um grupo de volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , expanda o nó de **Grupos de Volume** , com o botão direito um nome de grupo de volume e, em seguida, clique em **Tomar a cópia de segurança**. 

    ![Agregar imediatamente o grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Na caixa de diálogo **Tirar cópias de segurança** , selecione **Instantâneo Local** ou **Na nuvem instantâneo**e, em seguida, clique em **Criar**. 

    ![Tomar diálogo cópia de segurança](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Para confirmar que a cópia de segurança está em execução, expanda o nó de **tarefas** e, em seguida, clique em **Executar**. A cópia de segurança deve ser indicada.

5. Para ver o instantâneo concluído, expanda o nó do **Catálogo de cópia de segurança** , expanda o nome do grupo de volume e, em seguida, clique em **Instantâneo Local** ou **Na nuvem instantâneo**. A cópia de segurança será apresentada se foi concluída com êxito. 

## <a name="edit-a-volume-group"></a>Editar um grupo de volume

Utilize o seguinte procedimento para editar um grupo de volume.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , expanda o nó de **Grupos de Volume** , com o botão direito um nome de grupo de volume e, em seguida, clique em **Editar**. 

3. É apresentada a caixa de diálogo **criar um grupo de volume **. Pode alterar as entradas de **nome**, **aplicações**e **Volumes** . 

4. Clique em **OK** para guardar as alterações.

## <a name="delete-a-volume-group"></a>Eliminar um grupo de volume

Utilize o seguinte procedimento para eliminar um grupo de volume. 

>[AZURE.WARNING] Este procedimento também elimina todas as cópias de segurança associadas ao grupo de volume.

#### <a name="to-delete-a-volume-group"></a>Para eliminar um grupo de volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , expanda o nó de **Grupos de Volume** , com o botão direito um nome de grupo de volume e, em seguida, clique em **Eliminar**. 

3. É apresentada a caixa de diálogo **Eliminar grupo de Volume** . Escreva **Confirmar** na caixa de texto e, em seguida, clique em **OK**. 

    O grupo de volume eliminado desaparece a partir da lista no painel de **resultados** e todas as cópias de segurança que estão associadas esse grupo volume são eliminadas do catálogo cópia de segurança.

## <a name="next-steps"></a>Próximos passos

- Saiba como utilizar o [Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como utilizar o [Gestor de instantâneo StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md).
