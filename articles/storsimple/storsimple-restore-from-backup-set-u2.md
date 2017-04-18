<properties 
   pageTitle="Restaurar um StorSimple volume a partir da cópia de segurança | Microsoft Azure"
   description="Explica como utilizar a página de catálogo de cópia de segurança de serviço do Gestor de StorSimple para restaurar um StorSimple volume a partir de um conjunto de cópia de segurança."
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurar um StorSimple volume a partir de um conjunto de cópia de segurança (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Descrição geral

A página de **Catálogo de cópia de segurança** apresenta todos os conjuntos de cópia de segurança que são criados quando são disponibilizadas cópias de segurança manuais ou automatizadas. Pode utilizar esta página para a lista todas as cópias de segurança para uma política de cópia de segurança ou volume, selecione ou eliminar as cópias de segurança ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

 ![Página de cópia de segurança de catálogo](./media/storsimple-restore-from-backup-set-u2/restore.png)

Neste tutorial explica como utilizar a página de **Catálogo de cópia de segurança** para restaurar o seu dispositivo a partir de um conjunto de cópia de segurança.

Pode restaurar um volume a partir de um local ou uma cópia de segurança da nuvem. Qualquer um dos casos, a operação de restauro mostra o volume online imediatamente enquanto dados são transferidos em segundo plano. 

Antes de iniciar uma operação de restauro, deve ser em atenção o seguinte:

- **Que tem de ter o volume offline** – assumir o volume offline no anfitrião do e o dispositivo antes de iniciar a operação de restaurar. Apesar da operação de restauro dá automaticamente o volume online no dispositivo, tem de colocar manualmente o dispositivo online no anfitrião do. Pode trazer com o volume online no anfitrião do assim que o volume está online no dispositivo. (Não necessita de esperar até terminar a operação de restauro.) Para obter os procedimentos, aceda a [tomar um volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Tipo de volume depois de restaurar** – volumes de eliminados são restaurados com base no tipo no instantâneo; Isto é, volumes que foram localmente afixados são restaurados como volumes localmente afixados e volumes que foram camados são restaurados como volumes em camadas.

    Para volumes existentes, o tipo de utilização atual do volume substitui o tipo de que está armazenado no instantâneo. Por exemplo, se restaurar um volume a partir de um instantâneo que foi realizado quando o tipo de volume foi camado e tipo de volume é agora afixado localmente (devido a uma operação de conversão que foi efetuada), em seguida, o volume será restaurado como um localmente afixado volume. Da mesma forma, se um volume localmente afixado existente foi expandido e subsequentemente restaurado a partir de um instantâneo mais antigo realizado quando o volume era mais pequeno, o volume restaurado irá reter o tamanho de expandido atual.

    Não é possível converter um volume a partir de um volume em camadas para um volume localmente afixado ou de um volume localmente afixado para um volume em camadas enquanto o volume está a ser restaurado. Aguarde até que a operação de restauro está concluída e, em seguida, pode converter o volume para outro tipo. Para obter informações sobre a conversão de um volume, consulte [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 

- **O tamanho do volume será reflectido no volume restaurado** – esta é uma consideração importante se está a restaurar um volume localmente afixado que foi eliminado (uma vez que volumes localmente afixados configurações totalmente). Certifique-se de que tem espaço suficiente antes de tentar restaurar um volume localmente afixado que foi eliminado anteriormente. 

- **Que não é possível expandir um volume enquanto estiver a ser restaurada-** – Aguarde até que a operação de restauro estiver concluída antes de tentar para expandir o volume. Para obter informações sobre expandir um volume, vá para [modificar um volume](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Pode efetuar uma cópia de segurança enquanto está a restaurar um volume local** – para obter os procedimentos aceda a [utilizar o serviço do Gestor de StorSimple para gerir políticas de cópia de segurança](storsimple-manage-backup-policies.md).

- **Que pode cancelar uma operação de restauro** – se cancelar a tarefa de restauro, em seguida, o volume vai ser revertida para o estado em que estava antes que iniciou a operação de restaurar. Para obter os procedimentos, vá para [Cancelar uma tarefa](storsimple-manage-jobs-u2.md#cancel-a-job).

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

Pode utilizar a página de **Catálogo de cópia de segurança** para restaurar o volume do StorSimple a partir de uma cópia de segurança específica. Tenha em atenção, no entanto, esse restaurar um volume transforma o volume para o estado em que estava quando a cópia de segurança foi disponibilizada. Todos os dados que foram adicionados após a operação de cópia de segurança serão perdidos.

> [AZURE.WARNING] Restaurar a partir de uma cópia de segurança irão substituir os volumes existentes da cópia de segurança. Isto pode causar a perda de dados que foram escritas após a cópia de segurança foi tomada.

### <a name="to-restore-your-volume"></a>Para restaurar o volume

1. Na página de serviço StorSimple Manager, clique no separador de **catálogo de cópia de segurança** .

    ![Catálogo de cópia de segurança](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Selecione uma cópia de segurança definida da seguinte forma:
  1. Selecione o dispositivo adequado.
  2. Na lista pendente, selecione a política de cópia de segurança ou volume para a cópia de segurança que pretende selecionar.
  3. Especifique o intervalo de tempo.
  4. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) para executar esta consulta.
 
    As cópias de segurança associado o volume seleccionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.

3. Expanda a cópia de segurança definida para visualizar os volumes associados. Estes volumes devem ser tidos offline no anfitrião e dispositivo antes de poder restaurá-los. Aceder a volumes na página **Contentores de Volume** e, em seguida, siga os passos na [tomar um volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline) para colocá-los offline.

    > [AZURE.IMPORTANT] Certifique-se de que reencaminhado os volumes offline no anfitrião do primeiro, antes de publicar os volumes offline no dispositivo. Se não levar os volumes offline no anfitrião do, poderia potencialmente conduzir a danos nos dados.

4. Navegar de volta para o separador de **Catálogo de cópia de segurança** e selecione um conjunto de cópia de segurança.

5. Clique em **Restaurar** na parte inferior da página.

6. Será pedido de confirmação. Reveja as informações de restauro e, em seguida, selecione a caixa de verificação de confirmação.

    ![Página de confirmação](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Clique no ícone de verificação ![verificar ícone](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Este iniciará uma tarefa de restauro que pode ver acedendo a página **tarefas** . 

8. Depois da restaurar estiver concluída, pode verificar que o conteúdo do seu volumes é substituído por volumes da cópia de segurança.

![Vídeo disponível](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clonar e restaurar funcionalidades no StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se o restauro falhar

Vai receber um alerta se a operação de restauro falhar por qualquer motivo. Se tal acontecer, atualize a lista de cópia de segurança para confirmar que a cópia de segurança ainda é válida. Se a cópia de segurança é válida e está a restaurar a partir da nuvem, em seguida, problemas de conectividade poderão estar a causar o problema. 

Para concluir a operação de restauro, tirar o volume offline no anfitrião do e repita a operação de restaurar. Tenha em atenção que todas as modificações para os dados de volume que foram executadas durante o processo de restaurar serão perdidas.

## <a name="next-steps"></a>Próximos passos

- Saiba como [Gerir StorSimple volumes](storsimple-manage-volumes-u2.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
