<properties
    pageTitle="Carga teste a sua aplicação, utilizando os serviços de equipa do Visual Studio | Microsoft Azure"
    description="Saiba como limite teste suas aplicações do Azure ferro de serviço, utilizando os serviços de equipa do Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Carga teste a sua aplicação, utilizando os serviços de equipa do Visual Studio

Este artigo mostra como utilizar funcionalidades de teste de carregamento do Microsoft Visual Studio para o limite de teste de uma aplicação. Utiliza um ferro de serviço do Azure service com estado back-end e um Estado de serviço web front-end. A aplicação de exemplo utilizada aqui está uma simulator de localização de um avião. Fornecer um ID de um avião, hora de partida e destino. Os pedidos de processos de back-end a aplicação e o front-end apresenta um mapa de um avião que corresponde aos critérios.

O diagrama seguinte ilustra a aplicação de serviço ferro que irá testar.

![Diagrama da aplicação de localização de um avião de exemplo][0]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de fazer o seguinte procedimento:

- Obter uma conta de serviços de equipa do Visual Studio. Pode obter um gratuitamente no [Visual Studio Team Services](https://www.visualstudio.com).
- Obter e instalar o Visual Studio 2013 ou o Visual Studio 2015. Este artigo utiliza o Visual Studio 2015 Enterprise edition, mas Visual Studio 2013 e outras edições devem funcionam de forma semelhante.
- Implemente a aplicação de ambiente de teste. Veja [como implementar aplicações a um cluster remota utilizando o Visual Studio](service-fabric-publish-app-remote-cluster.md) para obter informações sobre esta.
- Compreenda o padrão da utilização da sua aplicação. Estas informações são utilizadas para simular o padrão de carregamento.
- Compreenda o objetivo para sua carga testar a ligação. Isto ajuda a interpretar e analisar os resultados dos testes de carregamento.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Criar e executar o projeto de desempenho da Web e teste de carregamento

### <a name="create-a-web-performance-and-load-test-project"></a>Criar um projeto de desempenho da Web e teste de carregamento

1. Abrir o Visual Studio 2015. Selecione **ficheiro** > **Novo** > **projeto** na barra de menus para abrir a caixa de diálogo **Novo projeto** .

2. Expanda o nó **Visual c#** e selecione **teste de** > **desempenho da Web e o project de teste de carregamento**. Dê um nome ao projeto e, em seguida, selecione o botão **OK** .

    ![Captura de ecrã da caixa de diálogo novo projeto][1]

    Deverá ver um novo projeto de desempenho da Web e teste de carga no Explorador de soluções.

    ![Captura de ecrã da solução Explorer que mostra o novo projeto][2]

### <a name="record-a-web-performance-test"></a>Registo de um teste de desempenho da web

1. Abra o projecto .webtest.

2. Selecione o ícone **Adicionar gravação** para iniciar uma sessão de gravação no seu browser.

    ![Captura de ecrã do ícone de adicionar gravação num browser][3]

    ![Captura de ecrã do botão gravar num browser][4]

3. Navegue para a aplicação de serviço ferro. O painel de gravação deve mostrar os pedidos web.

    ![Captura de ecrã de pedidos de web no painel de gravação][5]

4. Execute uma sequência de ações que esperava os utilizadores para executar. Estas ações são utilizadas como um padrão para gerar a carregar.

5. Quando terminar, selecione o botão **Parar** para parar a gravação.

    ![Captura de ecrã do botão Parar][6]

    O projeto .webtest no Visual Studio deve ter capturado uma série de pedidos de. Parâmetros dinâmicos são substituídos automaticamente. Neste momento, pode eliminar quaisquer pedidos de dependência extra, repetidos que não fazem parte do seu cenário de teste.

6. Guardar o projeto e, em seguida, selecione o comando **Executar testar** para executar o teste de desempenho web localmente e certifique-se de que tudo está a funcionar corretamente.

    ![Captura de ecrã do comando Executar teste][7]

### <a name="parameterize-the-web-performance-test"></a>Parametrizar o teste de desempenho da web

Pode parametrizar o teste de desempenho web convertê-lo para um teste de desempenho codificada web e, em seguida, editando o código. Como alternativa, pode ligar o teste de desempenho da web a uma lista de dados para que o teste itera os dados. Consulte o artigo [gerar e executar um teste de desempenho web codificada](https://msdn.microsoft.com/library/ms182552.aspx) para obter detalhes sobre como converter o teste de desempenho web um teste codificado. Consulte o artigo [Adicionar uma origem de dados para um desempenho web testar](https://msdn.microsoft.com/library/ms243142.aspx) para obter informações sobre como ligar dados a um teste de desempenho da web.

Neste exemplo, vamos irá converter o teste de desempenho web um teste codificado para que possa substituir o ID de um avião com um GUID gerado e adicionar mais pedidos para enviar voos para diferentes localizações.

### <a name="create-a-load-test-project"></a>Criar um projeto de teste de carregamento

Um projeto de teste de carga é composto por uma ou mais cenários descritos pelo teste de desempenho de web e teste de unidade, juntamente com as definições de teste de carga especificado adicional. Os seguintes passos mostram como criar um projeto de teste de carga:

1. No menu de atalho do seu projeto de desempenho da Web e teste de carga, selecione **Adicionar** > **Teste de carregamento**. No Assistente de **Teste de carga** , selecione o botão **seguinte** para configurar as definições de teste.

2. Na secção **Padrão de carregar** , escolha se pretende que uma carga de utilizador constante ou uma carga passo, o que é iniciado com alguns utilizadores e aumenta os utilizadores ao longo do tempo.

    Se tiver uma boa estimativa da quantidade de carga de utilizador e para ver como executa o sistema atual, selecione **Carregar constante**. Se o seu objetivo for saber se o sistema de forma consistente executa em diferentes carregamentos, escolha **Carregar passo**.

3. Na secção **Mistura de teste** , selecione o botão **Adicionar** e, em seguida, selecione o teste que pretende incluir no teste de carregamento. Pode utilizar a coluna de **distribuição** para especificar a percentagem do total testes executados para cada teste.

4. Na secção **Definições de executar** , especifique a duração de teste de carregamento.

    >[AZURE.NOTE] A opção de **Testar iterações** só está disponível quando executa um teste de carga localmente utilizando o Visual Studio.

5. Na secção **localização** das **Definições de executar**, especifique a localização onde os pedidos de teste de carregamento são gerados. O assistente poderá pedir-lhe para iniciar sessão na sua conta de serviços de equipa. Inicie sessão e, em seguida, escolha uma localização geográfica. Quando tiver terminado, selecione o botão **Concluir** .

6. Quando o teste de carga estiver criado, abra o projeto .loadtest e selecione executar definição, tal como **Executar definições**atuais > **Executar Settings1 [ativo]**. Este procedimento abre as definições de executar na janela de **Propriedades** .

7. Na secção de **resultados** da janela de propriedades de **Definições de executar** , a definição de **Armazenamento de detalhes de temporização** deve ter **nenhum** como o valor predefinido. Altere este valor para **Todos os detalhes individuais** para obter mais informações sobre a carga de resultados de teste. Consulte o artigo [Carregar testes](https://www.visualstudio.com/load-testing.aspx) para obter mais informações sobre como ligar aos serviços de equipa do Visual Studio e executa um teste de carregamento.

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Executar o teste de carga, utilizando os serviços de equipa do Visual Studio

Escolha o comando **Executar carga testar** para iniciar o teste executar.

![Captura de ecrã do comando Executar teste de carregamento][8]

## <a name="view-and-analyze-the-load-test-results"></a>Ver e analisar os resultados dos testes de carregamento

Como a carga de teste progride, as informações de desempenho são representado em gráfico. Deverá ver algo semelhante ao seguinte graph.

![Captura de ecrã do gráfico de desempenho para os resultados dos testes de carregamento][9]

1. Selecione a ligação **Transferir relatório** na parte superior da página. Após transferir o relatório, selecione o botão de **vista de relatório** .

    No separador **gráfico** pode ver gráficos para vários contadores de desempenho. No separador **Resumo** , são apresentados os resultados de teste globais. No separador **tabelas** mostra o número total de testes de carga passado e falha.

2. Selecione as ligações números no **teste** > **falhou** e os **erros** > **contagem** de colunas para ver os detalhes desse erro.

    No separador **Detalhes** mostra virtuais teste cenário informações de utilizador e para pedidos de falhadas. Estes dados podem ser útil se o teste de carga inclui vários cenários.

Consulte o artigo [Analisar carregar testar resultados na vista de gráficos da análise de teste carregar](https://www.visualstudio.com/load-testing.aspx) para obter mais informações sobre como visualizar os resultados dos testes de carregamento.

## <a name="automate-your-load-test"></a>Automatizar o teste de carregamento

Visual Studio equipa serviços carregar teste fornece APIs para o ajudar a gerir os testes de carga e analisar os resultados numa conta Team Services. Para mais informações, consulte [Nuvem carga testes Rest APIs](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Próximos passos
- [Monitorização e diagnosticar serviços numa configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
