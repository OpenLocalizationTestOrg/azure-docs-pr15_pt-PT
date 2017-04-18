<properties
   pageTitle="Atualizar a sua coleção de RemoteApp do Azure | Microsoft Azure"
   description="Saiba como atualizar a sua coleção de RemoteApp do Azure"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Atualizar uma coleção de no RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Seja reencaminhado vez, inevitavelmente, quando necessita de atualizar a aplicações ou imagem na sua coleção de RemoteApp do Azure. Se estiver a utilizar uma das imagens incluídas com a sua subscrição RemoteApp do Azure, na coleção de uma nuvem ou híbrido, todas as atualizações são processadas pelo RemoteApp do Azure propriamente dito, para que coloca mais facilmente.

No entanto, se estiver a utilizar uma imagem personalizada (que criadas de raiz ou que criou, modifique um dos nossos imagens), for o responsável pelo mantendo a imagem e apps. Se precisar de atualizar a sua imagem ou das aplicações dentro da mesma, tem de criar uma nova versão actualizada da imagem e, em seguida, substitua a imagem existente na sua coleção de com esta nova imagem atualizada.

Por isso, como aceda informações sobre a atualização da coleção de? É bastante simples:

1. Actualize a imagem que utilizou na sua coleção. Aplicar qualquer patches ou atualizações conforme necessário e, em seguida, guarde-o com um novo nome.
2. [Carregar](remoteapp-uploadimage.md) ou [Importar](remoteapp-image-on-azurevm.md) que imagem para RemoteApp.
3. Agora, na página coleções de sites, clique em **Atualizar**.
4. Selecione a nova imagem a partir da lista de **Imagem do modelo** .
4. Segue-se a peça complicada - tem de decidir como lidar com todos os utilizadores que estão a utilizar uma aplicação na coleção de. Tem as seguintes opções:
    - **Dar aos utilizadores 60 minutos após a atualização**. Assim que a atualização estiver concluída, o Azure RemoteApp apresentar uma mensagem a todos os utilizadores ativos informá-los para guardar o seu trabalho e terminar sessão e iniciar sessão novamente. Depois de 60 minutos, qualquer utilizadores ativos que não tem sessão iniciada desativar serão automaticamente terminados. Os utilizadores podem imediatamente voltar a iniciar sessão.
    - **Inicie sessão utilizadores saída imediatamente**. Assim que a atualização estiver concluída, termine a sessão todos os utilizadores automaticamente sem qualquer aviso. Se selecionar esta opção, os utilizadores podem perder dados. No entanto, estes podem voltar a ligar à aplicação imediatamente.

1. Clique na marca de verificação para iniciar a atualização.
