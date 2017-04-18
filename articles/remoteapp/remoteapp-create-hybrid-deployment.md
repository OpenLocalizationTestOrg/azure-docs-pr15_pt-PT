<properties
    pageTitle="Como criar uma coleção de híbrido para RemoteApp do Azure | Microsoft Azure"
    description="Saiba como criar uma implementação do RemoteApp que se liga à sua rede interna."
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

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Como criar uma coleção de híbrido para RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Existem dois tipos de RemoteApp do Azure coleções de sites:

- Nuvem: completamente reside no Azure. Pode optar por guardar todos os dados na nuvem (para uma coleção de apenas na nuvem) ou para ligar a sua coleção da um VNET e guardar dados aí.   
- Híbrido: inclui uma rede virtual do access no local – é necessária a utilização do Azure AD e um ambiente do Active Directory no local.

Não sabe qual precisa? Consulte o artigo [que tipo de coleção de precisa para RemoteApp do Azure](remoteapp-collections.md).

Neste tutorial orienta-o processo de criação de uma coleção de híbrido. Existem oito passos:

1.  A [imagem](remoteapp-imageoptions.md) que utilizar para a sua coleção de decidir. Pode criar uma imagem personalizada ou utilizar uma das imagens do Microsoft incluídas com a sua subscrição.
2. Configure a rede virtual. Consulte as informações de [planeamento de VNET](remoteapp-planvnet.md) e [Dimensionar](remoteapp-vnetsizing.md) .
2.  Crie uma coleção de.
2.  Aderir a sua coleção de para o seu domínio local.
3.  Adicione uma imagem do modelo à coleção de.
4.  Configure a sincronização de diretórios. Azure RemoteApp requer que integrar com o Azure Active Directory por um dos 1) configurar Azure Active sincronização de diretórios com a opção de sincronização de palavra-passe ou 2) configurar Azure Active sincronização de diretórios sem a opção de sincronização de palavra-passe, mas utilizar um domínio que esteja federado para o AD FS. Consulte as [informações de configuração do Active Directory com RemoteApp](remoteapp-ad.md).
5.  Publica aplicações RemoteApp.
6.  Configure o acesso do utilizador.

**Antes de começar**

Precisa de fazer o seguinte antes de criar a coleção de:

