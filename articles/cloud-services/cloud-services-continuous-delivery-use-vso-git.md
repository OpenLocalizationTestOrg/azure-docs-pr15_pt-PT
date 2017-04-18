<properties
    pageTitle="Entrega contínua com Git e Visual Studio Team Services no Azure | Microsoft Azure"
    description="Saiba como configurar os seus projetos da equipa de serviços de equipa do Visual Studio para utilizar Git automaticamente criem e implementem a funcionalidade de Web App nos serviços de aplicação de serviço de Azure ou na nuvem."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Entrega contínua para Azure utilizando os serviços de equipa do Visual Studio e Git

Pode utilizar projetos do Visual Studio Team Services equipa para aloja um repositório de Git para o seu código de origem e automaticamente criar e implementar ao Azure web apps ou serviços em nuvem sempre que uma consolidação notificações push para o repositório.

Irá precisar Visual Studio 2013 e o SDK do Azure instalado. Se ainda não tiver o Visual Studio 2013, transfira-o ao selecionar a ligação **começar gratuitamente** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure a partir de [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Precisa de uma conta de serviços de equipa do Visual Studio para concluir este tutorial: pode [Abrir uma conta do Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço na nuvem automaticamente criem e implementem para Azure, utilizando os serviços de equipa do Visual Studio, siga estes passos.

## <a name="1-create-a-git-repository"></a>1: criar um repositório de Git

1. Se ainda não tiver uma conta de serviços de equipa do Visual Studio, pode obter um [aqui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando criar o seu projeto de equipa, selecione Git como o seu sistema de controlo de origem. Siga as instruções para ligar o Visual Studio ao seu projeto de equipa.

2. No **Explorador de equipa**, selecione a ligação **clonar este repositório** .

    ![][3]

3. Especificar a localização da cópia local e, em seguida, selecione o botão de **clonar** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: criar um projeto e a consolidação-lo para o repositório

1. No **Explorador de equipa**, na secção **soluções** , selecione a ligação de **Novo** para criar um novo projeto no repositório de local.

    ![][4]

2. Pode implementar uma aplicação web ou num serviço na nuvem (aplicação do Azure) ao seguir os passos neste tutorial. Crie um novo projeto de serviço em nuvem Azure ou um novo projeto de ASP.NET MVC. Certifique-se de que o projeto destina-se o .NET Framework 4 ou posterior. Se estiver a criar um projecto de serviço de nuvem, adicione uma função de web do ASP.NET MVC e uma função de trabalho.
Se pretender criar uma aplicação web, o modelo de projeto de **Aplicação Web do ASP.NET** e, em seguida, selecione **MVC**. Para mais informações, consulte [criar uma aplicação web do ASP.NET na aplicação de serviço de Azure](../app-service-web/web-sites-dotnet-get-started.md) .

3. Abrir o menu de atalho para a solução e selecione **a consolidação**.

    ![][7]

4. Se esta for a primeira vez que utilizou Git nos serviços de equipa do Visual Studio, terá de fornecer algumas informações para identifique-se no Git. Na área de **Alterações pendentes** do **Explorer de equipa**, introduza o seu nome de utilizador e endereço de e-mail. Introduza um comentário para a consolidação e, em seguida, selecione o botão de **consolidação** .

    ![][8]

5. Tenha em atenção as opções para incluir ou excluir alterações específicas quando dar entrada. Se as alterações que pretende forem excluídas, selecione **Incluir tudo**.

6. Agora já consolidada as alterações na cópia local do repositório. Em seguida, sincronize essas alterações com o servidor ao selecionar a ligação de **sincronização** .

## <a name="3-connect-the-project-to-azure"></a>3: ligar o projecto a Azure

1. Agora que tem um repositório de Git nos serviços de equipa do Visual Studio com algum código de origem no mesmo, está pronto para ligar do repositório de git Azure.  No [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), selecione a aplicação de serviço ou web na nuvem ou crie um novo ao selecionar o ícone no canto inferior esquerdo e escolher **Serviço na nuvem** ou **Web App** e, em seguida, **Criar rápida**+.

    ![][9]

3. Para serviços em nuvem, selecione a ligação **Configurar de publicação com os serviços de equipa do Visual Studio** . Para aplicações web, selecione a ligação **Configurar a implementação de controlo de origem** .

    ![][10]

2. No Assistente de, escreva o nome da sua conta de serviços de equipa do Visual Studio na caixa de texto e escolha a ligação **Autorizar agora** . Poderá ser-lhe pedido para iniciar sessão.

    ![][11]

3. No diálogo pop-up **Pedido de ligação** , selecione **Aceitar** para autorizar Azure para configurar o seu projeto de equipa no Visual Studio Team Services.

    ![][12]

4. Depois de autorização de ser bem sucedida, verá uma lista pendente que contém os seus projetos do Visual Studio Team Services equipa.  Selecione o nome do projeto de equipa que criou nos passos anteriores e selecione o botão de marca de verificação o assistente.

    ![][13]

    Da próxima vez que um Consolidar notificações push para o seu repositório, serviços de equipa do Visual Studio irá criar e implementar o projeto para Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: acionar um reconstruir e implementar o projeto

1. No Visual Studio, abrir um ficheiro e alterá-la. Por exemplo, alterar o ficheiro `_Layout.cshtml` em vistas\\pasta partilhada numa função de web MVC.

    ![][17]

2. Edite o texto do rodapé para o site e guarde o ficheiro.

    ![][18]

3. No **Explorador de soluções**, abrir o menu de atalho para o nó de solução, o nó do projeto ou o ficheiro que alterou e, em seguida, selecione **a consolidação**.

4. Escreva um comentário e escolha **a consolidação**.

    ![][20]

5. Selecione a ligação de **sincronização** .

    ![][38]

6. Selecione a ligação **Push** para transmitir a consolidação para o repositório no Visual Studio Team Services. (Também pode utilizar o botão **sincronizar** para copiar o seu consolidadas para o repositório. A diferença é que a **sincronização** também obtém as alterações mais recentes do repositório.)

    ![][39]

7. Selecione o botão **principal** para regressar à home page do **Explorador de equipa** .

    ![][21]

8. Selecione **constrói** para ver as compilações em curso.

    ![][22]

    **Equipa Explorer** mostra que foi aciona uma compilação para sua dar entrada.

    ![][23]

9. Para ver um registo detalhado que a compilação progride, faça duplo clique no nome da compilação em curso.

10. Enquanto a compilação estiver em curso, veja a definição de compilação que foi criada quando utilizou o Assistente para criar uma ligação para Azure.  Abrir o menu de atalho para a definição de compilação e escolha **Editar definição de criar**.

    ![][25]

11. No separador **accionador** , verá que a definição de compilação está definida para cada dar entrada, por predefinição. (Para um serviço na nuvem, serviços de equipa do Visual Studio constrói e implementa o ramo principal para o ambiente de teste automaticamente. Continua a ter de fazer um passo manual para implementar o site direto. Para uma aplicação web que não tem o ambiente de teste,-implementa o ramo mestra diretamente para o site direto.

    ![][26]

1. No separador **processo** , pode ver que o ambiente de implementação está definido para o nome da sua aplicação de serviço ou web da nuvem.

    ![][27]

1. Especificar valores para as propriedades, se pretender diferentes valores que as predefinições. As propriedades de publicação Azure estão na secção de **implementação** e também poderá ter de definir parâmetros MSBuild. Por exemplo, numa nuvem projeto de serviço, para especificar uma configuração de serviço que não seja a "Nuvem", definir os parâmetros de MSbuild para `/p:TargetProfile=[YourProfile]` onde *[YourProfile]* corresponder a um ficheiro de configuração de serviço com um nome como ServiceConfiguration. *YourProfile*.cscfg.

    A tabela seguinte mostra as propriedades disponíveis na secção de **implementação** :

  	|Propriedade|Valor predefinido|
  	|---|---|
  	|Permitir que os certificados ou não fidedignos|Se for FALSO, certificados SSL tem sessão iniciados por uma autoridade de raiz.|
  	|Permitir a atualização|Permite a implementação para atualizar uma implementação existente em vez de criar um novo. Preserva o endereço IP.|
  	|Não elimine|Se for VERDADEIRO, não substituir uma implementação não relacionada existente (a atualização é permitida).|
  	|Caminho para as definições de implementação|O caminho para o seu ficheiro .pubxml para uma aplicação web, relativamente a pasta raiz da repo. Ignorado para serviços em nuvem.|
  	|Ambiente de implementação do SharePoint|O mesmo que o nome do serviço.|
  	|Ambiente de implementação do Azure|A aplicação ou na nuvem nome de serviço web.|

1. Desta vez, a compilação deve concluída com êxito.

    ![][28]

1. Se fizer duplo clique no nome de compilação, Visual Studio apresenta um **Resumo construir**, incluindo quaisquer resultados de teste de projetos de teste de unidade associado.

    ![][29]

1. No [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), pode ver a implementação associada no separador **implementações** quando o ambiente de teste está seleccionado.

    ![][30]

1.  Navegue para o URL do seu site. Para uma aplicação web, basta escolha o botão **Procurar** no portal. Para um serviço na nuvem, selecione o URL na secção **Rapidamente rápida** da página do **Dashboard** , que mostra o ambiente de teste.

    Implementações da integração contínua para serviços em nuvem são publicadas para o ambiente de teste por predefinição. Pode alterar, definindo a propriedade de **Ambiente de serviço de nuvem alternativo** para **produção**. Aqui é onde o URL do site na página de dashboard o serviço de nuvem.

    ![][31]

    Será aberto um novo separador do browser para revelar o seu site em execução.

    ![][32]

1.  Se efetuar alterações ao seu projeto, accionador mais constrói e serão acumuladas várias implementações. A mais recente está marcada como ativa.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: implementar uma compilação anterior

Este passo é opcional. No portal do Azure clássico, escolha uma implementação anterior e escolha **Implementar novamente** para rebobinar o seu site para uma anterior dar entrada. Note que isto irá acionar uma nova compilação no TFS e criar uma nova entrada no seu histórico de implementação.

![][34]

## <a name="6-change-the-production-deployment"></a>6: alterar a implementação de produção

Quando estiver pronto, pode promover o ambiente de transição para o ambiente de produção ao selecionar **trocar** no portal do Azure clássico. O ambiente de teste recentemente implementado é promovido a produção e o ambiente de produção anterior, se existirem, torna-se num ambiente de teste. A implementação do Active pode ser diferente de produção e teste ambientes, mas o histórico de implementação de compilações recentes é a mesma, independentemente do ambiente.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: implementar a partir de um ramo de trabalho.

Quando utiliza Git, normalmente efetuar alterações um ramo de trabalho e integrar o ramo principal quando o seu desenvolvimento atinge um Estado de concluído. Durante a fase de desenvolvimento de um projeto, irá pretende criar e implementar o ramo de trabalho para Azure.

1. No **Explorador de equipa**, selecione o botão **base** e, em seguida, selecione o botão de **ramos** .

    ![][40]

2. Selecione a ligação **Novo ramo** .

    ![][41]

3. Introduza o nome do ramo, tal como "trabalho" e selecione **Criar ramo**. Esta ação cria um novo local ramo.

    ![][42]

4. Publica o ramo. Selecione o nome do ramo de **ramos não publicados**e selecione **Publicar**.

    ![][44]

6. Por predefinição, apenas as alterações para o modelo global ramo acionam uma compilação contínua. Para configurar o contínua compilação para um ramo de trabalho, escolha a página **constrói** no **Explorador de equipa**e escolha **Editar definição de criar**.

7. Abra o separador **Definições da origem** . Em **monitorizadas ramifica para integração contínua e compilação**, selecione **clique aqui para adicionar uma nova linha**.

    ![][47]

8. Especifique o ramo criado, tal como chefes/refs/úteis.

    ![][48]

9. Efetuar uma alteração no código de, abrir o menu de atalho para o ficheiro alterado e, em seguida, selecione **a consolidação**.

    ![][43]

10. Selecione a ligação **Não sincronizada consolidadas** e selecione o botão **sincronizar** ou a ligação **de emissão** para copiar as alterações na cópia do ramo trabalhar no Visual Studio Team Services.

    ![][45]

11. Navegue para a vista **constrói** e localizar a compilação que apenas tem acionou para o ramo de trabalho.

## <a name="next-steps"></a>Próximos passos

Para obter mais sugestões sobre como utilizar Git com os serviços de equipa do Visual Studio, consulte [desenvolver e partilhar o seu código no Git utilizando o Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e informações sobre como utilizar um repositório de Git que não seja gerido pelo Visual Studio Team Services para publicar o Azure, consulte o artigo [Implementação contínuo à aplicação de serviço de Azure](../app-service-web/app-service-continuous-deployment.md). Para mais informações sobre os serviços de equipa do Visual Studio, consulte o artigo [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
