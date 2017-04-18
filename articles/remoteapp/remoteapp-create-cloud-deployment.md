<properties 
    pageTitle="Como criar uma coleção de nuvem de RemoteApp do Azure | Microsoft Azure" 
    description="Saiba como criar uma implementação do Azure RemoteApp que guarda os dados na nuvem Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Como criar uma coleção de nuvem de RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Existem dois tipos de [RemoteApp do Azure coleções de sites](remoteapp-collections.md): 

- Nuvem: completamente reside no Azure. Pode optar por guardar todos os dados na nuvem (para uma coleção de apenas na nuvem) ou para ligar a sua coleção da um VNET e guardar dados aí.   
- Híbrido: inclui uma rede virtual do access no local – é necessária a utilização do Azure AD e um ambiente do Active Directory no local.

Neste tutorial orienta-o processo de criação de uma coleção de nuvem. Existem quatro passos: 

1.  Crie uma coleção de RemoteApp do Azure.
2.  Opcionalmente, configure da sincronização de diretórios. Se estiver a utilizar o Azure AD + Active Directory, tem de sincronizar os utilizadores, contactos e palavras-passe a partir do seu no local Active Directory ao seu inquilino do Azure AD.
5.  Publica aplicações.
6.  Configure o acesso do utilizador.


**Antes de começar**

Precisa de fazer o seguinte antes de criar a coleção de:

- [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) no Azure RemoteApp. 
- Reunir informações sobre os utilizadores que pretende conceder acesso ao. Isto pode ser informações da conta Microsoft ou informações de conta de trabalho do Active Directory para os utilizadores.
- Este procedimento assume que é um dos aceder ao utilizar uma das imagens dos modelos fornecidas como parte da sua subscrição ou que já tem carregados a imagem do modelo que pretende utilizar. Se precisar de carregar uma imagem de modelo diferente, pode fazê-lo a partir da página de imagens de modelos. Basta clicar **carregar uma imagem do modelo** e siga os passos no assistente. 
- Pretende utilizar a imagem do Office 365 ProPlus? Consulte o artigo informações [aqui](remoteapp-officesubscription.md).
- Pretende fornecer aplicações personalizadas ou LOB programas? Criar uma nova [imagem](remoteapp-imageoptions.md) e utilizá-lo na sua coleção de nuvem.
- Descubra se é necessário ligar a um VNET. Se optar por ligar a um VNET, certifique-se de que cumpre as [diretrizes de redimensionamento](remoteapp-vnetsizing.md) e que a mesma [possa ligar à RemoteApp](remoteapp-vnet.md). Consulte o [artigo planeamento VNET ](remoteapp-planvnet.md)para obter mais informações.
- Se estiver a utilizar um VNET, decida se pretende associá-lo para o seu domínio do Active Directory local.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Passo 1: Criar uma coleção de nuvem - com ou sem um VNET##


Utilize os passos seguintes para **criar uma colecção de apenas na nuvem**:

1. No portal de gestão, aceda à página RemoteApp.
2. Clique em **Nova > criar rápida**.
3. Introduza um nome para a coleção de e selecione o seu região.
4. Escolha o plano que pretende utilizar - padrão ou básica.
5. Selecione o modelo a utilizar para esta coleção. 

    **Sugestão:** A sua subscrição RemoteApp vem com [imagens de modelos](remoteapp-images.md) que contêm o Office 365 ou Office 2013 (para utilização avaliação) programas, algumas publicado (tal como o Word) e outras pessoas pronto para publicar. Também pode criar uma nova [imagem](remoteapp-imageoptions.md) e utilizá-la na sua coleção de nuvem.


1. Clique em **Criar RemoteApp coleções de sites**.
    
    **Importantes:** Pode demorar até 30 minutos para aprovisionar a coleção.

Depois de ter sido criada a sua coleção de RemoteApp do Azure, faça duplo clique no nome da coleção de. Que irá abra a página de **Início rápido** - é sempre que terminar de configurar a coleção.

Utilize os passos seguintes para criar um **nuvem + coleção VNET**:

