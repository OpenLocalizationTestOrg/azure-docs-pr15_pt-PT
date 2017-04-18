<properties 
   pageTitle="Desative e elimine um dispositivo StorSimple | Microsoft Azure"
   description="Descreve como remover StorSimple dispositivo de serviço ao primeiro desativá-los e, em seguida, eliminá-la."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Desative e elimine um dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Pode optar por ter um dispositivo StorSimple fora do serviço (por exemplo, se estiver a substituição ou atualizar o seu dispositivo ou se já não estiver a utilizar StorSimple). Se este for o caso, terá de desativar o dispositivo para que possa eliminar. Desativá-los e interromper a ligação entre o dispositivo e o serviço do Gestor de StorSimple correspondente. Neste tutorial explica como remover um dispositivo de StorSimple do serviço pela primeira desativá-los-lo e, em seguida, eliminá-la. 

Quando desativar um dispositivo, quaisquer dados que foram armazenados localmente no dispositivo deixará de estar acessíveis. Podem ser recuperados apenas os dados associados com o dispositivo que foi armazenado na nuvem.  

>[AZURE.WARNING] Desativação é uma operação permanente e não pode ser anulada. Não é possível registar um dispositivo desativado com o serviço do Gestor de StorSimple a menos que o primeiro é reponha pela fábrica. 
>
>A fábrica de repor processo elimina todos os dados que foram armazenados localmente no seu dispositivo. Por conseguinte, é essencial que tirar um instantâneo da nuvem de todos os seus dados antes de desativar um dispositivo. Isto permitirá recuperar todos os dados numa fase posterior.

Este tutorial explica como:

- Desativar um dispositivo e eliminar os dados
- Desativar um dispositivo e manter os dados

Também explica como desactivação e eliminação funciona num dispositivo virtual StorSimple.

>[AZURE.NOTE] Antes de desativar um dispositivo de físico ou virtual StorSimple, certifique-se parar ou eliminar clientes e anfitriões que dependem do que o dispositivo.

## <a name="deactivate-and-delete-data"></a>Desative e eliminar dados

Se estiver interessado em eliminar completamente o dispositivo e não pretende manter os dados no dispositivo, em seguida, conclua os passos seguintes.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e eliminar os dados  

1. Antes de desativá-los um dispositivo, tem de eliminar todas as o volume contentores (e os volumes) associado ao dispositivo. Pode eliminar contentores de volume apenas depois de ter eliminado as cópias de segurança associadas.

2. Desative o dispositivo da seguinte forma:

    1. Na página Gestor de StorSimple serviço **dispositivos** , selecione o dispositivo que pretende desativar e, na parte inferior da página, clique em **desativar**.

    2. Será apresentada uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativar será iniciado e demorar alguns minutos a concluir.

3. Após a desativação, pode eliminar o dispositivo completamente. Eliminar um dispositivo remove-a partir da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode gerir já não o dispositivo eliminado. Utilize os seguintes passos para eliminar o dispositivo:

    1. Na página Gestor de StorSimple serviço **dispositivos** , selecione um dispositivo desativado que pretende eliminar.

    2. Na parte inferior da página, clique em **Eliminar**.

    3. Será pedido de confirmação. Clique em **Sim** para continuar.

    Poderá demorar alguns minutos para o dispositivo a ser eliminada.

## <a name="deactivate-and-retain-data"></a>Desative e manter dados

Se estiver interessado em eliminar o dispositivo, mas pretende manter os dados, em seguida, conclua os passos seguintes.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e manter os dados 

1. Desative o dispositivo. Todos os contentores de volume e instantâneos do dispositivo permanecerão.

    1. Na página Gestor de StorSimple serviço **dispositivos** , selecione o dispositivo que pretende desativar e, na parte inferior da página, clique em **desativar**.

    2. Será apresentada uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativar será iniciado e demorar alguns minutos a concluir.

2. Agora pode falhar sobre os contentores de volume e os instantâneos associados. Para obter os procedimentos, aceda a [recuperação activação e falhas para o seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Após a desativação e activação pós-falha, pode eliminar o dispositivo completamente. Eliminar um dispositivo remove-a partir da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode gerir já não o dispositivo eliminado. Conclua os passos seguintes para eliminar o dispositivo:
 
    1. Na página Gestor de StorSimple serviço **dispositivos** , selecione um dispositivo desativado que pretende eliminar.

    2. Na parte inferior da página, clique em **Eliminar**.

    3. Será pedido de confirmação. Clique em **Sim** para continuar.

    Poderá demorar alguns minutos para o dispositivo a ser eliminada.

## <a name="deactivate-and-delete-a-virtual-device"></a>Desative e elimine um dispositivo virtual

Para um dispositivo virtual StorSimple, desativação retira a atribuição máquina virtual. Em seguida, pode eliminar a máquina virtual e os recursos criados quando foi aprovisionado. Depois do dispositivo virtual é desactivado, não pode ser restaurada para o respetivo estado anterior. 

Resultados de Desativação nas seguintes ações:

- O dispositivo virtual StorSimple é removido.

- O OSDisk e os discos de dados criada para o dispositivo virtual StorSimple são removidos.

- À alojado serviço e à rede Virtual que foram criadas durante o aprovisionamento são retidas. Se não estiver a utilizar estas entidades, deverá eliminá-los manualmente.

- Instantâneos nuvem criados pelo dispositivo virtual StorSimple são retidos.

## <a name="next-steps"></a>Próximos passos
- Para restaurar o dispositivo de desativado para as predefinições de fábrica, vá para [Repor o dispositivo para as predefinições de fábrica do mesmo](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Para a assistência técnica, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md).

- Para saber mais sobre como utilizar o serviço do Gestor de StorSimple, aceda a [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md). 
