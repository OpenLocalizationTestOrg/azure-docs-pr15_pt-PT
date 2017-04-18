<properties 
   pageTitle="Catálogo de cópia de segurança do Gestor de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como utilizar o snap-in MMC do Gestor de instantâneo StorSimple para ver e gerir o catálogo de cópia de segurança."
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
   ms.date="04/26/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Utilizar o Gestor de instantâneo StorSimple para gerir o catálogo de cópia de segurança

## <a name="overview"></a>Descrição geral

A função principal do Gestor de instantâneo StorSimple é permitem-lhe criar cópias de segurança consistente para a aplicação de volumes de StorSimple sob a forma de instantâneos. Em seguida, são listados instantâneos num ficheiro XML denominado um *catálogo de cópia de segurança*. O catálogo de cópia de segurança organiza instantâneos ao grupo de volume e, em seguida, ao instantâneo local ou instantâneo na nuvem. 

Este tutorial descreve como pode utilizar o nó do **Catálogo de cópia de segurança** para concluir as seguintes tarefas:

- Restaurar um volume 
- Clonar um volume ou grupo de volume 
- Eliminar uma cópia de segurança 
- Recuperar um ficheiro
- Restaurar a base de dados do Gestor de instantâneo Storsimple

Pode ver o catálogo de cópia de segurança expandindo o nó do **Catálogo de cópia de segurança** no painel de **âmbito** e, em seguida, expandir o grupo de volume.

- Se clicar no nome do grupo volume, o painel de **resultados** mostra o número de instantâneos locais e na nuvem instantâneos disponíveis para o grupo de volume. 

