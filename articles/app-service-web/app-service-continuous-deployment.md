<properties
    pageTitle="Implementação contínua Azure de aplicação de serviço | Microsoft Azure"
    description="Saiba como ativar implementação contínua à aplicação de serviço de Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="continuous-deployment-to-azure-app-service"></a>Implementação contínua Azure de aplicação de serviço

Este tutorial mostra-lhe como configurar um fluxo de trabalho de implementação contínua para a sua aplicação de [Serviço de aplicação do Azure] . Integração do serviço de aplicação com BitBucket, GitHub e serviços de equipa do Visual Studio (VSTS) permite um fluxo de trabalho de implementação contínua onde Azure obtém nas atualizações mais recentes do seu projeto publicadas para um destes serviços. Implementação contínua é uma ótima opção para projetos onde vários e contribuições frequentes estão a ser integradas.

## <a name="overview"></a>Ativar a implementação contínua

Para ativar a implementação contínua, 

1. Publica o seu conteúdo de aplicação para o repositório que será utilizado para implementação contínua.  
    Para mais informações sobre o projeto de publicação para estes serviços, consulte o artigo [criar um repo (GitHub)], [criar uma repo (BitBucket)]e [começar a trabalhar com VSTS].

2. No pá de menu da sua aplicação no [portal do Azure], clique em **implementação de aplicações > Opções de implementação**. Clique em **Escolher origem**e, em seguida, selecione a origem de implementação.  

    ![](./media/app-service-continuous-deployment/cd_options.png)
    
    > [AZURE.NOTE] Para configurar um VSTS conta para a implementação de aplicação de serviço, consulte este [tutorial](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
    
3. Conclua o fluxo de trabalho de autorização. 

4. Na pá **origem de implementação** , escolha o projeto e ramo para implementar a partir de. Quando terminar, clique em **OK**.
  
    ![](./media/app-service-continuous-deployment/github_option.png)

    > [AZURE.NOTE] Quando activar a implementação contínua com GitHub ou BitBucket, serão apresentados projectos públicos e privados.

    Aplicação de serviço cria uma associação com o repositório selecionado, obtém nos ficheiros a partir do ramo especificado e mantém uma clonar do seu repositório para a sua aplicação de serviço de aplicação. Quando configurar a implementação de contínua VSTS a partir do portal Azure, a integração utiliza a aplicação de serviço do [motor de implementação de Kudu](https://github.com/projectkudu/kudu/wiki), que já automatiza compilação e implementação tarefas com todas as `git push`. Não tem de configurar separadamente implementação contínua no VSTS. Depois de concluir este processo, o pá da aplicação de **Opções de implementação** irá mostrar uma implementação do active que indica a implementação com êxito.

5. Para verificar que a aplicação é implementada com êxito, clique no **URL** no topo da pá a aplicação no portal do Azure. 

6. Para verificar a implementação contínua está a ocorrer a partir do repositório da sua escolha, transmitir uma alteração para o repositório. A aplicação deverá actualizar para refletir as alterações em breve após a conclusão de push para o repositório. Pode verificar que foi movido na atualização no pá **Opções de implementação** da sua aplicação.

## <a name="VSsolution"></a>Contínua implementação de uma solução do Visual Studio 

Direccionar uma solução do Visual Studio para a aplicação de serviço de Azure é tão fácil como conduza um ficheiro simples Index. O processo de implementação de aplicação de serviço simplifica todos os detalhes, incluindo restaurar NuGet dependências e de construção de binários da aplicação. Pode seguir as práticas recomendadas de controlo de origem de manutenção de código apenas no seu repositório Git e permitir que encarregam-se do resto de implementação de aplicação de serviço.

Os passos para conduza a solução do Visual Studio para a aplicação de serviço são o mesmo, tal como a [secção anterior](#overview), desde que pode configura o seu solução e repositório da seguinte forma:

-   Utilize a opção de controlo de origem do Visual Studio para gerar um `.gitignore` de ficheiros tal como a imagem abaixo ou manualmente adicionar um `.gitignore` ficheiro no seu raiz repositório com conteúdo semelhante a este [exemplo .gitignore](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore). 

    ![](./media/app-service-continuous-deployment/VS_source_control.png)
 
-   Adicione árvore de directórios a solução toda a sua repositório, com o ficheiro .sln na raiz da repositório.

Assim que tiver configurar o seu repositório tal como descrito e configurado a sua aplicação no Azure para contínua publicação a partir de uma repositórios Git onlinehttps, pode desenvolver a sua aplicação do ASP.NET localmente no Visual Studio e implemente continuamente o seu código simplesmente ao conduza as suas alterações para o repositório Git online.

## <a name="disableCD"></a>Desativar a implementação contínua

Para desativar a implementação contínua, 

1. No pá de menu da sua aplicação no [portal do Azure], clique em **implementação de aplicações > Opções de implementação**. Em seguida, clique em **Desligar** no pá **Opções de implementação** .

    ![](./media/app-service-continuous-deployment/cd_disconnect.png)    

2. Depois de responder **Sim** à mensagem de confirmação, pode voltar a pá da sua aplicação e clique em **implementação de aplicações > Opções de implementação** se pretender para configurar a publicação a partir de outra fonte.

## <a name="additional-resources"></a>Recursos adicionais

* [Como investigar problemas comuns com a implementação contínuo](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Como utilizar o PowerShell para Azure]
* [Como utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux]
* [Documentação Git]
* [Kudu de projeto](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

[Azure de aplicação de serviço]: https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/ 
[Portal do Azure]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Como utilizar o PowerShell para Azure]: ../articles/powershell-install-configure.md
[Como utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux]: ../articles/xplat-cli-install.md
[Documentação Git]: http://git-scm.com/documentation

[Criar um repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Introdução ao VSTS]: https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md
