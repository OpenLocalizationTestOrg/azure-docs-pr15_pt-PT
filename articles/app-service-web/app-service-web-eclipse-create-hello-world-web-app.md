<properties 
    pageTitle="Criar uma aplicação de Web do mundo Olá para Azure no Eclipse | Microsoft Azure" 
    description="Este tutorial mostra-lhe como utilizar o Toolkit de Azure para Eclipse para criar um Olá mundo Web App para o Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Criar uma aplicação de Web do mundo Olá para Azure no Eclipse

Este tutorial mostra como criar e implementar uma aplicação de Olá mundo básica para Azure como uma aplicação Web utilizando o [Toolkit de Azure para Eclipse]. É apresentado um exemplo de JSP básica para simplificar, mas passos altamente semelhantes seria adequados para um servlet Java, desde que se refere a implementação do Azure.

Quando tiver concluído neste tutorial, a aplicação terá o aspeto semelhante ao seguinte ilustração ao visualizar num browser:

![Pré-visualização do Olá mundo aplicação][01]
 
## <a name="prerequisites"></a>Pré-requisitos

* Um Java programador Kit (JDK), v 1,8 ou posterior.
* -Eclipse IDE para programadores de EE Java, Luna ou posterior. Isto pode ser transferido das <http://www.eclipse.org/downloads/>.
* Uma distribuição de um servidor web baseadas em Java ou servidor de aplicações, como Apache Tomcat ou cais.
* Uma subscrição Azure, que pode ser adquirida a partir do <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* O Toolkit Azure para Eclipse. Para mais informações, consulte o artigo [instalar o Toolkit de Azure para Eclipse].

## <a name="to-create-a-hello-world-application"></a>Para criar uma aplicação do mundo Olá

Primeiro, vamos começar com a criação de um projeto Java.

1. Iniciar Eclipse e no menu de clique em **ficheiro**, clique em **Novo**e, em seguida, clique em **Projeto de Web dinâmicos**. (Se não vir o **Projecto Web dinâmicos** listado como um projeto disponível depois de clicar em **ficheiro** e **Novo**, em seguida, faça o seguinte: clique em **ficheiro**, clique em **Novo**, clique em **projeto...**, expanda **Web**, clique em **Projeto de Web dinâmicos**e clique em **seguinte**.)

1. Para fins neste tutorial, dê um nome do projeto **/AminhaApWeb**. Ecrã irão aparecer semelhante ao seguinte:

    ![Criar um novo projeto de Web dinâmicos][02]

1. Clique em **Concluir**.

1. Vista de Project Explorer de Eclipse, expanda **/AminhaApWeb**. Com o botão direito **ConteúdoWeb**, clique em **Novo**e, em seguida, clique em **Ficheiro de JSP**.

1. Na caixa de diálogo **Novo ficheiro JSP** , nome de ficheiro **index.jsp**, manter a pasta principal como **/AminhaApWeb/ConteúdoWeb**e, em seguida, clique em **seguinte**.

1. Na caixa de diálogo **Seleccionar o modelo de JSP** , para fins neste tutorial, selecione **Novo ficheiro JSP (html)**e, em seguida, clique em **Concluir**.

1. Quando abre o ficheiro index.jsp no Eclipse, adicionar texto a apresentar dinamicamente **Olá mundo!** dentro dos existentes `<body>` elemento. O atualizada `<body>` conteúdo deve assemelhar-se o exemplo seguinte:

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Guarde index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Para implementar a aplicação a um contentor de aplicação Web do Azure

Existem várias formas às quais pode implementar uma aplicação web Java Azure. Este tutorial descreve um do mais simples: a aplicação será implementada num contentor de aplicação Web Azure - nenhum tipo de projecto especial nem ferramentas adicionais são necessários. O JDK e o software do contentor web serão fornecidos por si pela Azure, pelo que não existe sem necessidade de carregue o seu próprio; tudo o que necessita é a aplicação Web do Java. Como resultado, o processo de publicação para a sua aplicação irá demorar segundos, não minutos.

1. No Explorador de projeto do Eclipse, com o botão direito **/AminhaApWeb**.

1. No menu de contexto, selecione **Azure**, em seguida, clique em **Publicar como Azure Web App....**

    ![Publicar como Azure Web App][03]
   
    Em alternativa, enquanto o projeto de aplicação web é seleccionado no Project Explorer, pode clicar no botão de lista pendente de **Publicar** na barra de ferramentas e seleccione **Publicar como Azure Web App** a partir desse local:
   
    ![Publicar como Azure Web App][14]
   
1. Se lhe tiver não já iniciou sessão no Azure a partir do Eclipse, vai ser-lhe iniciar sessão na sua conta Azure:

    ![Azure início de sessão caixa de diálogo In][04]
   
    Se tiver várias contas do Azure, algumas dos pedidos durante o processo de início de sessão podem ser apresentadas mais do que uma vez, mesmo se aparecem como sendo o mesmo. Quando isto acontece, continue a seguir os instruções de início de sessão.

