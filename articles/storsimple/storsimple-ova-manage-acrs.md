<properties 
   pageTitle="Gerir os registos de controlo de acesso para a matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve como gerir os registos de controlo de acesso (ACRs) para determinar quais anfitriões podem ligar a um volume na matriz StorSimple Virtual."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de StorSimple para gerir registos de controlo de acesso para a matriz Virtual StorSimple 

## <a name="overview"></a>Descrição geral

Registos de controlo de acesso (ACRs) permitem-lhe especificar quais anfitriões podem ligar a um volume na matriz Virtual StorSimple (também conhecido como o StorSimple no local virtual dispositivo). ACRs estão definidos para um volume específico e conter os nomes qualificado dos anfitriões iSCSI (IQNs). Quando um anfitrião tenta ligar a um volume, o dispositivo verifica ACR associado com esse volume para o nome IQN e, se existir uma correspondência, em seguida, a ligação é estabelecida. A secção **registos de controlo do access** na página **Configurar** apresenta todos os registos do controlo de acesso com as IQNs correspondentes dos anfitriões.

Neste tutorial explica as seguintes tarefas de relacionados com ACR comuns:

- Obter o IQN
- Adicionar um registo de controlo de acesso 
- Editar um registo de controlo de acesso 
- Eliminar um registo de controlo de acesso 

> [AZURE.IMPORTANT] 
> 
> - Ao atribuir uma ACR a um volume, encarregam-se que o volume não seja em simultâneo acedido por mais do que um anfitrião do que não sejam agrupadas porque este foi danificada o volume. 
> - Ao eliminar uma ACR a partir de um volume, certifique-se de que o anfitrião correspondente está não a aceder ao volume porque a eliminação poderá resultar numa interrupção de leitura e escrita.

## <a name="get-the-iqn"></a>Obter o IQN

Execute os passos seguintes para obter IQN de um anfitrião do Windows que está a executar o Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicionar um ACR

Utilize a página de **configuração** de serviço do Gestor de StorSimple para adicionar ACRs. Normalmente, vai associar um ACR um volume.

Para obter informações sobre como associar um ACR um volume, vá para [Adicionar um volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Ao atribuir uma ACR a um volume, encarregam-se que o volume não seja em simultâneo acedido por mais do que um anfitrião que não sejam agrupadas porque este foi danificada o volume.
 
Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página de destino do serviço, selecione o seu serviço, faça duplo clique no nome do serviço e, em seguida, clique no separador **configuração** .

    ![separador Configuração](./media/storsimple-ova-manage-acrs/acr1.png)

2. Na lista de tabela em **registos de controlo de acesso**, fornece um **nome** para o seu ACR.

3. Em **nome de iniciador iSCSI**, fornece o nome IQN do seu anfitrião do Windows. 

4. Clique em **Guardar** na parte inferior da página para guardar o ACR recentemente criado. Irá ver a seguinte mensagem de confirmação.

    ![mensagem de confirmação](./media/storsimple-ova-manage-acrs/acr2.png)

5. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-manage-acrs/check-icon.png). Na lista tabular será atualizada para refletir esta adição.

## <a name="edit-an-acr"></a>Editar uma ACR

Utilize a página de **configuração** no portal do Azure clássico para editar ACRs. 

> [AZURE.NOTE] Deverá modificar apenas essas ACRs que não estão no momento em utilização. Para editar uma ACR associado a um volume que está atualmente a ser utilizado, deverá pela primeira vez tomar o volume offline.

Execute os passos seguintes para editar uma ACR.

#### <a name="to-edit-an-acr"></a>Para editar uma ACR

1. Na página de destino do serviço, selecione o seu serviço de, faça duplo clique no nome do serviço e, em seguida, clique no separador **configuração** .

2. Na lista de tabela, os registos de controlo de acesso, Paire o cursor sobre o ACR que pretende modificar.

3. Fornece um novo nome e/ou IQN para o ACR.

4. Clique em **Guardar** na parte inferior da página para guardar o ACR modificado. Verá uma mensagem de confirmação. 

5. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-manage-acrs/check-icon.png). Na lista tabular será atualizada para refletir esta alteração.

## <a name="delete-an-access-control-record"></a>Eliminar um registo de controlo de acesso

Utilize a página de **configuração** no portal do Azure clássico para eliminar ACRs. 

> [AZURE.NOTE] 
> 
> - Deverá eliminar apenas essas ACRs que não estão no momento em utilização. Para eliminar uma ACR associado a um volume que está atualmente a ser utilizado, deverá pela primeira vez tomar o volume offline.
> - Ao eliminar uma ACR a partir de um volume, certifique-se de que o anfitrião correspondente está não a aceder ao volume porque a eliminação poderá resultar numa interrupção de leitura e escrita.

Execute os seguintes passos para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso

1. Na página de destino do serviço, selecione o seu serviço de, faça duplo clique no nome do serviço e, em seguida, clique no separador **configuração** .

2. Na lista de tabela, os registos de controlo de acesso (ACRs), Paire o cursor sobre o ACR que pretende eliminar.

3. Um ícone de eliminar (**x**) aparecerá na coluna da direita representarem para o ACR que selecionar. Clique no ícone de **x** para eliminar o ACR. Irá ver a seguinte mensagem de confirmação.

    ![mensagem de confirmação](./media/storsimple-ova-manage-acrs/acr3.png)

5. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-manage-acrs/check-icon.png). Na lista tabular será atualizada para refletir a eliminação.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [Adicionar volumes de instalação e configuração do ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).
