<properties
   pageTitle="Clonar o volume do StorSimple | Microsoft Azure"
   description="Descreve os tipos de clonar diferente e quando utilizá-los e explica como pode utilizar uma cópia de segurança definida para clonar um volume individual."
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

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Utilizar o serviço do Gestor de StorSimple para clonar um volume

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Descrição geral

A página de **Catálogo de cópia de segurança** do serviço de StorSimple Gestor apresenta todos os conjuntos de cópia de segurança que são criados quando são disponibilizadas cópias de segurança manuais ou automatizadas. Pode utilizar esta página para a lista todas as cópias de segurança para uma política de cópia de segurança ou volume, selecione ou eliminar as cópias de segurança ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

![Página de catálogo de cópia de segurança](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Neste tutorial descreve como pode utilizar uma cópia de segurança definida para clonar um volume individual. Também explica a diferença entre clones *breves* e *permanente* . 

## <a name="create-a-clone-of-a-volume"></a>Criar um clonar de um volume

Pode criar um clonar no mesmo dispositivo, outro dispositivo ou mesmo uma máquina de virtual utilizando um local ou um instantâneo da nuvem.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Na página de serviço StorSimple Manager, clique no separador de **catálogo de cópia de segurança** e selecione um conjunto de cópia de segurança.

2. Expanda a cópia de segurança definida para visualizar os volumes associados. Clique em e selecione um volume do conjunto de cópia de segurança.

     ![Clonar um volume](./media/storsimple-clone-volume/HCS_Clone.png) 

3. Clique em **clonar** para começar a clonar o volume seleccionado.

4. No Assistente de Volume clonar, em **Especificar nome e localização**:

  1. Identificar um dispositivo de destino. Esta é a localização onde a clonar será criado. Pode escolher o mesmo dispositivo ou especificar noutro dispositivo. Se optar por um volume associado a outros fornecedores de serviço de nuvem (não Azure), a lista pendente para o dispositivo de destino irá mostrar apenas dispositivos físicos. Não é possível clonar um volume associado a outros fornecedores de serviço de nuvem num dispositivo virtual.

        >  [AZURE.NOTE] Certifique-se de que a capacidade necessária para o clonar for inferior a capacidade de disponível no dispositivo de destino.
  2. Especifique um nome de volume exclusivo para o seu clonar. Tem de conter o nome entre carateres 3 e 127.
  3. Clique no ícone de seta ![ícone de seta](./media/storsimple-clone-volume/HCS_ArrowIcon.png) para avançar para a página seguinte.

5. Em **anfitriões de especificar que podem utilizar este volume**:

  1. Especifique um registo de controlo de acesso (ACR) para o clonar. Pode adicionar uma nova ACR ou escolha a partir da lista existente.
  2. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-clone-volume/HCS_CheckIcon.png)para concluir a operação.

6. Uma tarefa clonar será iniciada e será notificado quando a clonar é criado com êxito. Clique em **Vista de tarefa** para monitorizar a tarefa de clonar na página **tarefas** .

7. Depois de concluída a tarefa de clonar:

  1. Ir para a página de **dispositivos** e selecione o separador **Contentores de Volume** . 
  2. Selecione o contentor de volume que está associado com o volume de origem clonar. Na lista de volumes, deverá ver o clonar que acabou de criar.

>[AZURE.NOTE] Monitorização e predefinido cópia de segurança são automaticamente desativados num clonado volume.

Um clonar que é criado desta forma é um clonar breves. Para mais informações sobre tipos de clonar, consulte o artigo [breves vs. clones permanentes](#transient-vs.-permanent-clones).

Este clonar é agora um volume normal e, qualquer operação que é possível num volume estará disponível para o clonar. Terá de configurar este volume para qualquer cópias de segurança.

## <a name="transient-vs-permanent-clones"></a>Transitória vs. clones permanentes

Clones breves e permanentes só são criadas quando são Clonar para um dispositivo diferente. Pode clonar um volume específico a partir de uma cópia de segurança definido para um dispositivo diferente. Um clonar criado desta forma é um clonar *breves* . O clonar breves terão referências para o volume original e irá utilizar esse volume para ler ao escrever localmente. 

Depois de tirar um instantâneo da nuvem de um clonar breves, o clonar resultante será um clonar *permanente* . O clonar permanente é independente e não tem as referências para o volume de original foi clonar a partir de.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários de clones breves e permanentes

As secções seguintes descrevem situações de exemplo na qual podem ser utilizados clones breves e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação de ao nível do item com um clonar breves

É necessário recuperar um ficheiro de apresentação do Microsoft PowerPoint um anos. O administrador de TI identifica a cópia de segurança específica a partir desse período de tempo e, em seguida, filtra o volume. O administrador, em seguida, clones o volume, localiza o ficheiro que está a procurar e fornece-lhe. Neste cenário, é utilizado um clonar breves. 
 
![Vídeo disponível](./media/storsimple-clone-volume/Video_icon.png) **vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clonar e restaurar funcionalidades no StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testes no ambiente de produção com um clonar permanente

Tem de verificar um testes de erros no ambiente de produção. Criar um clonar do volume no ambiente de produção ao efetuar um instantâneo de nuvem deste clonar. O volume clonado está agora independente. Neste cenário, é utilizado um clonar permanente.

## <a name="next-steps"></a>Próximos passos
- Saiba como [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 
