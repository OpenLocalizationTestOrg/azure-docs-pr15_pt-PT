<properties 
   pageTitle="Tutorial de cópia de segurança de matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve como agregar partilhas de matriz Virtual StorSimple e volumes."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Criar uma cópia de segurança a matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral 

Neste tutorial aplica-se para a versão do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo de virtual StorSimple no local ou dispositivo virtual StorSimple) em execução Março de 2016 disponibilidade geral (das versões DG) ou versões posteriores.

A matriz de Virtual StorSimple é um híbrido nuvem armazenamento no local dispositivo virtual que pode ser configurado como um servidor de ficheiros ou num servidor de iSCSI. Pode criar cópias de segurança, restaurar a partir de cópias de segurança e realizar dispositivo activação pós-falha se é necessária uma recuperação de falhas. Quando configurado como um servidor de ficheiros, também permite recuperação ao nível do item. Neste tutorial descreve como utilizar o portal do Azure clássico ou web StorSimple IU para criar cópias de segurança manuais e agendadas da sua matriz de Virtual StorSimple.


## <a name="back-up-shares-and-volumes"></a>Criar cópias partilhas e volumes de segurança

Cópias de segurança fornecem protecção em qualquer altura, melhorar a recuperação e minimizar restaurar horas de partilhas e volumes. Pode criar cópias uma partilha ou volume no seu dispositivo StorSimple de duas maneiras: **agendada** ou **Manual**. Cada um dos métodos é abordada nas secções seguintes.

> [AZURE.NOTE] Neste lançamento, cópias de segurança agendadas são criadas por uma política predefinida que é executado diariamente uma vez especificado e cópias de segurança de todos os partilhas ou volumes no dispositivo. Não é possível criar políticas personalizadas para cópias de segurança agendadas neste momento.

## <a name="change-the-backup-schedule"></a>Alterar a agenda da cópia de segurança

O dispositivo virtual StorSimple tem uma política de cópia de segurança predefinidos que inicia uma vez especificado do dia (22:30) e cópias todas as acções ou volumes no dispositivo uma vez por dia. Pode alterar a hora em que a cópia de segurança iniciada, mas a frequência e a retenção (que especifica o número de cópias de segurança para reter) não podem ser alterados. Durante estas cópias de segurança, todo o dispositivo virtual cópias de segurança; Por conseguinte, recomendamos que agende estas cópias de segurança das horas de expediente.

Execute os seguintes passos no [portal clássica Azure](https://manage.windowsazure.com/) para alterar a hora de início cópia de segurança predefinido.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar a hora de início para a política predefinida para cópia de segurança

1. Navegue para o separador de **configuração** do dispositivo.

2. Na secção da **cópia de segurança** , especifique a hora de início para a cópia de segurança diária.

3. Clique em **Guardar**.

### <a name="take-a-manual-backup"></a>Tirar uma cópia de segurança manual

Para além de cópias de segurança agendadas, pode tirar um manual (a pedido) cópia de segurança em qualquer altura.

#### <a name="to-create-a-manual-on-demand-backup"></a>Para criar uma cópia de segurança manual (a pedido)

1. Navegue até ao separador **partilhas** ou no separador de **Volumes** .

2. Na parte inferior da página, clique em **todas as cópias de segurança**. Vai ser-lhe para verificar que, afinal tirar agora a cópia de segurança. Clique no ícone de verificação ![verificar ícone](./media/storsimple-ova-backup/image3.png) para continuar a cópia de segurança.

    ![confirmação de cópia de segurança](./media/storsimple-ova-backup/image4.png)

    Será notificado do que uma tarefa de cópia de segurança está a ser iniciado.

    ![cópia de segurança inicial](./media/storsimple-ova-backup/image5.png)

    Será notificado do que a tarefa foi criada com êxito.

    ![tarefa de cópia de segurança criada](./media/storsimple-ova-backup/image7.png)

3. Para controlar o progresso da tarefa, clique em **Vista de tarefa**.

4. Depois de concluir a tarefa de cópia de segurança, aceda ao separador de **catálogo de cópia de segurança** . Deverá visualizar a cópia de segurança concluída.

    ![Cópia de segurança concluída](./media/storsimple-ova-backup/image8.png)

5. Defina as seleções de filtro para o dispositivo apropriado, a política de cópia de segurança e o intervalo de tempo e, em seguida, clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-backup/image3.png).

    A cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança que é apresentada no catálogo.

## <a name="view-existing-backups"></a>Ver cópias de segurança existentes

Execute os seguintes passos no portal do Azure clássico para ver as cópias de segurança existentes.

#### <a name="to-view-existing-backups"></a>Para ver as cópias de segurança existentes

1. Na página de serviço StorSimple Manager, clique no separador de **catálogo de cópia de segurança** .

2. Selecione uma cópia de segurança definida da seguinte forma:

    1. Selecione o dispositivo.

    2. Na lista pendente, selecione a partilha ou o volume para a cópia de segurança que pretende selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![](./media/storsimple-ova-backup/image3.png) para executar esta consulta.

    As cópias de segurança associado a partilha seleccionada ou volume deverá aparecer na lista de conjuntos de cópia de segurança.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **vídeo disponível**

Veja o vídeo para ver como pode criar partilhas, criar cópias partilhas de segurança e restaurar dados de uma matriz Virtual StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
