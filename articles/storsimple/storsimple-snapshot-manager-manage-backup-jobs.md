<properties 
   pageTitle="Tarefas de cópia de segurança do Gestor de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como utilizar o snap-in MMC do Gestor de instantâneo StorSimple para ver e gerir tarefas de cópia de segurança agendadas, atualmente em execução e concluídas."
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


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Utilizar o Gestor de instantâneo StorSimple para ver e gerir tarefas de cópia de segurança

## <a name="overview"></a>Descrição geral

O nó de **tarefas** no painel de **âmbito** mostra o **agendada**, **última 24 horas**e tarefas cópia de segurança **em execução** iniciados por si de forma interativa ou por uma política de configurado. 

Neste tutorial explica como pode utilizar o nó de **tarefas** para apresentar informações acerca de tarefas de cópia de segurança agendadas, recentes e atualmente em execução. (A lista de tarefas e informações sobre a correspondente aparece no painel de **resultados** .) Para além disso, pode botão direito do rato numa tarefa listados e ver um menu de contexto que lista ações disponíveis.

## <a name="view-scheduled-jobs"></a>Ver as tarefas agendadas

Utilize o procedimento seguinte para ver as tarefas agendadas de cópia de segurança.

#### <a name="to-view-scheduled-jobs"></a>Para ver as tarefas agendadas

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , expanda o nó de **tarefas** e, clique em **agendada**. As seguintes informações é apresentada no painel de **resultados** :

    - **Nome** – o nome do limite da agendada

    - **Executar o seguinte** – a data e hora do limite da seguinte agendada

    - **Executar o último** – a data e hora do limite da mais recente agendada

    >[AZURE.NOTE] Para instantâneos apenas únicos, o **Execute o seguinte** e **Execute última** serão os mesmos. 
 
    ![Tarefas agendadas de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Para executar ações adicionais sobre uma tarefa específica, o nome da tarefa no painel de **resultados** com o botão direito e selecione as opções do menu.

## <a name="view-recent-jobs"></a>Ver tarefas recentes

Utilize o seguinte procedimento para ver a cópia de segurança e restaurar as tarefas que foram concluídas no últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para ver tarefas recentes

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , expanda o nó de **tarefas** e, clique em **última 24 horas**. O painel de **resultados** mostra tarefas de cópia de segurança para as últimas 24 horas (até um máximo de 64 tarefas). As seguintes informações é apresentada no painel de **resultados** , dependendo das opções de **vista** que especificar:

    - **Nome** – o nome do limite da agendada.
 
    - **Introdução** – a data e hora quando começou o instantâneo.

    - **Parado** – a data e hora quando o instantâneo terminar ou foi terminado.

    - O tempo **decorrido** – a quantidade de tempo entre o **iniciado** e **parado** .

    - **Estado** – o estado da tarefa concluída recentemente. **Sucesso** indica que a cópia de segurança foi criada com êxito. **Falha na** indica que a tarefa não tiver sido executado com êxito.

    - **Informações** – o motivo da falha.

    - **Bytes processadas (MB)** – a quantidade de dados a partir do grupo de volume que foi processado (em MB). 

    ![Tarefas que executou o últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Para executar ações adicionais sobre uma tarefa específica, o nome da tarefa no painel de **resultados** com o botão direito e selecione as opções do menu.

    ![Eliminar uma tarefa](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Ver tarefas atualmente em execução

Utilize o seguinte procedimento para ver tarefas que estão atualmente em execução.

#### <a name="to-view-currently-running-jobs"></a>Para ver tarefas atualmente em execução

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , expanda o nó de **tarefas** e, clique em **Executar**. Dependendo das opções de **vista** que especificar, as seguintes informações é apresentada no painel de **resultados** : 

    - **Nome** – o nome do limite da agendada.

    - **Introdução** – a data e hora quando começou o instantâneo.

    - **Ponto de verificação** – a ação atual da cópia de segurança.

    - **Estado** – a percentagem de conclusão.
    
    - **Decorrido** – a quantidade de tempo que passou desde o início da cópia de segurança. 

    - **Débito médio (MB)** – rácio de bytes total dos dados processados a essa total tempo despendido para processamento (MB).

    - **Bytes processadas (MB)** – bytes total dos dados processados (MB).

    - **Bytes escritos (MB)** – total de bytes de dados escritos (MB). Incluir os dados, bem como os metadados e, por conseguinte, é normalmente maior que o processadas Bytes.

    ![Tarefas de atualmente em execução](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Para executar ações adicionais sobre uma tarefa específica, o nome da tarefa no painel de **resultados** com o botão direito e selecione as opções do menu.

## <a name="next-steps"></a>Próximos passos

- Saiba como utilizar o [Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [utilizar o Gestor de instantâneo StorSimple para gerir o catálogo de cópia de segurança](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

