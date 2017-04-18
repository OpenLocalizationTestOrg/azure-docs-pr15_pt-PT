<properties 
   pageTitle="Ver e gerir tarefas de StorSimple | Microsoft Azure"
   description="Descreve StorSimple página Gestor de serviço tarefas e como utilizá-la para controlar tarefas recentes, atuais e agendadas de cópia de segurança."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Utilizar o serviço do Gestor de StorSimple para ver e gerir tarefas de StorSimple

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Descrição geral

A página **tarefas** fornece um único portal central para visualização e gestão de tarefas que foram iniciadas em dispositivos ligados ao seu serviço de Gestor de StorSimple. Pode ver as tarefas agendadas, em execução, concluídas e falhadas para vários dispositivos. Os resultados são apresentados num formato tabular. 

![Página de tarefas](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Pode localizar rapidamente as tarefas que está interessado filtrando campos tais como:

- **Estado** – tarefas podem estar em execução, agendada, falha, concluído, cancelar ou cancelado.

- **Tipo de** – tarefas podem ser criadas como resultado de um agendada ou uma cópia de segurança a pedido (**Tirar cópias de segurança**), clonar, um restauro de dispositivo ou uma operação de atualização.

- **Dispositivos** – tarefas são iniciadas num determinado dispositivo ligado ao serviço.

- **De e até** – tarefas podem ser filtradas com base no intervalo da data e hora.

As tarefas filtradas estão distribuídos num quadro, em seguida, com base nos seguintes atributos:

- **Tipo de** – cópia de segurança, clonar, restaurar, activação pós-falha ou atualizar.

- **Estado** – em execução, agendada, falha, concluído, cancelar ou cancelado.

- **Entidade** – as tarefas podem ser associadas um volume, uma política de cópia de segurança ou um dispositivo. Uma tarefa clonar está associada um volume, Considerando que uma tarefa de cópia de segurança agendada está associada uma política de cópia de segurança. É criada uma tarefa de dispositivo como resultado de uma recuperação de falhas (DR) ou uma operação de restaurar.

- **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.

- **Started On** – data e hora quando a tarefa foi iniciada.

- **Progresso** – a percentagem de conclusão de uma tarefa em execução. Para uma tarefa concluída, esta deve estar sempre a 100%.

Lista de tarefas é atualizada a cada 30 segundos.

Pode efetuar as seguintes ações relacionadas com o projecto nesta página:

- Ver detalhes do projecto

- Cancelar uma tarefa

## <a name="view-job-details"></a>Ver detalhes do projecto

Execute os passos seguintes para ver os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes do projecto

1. Na página **tarefas** , apresenta projectos que está interessado ao executar uma consulta com os filtros adequados. Pode procurar concluída, executar ou cancelado tarefas.

2. Selecione uma tarefa.

3. Na parte inferior da página, clique em **Detalhes**.

4. Na caixa de diálogo **Detalhes da tarefa de cópia de segurança** , pode ver o estado, detalhes, estatísticas de tempo e as estatísticas de dados.

## <a name="cancel-a-job"></a>Cancelar uma tarefa

Execute os passos seguintes para cancelar uma tarefa em execução.

### <a name="to-cancel-a-job"></a>Para cancelar uma tarefa

1. Na página **tarefas** , apresenta a execução projectos que pretende cancelar ao executar uma consulta com os filtros adequados.

1. Selecione a tarefa.

1. Na parte inferior da página, clique em **Cancelar**.

1. Quando lhe for pedido de confirmação, clique em **Sim**.

Esta tarefa agora é cancelada.

## <a name="next-steps"></a>Próximos passos

- Saiba como [Gerir as políticas de cópia de segurança StorSimple](storsimple-manage-backup-policies.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
