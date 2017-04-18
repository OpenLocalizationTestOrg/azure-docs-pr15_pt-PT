<properties 
   pageTitle="Restaurar um StorSimple volume a partir da cópia de segurança | Microsoft Azure"
   description="Explica como utilizar a página de catálogo de cópia de segurança de serviço do Gestor de StorSimple para restaurar um StorSimple volume a partir de um conjunto de cópia de segurança."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um StorSimple volume a partir de um conjunto de cópia de segurança

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Descrição geral

A página de **Catálogo de cópia de segurança** apresenta todos os conjuntos de cópia de segurança que são criados quando são disponibilizadas cópias de segurança manuais ou automatizadas. Pode utilizar esta página para a lista todas as cópias de segurança para uma política de cópia de segurança ou volume, selecione ou eliminar as cópias de segurança ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

 ![Página de cópia de segurança de catálogo](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Neste tutorial explica como utilizar a página de **Catálogo de cópia de segurança** para restaurar um volume no seu dispositivo a partir de um conjunto de cópia de segurança.

## <a name="how-to-use-the-backup-catalog"></a>Como utilizar o catálogo de cópia de segurança 

A página de **Catálogo de cópia de segurança** fornece uma consulta que o ajuda a reduzir a cópia de segurança conjunto de seleção. Pode filtrar os conjuntos cópia de segurança que são obtidos os com base nos seguintes parâmetros:

- **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
- **Política de cópia de segurança** ou **volume** – a política de cópia de segurança ou o volume associada a este conjunto de cópia de segurança.
- **De** e **para** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados estão, em seguida, distribuídos num quadro com base nas seguintes atributos:

- **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.
- **Tamanho** – o tamanho real do conjunto de cópia de segurança.
- **Criado em** – a data e hora quando as cópias de segurança foram criadas. 
- **Tipo de** – conjuntos de cópia de segurança podem ser instantâneos locais ou na nuvem instantâneos. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, Considerando que um instantâneo de nuvem refere-se para a cópia de segurança dos dados de volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, Considerando que são escolhidos instantâneos na nuvem para resiliência de dados.
- **Iniciado por** – as cópias de segurança podem ser iniciadas automaticamente, de acordo com uma agenda ou manualmente por um utilizador. (Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar a opção **tirar cópias de segurança** para tirar uma cópia de segurança interativa.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume do StorSimple de uma cópia de segurança

Pode utilizar a página de **Catálogo de cópia de segurança** para restaurar o volume do StorSimple a partir de uma cópia de segurança específica. 

> [AZURE.WARNING] Restaurar a partir de uma cópia de segurança irão substituir os volumes existentes da cópia de segurança. Isto pode causar a perda de dados que foram escritas após a cópia de segurança foi tomada.

Antes de iniciar um restauro num volume, certifique-se de que o volume está offline. Irá precisar de levar o volume offline no anfitrião do primeiro e, em seguida, o dispositivo. Siga os passos na [demorar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline). Execute os passos seguintes para restaurar um volume de um conjunto de cópia de segurança.

### <a name="to-restore-from-a-backup-set"></a>Para restaurar a partir de um conjunto de cópia de segurança

1. Na página de serviço StorSimple Manager, clique no separador de **catálogo de cópia de segurança** .

    ![Catálogo de cópia de segurança](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Selecione uma cópia de segurança definida da seguinte forma:
  1. Selecione o dispositivo adequado.
  2. Na lista pendente, selecione a política de cópia de segurança ou volume para a cópia de segurança que pretende selecionar.
  3. Especifique o intervalo de tempo.
  4. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) para executar esta consulta.
 
    As cópias de segurança associado o volume seleccionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.

3. Expanda a cópia de segurança definida para visualizar os volumes associados. Estes volumes devem ser tidos offline no anfitrião e dispositivo antes de poder restaurá-los. Siga os passos na [demorar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Certifique-se de que reencaminhado os volumes offline no anfitrião do primeiro, antes de publicar os volumes offline no dispositivo. Se não levar os volumes offline no anfitrião do, poderia potencialmente conduzir a danos nos dados.

4. Selecione um conjunto de cópia de segurança. Clique em **Restaurar** na parte inferior da página.

6. Será pedido de confirmação. 

    ![Página de confirmação](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Reveja as informações de restaurar e clique no ícone de verificação ![verificar ícone](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Este iniciará uma tarefa de restauro que pode ver acedendo a página **tarefas** . 

8. Depois da restaurar estiver concluída, pode verificar que o conteúdo do seu volumes é substituído por volumes da cópia de segurança.

![Vídeo disponível](./media/storsimple-restore-from-backup-set/Video_icon.png) **vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clonar e restaurar funcionalidades no StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Próximos passos

- Saiba como [Gerir StorSimple volumes](storsimple-manage-volumes.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
