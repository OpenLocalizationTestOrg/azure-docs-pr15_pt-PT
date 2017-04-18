<properties 
    pageTitle="O que é o Azure RemoteApp? | Microsoft Azure" 
    description="Saiba como a partilha de aplicações e recursos para qualquer dispositivo através de Azure RemoteApp." 
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
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>O que é o Azure RemoteApp?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp traz a funcionalidade do programa do Microsoft RemoteApp no local, cópia pelos serviços de ambiente de trabalho remoto, para Azure. Azure RemoteApp ajuda-o a fornecer acesso seguro e remoto às aplicações a partir de muitos dispositivos de utilizador diferentes. Azure RemoteApp aloja que mostra basicamente não persistente sessões de Terminal Server na nuvem e, receberá a utilizá-los e partilhá-los com os seus utilizadores.

Com o Azure RemoteApp pode partilhar aplicações e recursos com utilizadores em praticamente qualquer dispositivo. As suas aplicações na nuvem, podemos alojar dimensionamento para satisfazer necessidades de utilizador e o que significa que recomendamos encarregam-se do hardware. Tudo o que tem de fazer é carregar as aplicações que pretende partilhar e, em seguida, obtenha os utilizadores utilizem nessas aplicações. [Obtêm os utilizadores manter os seus próprios dispositivos](remoteapp-clients.md), enquanto faz a gestão tudo através do portal do Azure. Ainda tem a opção de com as suas credenciais da empresa, permitindo-lhe garantir a segurança dos dados e apps.

Continue a ler para mais informações sobre RemoteApp do Azure, ou, se já podemos ter CONVICTOS, o que [experimentar saída agora](https://azure.microsoft.com/services/remoteapp/).

Tiver questões sobre RemoteApp do Azure? Veja os nossos [FAQ](remoteapp-faq.md).

Azure RemoteApp faz parte do [Microsoft infraestrutura de ambiente de Trabalho Virtual](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novo!** Pretende saber mais sobre RemoteApp do Azure? Ou pronto para validar RemoteApp do Azure à escala? Participar no nosso semanal [a especialistas seminário Web](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Coleções de RemoteApp Azure
Existem dois tipos de [RemoteApp do Azure coleções de sites](remoteapp-collections.md):


- Uma **coleção de nuvem** está alojado no e armazena os dados para programas na nuvem. Os utilizadores podem aceder aplicações ao iniciar sessão com a sua conta Microsoft ou as credenciais da empresa sincronizados ou Federado com o Azure Active Directory.

    Escolha uma coleção de nuvem quando a aplicação que pretende partilhar não requer uma ligação a qualquer outro recurso rede privada da sua empresa (por exemplo, através de um dispositivo VPN). Se a aplicação utiliza recursos na Internet, OneDrive ou Azure, uma coleção de nuvem irá funcionar para si. Também é mais rápida criar.

- Uma **coleção de híbrida** está alojado no e armazena os dados na nuvem Azure, mas também dados do access permite que os utilizadores e recursos armazenados na sua rede local. Os utilizadores podem aceder aplicações ao iniciar sessão com as respetivas credenciais da empresa sincronizados ou Federado com o Azure Active Directory.

    Escolha uma colecção de híbrido se necessitar de uma ligação para recursos na rede privada da sua empresa. Por exemplo, se a aplicação precisa de aceder a um dos seguintes procedimentos:

    - Servidores de ficheiro localizados na intranet
    - Quicken
    - Bases de dados através de um firewall

    Este é geralmente mais útil para grandes empresas com muitas recursos nas suas redes privadas que não podem ser movidas para a nuvem.

As coleções de ficheiros diferentes tem várias opções, incluindo redes, para que figura saída [que coleção](remoteapp-collections.md) melhor funcione para si. 


### <a name="updating-your-collection"></a>Atualizar a sua coleção de
Uma das principais diferenças entre as coleções híbrido e nuvem é atualizações de software que forma são processadas. Uma coleção de nuvem que utiliza a imagem pré-instalado do Office 365 ProPlus ou Office 2013, não tiver de se preocupar atualizações. O serviço mantém si próprio e descer saída atualizações contínua, para aplicações e o sistema operativo.

Para coleções de híbrido, bem como coleções na nuvem que utilizam uma imagem de modelo personalizado, for o responsável pelo mantendo a imagem e apps. Para imagens façam parte de um domínio, pode controlar atualizações utilizando ferramentas, como o Windows Update, política de grupo ou no Centro de sistema.

Depois de atualizar a sua imagem de modelo personalizado, carregue a nova imagem para a nuvem Azure e, em seguida, atualize a coleção de utilizar a nova imagem. (Pode fazê-lo a partir da RemoteApp do Azure **Início rápido** ou a página do Dashboard.)

Para obter mais informações, consulte a [atualizar a sua coleção](remoteapp-update.md) .

## <a name="supported-remoteapp-clients"></a>Clientes RemoteApp suportados
Azure RemoteApp é suportado em aplicações de cliente do RemoteApp para Windows e Windows RT, bem como as aplicações de ambiente de trabalho remoto do Microsoft para Mac, iOS e Android. Os utilizadores podem utilizar estas aplicações no seu telemóvel ou calcular dispositivos para aceder aos programas de RemoteApp do Azure novos.

Consulte o artigo [aceder a suas aplicações no Azure RemoteApp](remoteapp-clients.md) para obter mais informações sobre os clientes.

## <a name="next-steps"></a>Próximos passos
Ir! Experimente! Estes artigos ajudarem a começar com RemoteApp do Azure:

- [Que tipo de coleção de que necessita para RemoteApp do Azure?](remoteapp-collections.md)
- [Criar uma imagem de RemoteApp do Azure](remoteapp-imageoptions.md)
- [Como criar uma coleção de nuvem de RemoteApp do Azure](remoteapp-create-cloud-deployment.md)
- [Como criar uma coleção de híbrido de RemoteApp do Azure](remoteapp-create-hybrid-deployment.md)
- [Como licenciamento funciona RemoteApp do Azure?](remoteapp-licensing.md)
- [Práticas recomendadas para utilizar RemoteApp do Azure](remoteapp-bestpractices.md)
- [Perguntas mais frequentes do Azure RemoteApp](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Ajude-nos ajudá-lo 
Sabia que, para além de classificação deste artigo e fazer comentários para baixo abaixo, pode efetuar alterações ao artigo propriamente dito? Algo em falta? Algo errado? Escrever algo que é apenas confuso? Deslocar para cima e clique em **Editar no GitHub** ou **Editar** para efetuar alterações - aqueles seja reencaminhado-para revisão e, em seguida, assim que iniciar a sessão nos mesmos, verá as alterações e melhorias para a direita aqui.