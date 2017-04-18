<properties 
   pageTitle="Gerir as políticas de cópia de segurança StorSimple | Microsoft Azure"
   description="Explica como pode utilizar o serviço do Gestor de StorSimple para criar e gerir cópias de segurança manuais, agendas de cópia de segurança e retenção de cópia de segurança."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Utilizar o serviço do Gestor de StorSimple para gerir políticas de cópia de segurança (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial explica como utilizar StorSimple página Gestor de serviço **Políticas de cópia de segurança** para controlar processos de cópia de segurança e retenção de cópia de segurança para os volumes de StorSimple. Também descreve como concluir uma cópia de segurança manual.

Quando agregar um volume, pode optar por criar um instantâneo local ou um instantâneo da nuvem. Se estiver a criar uma cópia de segurança um volume localmente afixado, recomendamos que especificar um instantâneo da nuvem. Demorar um grande número de instantâneos locais de um volume localmente afixado à forma com um conjunto de dados que tem muitas vasilha irá resultar numa situação na qual rapidamente poderia executar espaço local. Se optar por instantâneos local, recomendamos que tirar menos instantâneos diários para criar uma cópia de segurança do estado mais recente, mantê-las para um dia e, em seguida, elimine-as.

Quando der um instantâneo da nuvem de um volume localmente afixado, copie apenas os dados alterados na nuvem, onde é deduplicated e comprimido. 

## <a name="the-backup-policies-page"></a>A página de políticas de cópia de segurança

A página de **Políticas de cópia de segurança** permite-lhe gerir políticas de cópia de segurança e agendar local e na nuvem instantâneos. (Políticas de cópia de segurança são utilizadas para configurar a cópia de segurança agendas e retenção de cópia de segurança para uma coleção de volumes.) Políticas de cópia de segurança lhe permitem tirar um instantâneo de vários volumes em simultâneo. Isto significa que as cópias de segurança criadas por uma política de cópia de segurança será cópias falha consistentes. A página de **Políticas de cópia de segurança** lista as políticas de cópia de segurança, os tipos de, os volumes associados, o número de cópias de segurança retidos e a opção para ativar estas políticas.

A página de **Políticas de cópia de segurança** também permite-lhe filtrar as políticas de cópia de segurança existentes por uma ou mais dos seguintes campos:

- **Nome da política** – o nome associado a política. Os diferentes tipos de políticas de incluem:

   - Políticas agendadas, explicitamente são criadas pelo utilizador.
   - Políticas automáticas, que são criadas quando a cópia de segurança predefinidos para esta opção de volume foi ativada no momento da criação de volume. Estas políticas são nomeadas o *nome do volume*_Default onde *nome do volume* refere-se para o nome do volume StorSimple configurado pelo utilizador no portal do Azure clássico. As políticas que foram automáticas resultam em diária instantâneos de nuvem do início ao tempo de dispositivo de 22:30.
   - Políticas importadas, que foram originalmente criadas no Gestor de instantâneo StorSimple. Estes têm uma etiqueta que descreva o anfitrião do Gestor de instantâneo StorSimple que as políticas que foram importadas a partir de.

- **Volumes** – os volumes associados à política. Todos os volumes associados a uma política de cópia de segurança são agrupados quando são criadas as cópias de segurança.

- **Última cópia de segurança com êxito** – a data e hora da última bem sucedida cópia de segurança que foi tomada com esta política.

- **Próxima cópia de segurança** – a data e hora da cópia de segurança de agendada seguinte que será iniciada por esta política.

- **Agendas** – o número de agendas associada a política de cópia de segurança.

As operações utilizadas com frequência, que pode executar a partir desta página são:

- Adicionar uma política de cópia de segurança 
- Adicionar ou modificar uma agenda 
- Eliminar uma política de cópia de segurança 
- Tirar uma cópia de segurança manual 
- Criar uma política de cópia de segurança personalizada com vários volumes e agendas 

## <a name="add-a-backup-policy"></a>Adicionar uma política de cópia de segurança

Adicione uma política de cópia de segurança para agendar automaticamente as cópias de segurança. Execute os seguintes passos no portal do Azure clássico para adicionar uma política de cópia de segurança para o seu dispositivo StorSimple. Depois de adicionar a política, pode definir uma agenda (consulte o artigo [Adicionar ou modificar uma agenda](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Vídeo disponível](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **vídeo disponível**

Para ver um vídeo que demonstra como criar um local ou na nuvem política de cópia de segurança, clique em [aqui](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar uma agenda

Pode adicionar ou modificar uma agenda que está anexada a uma política de cópia de segurança existente no seu dispositivo StorSimple. Execute os seguintes passos no portal do Azure clássico para adicionar ou modificar uma agenda.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança

Execute os seguintes passos no portal do Azure clássico para eliminar uma política de cópia de segurança no seu dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Tirar uma cópia de segurança manual

Execute os seguintes passos no portal do Azure clássico para criar uma cópia de segurança (manual) para um único volume a pedido.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Criar uma política de cópia de segurança personalizada com vários volumes e agendas

Execute os seguintes passos no portal do Azure clássico para criar uma política de cópia de segurança personalizada que tem vários volumes e agendas.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
