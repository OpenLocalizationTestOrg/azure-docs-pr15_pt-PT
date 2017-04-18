<properties
    pageTitle="Criar um serviço em nuvem Olá mundo para Azure no Eclipse"
    description="Saiba como criar uma aplicação de Olá mundo simple utilizando o Toolkit de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Criar um serviço em nuvem Olá mundo para Azure no Eclipse #

Os passos seguintes mostram-lhe como criar e implementar uma aplicação básica JSP Azure utilizando o Toolkit de Azure para Eclipse. Um exemplo JSP é apresentado para simplificar, mas passos altamente semelhantes seria adequados para um servlet Java, desde que se refere a implementação do Azure.

A aplicação terá uma aspeto semelhante ao seguinte:

![][ic600360]

## <a name="prerequisites"></a>Pré-requisitos ##

* Um Java programador Kit (JDK), v 1.7 ou posterior.
* -Eclipse IDE para os programadores de EE Java, Indigo ou posterior. Isto pode ser transferido das <http://www.eclipse.org/downloads/>.
* Uma distribuição de um servidor web baseadas em Java ou servidor de aplicações, tal como Apache Tomcat, GlassFish, JBoss Application Server, cais ou IBM® WebSphere® aplicação Server liberdade Core.
* Uma subscrição Azure, que pode ser adquirida a partir do <http://azure.microsoft.com/pricing/purchase-options/>.
* O Toolkit Azure para Eclipse. Para mais informações, consulte o artigo [instalar o Toolkit de Azure para Eclipse][].

## <a name="to-create-a-hello-world-application"></a>Para criar uma aplicação Olá mundo ##

Primeiro, vamos começar com a criação de um projeto Java.

*  Iniciar Eclipse e no menu de clique em **ficheiro**, clique em **Novo**e, em seguida, clique em **Projeto de Web dinâmicos**. (Se não vir **Dinâmico Web Project** listado como um projeto disponível depois de clicar em **ficheiro**, a **Nova**, em seguida, faça o seguinte: clique em **ficheiro**, clique em **Novo**, clique em **projeto...**, expanda **Web**, clique em **Projeto de Web dinâmicos**e clique em **seguinte**.)
*  Para fins neste tutorial, dê um nome do projeto **MyHelloWorld**. (Certifique-se de utiliza este nome, passos subsequentes neste tutorial esperar o ficheiro de guerra para ter o nome MyHelloWorld). Ecrã irão aparecer semelhante ao seguinte: ![][ic589576]
* Clique em **Concluir**.
* Vista de Project Explorer de Eclipse, expanda **MyHelloWorld**. Com o botão direito **ConteúdoWeb**, clique em **Novo**e, em seguida, clique em **Ficheiro de JSP**.
* Na caixa de diálogo **Novo ficheiro JSP** , dê um nome de ficheiro **index.jsp**. Manter a pasta principal como **MyHelloWorld/ConteúdoWeb**, conforme mostrado no seguinte:  ![][ic659262]
* Na caixa de diálogo **Seleccionar o modelo de JSP** , para fins neste tutorial, selecione **Novo ficheiro JSP (html)** e clique em **Concluir**.
* Quando abre o ficheiro index.jsp no Eclipse, adicionar texto a apresentar dinamicamente **Olá mundo!** dentro dos existentes `<body>` elemento. O atualizada `<body>` conteúdo deverá aparecer como o seguinte:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Guarde index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Para implementar a aplicação para Azure, a forma mais rápida e simple ##

Assim que tiver uma aplicação de web Java pronta para testar, pode utilizar o seguinte atalho para experimentar diretamente na nuvem Azure.

1. No Explorador de projeto do Eclipse, clique em **MyHelloWorld**.
1. Na barra de ferramentas Eclipse, clique em botão pendente **Publicar** e, em seguida, clique em **Publicar como Azure serviço em nuvem**
    ![][publishDropdownButton]
1. Se estiver a publicar esta aplicação para Azure pela primeira vez e não tiver criado um projecto de implementação do Azure para esta aplicação antes, um projecto de implementação do Azure criado por si automaticamente. Deverá visualizar a linha de comandos seguinte, que enumera também o pacote JDK e application server que será implementada automaticamente para executar a sua aplicação.
    ![][ic789598]

    Esta abordagem atalho permite uma forma rápida e fácil de teste a sua aplicação no Azure sem ter de configurar um servidor específico ou JDK que é a diferença entre as predefinições. Se estiver satisfeito com as predefinições, pode clicar em **OK** para continuar com os seguintes passos.
    No entanto, se pretender alterar o JDK ou application server para utilizar para a sua aplicação, pode fazê-lo mais tarde ao editar o projeto de implementação do Azure que foi criado automaticamente ou pode clicar em **Cancelar** agora e consulte a **secção de projetos de implementação do Azure acerca** deste tutorial.
