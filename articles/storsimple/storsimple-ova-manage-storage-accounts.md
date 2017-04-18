<properties 
   pageTitle="Gerir a sua conta de armazenamento StorSimple | Microsoft Azure"
   description="Explica como pode utilizar a página de Gestor de StorSimple configurar para adicionar, editar, eliminar ou rodar as teclas de segurança de uma conta de armazenamento associada a matriz Virtual StorSimple."
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
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de StorSimple para gerir contas de armazenamento para StorSimple Virtual matriz

## <a name="overview"></a>Descrição geral

Página **Configurar** apresenta os parâmetros do serviço global que podem ser criados no serviço StorSimple gestor. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

- Contas de armazenamento 
- Registos de controlo do Access 

Neste tutorial explica como pode utilizar a página de **Configurar** para adicionar, editar ou eliminar contas de armazenamento para sua matriz de Virtual StorSimple. As informações neste tutorial só se aplica à matriz Virtual StorSimple executar software de lançamento de Março de 2016 das versões DG.

 ![Configurar página](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

As contas de armazenamento contêm as credenciais de que o dispositivo utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços na nuvem. Para contas do Microsoft Azure armazenamento, estas são as credenciais como o nome da conta e a chave primária de acesso. 

Na página **Configurar** , todas as contas de armazenamento que são criadas para a subscrição de faturação são apresentadas num formato tabular que contém as seguintes informações:

- **Nome** – o nome exclusivo atribuído à conta quando foi criada.
- O **SSL ativado** – se o SSL está ativado e nuvem de dispositivo de comunicação é sobre o canal seguro.

As tarefas mais comuns relacionados com contas de armazenamento que podem ser executadas na página **Configurar** são:

- Adicionar uma conta de armazenamento 
- Editar uma conta de armazenamento 
- Eliminar uma conta de armazenamento 


## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Existem três tipos de contas de armazenamento que podem ser utilizados com o seu dispositivo StorSimple.

- **Contas de armazenamento gerados automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como esta conta de armazenamento é criada, consulte o artigo [criar um novo serviço](storsimple-ova-manage-service.md#create-a-service). 
- **Contas de armazenamento na subscrição do serviço** – estas são as contas do Azure armazenamento que estão associadas a subscrição do mesmo como que o serviço. Para saber mais sobre como estes armazenamento são criadas contas, consulte o artigo [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md). 
- **Contas de armazenamento fora a subscrição do serviço** – estas são as contas do Azure armazenamento que não estejam associadas com o seu serviço e provavelmente existia antes do serviço foi criado.

Cada matriz Virtual StorSimple cria um contentor (com um prefixo hcs) na conta de armazenamento associado. Neste contentor tem todos os dados na nuvem para o seu dispositivo. Não elimine este contentor acedendo ao mesmo através do serviço de armazenamento do Windows Azure, tal como esta ação irá resultar na perda de dados.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Pode adicionar uma conta de armazenamento à configuração do serviço de Gestor de StorSimple ao fornecer um nome amigável exclusivo e as credenciais de acesso que estão ligadas à conta de armazenamento. Também tem a opção de ativar o modo do segura de sockets layer (SSL) para criar um canal seguro para comunicação de rede entre o seu dispositivo e na nuvem.

Pode criar várias contas para um fornecedor de serviço de nuvem determinado. Enquanto está a ser guardada a conta de armazenamento, o serviço tenta comunicar com o seu fornecedor de serviços na nuvem. As credenciais e os materiais de acesso que forneceu serão autenticados neste momento. Apenas se a autenticação é concluída com êxito, é criada uma conta de armazenamento. Se a autenticação falhar, em seguida, uma mensagem de erro adequado será apresentada.

As contas de armazenamento do Gestor de recursos criadas no Azure portal também são suportadas com StorSimple. As contas de armazenamento do Gestor de recursos não irão aparecer na lista pendente de seleção, apenas o armazenamento contas criadas no portal do Azure clássico serão apresentadas. Contas de armazenamento do Gestor de recursos terá de ser adicionada utilizando o procedimento para adicionar uma conta de armazenamento, conforme descrito abaixo.

O procedimento para adicionar uma conta de armazenamento clássica Azure é detalhado abaixo.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Pode editar uma conta de armazenamento utilizada pelo seu dispositivo. Se editar uma conta de armazenamento que está atualmente a ser utilizado, os campos disponíveis para modificar são as teclas de acesso e o modo de SSL para a conta de armazenamento. Pode fornecer a nova tecla de acesso de armazenamento ou modificar a seleção de **SSL Activar modo** e guardar as definições de atualizado.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Na página de destino do serviço, selecione o seu serviço, faça duplo clique no nome do serviço e, em seguida, clique em **Configurar**.

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo **Contas de armazenamento Add/Edit** :

  1. Na lista pendente de **Armazenamento contas**, selecione uma conta existente que pretende modificar. 
  2. Se for necessário, pode modificar a seleção de **Ativar o modo de SSL** .
  3. Pode escolher a gerar as teclas de acesso de conta de armazenamento. Para mais informações, consulte o artigo [gerar as teclas de conta de armazenamento](storage-create-storage-account.md#manage-your-storage-access-keys). Fornece a nova chave de conta de armazenamento. Para uma conta de armazenamento Azure, esta é a chave primária de acesso. 
  4. Clique no ícone de verificação ![verificar ícone](./media/storsimple-ova-manage-storage-accounts/checkicon.png) para guardar as definições. Na página **Configurar** serão atualizadas as definições. 
  5. Na parte inferior da página, clique em **Guardar** para guardar as definições recentemente atualizadas. 

     ![Editar uma conta de armazenamento](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Eliminar uma conta de armazenamento

> [AZURE.IMPORTANT] Pode eliminar uma conta de armazenamento apenas se não estiver a ser utilizado. Se uma conta de armazenamento está a ser utilizado, será notificado.

#### <a name="to-delete-a-storage-account"></a>Para eliminar uma conta de armazenamento

1. Na página de destino de serviço de Gestor de StorSimple, selecione o seu serviço, faça duplo clique no nome do serviço e, em seguida, clique em **Configurar**.

2. Na lista de contas de armazenamento de tabela, Paire o cursor sobre a conta que pretende eliminar.

3. Será apresentado um ícone de eliminar (**x**) na coluna da direita representarem para essa conta de armazenamento. Clique no ícone de **x** para eliminar as credenciais.

4. Quando lhe for pedido de confirmação, clique em **Sim** para continuar com a eliminação. Na lista tabular será atualizada para refletir as alterações.

5. Na parte inferior da página, clique em **Guardar** para guardar as definições recentemente atualizadas.


## <a name="next-steps"></a>Próximos passos

- Obter informações sobre como [administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
