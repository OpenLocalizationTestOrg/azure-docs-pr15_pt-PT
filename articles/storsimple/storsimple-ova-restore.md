<properties
   pageTitle="Restaurar a partir de uma cópia de segurança da sua matriz de Virtual StorSimple"
   description="Saiba mais sobre como restaurar uma cópia de segurança da sua matriz de Virtual StorSimple."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Restaurar a partir de uma cópia de segurança da sua matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral 

Este artigo aplica-se para o lançamento do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo de virtual StorSimple no local ou dispositivo virtual StorSimple) em execução Março de 2016 disponibilidade geral (das versões DG) ou posterior. Este artigo descreve passo a passo como restaurar a partir de um conjunto de cópia de segurança das suas partilhas ou volumes no seu matriz de Virtual StorSimple. O artigo também apresenta detalhes sobre como a recuperação ter ao nível do item funciona no seu matriz Virtual StorSimple que esteja configurado como um servidor de ficheiros.


## <a name="restore-shares-from-a-backup-set"></a>Restaurar partilhas a partir de um conjunto de cópia de segurança


**Antes de tentar restaurar partilhas, certifique-se de que tem espaço suficiente no dispositivo para concluir esta operação.** Para restaurar a partir de uma cópia de segurança, no [portal clássica Azure](https://manage.windowsazure.com/), execute os passos seguintes.

#### <a name="to-restore-a-share"></a>Para restaurar uma partilha

1.  Navegue para a **cópia de segurança de catálogo**. Filtrar por intervalo de tempo para procurar as cópias de segurança e dispositivo adequado. Clique no ícone de verificação ![](./media/storsimple-ova-restore/image1.png) para executar a consulta.


1.  Na lista de conjuntos de cópia de segurança apresentado, clique em e selecione uma cópia de segurança específica. Expanda a cópia de segurança para ver várias acções em-lo. Clique em e selecione uma partilha de que pretende restaurar.

2.  Na parte inferior da página, clique em **Restaurar como novo**.

3.  Isto irá iniciar o Assistente de **Restauro como nova partilha** . Na página **Especificar nome e localização** :


    1.  Verifique se o nome do dispositivo de origem. Isto deve ser o dispositivo que contém a partilha de que pretende restaurar. A seleção do dispositivo a cinzento. Para selecionar um dispositivo de origem diferentes, terá de sair do assistente e voltar a cópia de segurança definir novamente.

    2.  Fornece um nome de partilha. O nome da partilha tem de conter carateres de 3 e 127.

    3.  Rever o tamanho, tipo e permissões associadas a partilha de que está a tentar restaurar. Pode poderão modificar as propriedades de partilha através do Explorador do Windows, após o restauro está concluído.

    4.  Clique no ícone de verificação ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Depois da tarefa de restauro estiver concluída, o restauro será iniciado e verá outra notificação. Para monitorizar o progresso da restaurar, clique em **tarefa da vista**. Esta será direcionado para a página **tarefas** .

2.  Pode controlar o progresso da tarefa de restauro. Quando o restauro estiver 100% concluída, navegar para a página de **partilhas** no seu dispositivo.

3.  Agora pode ver a nova partilha restaurada na lista de acções no seu dispositivo. Tenha em atenção que restaurar é feito para o mesmo tipo de partilhar. Uma partilha em camadas é restaurada como camadas e uma partilha localmente afixada como uma partilha localmente afixada.

Tem agora concluir a configuração no dispositivo e aprendeu como fazer cópia de segurança ou restaurar uma partilha. 


## <a name="restore-volumes-from-a-backup-set"></a>Restaurar volumes de um conjunto de cópia de segurança


Para restaurar a partir de uma cópia de segurança, no portal do Azure clássico, execute os passos seguintes. A operação de restauro restaura a cópia de segurança para um novo volume no mesmo dispositivo virtual; Não é possível restaurar para um dispositivo diferente.

#### <a name="to-restore-a-volume"></a>Para restaurar um volume

1.  Navegue para a **cópia de segurança de catálogo**. Filtrar por intervalo de tempo para procurar as cópias de segurança e dispositivo adequado. Clique no ícone de verificação ![](./media/storsimple-ova-restore/image1.png) para executar a consulta.

2.  Na lista de conjuntos de cópia de segurança apresentado, clique em e selecione uma cópia de segurança específica. Expanda a cópia de segurança para ver os volumes vários em-lo. Selecione o nível de volume que pretende restaurar. 

5.  Na parte inferior da página, clique em **Restaurar como novo**. Será iniciado o Assistente de **Restauro como novo volume** .

1.  Na página **Especificar nome e localização** :


    1.  Verifique se o nome do dispositivo de origem. Isto deve ser o dispositivo que contém o volume que pretende restaurar. A seleção do dispositivo não está disponível. Para selecionar um dispositivo de origem diferentes, terá de sair do assistente e voltar a cópia de segurança definir novamente.

    2.  Forneça um nome de volume para o volume a ser restaurado como novo. O nome do volume tem de conter carateres de 3 e 127.

    3.  Clique no ícone de seta.

        ![](./media/storsimple-ova-restore/image12.png)

1.  Na página **especificar anfitriões que podem utilizar este volume** , selecione os ACRs adequados na lista pendente.

    ![](./media/storsimple-ova-restore/image13.png)

1.  Clique no ícone de verificação ![](./media/storsimple-ova-restore/image1.png). Este iniciará uma tarefa de restauro e irá ver a seguinte notificação que a tarefa está em curso.

2.  Depois da tarefa de restauro estiver concluída, o restauro será iniciado e verá outra notificação. Para monitorizar o progresso da restaurar, clique em **tarefa da vista**. Esta será direcionado para a página **tarefas** .

3.  Pode controlar o progresso da tarefa de restauro. Navegar para a página de **Volumes** no seu dispositivo.

4.  Agora pode ver o novo volume restaurado na lista de volumes no seu dispositivo. Tenha em atenção que restaurar é feito para o mesmo tipo de volume. Um volume em camadas é restaurado como camadas e um localmente afixado volume é restaurado como um localmente afixado volume.

5.  Quando o volume aparece online na lista de volumes, o volume está disponível para utilização.  No anfitrião do iniciador iSCSI, atualize a lista de alvos na janela de propriedades do iniciador de iSCSI.  Um novo destino que contém o nome do volume restaurado deverão aparecer como 'inativo' sob a coluna de estado.

6.  Selecione o destino e clique em **Ligar**.   Depois do iniciador estiver ligado a de destino, deverá alterar o estado para **ligado**. 

7.  Na janela de **Gestão do disco** , os volumes montados aparecerá como é mostrado na seguinte ilustração. O volume de descoberta de contexto (clique no nome do disco) e, em seguida, clique em **Online**.

> [AZURE.IMPORTANT] Quando tentar restaurar um volume ou numa partilha a partir de um conjunto de cópia de segurança, se a tarefa de restauro falhar, um destino em volume ou partilhar ainda pode ser criado no portal. É importante que eliminar este volume de destino ou partilhar no portal para minimizar quaisquer problemas futuros decorrente deste elemento.

## <a name="item-level-recovery-ilr"></a>Recuperação de ao nível do item (ILR)

Nesta versão apresenta a recuperação ao nível do item (ILR) num dispositivo virtual StorSimple configurado como um servidor do ficheiro. A funcionalidade permite-lhe fazer granular recuperação de ficheiros e pastas a partir de uma cópia de segurança na nuvem de todas as acções no dispositivo StorSimple. Os utilizadores possam obter ficheiros eliminados a partir de cópias de segurança recentes utilizando um modelo de gestão personalizada.

Partilha de cada tem uma pasta de *.backups* que contém as cópias de segurança mais recentes. O utilizador pode navegar para a cópia de segurança pretendida, copiar relevantes ficheiros e pastas da cópia de segurança e restaurá-las. Isto elimina as chamadas para os administradores para restaurar ficheiros a partir de cópias de segurança.

1.  Quando executa o ILR, pode ver as cópias de segurança através do Explorador do Windows. Clique em partilhar específico ao qual pretende observe a cópia de segurança. Irá ver uma pasta de *.backups* criada em partilhar que armazena todas as cópias de segurança. Expanda a pasta *.backups* para ver as cópias de segurança. A pasta, em seguida, irá mostrar a vista de explodida da hierarquia de cópia de segurança inteira. Esta vista é criada a pedido e normalmente assume apenas apenas alguns segundos para criar.

    As últimos 5 cópias de segurança são apresentadas desta forma e podem ser utilizadas para executar uma recuperação ao nível do item. As cópias de segurança recentes 5 incluem a predefinição agendada e as cópias de segurança manuais.

    
    -   **Agendadas cópias de segurança** com o nome como &lt;nome do dispositivo&gt;DailySchedule-AAAAMMDD HHMMSS UTC.

    -   **Cópias de segurança manuais** com o nome como Ad-hoc-AAAAMMDD-HHMMSS-UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identifique a cópia de segurança que contém a versão mais recente do ficheiro eliminada. Apesar do nome da pasta contém um carimbo de UTC em cada um dos casos acima, a hora em que foi criada na pasta é o tempo de dispositivo real quando a cópia de segurança iniciada. Utilize o carimbo de hora de pasta para localizar e identificar as cópias de segurança.

2.  Localize a pasta ou o ficheiro que pretende restaurar na cópia de segurança identificados no passo anterior. Nota que só podem ver os ficheiros ou pastas que possui as permissões para. Se não for possível aceder à determinados ficheiros ou pastas, terá de contacte um administrador de partilha que pode utilizar o Explorador do Windows para editar as permissões de partilha e dão-lhe acesso para o ficheiro específico ou pasta. É aconselhável recomendado que o administrador de partilha de ser um grupo de utilizadores em vez de um único utilizador.

3.  Copie o ficheiro ou a pasta para a partilha adequada no servidor StorSimple ficheiro.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **vídeo disponível**

Veja o vídeo para ver como pode criar partilhas, criar cópias partilhas de segurança e restaurar dados de uma matriz Virtual StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [administrar a sua matriz de Virtual StorSimple através da IU da web local](storsimple-ova-web-ui-admin.md).