1. No portal de gestão, aceda à página RemoteApp do Azure.
2. Clique em **Novo** > **criar com VNET**.
3. Introduza um nome para a coleção.
4. Escolha o plano que pretende utilizar - padrão ou básica.
5. Selecione VNET já tiver criado. Não sabe como fazê-lo? Por agora, os passos são no tópico [híbrido](remoteapp-create-hybrid-deployment.md) .
6. Decida se pretende participar na sua coleção de para o seu domínio. Se Sim, terá de utilizar o AD Connect para integrar o Azure AD e o seu ambiente do Active Directory. Que está incluído no abaixo no **passo 2**.
6. Clique em **Criar RemoteApp coleções de sites**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Passo 2: Configurar a sincronização de diretórios do Active Directory (opcional) ##

Se pretender utilizar o Active Directory, RemoteApp do Azure requer da sincronização de diretórios entre o Azure Active Directory e o Active Directory no local para sincronizar os utilizadores, contactos e palavras-passe ao seu inquilino do Azure Active Directory. Consulte o artigo [Configurar o Active Directory para RemoteApp do Azure](remoteapp-ad.md) para planeamento de informações. Também pode aceder diretamente ao [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) para obter informações.

## <a name="step-3-publish-apps"></a>Passo 3: Publicar aplicações ##

Uma aplicação do Azure RemoteApp é a aplicação ou o programa que fornecer aos seus utilizadores. Encontra-se na imagem do modelo carregado para a coleção. Quando um utilizador acede a uma aplicação, a aplicação é apresentada ser executado no seu ambiente de trabalho local, mas na verdade está em execução numa máquina virtual no Azure. 

Antes dos utilizadores podem aceder aplicações, tem de publicá-los – publicar aplicações permite os seus utilizadores acedem as aplicações através do cliente de ambiente de trabalho remoto.
 
Pode publicar várias aplicações à coleção de RemoteApp do Azure. Na página de publicação, clique em **Publicar** para adicionar um programa. Quer pode publicar a partir do menu **Iniciar da imagem modelo ou ao especificar o caminho na imagem do modelo para a aplicação** . Se optar por adicionar a partir do menu **Iniciar** , selecione a aplicação para publicar. Se optar por forneça o caminho para a aplicação, fornece um nome para a aplicação e o caminho para a imagem do modelo de onde está instalado.

## <a name="step-4-configure-user-access"></a>Passo 4: Configurar o acesso de utilizador ##

Agora que criou a sua coleção de, tem de adicionar os utilizadores que pretende que possam utilizar os recursos remotos. Se estiver a utilizar o Active Directory, os utilizadores que fornecer acesso ao precisa de existir no inquilino do Active Directory associado à subscrição que utilizou para criar nesta coleção.

1.  Na página de início rápido, clique em **configurar o acesso do utilizador**. 
2.  Introduza a conta profissional (a partir do Active Directory) ou uma conta Microsoft que pretende conceder acesso a.

    **Notas:** 

    Certifique-se de que utiliza o “user@domain.com” formatar.

    Se estiver a utilizar Office 365 ProPlus na sua coleção de, tem de utilizar as identidades do Active Directory para os seus utilizadores. Isto ajuda a validar licenciamento. 

3.  Depois dos utilizadores são validados, clique em **Guardar**.


## <a name="next-steps"></a>Próximos passos ##

Já está - tiver criado e implementado a sua coleção de nuvem RemoteApp do Azure com êxito. O passo seguinte é para que os utilizadores transferir e instalar o cliente de ambiente de trabalho remoto. Pode encontrar o URL para o cliente na página de início rápido do Azure RemoteApp. Em seguida, tem os utilizadores iniciam para o cliente e aceder as aplicações publicado.

### <a name="help-us-help-you"></a>Ajude-nos ajudá-lo 
Sabia que, para além de classificação deste artigo e fazer comentários para baixo abaixo, pode efetuar alterações ao artigo propriamente dito? Algo em falta? Algo errado? Escrever algo que é apenas confuso? Deslocar para cima e clique em **Editar no GitHub** para efetuar alterações - aqueles seja reencaminhado-para revisão e, em seguida, assim que iniciar a sessão nos mesmos, verá as alterações e melhorias para a direita aqui.