- Se clicar em **Instantâneo Local** ou **Instantâneo de nuvem**, o painel de **resultados** mostra as seguintes informações sobre cada instantâneo cópia de segurança (dependendo das suas definições de **vista** ): 

    - **Nome** – o tempo que limite da foi despendido. 

    - **Tipo de** – se se trata de um instantâneo local ou um instantâneo da nuvem. 

    - **Proprietário** – o proprietário do conteúdo. 

    - **Disponível** – se o instantâneo neste momento não existe. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **Falso** indica que o instantâneo já não está disponível. 

    - **Importadas** – se tenha sido importada a cópia de segurança. **Verdadeiro** indica que a cópia de segurança foi importada a partir do serviço do Gestor de StorSimple ao tempo que o dispositivo foi configurado no Gestor de StorSimple instantâneo; **Falso** indica que não foi importado, mas foi criado pelo StorSimple instantâneo gestor. (-Lhe identificar facilmente um grupo de volume importados porque é adicionado um sufixo que identifica o dispositivo a partir do qual tenha sido importado o grupo de volume.)

    ![Catálogo de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Se expanda **Instantâneo Local** ou **Na nuvem instantâneo**e, em seguida, clique em nome de um instantâneo individual, o painel de **resultados** mostra as seguintes informações sobre o instantâneo que selecionou:

    - **Nome** – o volume identificado por uma letra de unidade. 

    - **Nome local** – o nome local da unidade (se disponível). 

    - **Dispositivo** – o nome do dispositivo no qual se encontra o volume. 

    - **Disponível** – se o instantâneo neste momento não existe. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **Falso** indica que o instantâneo já não está disponível. 


## <a name="restore-a-volume"></a>Restaurar um volume

Utilize o seguinte procedimento para restaurar um volume da cópia de segurança.

#### <a name="prerequisites"></a>Pré-requisitos

Se ainda não o tiver feito, crie um volume e o grupo de volume e, em seguida, elimine o volume. Por predefinição, o Gestor de instantâneo StorSimple cópias de um volume antes permitindo-lo a ser eliminada. Este precauções podem evitar a perda de dados se o volume for acidentalmente eliminado ou se os dados tem de ser recuperadas por qualquer motivo. 

Gestor de instantâneo StorSimple apresenta a seguinte mensagem enquanto que cria a cópia de segurança precaução.

![Mensagem de instantâneo automáticas](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]Não consegue eliminar um volume que faz parte de um grupo de volume. A opção Eliminar não está disponível. <br>

#### <a name="to-restore-a-volume"></a>Para restaurar um volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , expanda o nó do **Catálogo de cópia de segurança** , expandir um grupo de volume e, em seguida, clique em **Instantâneos Local** ou **Na nuvem instantâneos**. É apresentada uma lista de instantâneos cópia de segurança no painel de **resultados** . 

3. Localizar a cópia de segurança que pretende restaurar, botão direito do rato e, em seguida, clique em **Restaurar**. 

    ![Restaurar o catálogo de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. Na página confirmation, reveja os detalhes, escreva **Confirmar**e, em seguida, clique em **OK**. Gestor de instantâneo StorSimple utiliza a cópia de segurança para restaurar o volume. 

    ![Restaurar a mensagem de confirmação](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. Pode monitorizar a ação de restaurar medida que é executada. No painel de **âmbito** , expanda o nó de **tarefas** e, em seguida, clique em **Executar**. Detalhes de tarefa aparecem no painel de **resultados** . Quando a tarefa de restauro estiver concluída, os detalhes da tarefa são transferidos para a lista de **última 24 horas** .

## <a name="clone-a-volume-or-volume-group"></a>Clonar um volume ou grupo de volume

Utilize o seguinte procedimento para criar um duplicado (clonar) de um grupo de volume ou volume.

#### <a name="to-clone-a-volume-or-volume-group"></a>Para clonar um volume ou grupo de volume

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , expanda o nó do **Catálogo de cópia de segurança** , expandir um grupo de volume e, em seguida, clique em **Nuvem instantâneos**. Uma lista de cópias de segurança é apresentada no painel de **resultados** .

3. Localize o volume ou grupo de volume que pretende clonar, com o botão direito o volume ou o nome do grupo de volume e, em **clonar**. É apresentada a caixa de diálogo **Clonar nuvem instantâneo** .

    ![Clonar um instantâneo da nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Preencha a caixa de diálogo **Clonar nuvem instantâneo** da seguinte forma: 

    1. Na caixa de texto **nome** , escreva um nome para o volume clonado. Este nome aparecerá no nó **Volumes** . 

    2. (Opcional) selecione a **unidade**e, em seguida, selecione uma letra de unidade a partir da lista pendente. 

    3. (Opcional) selecione **Pasta (NTFS)**e escreva um caminho da pasta ou clique em Procurar e selecione uma localização para a pasta. 

    4. Clique em **Criar**.

5. Quando o processo de clonagem estiver concluído, tem de iniciar o volume clonado. Iniciar o Gestor de servidor e, em seguida, comece a gestão do disco. Para obter instruções detalhadas, consulte o artigo [volumes de montagem](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de ser inicializado, o volume estará listado no nó **Volumes** no painel de **âmbito** . Se não vir o volume listado, atualize a lista de volumes (clique com o botão direito do rato em **Volumes** e, em seguida, clique em **Atualizar**).

## <a name="delete-a-backup"></a>Eliminar uma cópia de segurança

Utilize o seguinte procedimento para eliminar um instantâneo do catálogo cópia de segurança. 

>[AZURE.NOTE]Eliminar um instantâneo elimina os dados de cópia de segurança associados o instantâneo. No entanto, o processo de limpeza de dados a partir da nuvem poderá demorar algum tempo.<br>
 
#### <a name="to-delete-a-backup"></a>Para eliminar uma cópia de segurança

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , expanda o nó do **Catálogo de cópia de segurança** , expandir um grupo de volume e, em seguida, clique em **Instantâneos Local** ou **Na nuvem instantâneos**. É apresentada uma lista de instantâneos no painel de **resultados** . 

3. Com o botão direito do instantâneo que pretende eliminar e, em seguida, clique em **Eliminar**.

4. Quando for apresentada a mensagem de confirmação, clique em **OK**. 

## <a name="recover-a-file"></a>Recuperar um ficheiro

Se um ficheiro é eliminado acidentalmente a partir de um volume, pode recuperar o ficheiro de obter um instantâneo que previamente datas a eliminação, utilizando o instantâneo para criar um clonar do volume e, em seguida, copiar o ficheiro a partir do volume clonado para o volume original.

#### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem uma cópia de segurança atual do grupo de volume. Em seguida, elimine um ficheiro armazenado dos volumes nesse grupo de volume. Por fim, utilize os passos seguintes para restaurar o ficheiro eliminado da cópia de segurança. 

#### <a name="to-recover-a-deleted-file"></a>Para recuperar um ficheiro eliminado

1. Clique no ícone de Gestor de instantâneo StorSimple no ambiente de trabalho. É apresentada a janela da consola de StorSimple instantâneo gestor. 

2. No painel de **âmbito** , expanda o nó do **Catálogo de cópia de segurança** e navegue para um instantâneo que contém o ficheiro eliminado. Normalmente, deverá seleccionar um instantâneo que foi criado imediatamente antes da eliminação. 

3. Localizar o volume ao qual pretende clonar, botão direito do rato e clique em **clonar**. É apresentada a caixa de diálogo **Clonar nuvem instantâneo** .

    ![Clonar um instantâneo da nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Preencha a caixa de diálogo **Clonar nuvem instantâneo** da seguinte forma: 

   1. Na caixa de texto **nome** , escreva um nome para o volume clonado. Este nome aparecerá no nó **Volumes** . 

   2. (Opcional) Selecione a **unidade**e, em seguida, selecione uma letra de unidade a partir da lista pendente. 

   3. (Opcional) Selecione **Pasta (NTFS)**e escreva um caminho da pasta ou clique em **Procurar** e selecione uma localização para a pasta. 

   4. Clique em **Criar**. 

5. Quando o processo de clonagem estiver concluído, tem de iniciar o volume clonado. Iniciar o Gestor de servidor e, em seguida, comece a gestão do disco. Para obter instruções detalhadas, consulte o artigo [volumes de montagem](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de ser inicializado, o volume estará listado no nó **Volumes** no painel de **âmbito** . 

    Se não vir o volume listado, atualize a lista de volumes (clique com o botão direito do rato em **Volumes** e, em seguida, clique em **Atualizar**).

6. Abra a pasta NTFS que contém o volume clonado, expanda o nó **Volumes** e, em seguida, abra o volume clonado. Localize o ficheiro que pretende recuperar e copiá-lo para o volume principal.

7. Depois de restaurar o ficheiro, pode eliminar a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar a base de dados do Gestor de instantâneo StorSimple

Deve regularmente cópias da base de dados StorSimple instantâneo Manager no computador anfitrião. Se ocorre uma falhas ou o computador anfitrião falha por qualquer motivo, pode restaurá-la da cópia de segurança. Criar a cópia de segurança da base de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Criar cópias de segurança e restaurar a base de dados

1. Pare o serviço de gestão do Microsoft StorSimple:

    1. Inicie o Gestor de servidor.

    2. No dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**.

    3. Na janela de **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**.

    4. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **Parar o serviço**.

2. No computador anfitrião, navegue até à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData é uma pasta oculta.
 
3. Localizar o ficheiro XML de catálogo, copie o ficheiro e armazenar a cópia numa localização segura ou na nuvem. Se o anfitrião falhar, pode utilizar este ficheiro de cópia de segurança para ajudar a recuperar as políticas de cópia de segurança que criou no StorSimple instantâneo Manager.

    ![Ficheiro de cópia de segurança catálogo StorSimple Azure](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Reinicie o serviço de gestão do Microsoft StorSimple: 

    1. No dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**.
    
    2. Na janela de **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**.

    3. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**.

5. No computador anfitrião, navegue até à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

6. Elimine o ficheiro XML de catálogo e substituí-la com a versão de cópia de segurança que criou. 

7. Clique no ícone de StorSimple instantâneo Gestor de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como [utilizar o Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba mais sobre [StorSimple instantâneo Gestor de tarefas e fluxos de trabalho](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
