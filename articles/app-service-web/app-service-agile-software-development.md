<properties
    pageTitle="Desenvolvimento de agile software com o serviço de aplicação do Azure"
    description="Saiba como criar aplicações complexas alta escala com a aplicação de serviço de Azure de uma forma que suporta o desenvolvimento de software ágil."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>


# <a name="agile-software-development-with-azure-app-service"></a>Desenvolvimento de agile software com o serviço de aplicação do Azure #

Neste tutorial, irá aprender a criar aplicações complexas alta escala com a [Aplicação de serviço de Azure](/services/app-service/) de uma forma que suporta o [desenvolvimento de software ágil](https://en.wikipedia.org/wiki/Agile_software_development). Parte do princípio de que já conhece como [Implementar aplicações complexas seja previsível no Azure](app-service-deploy-complex-application-predictably.md).

Limitações no processos técnicos com frequência podem realçar impedir implementação com êxito de métodos ágil. Azure de aplicação de serviço, com funcionalidades como [publicação contínua](app-service-continuous-deployment.md), [ambientes de transição](web-sites-staged-publishing.md) (faixas) e [monitorização](web-sites-monitor.md), quando associado à forma externos orchestration e gestão de implementação no [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md), pode ser parte de uma solução ótima para programadores que Aproveite desenvolvimento de software ágil.

A tabela que se segue é uma pequena lista dos requisitos associados Desenvolvimento ágil e como Azure serviços permite dos mesmos.

| Requisito de registo | Como o Azure permite |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Construir com cada consolidação<br>-Construir automaticamente e rápida | Quando configurado com implementação contínua, serviço de aplicação do Azure pode funcionar como compilações live-a trabalhar rapidamente com base num ramo Dev Center. Sempre que o código seguia para o ramo, é automaticamente criado e começar a trabalhar em direto no Azure.|
| -Torne compilações de teste automático | Carregar testes, testes web, etc., pode ser implementada com o modelo de Gestor de recursos do Azure.|
| -Realizarem testes num clonar de ambiente de produção | Modelos de Gestor de recursos Azure podem ser utilizados para criar o ambiente de produção Azure (incluindo as definições da aplicação, modelos de cadeia de ligação, dimensionamento, etc.) para testar a ligação rapidamente e de maneira previsível que diz.|
| -Visualize o resultado de compilação mais recente facilmente | Implementação contínua para Azure a partir de um repositório significa que pode testar o novo código numa aplicação direto imediatamente após a consolidar as alterações. |
| -Consolidar ramo principal diariamente<br>-Automatizar a implementação | Integração contínua de uma aplicação de produção com ramificação principal de um repositório implementa automaticamente cada consolidar/impressão em série no ramo principal para produção. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>O que vai fazer ##

Irá guiá-lo através de um fluxo de trabalho normal Dev Center-teste fase de produção para poder publicar novas alterações a aplicação de exemplo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , que consiste em duas [aplicações web](/services/app-service/web/), um ser front-end (MOR) e o outro a ser Web API back-end (ser) e uma [base de dados SQL](/services/sql-database/). Irá funcionar com a arquitetura de implementação apresentada abaixo:

![](./media/app-service-agile-software-development/what-1-architecture.png)

Para colocar a imagem em palavras:

-   A arquitetura de implementação é dividida em três distintos ambientes (ou [grupos de recursos](../azure-resource-manager/resource-group-overview.md) no Azure), cada uma com as suas próprias [plano de serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), definições de [dimensionamento](web-sites-scale.md) e base de dados SQL. 
-   Cada ambiente pode ser gerido em separado. Ainda podem existir em diferentes subscrições.
-   Teste e de produção são implementados como duas faixas da aplicação de serviço de aplicação do mesmo. O ramo mestra está configurada para integração contínua com a transição ranhura.
-   Quando a consolidação ramo mestra é verificada na transição ranhura (com dados de produção), a aplicação de transição verificada é convertida em de produção ranhura [com sem indisponibilidade](web-sites-staged-publishing.md).

O ambiente de produção e transição é definido pelo modelo na [ * &lt;repository_root >*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Os ambientes de teste de verificação e Dev Center são definidos pelas modelo na [ * &lt;repository_root >*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

Também irá utilizar a estratégia ramificação típica, com o código de mover do ramo Dev Center por excesso para o ramo de teste, em seguida, para o ramo principal (mover para cima na qualidade, por isso, ao speak).

![](./media/app-service-agile-software-development/what-2-branches.png) 

## <a name="what-you-will-need"></a>O que irá precisar ##

-   Uma conta do Azure
-   Uma conta de [GitHub](https://github.com/)
-   Shell de Git (instalado com o [GitHub para Windows](https://windows.github.com/)) - Isto permite-lhe executar a Git e o PowerShell comandos na mesma sessão 
-   Bits mais recentes do [PowerShell do Azure](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi)
-   Noções básica sobre dos seguintes procedimentos:
    -   Implementação de modelo de [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) (também consultar [Implementar uma aplicação complexa seja previsível no Azure](app-service-deploy-complex-application-predictably.md))
    -   [Git](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Precisa de uma conta Azure para concluir este tutorial:
> + Pode [Abrir uma conta Azure gratuitamente](/pricing/free-trial/) - obtém créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que está a ser utilizados para cima pode manter a conta e utilização livre Azure serviços, como Web Apps.
> + Pode [Ativar benefícios de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/) - subscrição o Visual Studio fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.
>
> Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="set-up-your-production-environment"></a>Configurar o seu ambiente de produção ##

>[AZURE.NOTE] O script utilizado neste tutorial irá configurar automaticamente a publicação contínua a partir do repositório de GitHub. Isto requer que as suas credenciais GitHub já sejam encontram armazenadas no Azure, caso contrário, a implementação em script irá falhar ao tentar configurar definições de controlo de origem para o web apps. 
>
>Para armazenar as suas credenciais GitHub Azure, crie uma aplicação web no [Portal do Azure](https://portal.azure.com/) e [Configurar GitHub implementação](app-service-continuous-deployment.md). Só tem de fazer uma vez. 

Num cenário de DevOps típico, tiver uma aplicação que está a ser executado em direto no Azure e que pretende efetuar alterações ao mesmo através da publicação contínua. Neste cenário, tem de um modelo que desenvolvido, teste e utilizado para implementar o ambiente de produção. Irá configurar nesta secção.

1.  Crie o seu próprio bifurcação do repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Para obter informações sobre como criar o seu bifurcação, consulte o artigo [bifurcação um Repo](https://help.github.com/articles/fork-a-repo/). Quando a bifurcação estiver criada, pode vê-lo no seu browser.
 
    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Abra uma sessão de Git Shell. Se ainda não tenha da Shell de Git, instale agora [GitHub para Windows](https://windows.github.com/) .

3.  Crie um clonar local do seu bifurcação executando o seguinte comando:

        git clone https://github.com/<your_fork>/ToDoApp.git 

4.  Assim que tiver o seu clonar local, navegue para * &lt;repository_root >*\ARMTemplates e executar o deploy.ps1 script da seguinte forma:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.  Quando lhe for pedido, escreva o nome de utilizador pretendida e a palavra-passe de acesso de base de dados.

    Deverá ver o progresso de aprovisionamento recursos de vários Azure. Quando concluir a implementação, o script irá iniciar a aplicação no browser e dar-lhe um sinal sonoro amigável.

    ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
    >[AZURE.TIP] Consulte o artigo * &lt;repository_root >*\ARMTemplates\Deploy.ps1, para ver como gera recursos com os IDs exclusivos. Pode utilizar a mesma abordagem para criar a mesma implementação que diz sem se preocupar em conflito de nomes de recursos.
 
6.  Novamente na sessão de Git Shell, execute:

        .\swap –Name ToDoApp<unique_string>master

    ![](./media/app-service-agile-software-development/production-4-swap.png)

7.  Quando tiver terminado do script, volte ao navegar para endereço o front-end (http://ToDoApp*&lt;unique_string >*master.azurewebsites.net/) para ver a aplicação a ser executada em produção.
 
5.  Inicie sessão no [Portal do Azure](https://portal.azure.com/) e veja o que é criado.

    Deverá conseguir ver duas aplicações web no mesmo grupo de recursos, uma com o `Api` sufixo no nome. Se observe a vista do grupo de recursos, também verá a base de dados do SQL e servidor, o plano de serviço de aplicação e as faixas de transição para o web apps. Procure entre os diferentes recursos e compará-los com * &lt;repository_root >*\ARMTemplates\ProdAndStage.json para ver como estão configurados no modelo.

    ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Acabou de configurar o ambiente de produção. Em seguida, irá iniciar uma nova actualização para a aplicação.

## <a name="create-dev-and-test-branches"></a>Crie Dev Center e teste ramificações ##

Agora que tem uma aplicação complexa a ser executada em produção no Azure, irá tornar uma atualização para a sua aplicação em conformidade com a metodologia ágil. Nesta secção, terá de criar o Dev Center e testar ramificações que precisa de fazer as atualizações necessárias.

1.  Crie o ambiente de teste primeiro. Na sua sessão de Git Shell, execute os seguintes comandos para criar o ambiente para um novo ramo denominado **NewUpdate**. 

        git checkout -b NewUpdate
        git push origin NewUpdate 
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.  Quando lhe for pedido, escreva o nome de utilizador pretendida e a palavra-passe de acesso de base de dados. 

    Quando concluir a implementação, o script irá iniciar a aplicação no browser e dar-lhe um sinal sonoro amigável. E apenas essa, como tem agora um novo ramo com as suas próprias ambiente de teste. Observar para rever algumas coisas sobre este ambiente de teste:

    -   Pode criá-la na qualquer subscrição Azure. Isto significa que o ambiente de produção pode ser gerido separadamente a partir do seu ambiente de teste.
    -   Ambiente de teste está em execução direto no Azure.
    -   Ambiente de teste for idêntico ao ambiente de produção, exceto as faixas de transição e as definições de dimensionamento. Pode saber isto, uma vez que estas são as diferenças entre ProdandStage.json e Dev.json apenas.
    -   Pode gerir o seu ambiente de teste na sua própria plano de serviço de aplicação, com uma camada preço diferente (como **Free**).
    -   Eliminar este ambiente de teste será tão simple como eliminar o grupo de recursos. Irá encontrar informações sobre como fazer este [mais tarde](#delete).

2.  Avance para criar um ramo de Dev Center executando os seguintes comandos:

        git checkout -b Dev
        git push origin Dev
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.  Quando lhe for pedido, escreva o nome de utilizador pretendida e a palavra-passe de acesso de base de dados. 

    Observar para rever algumas coisas sobre este ambiente Dev Center: 

    -   O ambiente do Dev Center tem uma configuração idêntica ao ambiente de teste, uma vez que é implementado a utilizar o mesmo modelo.
    -   Cada ambiente Dev Center pode ser criado na subscrição do Azure do programador, sair do ambiente de teste para ser geridas separadamente.
    -   O ambiente do Dev Center está em execução direto no Azure.
    -   Eliminar o ambiente Dev Center é tão simple como eliminar o grupo de recursos. Irá encontrar informações sobre como fazer este [mais tarde](#delete).

>[AZURE.NOTE] Quando tiver múltiplos programadores a nova atualização a trabalhar, cada um deles pode facilmente criar um ambiente de Dev Center dedicada e ramo efetuando o seguinte procedimento:
>
>1. Criar os seus próprios bifurcação do repositório no GitHub (consulte [bifurcação um Repo](https://help.github.com/articles/fork-a-repo/)).
>2. Clonar bifurcação no seu computador local
>3. Execute os comandos para criar os seus próprios Dev Center ramo e o ambiente do mesmo.

Quando terminar, o seu bifurcação GitHub deve ter três ramificações:

![](./media/app-service-agile-software-development/test-1-github-view.png)

E que deve ter seis aplicações web (três conjuntos de dois) em grupos de recursos separado três:

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE] Tenha em atenção que ProdandStage.json Especifica o ambiente de produção para utilizar o **padrão** preços camada, que é adequada para escalabilidade da aplicação de produção.

## <a name="build-and-test-every-commit"></a>Criar e testar cada consolidação ##

Os ficheiros de modelo ProdAndStage.json e Dev.json já especificam os parâmetros de origem do controlo, que, por predefinição configura contínua de publicação para a aplicação web. Por conseguinte, de cada consolidação para o GitHub ramo accionadores uma implementação automática para Azure a partir desse ramo. Vamos ver como a configuração funciona agora.

1.  Certifique-se de que é ramo Dev Center do repositório local. Para fazer isto, execute o seguinte comando na Shell de Git:

        git checkout Dev

2.  Efetue uma alteração simple a camada de IU a aplicação alterando o código para utilizar listas de [arranque](http://getbootstrap.com/components/) . Abrir * &lt;repository_root >*\src\MultiChannelToDo.Web\index.cshtml e efetuar a alteração realçada abaixo:

    ![](./media/app-service-agile-software-development/commit-1-changes.png)

    >[AZURE.NOTE] Se não conseguir ler a imagem acima: 
    >
    >- Na linha 18, altere `check-list` para `list-group`.
    >- Na linha 19, altere `class="check-list-item"` para `class="list-group-item"`.

3.  Guarde a alteração. Novamente na Shell de Git, execute os seguintes comandos:

        cd <repository_root>
        git add .
        git commit -m "changed to bootstrap style"
        git push origin Dev
 
    Estes comandos git são semelhantes às "dar entrada do seu código" no outro sistema de controlo de origem como TFS. Quando executa `git push`, a nova consolidação accionadores push um código automático para Azure, que recompila os a aplicação para refletir a alteração no ambiente Dev Center.

4.  Para verificar que ocorreu push este código para o seu ambiente Dev Center, aceda à pá do seu ambiente Dev Center web app e observe a peça de **implementação** . Deverá conseguir ver a sua última mensagem consolidação.

    ![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.  A partir daí, clique em **Procurar** para ver a alteração de nova na aplicação do live no Azure.

    ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

    Esta é uma alteração muito mínima para a aplicação. No entanto, muitas vezes novas alterações a uma aplicação web complexa tem efeitos inesperados e indesejáveis de lado. Ser capaz de facilmente teste cada consolidar em direto compilações permite-lhe capturas estes problemas antes dos seus clientes vêem-los.

Por agora, deverá se sentir confortável com a realização que, como um programador no projeto **NewUpdate** , poderão facilmente criar um ambiente do Dev Center para si, em seguida, crie cada consolidar e testar cada compilação.

## <a name="merge-code-into-test-environment"></a>Intercalar código no ambiente de teste ##

Quando estiver pronto para o seu código de emissão de ramo Dev Center até NewUpdate ramo, é o processo de git padrão:

1.  Intercale qualquer consolidadas novas para NewUpdate o ramo de Dev Center no GitHub, tal como consolidadas criado por outros programadores. Qualquer consolidação de nova no GitHub irá acionar uma push de código e crie no ambiente Dev Center. Pode, em seguida, certifique-se de que o seu código Dev Center ramo continuam a funcionar com o bits mais recente do ramo NewUpdate.

2.  Intercale todos os seus consolidadas novas a partir do ramo Dev Center NewUpdate ramo no GitHub. Esta ação accionadores um push de código e compilação no ambiente de teste. 

Nota novamente que porque implementação contínua já está configurada com estas ramificações git, não precisa de tomar qualquer acção como executar a integração constrói. Basta necessitar de efetuar práticas de controlo padrão de origem utilizando git e Azure fará todos os processos de compilação por si.

Agora, vamos transmitir o seu código para **NewUpdate** ramo. Na Shell de Git, execute os seguintes comandos:

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

Já está! 

Vá para o pá de aplicação web para o seu ambiente de teste ver o seu novo consolidar (fundido com NewUpdate ramo) agora enviado para o ambiente de teste. Em seguida, clique em **Procurar** para ver que a alteração de estilo está a ser executado em direto no Azure.

## <a name="deploy-update-to-production"></a>Implementar a actualização de produção ##

Direccionar código para o ambiente de teste e de produção deve sentir não diferente da que já fez quando premido código para o ambiente de teste. É muito simples. 

Na Shell de Git, execute os seguintes comandos:

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

Lembre-se de que baseada na forma como o ambiente de teste e de produção é ProdandStage.json o programa de configuração, o novo código seguia a ranhura **de transição** e está a ser executado aí. Por isso, se navegar para URL a transição ranhura, verá o novo código de executar o aí. Para fazer isto, execute o `Show-AzureWebsite` cmdlet na Shell de Git.

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
E agora, depois de validar a atualização da transição ranhura, a única coisa por fazer consiste em trocá-lo para produção. Na Shell de Git, basta execute os seguintes comandos:

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

Parabéns! Tenha publicado com êxito uma nova actualização a sua aplicação web de produção. O que acontece mais é que apenas a efetuou criando facilmente Dev Center e testar ambientes e criar e testar cada consolidação. Estes são cruciais blocos modulares para o desenvolvimento de software ágil.

<a name="delete"></a>
## <a name="delete-dev-and-test-enviroments"></a>Eliminar Dev Center e testar o enviroments ##

Uma vez que tenham intencionalmente architected os ambientes Dev Center e teste para ser grupos de recursos autónomo, é muito fácil eliminá-los. Para eliminar aqueles que criou neste tutorial, a GitHub ramificações e o Azure artefactos, basta executados os seguintes comandos na Shell de Git:

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## <a name="summary"></a>Resumo ##

Desenvolvimento de software ágil é um tem de fazer para muitas empresas que pretende para adotar o Azure como os respetivos plataforma de aplicação. Neste tutorial, aprendeu como criar e suprimir para baixo réplicas exatas ou perto réplicas do ambiente de produção com facilidade, mesmo para aplicações complexas. Também aprendeu como tirar partido esta capacidade de criar um processo de desenvolvimento que pode criar e testar cada consolidar única no Azure. Neste tutorial começadas é apresentado lhe como pode melhor utilizar serviço de aplicação do Azure e Gestor de recursos do Azure em conjunto para criar uma solução de DevOps caters para ágil métodos. Em seguida, pode criar neste cenário executando técnicas DevOps avançadas como [testes de produção](app-service-web-test-in-production-get-start.md). Para um cenário de testes de produção comuns, consulte o artigo [Flighting implementação (testes beta) na aplicação de serviço de Azure](app-service-web-test-in-production-controlled-test-flight.md).

## <a name="more-resources"></a>Mais recursos ##

-   [Implementar uma aplicação complexa seja previsível no Azure](app-service-deploy-complex-application-predictably.md)
-   [Desenvolvimento ágil na prática: sugestões e truques para ciclo de desenvolvimento modernizar](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-   [Estratégias de implementação avançadas para utilizar o Gestor de recursos modelos de aplicações Web do Azure](http://channel9.msdn.com/Events/Build/2015/2-620)
-   [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md)
-   [JSONLint - a validação JSON](http://jsonlint.com/)
-   [ARMClient – configurar o GitHub publicação ao site](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-   [Git ramificação – ramificação básicos e intercalar](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Blogue de David Ebbo](http://blog.davidebbo.com/)
-   [Azure PowerShell](../powershell-install-configure.md)
-   [Ferramentas de linha de comandos em diferentes plataformas Azure](../xplat-cli-install.md)
-   [Criar ou editar utilizadores no Azure AD](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-   [Project Kudu Wiki](https://github.com/projectkudu/kudu/wiki)
