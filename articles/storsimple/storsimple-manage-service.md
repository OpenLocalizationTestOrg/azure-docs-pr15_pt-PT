<properties 
   pageTitle="Implementar o serviço do Gestor de StorSimple | Microsoft Azure"
   description="Explica como criar e eliminar o serviço do Gestor de StorSimple no portal clássico do Azure e descreve como gerir a chave de registo do serviço."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-manager-service"></a>Implementar o serviço do Gestor de StorSimple

## <a name="overview"></a>Descrição geral

O serviço do Gestor de StorSimple é executado no Microsoft Azure e liga à múltiplos StorSimple dispositivos. Depois de criar o serviço, pode utilizá-lo para gerir os dispositivos a partir do portal clássico do Microsoft Azure em execução num browser. Esta opção permite-lhe monitorizar todos os dispositivos que estão ligados para o serviço do Gestor de StorSimple a partir de uma localização única e central, portanto minimizar encargos administrativos.

A página de destino do Gestor de StorSimple lista todos os serviços de Gestor de StorSimple que pode utilizar para gerir os dispositivos de armazenamento StorSimple. Para cada serviço StorSimple Gestor, é apresentada a seguinte informação na página Gestor de StorSimple de:

- **Nome** – o nome que tiver sido atribuído ao seu serviço de Gestor de StorSimple quando foi criada. O nome do serviço não pode ser alterado após o serviço é criado.

- **Estado** – o estado do serviço, que pode ser **ativo**, **Criar**ou **Online**.

- **Localização** – a localização geográfica na qual o dispositivo StorSimple será implementado.

- **Subscrição** – a subscrição de faturação associado ao seu serviço.

As tarefas comuns que podem ser executadas através da página de Gestor de StorSimple são:

- Criar um serviço
- Eliminar um serviço
- Obter a chave de registo do serviço
- Gerar a chave de registo do serviço

Neste tutorial descreve como efetuar cada uma das seguintes tarefas.

## <a name="create-a-service"></a>Criar um serviço

Utilize a opção de **Criação rápida** para criar um serviço de Gestor de StorSimple se pretende implementar o seu dispositivo StorSimple. Para criar um serviço, tem de ter:

- Uma subscrição com um acordo empresarial
- Uma conta de armazenamento do Microsoft Azure active
- As informações de faturaçãohttps que são utilizadas para gestão de acesso

Também pode optar por gerar uma conta de armazenamento predefinida quando criar o serviço.

Um serviço único pode gerir vários dispositivos. No entanto, um dispositivo não é possível abranger vários serviços. Uma grande empresa pode ter várias instâncias de serviço para trabalhar com diferentes subscrições, organizações ou até mesmo implementação localizações. Tenha em atenção que precisa de separar instâncias de serviço do Gestor de StorSimple para gerir dispositivos de série StorSimple 8000 e StorSimple Virtual matrizes.

Execute os passos seguintes para criar um serviço.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminar um serviço

Antes de eliminar um serviço, certifique-se de que nenhuma dispositivos ligados a estão a utilizar. Se o serviço é utilizado, desative os dispositivos ligados. A operação de desativar irá Server a ligação entre o dispositivo e o serviço, mas manter os dados de dispositivo na nuvem. 

[AZURE.IMPORTANT] Depois de um serviço é eliminado, a operação não pode ser revertida. Todos os dispositivos que estava a utilizar o serviço terá de ser fábrica repor antes de poder ser utilizada com outro serviço. Neste cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.

Execute os seguintes passos para eliminar um serviço.

### <a name="to-delete-a-service"></a>Para eliminar um serviço

1. Na página do **Gestor de StorSimple serviço** , selecione o serviço que pretende eliminar.

1. Clique em **Eliminar** na parte inferior da página.

1. Clique em **Sim** na notificação de confirmação. Poderá demorar alguns minutos para o serviço a ser eliminada.

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

Depois de ter criado um serviço com êxito, terá de registar o seu dispositivo StorSimple com o serviço. Para registar o seu dispositivo StorSimple primeiro, terá a chave de registo do serviço. Para registar dispositivos adicionais com um serviço de StorSimple existente, terá a chave de registo e a chave de encriptação de dados de serviço (que é gerada no primeiro dispositivo durante o registo). Para mais informações sobre a chave de encriptação de dados do serviço, consulte o artigo [StorSimple segurança](storsimple-security.md). Pode obter a chave de registo acedendo a **Chave de registo** na página **Serviços** .

Execute os passos seguintes para obter a chave de registo do serviço.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Manter a chave de registo do serviço numa localização segura. Terá esta tecla, bem como a chave de encriptação de dados de serviço, para registar dispositivos adicionais com este serviço. Depois de obter a chave de registo do serviço, terá de configurar o seu dispositivo através do Windows PowerShell para a interface de StorSimple.

Para obter detalhes sobre como utilizar esta chave de registo, consulte o artigo [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-2-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Gerar a chave de registo do serviço

Terá de voltar a gerar uma chave de registo de serviço se o é necessários para efetuar a rotação da chave ou se a lista de administradores do serviço foi alterado. Quando gerar a tecla, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que já estavam registados são afetados por este processo.

Execute os passos seguintes para gerar uma chave de registo de serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para gerar a chave de registo do serviço

1. Na página do **Gestor de StorSimple serviço** , clique em **Chave de registo**.

1. Na caixa de diálogo **Chave de registo do serviço** , clique em **Gerar**.

1. Verá uma mensagem de confirmação. Clique em **OK** para continuar com a nova geração.

1. Uma nova chave de registo de serviço serão apresentados.

1. Copie esta tecla e guardá-lo para registar quaisquer novos dispositivos com este serviço.

1. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-service/HCS_CheckIcon.png) para fechar esta caixa de diálogo.


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [processo de implementação de StorSimple](storsimple-deployment-walkthrough.md).

- Saiba mais sobre como [Gerir a sua conta de armazenamento StorSimple](storsimple-manage-storage-accounts.md).

- Saiba mais sobre como [utilizar o serviço de Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 
