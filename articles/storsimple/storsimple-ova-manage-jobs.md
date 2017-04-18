<properties 
   pageTitle="Ver e gerir tarefas de matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve StorSimple página Gestor de serviço tarefas e como utilizá-la para controlar tarefas recentes e atuais para a matriz Virtual StorSimple."
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
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de StorSimple para ver tarefas para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

A página **tarefas** fornece um único portal central para ver e gerir tarefas que são iniciadas no Virtual matrizes (também conhecido como dispositivos virtuais no local) que estão ligadas no seu serviço de Gestor de StorSimple. Pode ver as tarefas em execução, concluídas e falhadas para vários dispositivos virtuais. Os resultados são apresentados num formato tabular. 

![Página de tarefas](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Pode localizar rapidamente as tarefas que está interessado filtrando campos tais como:

- **Estado** – pode procurar todos os, trabalhos em execução, concluídos ou falhados.
- **De e até** – tarefas podem ser filtradas com base no intervalo da data e hora.
- **Tipo de** – o tipo de tarefa pode ser restauro de cópia de segurança, all, activação pós-falha, transfira as atualizações ou instalar atualizações.
- **Dispositivos** – tarefas são iniciadas num dispositivo específico ligado ao serviço. As tarefas filtradas estão distribuídos num quadro, em seguida, com base nos seguintes atributos:

    - **Tipo de** – o tipo de tarefa pode ser restauro de cópia de segurança, all, activação pós-falha, transfira as atualizações ou instalar atualizações.

    - **Estado** – pode tarefas ser todos, executar, concluídos ou falha.

    - **Entidade** – as tarefas podem ser associadas um volume, partilhar ou dispositivo. 

    - **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.

    - **Introdução no** – o tempo quando a tarefa foi iniciada.

    - **Progresso** – a percentagem de conclusão de uma tarefa em execução. Para uma tarefa concluída, esta deve estar sempre a 100%.

Lista de tarefas é atualizada a cada 30 segundos.

## <a name="view-job-details"></a>Ver detalhes do projecto

Execute os passos seguintes para ver os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes do projecto

1. Na página **tarefas** , apresenta projectos que está interessado ao executar uma consulta com os filtros adequados. Pode procurar tarefas concluídas ou em execução.

2. Selecione uma tarefa a partir da lista de tarefas de tabela.

3. Na parte inferior da página, clique em **Detalhes**.

4. Na caixa de diálogo **Detalhes** , pode ver o estado, detalhes e estatísticas de tempo. A ilustração seguinte mostra um exemplo da caixa de diálogo **Detalhes da tarefa de cópia de segurança** .
 
    ![Página de detalhes do projecto](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas da tarefa quando a máquina virtual colocados em pausa no hipervisor

Quando uma tarefa estiver em curso na sua matriz de Virtual StorSimple e o dispositivo (máquina virtual aprovisionada no hipervisor) está em pausa para superior a 15 minutos, a tarefa irá falhar. Isto é devido ao seu tempo StorSimple Virtual matriz a ser dessincronizado com a hora do Microsoft Azure. Um exemplo de uma falha da tarefa de restaurar é apresentado na captura de ecrã seguinte.

![Restaurar a falha da tarefa](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Estas falhas serão aplicadas a cópia de segurança, restaurar, atualização e activação pós-falha tarefas. Se o seu máquina virtual está aprovisionada em Hyper-V, o computador eventualmente sincronizar tempo com o seu hipervisor. Assim que o que acontece, pode reiniciar o seu trabalho. 

## <a name="next-steps"></a>Próximos passos

[Saiba como utilizar a web local da IU para administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
