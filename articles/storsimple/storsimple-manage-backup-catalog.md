<properties 
   pageTitle="Gerir o catálogo de cópia de segurança StorSimple | Microsoft Azure"
   description="Explica como utilizar a página de catálogo de cópia de segurança do serviço de StorSimple Gestor da lista, selecione e eliminar conjuntos de cópia de segurança para um volume."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Utilizar o serviço do Gestor de StorSimple para gerir o seu catálogo de cópia de segurança

## <a name="overview"></a>Descrição geral

A página de **Catálogo de cópia de segurança** de serviço de Gestor StorSimple apresenta todos os conjuntos de cópia de segurança que são criados quando são disponibilizadas cópias de segurança manuais ou agendadas. Pode utilizar esta página para a lista todas as cópias de segurança para uma política de cópia de segurança ou volume, selecione ou eliminar as cópias de segurança ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

Neste tutorial explica como da lista, selecione e eliminar um conjunto de cópia de segurança. Para saber como restaurar o seu dispositivo de cópia de segurança, vá para [restaurar a partir de um conjunto de cópia de segurança do seu dispositivo](storsimple-restore-from-backup-set.md). Para saber como clonar um volume, aceda a [clonar um volume StorSimple](storsimple-clone-volume.md).

![Catálogo de cópia de segurança](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

A página de **Catálogo de cópia de segurança** fornece uma consulta para refinar a sua cópia de segurança do conjunto de seleção. Pode filtrar os conjuntos de cópia de segurança que são obtidos os, com base nos seguintes parâmetros:

- **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.

- **Política de cópia de segurança ou Volume** – a política de cópia de segurança ou o volume associada a este conjunto de cópia de segurança.

- **De e até** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados estão, em seguida, distribuídos num quadro com base nas seguintes atributos:

- **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.

- **Tamanho** – o tamanho real do conjunto de cópia de segurança.

- **Criado em** – a data e hora quando as cópias de segurança foram criadas. 

- **Tipo de** – conjuntos de cópia de segurança podem ser instantâneos locais ou na nuvem instantâneos. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, Considerando que um instantâneo de nuvem refere-se para a cópia de segurança dos dados de volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, Considerando que são escolhidos instantâneos na nuvem para resiliência de dados.

- **Iniciado por** – as cópias de segurança podem ser iniciadas automaticamente por uma agenda ou manualmente por um utilizador. Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar a opção **tirar cópias de segurança** para tirar uma cópia de segurança manual.

## <a name="list-backup-sets-for-a-volume"></a>Conjuntos de cópia de segurança da lista para um volume
 
Conclua os passos seguintes para listar todas as cópias de segurança para um volume.

#### <a name="to-list-backup-sets"></a>Para conjuntos de cópia de segurança de lista

1. Na página de serviço StorSimple Manager, clique no separador de **catálogo de cópia de segurança** .

2. Filtrar as seleções da seguinte forma:

    1. Selecione o dispositivo adequado.

    2. Na lista pendente, selecione um volume para ver as cópias de segurança correspondente.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.
 
    As cópias de segurança associadas ao volume selecionado deverão aparecer na lista de conjuntos de cópia de segurança.

## <a name="select-a-backup-set"></a>Selecione um conjunto de cópia de segurança

Conclua os passos seguintes para selecionar uma cópia de segurança definido para um volume ou política de cópia de segurança.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de cópia de segurança

1. Na página de serviço StorSimple Manager, clique no separador de **catálogo de cópia de segurança** .

2. Filtrar as seleções da seguinte forma:

    1. Selecione o dispositivo adequado.

    2. Na lista pendente, selecione a política de cópia de segurança ou volume para a cópia de segurança que pretende selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.

    As cópias de segurança associado o volume seleccionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.

3. Selecione e expandir um conjunto de cópia de segurança. São apresentadas as opções de **Restaurar** e **Eliminar** na parte inferior da página. Pode efetuar qualquer uma das seguintes ações no conjunto de cópia de segurança que selecionou.

## <a name="delete-a-backup-set"></a>Eliminar um conjunto de cópia de segurança

Elimine uma cópia de segurança quando já não pretende manter os dados associados. Execute os seguintes passos para eliminar um conjunto de cópia de segurança.

#### <a name="to-delete-a-backup-set"></a>Para eliminar um conjunto de cópia de segurança

1. Na página de serviço StorSimple Manager, clique no **separador de catálogo de cópia de segurança**.

2. Filtrar as seleções da seguinte forma:

    1. Selecione o dispositivo adequado.

    2. Na lista pendente, selecione a política de cópia de segurança ou volume para a cópia de segurança que pretende selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.

    As cópias de segurança associado o volume seleccionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.

3. Selecione e expandir um conjunto de cópia de segurança. São apresentadas as opções de **Restaurar** e **Eliminar** na parte inferior da página. Clique em **Eliminar**.

4. Será notificado quando a eliminação estiver em curso e quando foi concluído com êxito. Depois da eliminação estiver concluída, atualize a consulta nesta página. O conjunto de cópia de segurança eliminado já não irá aparecer na lista de conjuntos de cópia de segurança.

## <a name="next-steps"></a>Próximos passos

- Saiba como [utilizar o catálogo de cópia de segurança para restaurar o seu dispositivo de um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