1. Depois de com êxito tenha sessão iniciada na sua conta Azure, a caixa de diálogo **Gerir subscrições** irá apresentar uma lista de subscrições que estão associadas com as suas credenciais. Se existirem múltiplas subscrições listados e que pretende trabalhar com apenas um subconjunto específico-las, opcionalmente, pode desmarcar aqueles que pretende utilizar. Quando tiver selecionado as suas subscrições, clique em **Fechar**.

    ![Gerir a caixa de diálogo de subscrições][05]
   
1. Quando for apresentada a caixa de diálogo **Implementar a Azure Web App contentor** , apresentem quaisquer contentores de aplicação Web que tenha criado anteriormente; Se não tiver criado quaisquer contentores, a lista estará vazia.

    ![Implementar a caixa de diálogo contentor do Azure Web App][06]
   
1. Se não tiver criado um contentor de aplicação Web Azure antes ou se pretender para publicar a sua aplicação para um novo contentor, utilize os passos seguintes. Caso contrário, selecione um contentor de aplicação Web existente e avance para o passo 7 abaixo.

    1. Clique em **Novo …**

        ![Implementar a caixa de diálogo contentor do Azure Web App][15]

    1. Vai ser apresentada a caixa de diálogo **Novo contentor de aplicação Web** :

        ![Nova caixa de diálogo do contentor de aplicação Web][07a]

    1. Introduza uma **etiqueta de DNS** para o contentor de aplicação Web; Isto irá formulário a etiqueta DNS folha do URL para a aplicação web anfitrião no Azure. (Tenha em atenção que o nome deve estar disponíveis e se ajustarem à Azure Web App requisitos de nomenclatura.)

    1. No menu pendente **Contentor Web** , selecione o software adequado para a sua aplicação.

        Atualmente, pode escolher a partir de Tomcat 8, Tomcat 7 ou cais 9. Uma distribuição recente do software selecionado será fornecida pelo Azure e é executada numa distribuição de 8 JDK criado pelo Oracle e fornecida pela Azure recente.

    1. No menu pendente **subscrição** , selecione a subscrição que pretende utilizar para esta implementação.

    1. No menu pendente **Grupo de recursos** , selecione o grupo de recursos com o qual pretende associar a sua aplicação Web. (Azure recurso grupos permitem agrupar recursos relacionados para, por exemplo, podem ser eliminados em conjunto.)

        Pode selecionar um grupo de recursos existente (se já tiver alguma) e ignorar para o passo g abaixo ou utilize o que se segue estes passos para criar um novo grupo de recursos:

        * Clique em **Novo …**

        * Vai ser apresentada a caixa de diálogo de **Novo grupo de recursos** :

            ![Caixa de diálogo de novo grupo de recursos][08]

        * Na caixa de texto **nome** , especifique um nome para o novo grupo de recursos.

        * Na **região** pendente menu, selecione os dados Azure apropriados centrar localização para o seu grupo de recursos.

        * OPCIONAL: Por predefinição, uma distribuição recente da Java 8 será implementada por Azure automaticamente para o contentor de aplicação web como o seu JVM. No entanto, pode especificar uma versão diferente e distribuição da JVM se a sua aplicação Web requerido pela. Para especificar o JDK para a sua aplicação Web, clique no separador **JDK** e selecione uma das seguintes opções:

            * **Implementar a predefinição JDK oferecido pelo serviço Azure Web Apps**: esta opção, irá implementar uma distribuição recente da Java 8.

            * **Implementar um 3 festa JDK disponível no Azure**: esta opção permite-lhe escolher a partir da lista de JDKs que são fornecidas pelo Microsoft Azure.

            * **Implementar o meu próprio JDK a partir desta localização de transferência**: esta opção permite-lhe especificar o seu próprio distribuição JDK, que tem de ser fornecida como um ficheiro ZIP e carregada para uma localização de transferência disponível ao público ou uma conta de armazenamento Azure para o qual tem acesso.

            ![Nova caixa de diálogo do contentor de aplicação Web][07b]

    * Clique em **OK**.

    1. Menu pendente **Plano de serviço de aplicação** apresenta os planos do serviço de aplicação que estão associados com o grupo de recursos que selecionou. (Informações como a localização da sua aplicação Web, a camada preços e o tamanho da instância cluster de especificar planos do serviço de aplicação. Um plano de serviço de aplicação única podem ser utilizado para várias aplicações Web, que é o motivo pelo qual são mantida separadamente a partir de uma implementação do Web App específica.)

        Pode selecionar uma existente aplicação serviço planear (se já tiver alguma) e ignorar para o passo abaixo h ou utilize o que se segue estes passos para criar um novo plano de serviço de aplicação:

        * Clique em **Novo …**

        * Vai ser apresentada a caixa de diálogo **Novo plano de serviço de aplicação** :

            ![Nova caixa de diálogo de plano de serviço de aplicação][09]

        * Na caixa de texto **nome** , especifique um nome para o seu plano de serviço de nova aplicação.

        * No menu de **localização** pendente, selecione os dados Azure apropriados centrar localização para o plano.

        * No menu pendente **Preços camada** , selecione a definição de preços adequado para o plano. Pode escolher **livre**para fins de teste.

        * No o **Tamanho da instância de** menu pendente, selecione a instância adequada Dimensionar para o plano. Para fins de teste pode escolher **pequenas**.

    1. Assim que concluiu todos os passos acima, a caixa de diálogo novo contentor de aplicação Web deverá assemelhar-se a ilustração seguinte:

        ![Nova caixa de diálogo do contentor de aplicação Web][10]

    1. Clique em **OK** para concluir a criação do seu novo contentor Web App.

        Aguarde alguns segundos para a lista dos contentores Web App para ser atualizados, e o contentor de aplicação web recentemente criado deverão ser selecionado na lista.