1. Na caixa de diálogo **publicar no Azure** :
    1. Se não existirem sem subscrições para selecionar ainda na lista de **subscrição** , siga estes passos para importar as informações de subscrição:
        1. Clique em **Importar a partir de ficheiro de definições de publicar**.
        1. Na caixa de diálogo **Importar as informações da subscrição** , clique em **Transferir o ficheiro de definições de publicar**. Se ainda não está iniciou para a sua conta Azure, vai ser-lhe para iniciar sessão. Em seguida, será solicitado que guardar um Azure publicar o ficheiro de definições. Guardá-lo para o seu computador local.
        1. Ainda na caixa de diálogo **Importar as informações da subscrição** , clique no botão **Procurar** , selecione o ficheiro de definições de publicar que guardou localmente no passo anterior e, em seguida, clique em **Abrir**. Ecrã deverá ter um aspeto semelhante ao seguinte: ![][ic644267]
        1. Clique em **OK**.
    1. Para a **subscrição**, selecione a subscrição que pretende utilizar para a sua implementação.
    1. Para a **conta de armazenamento**, selecione a conta de armazenamento que pretende utilizar ou clique em **Novo** para criar uma nova conta de armazenamento.
    1. **Nome do serviço**, selecione o serviço de nuvem que pretende utilizar ou clique em **Novo** para criar um novo serviço na nuvem.
    1. **SO de destino**, selecione a versão do sistema operativo que pretende utilizar para a sua implementação.
    1. Para o **ambiente de destino**, para fins neste tutorial, selecione **teste**. (Quando estiver pronto para implementar o seu site de produção, irá alterar este **produção**.)
    1. Opcional: Certifique-se de que a **Substituir implementação anterior** está selecionada se pretender que a sua implementação nova para substituir automaticamente a implementação anterior. Quando ativar esta opção, irá não problemas de experiência "409 conflito" quando publicar na mesma localização.
        Tenha em atenção que a caixa de diálogo **publicar no Azure** contém uma secção para **Acesso remoto**. Por predefinição, acesso remoto não está ativado e podemos não permitirá-lo para este exemplo. Para ativar o acesso remoto, irá introduzir um nome de utilizador e palavra-passe para utilizar quando iniciar sessão remotamente. Para mais informações acerca de acesso remoto, consulte o artigo [Ativar o acesso remoto para implementações do Azure no Eclipse][].
        A caixa de diálogo **publicar no Azure** irão aparecer semelhante ao seguinte: ![][ic719488]
1. Clique em **Publicar** para publicar o ambiente de teste.
    Quando lhe for pedido para efetuar uma compilação completa, clique em **Sim**. Isto poderá demorar vários minutos até que a primeira compilação.
    Um **Registo de atividade Azure** será apresentada na secção Vistas Eclipse com separadores.
    ![][ic719489]
   Pode utilizar este registo, bem como a vista **da consola** , para ver o progresso da sua implementação. Alternativa é inicie a sessão [Portal de gestão do Azure][]e utilize a secção **Serviços em nuvem** para monitorizar o estado.
1. Quando a sua implementação com êxito for implementada, o **Registo de atividade Azure** mostrará um estado **publicado**. Clique em **publicado**, conforme apresentado na seguinte imagem, e o browser será aberta uma instância da sua implementação.
    ![][ic719490]

Visto que trata de uma implementação para ambiente de teste, o nome de DNS será igualmente da http:// formulário&lt;*guid*&gt;. cloudapp.net e o irá URL contêm o nome de DNS plus um sufixo para a sua aplicação. Por exemplo, http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (A parte **MyHelloWorld** é entre maiúsculas e minúsculas). Também pode ver o nome de DNS se clicar no nome da implementação no Portal de gestão da plataforma do Azure (numa parte Cloud Services do portal de gestão).

Apesar de foi este guia passo a passo para uma implementação para o ambiente de teste, uma implementação produção segue-se os mesmos passos, exceto dentro da caixa de diálogo **publicar no Azure** , selecione **produção** em vez **de transição** para o **ambiente de destino**. Uma implementação, nos resultados de produção com base no nome de DNS da sua escolha, em vez de um GUID conforme utilizado para transição de um URL.