- [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) no Azure RemoteApp.
- Crie uma conta de utilizador no Active Directory para utilizar como a conta de serviço RemoteApp do Azure. Restringir as permissões para esta conta para que só pode associar máquinas para o domínio.
- Recolher informações sobre a sua rede no local: endereço IP informações e detalhes do dispositivo VPN.
- Instale o módulo [Azure PowerShell](../powershell-install-configure.md) .
- Reunir informações sobre os utilizadores que pretende conceder acesso ao. Terá do nome principal de utilizador do Azure Active Directory (por exemplo, name@contoso.com) para cada utilizador. Certifique-se de que o UPN corresponde entre Azure AD e o Active Directory.
- Selecione a sua imagem do modelo. Uma imagem do modelo de RemoteApp do Azure contém as aplicações e programas que pretende publicar para os seus utilizadores. Para mais informações, consulte [Opções de imagem RemoteApp do Azure](remoteapp-imageoptions.md) .
- Pretende utilizar a imagem do Office 365 ProPlus? Consulte o artigo informações [aqui](remoteapp-officesubscription.md).
- [Configure o Active Directory para RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Passo 1: Configurar a sua rede virtual
Pode implementar uma coleção de híbrido que utiliza uma rede virtual Azure existente ou pode criar uma nova rede virtual. Uma rede virtual permite que os dados do access os utilizadores na sua rede local através de recursos remotos RemoteApp. Utilizar uma rede virtual Azure dá-o acesso à rede direta coleções de sites a outros serviços Azure e máquinas virtuais implementadas a essa rede virtual.

Certifique-se de que reveja as informações de [planeamento de VNET](remoteapp-planvnet.md) e [tamanho VNET](remoteapp-vnetsizing.md) antes de criar o seu VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Criar um VNET Azure e associá-lo para a sua implementação do Active Directory

Comece por criar uma [rede virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Isto é feito no separador **rede** no portal do Azure. Tem de se ligar à rede virtual para a implementação do Active Directory que é sincronizada para o inquilino do Azure Active Directory.

Para mais informações, consulte [criar uma rede virtual através do portal Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Certifique-se a que sua rede virtual está pronto para ser RemoteApp do Azure
Vamos antes de criar a coleção, certifique-se de que a sua nova rede virtual está pronta. Pode validar isto efetuando o seguinte procedimento:

1. Crie uma máquina de virtual Azure dentro da sub-rede da rede virtual que acabou de criar para RemoteApp.
2. Utilize o ambiente de trabalho remoto para ligar à máquina virtual. (Clique em **Ligar**).
3. Participá-lo para a implementação do Active Directory mesmo que pretende utilizar para RemoteApp.

Que funcionou? A rede virtual e sub-rede estão prontos a RemoteApp do Azure!

Pode encontrar mais informações sobre como criar máquinas virtuais Azure e estabelecer ligação ao-los com o ambiente de trabalho remoto [aqui](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Passo 2: Criar uma coleção de RemoteApp do Azure ##



1. No [portal do Azure](http://manage.windowsazure.com), aceda à página RemoteApp do Azure.
2. Clique em **Nova > criar com VNET**.
3. Introduza um nome para a coleção.
4. Escolha o plano que pretende utilizar - padrão ou básica.
5. Selecione o VNET pendente lista e, em seguida, sua sub-rede aceda.
6. Selecione aderir ao mesmo para o seu domínio.
5. Clique em **Criar RemoteApp coleções de sites**.

Depois de ter sido criada a sua coleção de RemoteApp do Azure, faça duplo clique no nome da coleção de. Que irá abra a página de **Início rápido** - é sempre que terminar de configurar a coleção.

Algo correr mal? Consulte as [informações de resolução de problemas do híbrido coleções de sites](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Passo 3: Ligar a sua coleção de para o domínio local ##


1. Na página de **Início rápido** , clique em **participar um domínio local**.
2. Adicione a conta de serviço do Azure RemoteApp para o seu domínio do Active Directory local. Terá do nome de domínio, a unidade organizacional, o nome de utilizador da conta de serviço e a palavra-passe.

    Este é as informações recolhidas a se seguiu os passos no [Configurar RemoteApp do Azure no Active Directory](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Passo 4: Hiperligação para uma imagem de RemoteApp do Azure ##

Uma imagem do modelo de RemoteApp do Azure contém os programas que pretende partilhar com utilizadores. Pode criar uma nova [imagem do modelo de](remoteapp-imageoptions.md) ou ligar a uma imagem existente (uma já importados ou carregado para RemoteApp do Azure). Também pode ligar a uma das RemoteApp do Azure [imagens de modelos](remoteapp-images.md) que contêm do Office 365 ou programas do Office 2013 (para utilização avaliação).

Se está a carregar a nova imagem, tem de introduzir o nome e escolha a localização para a imagem. Na página seguinte do assistente, verá um conjunto de cmdlets do PowerShell - copiar e executar estes cmdlets a partir de uma elevada Windows PowerShell para carregar a imagem especificada.

Se estiver a ligar a uma imagem de modelo existente, especifique simplesmente a subscrição Azure associada, a localização e o nome da imagem.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Passo 5: Configurar a sincronização de diretórios do Active Directory ##

Azure RemoteApp requer que integrar com o Azure Active Directory por um dos 1) configurar Azure Active sincronização de diretórios com a opção de sincronização de palavra-passe ou 2) configurar Azure Active sincronização de diretórios sem a opção de sincronização de palavra-passe, mas utilizar um domínio que esteja federado para o AD FS.

Consulte o artigo [Ligar AD](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) - este artigo ajuda-o a configurar integração de diretórios em 4 passos.

Consulte o artigo [informações gerais de sincronização de diretórios](http://msdn.microsoft.com//library/azure/hh967642.aspx) para planeamento informações e os passos detalhados.

## <a name="step-6-publish-apps"></a>Passo 6: Publicar aplicações ##

Uma aplicação do Azure RemoteApp é a aplicação ou o programa que fornecer aos seus utilizadores. Encontra-se na imagem do modelo carregado para a coleção. Quando um utilizador acede a uma aplicação, aparenta executar no seu ambiente de trabalho local, mas na verdade está em execução no Azure.

Antes dos utilizadores podem aceder aplicações, tem de publicá-los – Isto permite que os seus utilizadores acedem as aplicações através do cliente de ambiente de trabalho remoto.

Pode publicar várias aplicações à coleção de. Na página de publicação, clique em **Publicar** para adicionar uma aplicação. Quer pode publicar a partir do menu **Iniciar da imagem modelo ou ao especificar o caminho na imagem do modelo para a aplicação** . Se optar por adicionar a partir do menu **Iniciar** , escolha o programa para adicionar. Se optar por forneça o caminho para a aplicação, fornece um nome para a aplicação e o caminho para a imagem do modelo de onde está instalado.

## <a name="step-7-configure-user-access"></a>Passo 7: Configurar o acesso de utilizador ##

Agora que criou a sua coleção de, tem de adicionar os utilizadores que pretende que possam utilizar os recursos remotos. Os utilizadores que fornecer acesso ao precisa de existir no inquilino do Active Directory associado à subscrição que utilizou para criar nesta coleção de RemoteApp do Azure.

1.  Na página de início rápido, clique em **configurar o acesso do utilizador**.
2.  Introduza a conta profissional (a partir do Active Directory) ou uma conta Microsoft que pretende conceder acesso a.

    **Notas:**

    Certifique-se de que utiliza o “user@domain.com” formatar.

    Se estiver a utilizar Office 365 ProPlus na sua coleção de, tem de utilizar as identidades do Active Directory para os seus utilizadores. Isto ajuda a validar licenciamento.


3.  Assim que os utilizadores são validados, clique em **Guardar**.


## <a name="next-steps"></a>Próximos passos ##
Já está - tiver criado e implementado a sua coleção de implementações do Azure RemoteApp com êxito. O passo seguinte é para que os utilizadores transferir e instalar o cliente de ambiente de trabalho remoto. Pode encontrar o URL para o cliente na página de início rápido do Azure RemoteApp. Em seguida, tem os utilizadores iniciam para o cliente e aceder as aplicações publicado.



### <a name="help-us-help-you"></a>Ajude-nos ajudá-lo
Sabia que, para além de classificação deste artigo e fazer comentários para baixo abaixo, pode efetuar alterações ao artigo propriamente dito? Algo em falta? Algo errado? Escrever algo que é apenas confuso? Deslocar para cima e clique em **Editar no GitHub** para efetuar alterações - aqueles seja reencaminhado-para revisão e, em seguida, assim que iniciar a sessão nos mesmos, verá as alterações e melhorias para a direita aqui.
