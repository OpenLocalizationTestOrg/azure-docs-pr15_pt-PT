<properties
    pageTitle="Entrega contínua com o Visual Studio Team Services no Azure | Microsoft Azure"
    description="Saiba como configurar os seus projetos de equipa do Visual Studio Team Services automaticamente criem e implementem a funcionalidade de Web App nos serviços de aplicação de serviço de Azure ou na nuvem."
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Entrega contínua para Azure utilizando os serviços de equipa do Visual Studio

Pode configurar os seus projetos de equipa do Visual Studio Team Services para criar e implementar em aplicações Azure web ou automaticamente serviços em nuvem.  (Para obter informações sobre como configurar uma compilação contínua e implementar sistema utilizando um *no local* Team Foundation Server, consulte [Entrega contínua para serviços em nuvem no Azure](cloud-services-dotnet-continuous-delivery.md)).

Neste tutorial assume que tem Visual Studio 2013 e o SDK do Azure instalado. Se ainda não tiver o Visual Studio 2013, transfira-o ao selecionar a ligação **começar gratuitamente** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure a partir de [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Precisa de uma conta de serviços de equipa do Visual Studio para concluir este tutorial: pode [Abrir uma conta do Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço na nuvem automaticamente criem e implementem para Azure, utilizando os serviços de equipa do Visual Studio, siga estes passos.

## <a name="1-create-a-team-project"></a>1: criar um projeto de equipa

Siga as instruções [aqui](http://go.microsoft.com/fwlink/?LinkId=512980) para criar o seu projeto de equipa e ligá-lo para Visual Studio. Este tutorial assume que está a utilizar o controlo de versão Foundation (TFVC) da equipa como a solução de controlo de origem. Se pretender utilizar Git para controlo de versão, consulte o artigo [a versão de Git destas instruções](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2: dar entrada de um projecto para controlo de origem

1. No Visual Studio, abra a solução que pretende implementar ou crie um novo.
Pode implementar uma aplicação web ou num serviço na nuvem (aplicação do Azure) ao seguir os passos neste tutorial.
Se pretender criar uma nova solução, crie um novo projeto de serviço em nuvem Azure, ou um novo projeto de ASP.NET MVC. Certifique-se de que o projeto destinos .NET Framework 4 ou 4,5 e se estiver a criar um projecto de serviço de nuvem, adicione uma função de web do ASP.NET MVC e uma função de trabalho e, selecione a aplicação da Internet para a função da web. Quando lhe for pedido, selecione a **Aplicação da Internet**.
Se pretender criar uma aplicação web, o modelo de projeto de aplicação Web do ASP.NET e, em seguida, selecione MVC. Consulte o artigo [criar uma aplicação web do ASP.NET na aplicação de serviço de Azure](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Serviços de equipa do Visual Studio suporta apenas CI implementações das aplicações de Web do Visual Studio neste momento. Projetos de Web Site estão fora do âmbito.

1. Abrir o menu de contexto para a solução e selecione **Adicionar solução do controlo de origem**.

    ![][5]

1. Aceitar ou alterar as predefinições e selecione o botão **OK** . Assim que o processo for concluída, ícones da origem de controlo aparecem no **Explorador de solução**.

    ![][6]

1. Abrir o menu de atalho para a solução e selecione **Dar entrada**.

    ![][7]

1. Na área de **Alterações pendentes** do **Explorer de equipa**, escreva um comentário para dar entrada e selecione o botão **Dar entrada** .

    ![][8]

    Tenha em atenção as opções para incluir ou excluir alterações específicas quando dar entrada. Se as alterações desejadas forem excluídas, selecione a ligação **Incluir todos os** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3: ligar o projecto a Azure

1. Agora que tem um projeto de equipa VS Team Services com algum código de origem no mesmo, está pronto para ligar o seu projeto de equipa ao Azure.  No [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), selecione a aplicação de serviço ou web na nuvem ou crie um novo ao selecionar a **+** ícone no canto inferior esquerdo e escolhendo **Serviço na nuvem** ou **Web App** e, em seguida, **Criar rápida**. Selecione a ligação **Configurar de publicação com os serviços de equipa do Visual Studio** .

    ![][10]

1. No Assistente de, escreva o nome da sua conta de serviços de equipa do Visual Studio na caixa de texto e clique na ligação **Autorizar agora** . Poderá ser-lhe pedido para iniciar sessão.

    ![][11]

1. No diálogo pop-up **Pedido de ligação** , escolha o botão **Aceitar** para autorizar Azure para configurar o seu projeto equipa nos serviços de equipa VS.

    ![][12]

1. Quando a autorização de ser bem sucedida, verá uma lista pendente que contém uma lista dos seus projetos do Visual Studio Team Services equipa. Selecione o nome do projeto de equipa que criou nos passos anteriores e, em seguida, selecione o botão de marca de verificação o assistente.

    ![][13]

1. Depois do projeto estiver ligado, verá algumas instruções para dar entrada alterações ao seu projeto do Visual Studio Team Services equipa.  Na sua próxima dar entrada, os serviços de equipa do Visual Studio irá criar e implementar o projeto para Azure.  Experimente isto agora por clicar na ligação **Dar entrada do Visual Studio** e, em seguida, a ligação de **Iniciação Visual Studio** (ou no botão do **Visual Studio** equivalente na parte inferior do ecrã portal).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: acionar um reconstruir e implementar o projeto

1. No Visual Studio **Explorer de equipa**, selecione a ligação de **Origem de controlo Explorer** .

    ![][15]

1. Navegue para o seu ficheiro de solução e abra-o.

    ![][16]

1. No **Explorador de soluções**, abrir um ficheiro e alterá-la. Por exemplo, alterar o ficheiro `_Layout.cshtml` em vistas\\pasta partilhada numa função de web MVC.

    ![][17]

1. Editar o logótipo para o site e selecione **Ctrl + G** para guardar o ficheiro.

    ![][18]

1. No **Explorador de equipa**, selecione a ligação de **Alterações pendentes** .

    ![][19]

1. Introduza um comentário e, em seguida, selecione o botão **Dar entrada** .

    ![][20]

1. Selecione o botão **principal** para regressar à home page do **Explorador de equipa** .

    ![][21]

1. Selecione a ligação **constrói** para ver as compilações em curso.

    ![][22]

    **Equipa Explorer** mostra que foi aciona uma compilação para sua dar entrada.

    ![][23]

1. Faça duplo clique no nome da compilação em curso para ver um registo detalhado que a compilação progride.

    ![][24]

1. Enquanto a compilação estiver em curso, veja a definição de compilação que foi criada quando ligados TFS Azure utilizando o assistente.  Abrir o menu de atalho para a definição de compilação e escolha **Editar definição de criar**.

    ![][25]

    No separador **accionador** , verá que a definição de compilação está definida para cada dar entrada por predefinição.

    ![][26]

    No separador **processo** , pode ver que o ambiente de implementação está definido para o nome da sua aplicação de serviço ou web da nuvem. Se estiver a trabalhar com aplicações web, as propriedades que vê será diferentes das mostrado aqui.

    ![][27]

1. Especificar valores para as propriedades, se pretender diferentes valores que as predefinições. As propriedades para publicação Azure estão na secção de **implementação** .

    A tabela seguinte mostra as propriedades disponíveis na secção de **implementação** :

  	|Propriedade|Valor predefinido|
  	|---|---|
  	|Permitir que os certificados ou não fidedignos|Se for FALSO, certificados SSL tem sessão iniciados por uma autoridade de raiz.|
  	|Permitir a atualização|Permite a implementação para atualizar uma implementação existente em vez de criar um novo. Preserva o endereço IP.|
  	|Não elimine|Se for VERDADEIRO, não substituir uma implementação não relacionada existente (a atualização é permitida).|
  	|Caminho para as definições de implementação|O caminho para o seu ficheiro .pubxml para uma aplicação web, relativamente a pasta raiz da repo. Ignorado para serviços em nuvem.|
  	|Ambiente de implementação do SharePoint|O mesmo que o nome do serviço.|
  	|Ambiente de implementação do Azure|A aplicação ou na nuvem nome de serviço web.|

1. Se estiver a utilizar vários configurações do serviço (.cscfg ficheiros), pode especificar a configuração do serviço pretendido na definição **criar, avançadas, MSBuild argumentos** . Por exemplo, para utilizar ServiceConfiguration.Test.cscfg, defina os MSBuild argumentos opção linha `/p:TargetProfile=Test`.

    ![][38]

    Desta vez, a compilação deve concluída com êxito.

    ![][28]

1. Se fizer duplo clique no nome de compilação, Visual Studio apresenta um **Resumo construir**, incluindo quaisquer resultados de teste de projetos de teste de unidade associado.

    ![][29]

1. No [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), pode ver a implementação associada no separador **implementações** quando o ambiente de teste está seleccionado.

    ![][30]

1.  Navegue para o URL do seu site. Para uma aplicação web, basta clicar no botão **Procurar** na barra de comandos. Para um serviço na nuvem, selecione o URL na secção **Rapidamente rápida** da página do **Dashboard** , que mostra o ambiente de teste para um serviço na nuvem. Implementações da integração contínua para serviços em nuvem são publicadas para o ambiente de teste por predefinição. Pode alterar, definindo a propriedade de **Ambiente de serviço de nuvem alternativo** para **produção**. Esta captura de ecrã mostra onde o URL do site na página de dashboard o serviço de nuvem.

    ![][31]

    Será aberto um novo separador do browser para revelar o seu site em execução.

    ![][32]

    Para serviços em nuvem, se fazer outras alterações ao seu projeto, accionador mais constrói e serão acumuladas várias implementações. A mais recente marcada como ativa.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: implementar uma compilação anterior

Este passo aplica-se aos serviços em nuvem e é opcional. No portal do Azure clássico, escolha uma implementação anterior e, em seguida, selecione o botão **Implementar novamente** para rebobinar o seu site para uma anterior dar entrada.  Note que isto irá acionar uma nova compilação no TFS e criar uma nova entrada no seu histórico de implementação.

![][34]

## <a name="6-change-the-production-deployment"></a>6: alterar a implementação de produção

Este passo só se aplica a serviços em nuvem, não a aplicações web. Quando estiver pronto, pode promover o ambiente de transição para o ambiente de produção ao selecionar o botão **trocar** no portal do Azure clássico. O ambiente de teste recentemente implementado é promovido a produção e o ambiente de produção anterior, se existirem, torna-se num ambiente de teste. A implementação do Active pode ser diferente de produção e teste ambientes, mas o histórico de implementação de compilações recentes é a mesma, independentemente do ambiente.

![][35]

## <a name="7-run-unit-tests"></a>7: executar testes de unidade

Este passo só se aplica a web apps, não cloud services. Para colocar uma porta de qualidade na implementação, pode executar testes de unidade e se estes falharem, pode deixar a implementação.

1.  No Visual Studio, adicione um projeto de teste de unidade.

    ![][39]

1.  Adicione referências de projeto para o projeto que pretende testar.

    ![][40]

1.  Adicione alguns testes de unidade. Para começar a utilizar, experimente um teste fictício que irá sempre decorrer.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Editar a definição de compilação, selecione o separador de **processo** e expanda o nó de **teste** .

1.  Defina a **compilação falhar em caso de falha de teste** para True. Isto significa que a implementação não ocorre a menos que os testes passam.

    ![][41]

1.  Fila de uma nova compilação.

    ![][42]

    ![][43]

1. Enquanto a compilação é processo, marque ao seu progresso.

    ![][44]

    ![][45]

1. Depois da compilação está concluída, verificar os resultados de teste.

    ![][46]

    ![][47]

1.  Experimente criar um teste que irá falhar. Adicionar um novo ensaio ao copiar a primeira parte, mude o nome e comentar a linha de código que diz que notimplementedexception é uma exceção esperada.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Dar entrada a alteração fila uma nova compilação.

    ![][48]

1. Ver os resultados de teste para ver os detalhes sobre a falha.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Próximos passos
Para mais informações sobre unidade testes nos serviços de equipa do Visual Studio, consulte o artigo [executar testes de unidade na sua compilação](http://go.microsoft.com/fwlink/p/?LinkId=510474). Se estiver a utilizar Git, consulte o artigo [partilhar o seu código no Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [implementação contínua à aplicação de serviço de Azure](../app-service-web/app-service-continuous-deployment.md).  Para mais informações sobre os serviços de equipa do Visual Studio, consulte o artigo [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
