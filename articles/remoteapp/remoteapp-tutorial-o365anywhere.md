<properties
   pageTitle="Obter a mesma experiência do Office 365 em qualquer dispositivo com RemoteApp do Azure | Microsoft Azure"
   description="Saiba como partilhar qualquer aplicação do Office 365 com os seus utilizadores utilizando RemoteApp do Azure."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Obter a mesma experiência do Office 365 em qualquer dispositivo com RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este artigo aborda como implementar o Office 365 em qualquer dispositivo na sua empresa. Podem obter os seus utilizadores as mesmas capacidades e a experiência de IU Android, Apple e Windows.

Vamos irá efetuar este utilizando RemoteApp do Azure pelo Office 365 de alojamento em máquinas virtuais capaz de escala no Azure que os utilizadores podem ligar. Este conjunto de máquinas virtuais chamamos de uma coleção"nuvem".

## <a name="create-a-cloud-collection"></a>Criar uma coleção de nuvem

Em primeiro lugar depois de ter criado uma conta Azure, navegue até ao **RemoteApp** ao clicar na ligação no lado esquerdo.
![A mostrar Azure RemoteApp no Portal do Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Em seguida, continue ao clicar em **Novo** na parte inferior e "criar rápida" uma coleção de. Fornece um nome, a região, a subscrição, o plano e a imagem "Proffesional do Office 2013" que fornecemos.
![Caixa de diálogo Criar](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Depois de concluir o formulário que deverá começar o processo de criação de coleções de sites. Poderá demorar até para uma hora ou de forma.

![Em espera](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Assim que o processo for concluído, irá procurar algo parecido com. Se clico **publicação** podemos ver que a maioria das aplicações do Office foram publicadas para-nos já.
![Colecção de criado](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Aplicações publicadas](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Neste momento também pode adicionar mais utilizadores que têm acesso a esta coleção ao clicar em **Acesso do utilizador**.
![Configurar o acesso de utilizador](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Agora vamos experimentar ligar-se ao Office 365!

## <a name="connect-to-office-365"></a>Ligar para o Office 365

Vamos head sobre para [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), desloque-se para baixo e clique em **Transferir clientes** para instalar o cliente de RemoteApp do Azure no dispositivo que onde estiver. São as capturas de ecrã abaixo para Windows.

Assim que a aplicação é iniciada ser-lhe-á pedido para iniciar sessão com a sua conta Microsoft (anteriormente chamada um "Live ID"), utilize o mesmo como a sua conta Azure por agora. Quando tem sessão iniciada na devem ver uma notificação sobre convites novos, clique em não existem e deverá ver uma lista semelhante ilustrada abaixo. Aceite o convite, que corresponde ao seu e-mail de proprietário da conta Azure.

![Novo convite](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

O aspeto de quando existem convites de novos.

![Aceitar uma aplicação](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Depois de aceitar o convite deverá ver todas as aplicações do Office no cliente do Azure RemoteApp.

![Lista de aplicações](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Quando clica em qualquer um da que aplicação deverá começar a máquina virtual Azure e deve ser todos definidos! Desfrute!

![Iniciar](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
