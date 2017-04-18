<properties 
   pageTitle="Gerir a sua conta de armazenamento StorSimple | Microsoft Azure"
   description="Explica como pode utilizar a página de Gestor de StorSimple configurar para adicionar, editar e eliminar ou rodar as teclas de segurança de uma conta de armazenamento."
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
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Utilizar o serviço do Gestor de StorSimple para gerir a sua conta de armazenamento

## <a name="overview"></a>Descrição geral

Página **Configurar** apresenta todos os parâmetros de serviço global que podem ser criados no serviço StorSimple gestor. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

- Contas de armazenamento 
- Modelos de largura de banda 
- Registos de controlo do Access 

Neste tutorial explica como pode utilizar a página **Configurar** para adicionar, editar, ou elimine contas de armazenamento ou rodar as teclas de segurança de uma conta de armazenamento.

 ![Configurar página](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

As contas de armazenamento contêm as credenciais de que o dispositivo utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços na nuvem. Para contas do Microsoft Azure armazenamento, estas são as credenciais como o nome da conta e a chave primária de acesso. 

Na página **Configurar** , todas as contas de armazenamento que são criadas para a subscrição de faturação são apresentadas num formato tabular que contém as seguintes informações:

- **Nome** – o nome exclusivo atribuído à conta quando foi criada.
- O **SSL ativado** – se o SSL está ativado e nuvem de dispositivo de comunicação é sobre o canal seguro.
- **Utilizado por** – o número de volumes utilizando a conta de armazenamento.

As tarefas mais comuns relacionados com contas de armazenamento que podem ser executadas na página **Configurar** são:

- Adicionar uma conta de armazenamento 
- Editar uma conta de armazenamento 
- Eliminar uma conta de armazenamento 
- Rotação da chave de contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Existem três tipos de contas de armazenamento que podem ser utilizados com o seu dispositivo StorSimple.

- **Contas de armazenamento gerados automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como esta conta de armazenamento é criada, consulte o artigo [passo 1: criar um novo serviço](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) no [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough.md). 
- **Contas de armazenamento na subscrição do serviço** – estas são as contas do Azure armazenamento que estão associadas a subscrição do mesmo como que o serviço. Para saber mais sobre como estes armazenamento são criadas contas, consulte o artigo [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md). 
- **Contas de armazenamento fora a subscrição do serviço** – estas são as contas do Azure armazenamento que não estejam associadas com o seu serviço e provavelmente existia antes do serviço foi criado.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Pode adicionar uma conta de armazenamento ao fornecer um nome amigável exclusivo e as credenciais de acesso que estão ligadas à conta de armazenamento (com o fornecedor de serviço de nuvem especificada). Também tem a opção de ativar o modo do segura de sockets layer (SSL) para criar um canal seguro para comunicação de rede entre o seu dispositivo e na nuvem.

Pode criar várias contas para um fornecedor de serviço de nuvem determinado. Tenha em atenção, no entanto, quando é criada uma conta de armazenamento, não é possível alterar o fornecedor de serviços na nuvem.

Enquanto está a ser guardada a conta de armazenamento, o serviço tenta comunicar com o seu fornecedor de serviços na nuvem. As credenciais e os materiais de acesso que forneceu serão autenticados neste momento. Apenas se a autenticação é concluída com êxito, é criada uma conta de armazenamento. Se a autenticação falhar, será apresentada uma mensagem de erro adequado.

As contas de armazenamento do Gestor de recursos criadas no Azure portal também são suportadas com StorSimple. As contas de armazenamento do Gestor de recursos não irão aparecer na lista pendente de seleção quando tentar criar um contentor de volume, apenas as contas de armazenamento criadas no portal do Azure clássico será apresentada. Contas de armazenamento do Gestor de recursos terá de ser adicionada utilizando o procedimento para adicionar uma conta de armazenamento descrita abaixo.

> [AZURE.NOTE] O procedimento para adicionar uma conta de armazenamento difere conforme a versão do software StorSimple que estiver a utilizar. Certifique-se de que siga o procedimento correto para a sua versão de StorSimple.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Pode editar uma conta de armazenamento é utilizada por um contentor de volume. Se editar uma conta de armazenamento que está atualmente a ser utilizado, o campo apenas disponível para modificar é a tecla de acesso para a conta de armazenamento. Pode fornecer a nova tecla de acesso de armazenamento e guardar as definições de atualizado.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Na página de destino do serviço, selecione o seu serviço, faça duplo clique no nome do serviço e, em seguida, clique em **Configurar**.

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo **Contas de armazenamento Add/Edit** :

  1. Na lista pendente de **Armazenamento contas**, selecione uma conta existente que pretende modificar. Isto também pode incluir as contas de armazenamento que foram geradas automaticamente quando o serviço foi criado pela primeira vez.
  2. Se for necessário, pode modificar a seleção de **Ativar o modo de SSL** .
  3. Pode escolher rodar as teclas de acesso de conta de armazenamento. Consulte o artigo [rotação da chave de contas de armazenamento](#key-rotation-of-storage-accounts) para obter mais informações sobre como executar a rotação da chave.
  4. Clique no ícone de verificação ![verificar ícone](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) para guardar as definições. Na página **Configurar** serão atualizadas as definições. Clique em **Guardar** para guardar as definições recentemente atualizadas.

     ![Editar uma conta de armazenamento](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Eliminar uma conta de armazenamento

> [AZURE.IMPORTANT] Pode eliminar uma conta de armazenamento apenas se não é utilizada por um contentor de volume. Se uma conta de armazenamento está a ser utilizada por um contentor de volume, elimine o contentor de volume e, em seguida, eliminar a conta de armazenamento associado.

#### <a name="to-delete-a-storage-account"></a>Para eliminar uma conta de armazenamento

1. Na página de destino de serviço de Gestor de StorSimple, selecione o seu serviço, faça duplo clique no nome do serviço e, em seguida, clique em **Configurar**.

2. Na lista de contas de armazenamento de tabela, Paire o cursor sobre a conta que pretende eliminar.

3. Será apresentado um ícone de eliminar (**x**) na coluna da direita representarem para essa conta de armazenamento. Clique no ícone de **x** para eliminar as credenciais.

4. Quando lhe for pedido de confirmação, clique em **Sim** para continuar com a eliminação. Na lista tabular será atualizada para refletir as alterações.

## <a name="key-rotation-of-storage-accounts"></a>Rotação da chave de contas de armazenamento

Por motivos de segurança, rotação da chave é frequentemente um requisito nos centros de dados. 

> [AZURE.NOTE] As seguintes informações de rotação da chave e o procedimento de rotação aplicar a apenas contas do Microsoft Azure armazenamento. Se estiver a utilizar outro fornecedor de serviços na nuvem, pode gerir as teclas de conta de armazenamento através do dashboard esse fornecedor.
 
Cada subscrição do Microsoft Azure pode ter uma ou mais contas de armazenamento associado. O acesso nestas contas é controlado pelas teclas de acesso e de subscrição para cada conta de armazenamento. 

Quando cria uma conta de armazenamento, Microsoft Azure gera duas teclas de acesso de armazenamento de 512 bits que são utilizadas para autenticação, quando a conta de armazenamento é acedida. Está a ter duas teclas de acesso de armazenamento permite-lhe gerar chaves com acesso para esse serviço ou sem interrupções no seu serviço de armazenamento. A chave que está atualmente a ser utilizado é a chave *primária* e chave de cópia de segurança é referida como a chave *secundária* . Uma destas duas chaves tem de ser fornecida quando o dispositivo do Microsoft Azure StorSimple acede ao seu fornecedor de serviços de armazenamento na nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação da chave?

Normalmente, aplicações utilizam apenas uma das chaves para aceder aos seus dados. Após um determinado período de tempo, pode ter suas aplicações de mudar o para utilizar na segunda chave. Depois de ter mudado suas aplicações para a chave secundária, pode retirar a primeira tecla e, em seguida, gerar uma nova chave. Utilizando as teclas de duas segundo este método permite que o acesso de aplicações para os dados sem sempre qualquer tempo de inatividade.

As teclas de conta de armazenamento sempre são armazenadas no serviço num formulário encriptado. Contudo, estes podem ser repor através do serviço de Gestor de StorSimple. O serviço pode obter a chave primária e chave secundária para todas as contas de armazenamento na mesma subscrição, incluindo contas criadas no serviço de armazenamento, bem como o armazenamento de predefinido criadas contas geradas quando o serviço de Gestor de StorSimple foi primeiro. O serviço de Gestor de StorSimple irá obter sempre estas teclas a partir do portal clássico Azure e, em seguida, armazená-los de uma forma encriptada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode gerar ou alterar a chave primária ou secundária acedendo diretamente a conta de armazenamento (através do serviço de armazenamento do Windows Azure). O serviço do Gestor de StorSimple não ver esta alteração automaticamente.

Para informar o serviço do Gestor de StorSimple da alteração, será precisa de para aceder ao serviço de Gestor de StorSimple acesso à conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo de qual foi alterado). Em seguida, o serviço obtém a chave mais recente, encripta as teclas e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar teclas para contas de armazenamento na mesma subscrição do que o serviço (apenas para o Azure)

1. Na página **Serviços** , clique no separador **Configurar** .

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo, faça o seguinte:

  1. Selecione a conta de armazenamento com a chave que pretende sincronizar. As teclas de conta de armazenamento são encriptadas quando são apresentados.
  2. No serviço StorSimple Gestor, tem de atualizar a chave que foi alterada anteriormente no serviço de armazenamento do Windows Azure. Se a chave primária do access foi alterada (recuperada), clique em **sincronizar chave primária**. Se a chave secundária tiver sido alterada, clique em **sincronizar chave secundária**.

    ![sincronizar teclas](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar teclas para contas de armazenamento fora a subscrição do serviço

1. Na página **Serviços** , clique no separador **Configurar** .

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo, faça o seguinte:

  1. Selecione a conta de armazenamento com a tecla de acesso que pretende atualizar.
  2. Terá de atualizar a tecla de acesso de armazenamento no serviço StorSimple gestor. Neste caso, pode ver a tecla de acesso de armazenamento. Introduza a nova chave na caixa **Tecla de acesso de conta de armazenamento**y. 
  3. Guarde as suas alterações. Agora deverá ser actualizado a sua chave de acesso da conta de armazenamento.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [segurança StorSimple](storsimple-security.md).
- Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