>[AZURE.WARNING] Neste momento ter implementado a aplicação Azure na nuvem. No entanto, antes de continuar, aperceba que uma aplicação implementada, mesmo que não está em execução, continua a imputação dos tempo a cobrar para a sua subscrição. Por isso, é extremamente importante que eliminar implementações indesejadas da sua subscrição Azure.

## <a name="about-azure-deployment-projects"></a>Acerca de projectos de implementação do Azure ##

Para implementar um ou mais aplicações de Java para Azure, é necessário um projeto de implementação do Azure. A função do "pacote" que as aplicações têm de ser moldado para poder ser publicado no Azure para ser reproduzido.

Para obter informações sobre as suas aplicações, além de um projecto de implementação do Azure também contém informações sobre outros componentes principais da sua implementação, mais nenhumas: o contentor de servidor de aplicação para executar a aplicação web no e runtime Java executá-la. Azure suporta uma seleção grande de Java runtimes e pode escolher entre os servidores de aplicações Java.

Apesar do exemplo aqui utilizado é bastante simplificado para fins educacionais, um projecto de implementação do Azure também pode conter outras informações de configuração importantes que permite-lhe criar serviços em nuvem quase arbitrariamente complexas, dimensionáveis, altamente disponível, várias camadas com as suas aplicações. Pode ativar **afinidade sessão ("sessões autocolantes")**, **rápida de colocação em cache**, **depuração remota**, **SSL descarregar**, **/ porta de firewall encaminhamento**, **acesso remoto**e um número de outros capacidades poderosas.

Se tiver concluído a secção anterior deste Tutorial ("para implementar a aplicação para Azure, a forma mais rápida e simple"), verá agora um novo projeto de implementação do Azure no Explorador de projecto gerado automaticamente para si e com o nome "**MyHelloWorld_onAzure**".

Poderá ter também começou a este tutorial pela primeira vez a criar um projeto em branco implementação Azure si próprio e, em seguida, adicionando suas aplicações para o mesmo. É uma mais longa do processo, mas que lhe dá maior controlo sobre a configuração inicial do início.

Para criar um novo projeto de implementação do Azure de raiz, clique no botão **Novo projeto de implementação do Azure** ![][ic710876].

Independentemente de se estiver a trabalhar com um projeto de implementação do Azure já existente ou criar um partir da raiz, é possam alterar as suas definições de configuração e componentes, tal como o JDK ou servidor de aplicações, quatros facilmente em qualquer altura.

Para alterar JDK, ou o servidor da aplicação ou a lista de aplicações num projeto de implementação do Azure existente:

1. Expanda o nó do projeto (por exemplo, **MyHelloWorld_onAzure**) no Explorador de projecto
2. Botão direito do rato **WorkerRole1**
3. Expandir o submenu **Azure** no menu de contexto
4. Clique em **configuração do servidor**