1. Agora está pronto para concluir a implementação inicial da sua aplicação Web para Azure:

    ![Implementar a caixa de diálogo contentor do Azure Web App][11]

    Clique em **OK** para implementar a aplicação de Java para o contentor selecionado do Web App.

    Por predefinição, a aplicação será implementada como um subdirectório do servidor da aplicação. Se pretende que seja implementado como a aplicação de raiz, selecione a caixa de verificação **Implementar a raiz** antes de clicar em **OK**.

1. Em seguida, deverá ver a vista de **Registo de atividade Azure** , que irá indicar o estado da implementação da sua aplicação Web.

    ![Registo de atividade Azure][12]

    O processo de implementá-lo na sua aplicação Web do Azure deverá tomar apenas alguns segundos para concluir. Quando o pronto de aplicação, verá uma ligação com o nome **publicados** na coluna **Estado** . Quando clicar na ligação, irá levá-lo para a home page da sua aplicação Web implementada.

## <a name="updating-your-web-app"></a>Atualizar a sua aplicação Web

Atualizar um existente a executar o Azure Web App é um processo de rápida e fácil de gerir e tem duas opções para atualizar:

* Pode atualizar a implementação de uma aplicação Web do Java existente.
* Pode publicar uma aplicação Java adicional para o mesmo contentor de aplicação Web.

Em ambos os casos, o processo for idêntico e demora apenas alguns segundos:

1. No Explorador de projecto Eclipse, com o botão direito na aplicação de Java que pretende atualizar ou adicionar a um contentor de aplicação Web existente.

1. Quando for apresentado o menu de contexto, selecione **Azure** e, em seguida, **Publicar como Azure Web App....**

1. Uma vez que já tiver iniciado anteriormente, irá ver uma lista de contentores de aplicação Web existentes. Selecione aquele que pretende publicar ou voltar a publicar a aplicação de Java e clique em **OK**.

De aguardar alguns segundos mais tarde, a vista do **Registo de atividade Azure** irá apresentar a sua implementação actualizada como **publicada** e poderão confirmar a sua aplicação atualizada num browser.

## <a name="stopping-an-existing-web-app"></a>Parar a uma aplicação Web existente

Para parar de um existente Azure Web App contentor, (incluindo todas as aplicações Java implementadas na mesma), pode utilizar a vista do **Explorador de Azure** .

Se a vista do **Explorador de Azure** ainda não estiver aberta, pode abri-lo clicando em seguida, menu de **janela** no Eclipse, em seguida, clique em **Mostrar a vista**, **outros …**e, em seguida **Azure**e, em seguida, clique em **Azure Explorador**. Se não anteriormente iniciou sessão, vai apresentar-lhe para fazê-lo.

Quando a vista do **Explorador de Azure** for apresentada, utilizar siga estes passos para parar a sua aplicação Web: 

1. Expanda o nó do **Azure** .

1. Expanda o nó do **Web Apps** . 

1. Com o botão direito do Web App pretendida.

1. Quando for apresentado o menu de contexto, clique em **Parar**.

    ![Parar a uma aplicação Web existente][13]

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre os conjuntos de ferramentas do Azure para comunicação de IDES: Java, consulte as ligações seguintes:

- [Toolkit Azure para Eclipse]
  - [Instalar o Toolkit Azure para Eclipse]
  - *Criar uma aplicação de Web do mundo Olá para Azure no Eclipse (Este artigo)*
  - [Quais são as novidades no Azure Toolkit para Eclipse]
- [Toolkit Azure para IntelliJ]
  - [Instalar o Toolkit Azure para IntelliJ]
  - [Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]
  - [Quais são as novidades no Azure Toolkit para IntelliJ]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

Para obter informações adicionais sobre a criação de Azure Web Apps, consulte o artigo [Descrição geral de aplicações Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Toolkit Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Toolkit Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Instalar o Toolkit Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Instalar o Toolkit Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Quais são as novidades no Azure Toolkit para Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Quais são as novidades no Azure Toolkit para IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro de programadores do Azure Java]: https://azure.microsoft.com/develop/java/
[Descrição geral de aplicações Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png