Independentemente de se começou a estes passos de configuração do servidor ao editar um projecto de implementação do Azure existente, conforme mostrado acima ou criar um novo do zero, verá o mesmo tipo de caixas de diálogo permitindo-lhe configurar a sua JDK, componentes de servidor e a aplicação. Para saber mais como alterar as definições nessas caixas de diálogo, por exemplo alterar JDK, o servidor da aplicação e adicionar ou remover aplicações numa implementação, consulte o artigo de [Propriedades do servidor de configuração][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Apenas para o Windows: implementar a aplicação para o emulador cluster ##

>[AZURE.NOTE] O Azure emulador só está disponível no Windows. Ignore esta secção se estiver a utilizar um sistema operativo que não seja o Windows.

Se tiver criado um novo projeto de implementação do Azure seguindo os passos descritos anterior, ou seja, implicitamente, publicando a sua aplicação para o Azure, o JDK e a aplicação os servidores de tem sido configurados para a nuvem, mas não para emulação local. Para preparar o seu projeto para testar o emulador local, siga estes passos:

1. No Explorador de projeto do Eclipse, clique em **MyHelloWorld_onAzure**.
1. Botão direito do rato no **WorkerRole1**.
1. Expanda o submenu **Azure** no menu de contexto.
1. Clique em **configuração do servidor**.
1. No separador **JDK** , verifique se o toolkit previamente tiver configurado uma predefinida JDK local para si. Se não, ou se pretende alterar as predefinições assumidas, certifique-se de que a caixa de verificação **utilizar JDK a partir deste caminho de ficheiro para testar a ligação localmente** está selecionada e a localização da instalação JDK que pretende utilizar está especificada. Se pretende alterá-la, clique no botão **Procurar** e utilizar o controlo de procurar, selecione a localização do directório de JDK para utilizar.
1. Clique no separador **servidor** .
1. Na caixa de texto do **caminho do servidor Local** na parte inferior da caixa de diálogo, introduza o caminho de um servidor instalado localmente que corresponde ao tipo e número de versão principal do servidor seleccionado na parte superior da caixa de diálogo, sob a caixa de verificação **Implementar um servidor deste tipo** . Se quiser utilizar um tipo diferente ou uma versão principal do servidor da aplicação, altere a seleção nessa caixa de verificação primeiro.
1. Clique em **OK**.
1. Na barra de ferramentas Eclipse, clique no botão **executar no Azure emulador** , ![][ic710879]. Se o botão **executar no Azure emulador** não estiver ativado, certifique-se de que **MyHelloWorld_onAzure** está selecionada no Explorador de projecto do Eclipse e certifique-se de que Project Explorer do Eclipse tem foco como a janela atual. Em primeiro lugar esta será iniciado uma compilação completa do seu projeto e, em seguida, iniciar a sua aplicação de web de Java no emulador de cluster. (Nota que, consoante as características de desempenho do seu computador, a primeira compilação poderá demorar entre de aguardar alguns segundos para alguns minutos, mas constrói subsequentes receberão mais rápido.) Após o primeiro passo de compilação ter sido concluído, será pedido ao controlo de conta de utilizador do Windows (UAC) para permitir que este comando efetuar alterações ao seu computador. Clique em **Sim**.

>[AZURE.IMPORTANT] Se não vir a linha de comandos UAC, verifique a barra de tarefas do Windows para o ícone UAC e clique pela primeira vez. Por vezes o UAC pedido não aparecem como uma janela mais acima, mas está visível apenas como um ícone de barra de tarefas.

1. Examine a saída do emulador cluster IU para determinar se existem problemas com o seu projeto. Consoante o conteúdo da sua implementação, poderá demorar alguns minutos para a sua aplicação ser iniciado totalmente o emulador cluster.
1. Inicie o browser e utilizar o URL `http://localhost:8080/MyHelloWorld` como o endereço (a `MyHelloWorld` parte do URL é sensível às maiúsculas). Deverá visualizar a aplicação de MyHelloWorld (o resultado de index.jsp), semelhante a imagem seguinte: ![][ic589579]

Quando estiver pronto para parar a aplicação a partir da ser executada em emulador cluster, na barra de ferramentas Eclipse, clique no botão **Repor emulador Azure** , ![][ic710880].

## <a name="to-delete-your-deployment"></a>Para eliminar a sua implementação ##

Para eliminar a sua implementação dentro o Toolkit de Azure para Eclipse, certifique-se de que **MyHelloWorld_onAzure** está selecionada no Project Explorer do Eclipse, certifique-se de que o Project Explorer Eclipse tem janela atual do concentrar-se e, em seguida, clique no botão **Anular publicação** , ![][ic710883], na barra de ferramentas Eclipse. (O que pode fazer a mesma operação clicando **MyHelloWorld_onAzure** no Project Explorer do Eclipse, clicando em **Azure** e, em seguida, clicando em **Undeploy a partir do Azure nuvem**.) Esta ação irá apresentar a caixa de diálogo **Anular a publicação de projeto do Azure** .

![][ic719491]

Selecione o serviço de subscrição e na nuvem que contém a sua implementação, selecione a implementação que pretende eliminar e, em seguida, clique em **Anular publicação**.

(Uma alternativa a utilizar o toolkit para eliminar a implementação é utilizar a secção **Serviços em nuvem** do Portal de gestão do Azure: navegar para a sua implementação, selecione-o e, em seguida, clique no botão **Eliminar** . Isto parar e, em seguida, eliminar, de implementação. Se apenas pretende parar de implementação e não eliminá-lo, clique no botão **Parar** em vez de no botão **Eliminar** , mas tal como mencionado acima, se não está a eliminar a implementação, cobrar taxas irão continuar a imputação para a sua implementação, mesmo se está parado).

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

[Quais são as novidades no Azure Toolkit para Eclipse][]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de gestão do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Activar o acesso remoto para implementações Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades do servidor de configuração]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Quais são as novidades no Azure Toolkit para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
