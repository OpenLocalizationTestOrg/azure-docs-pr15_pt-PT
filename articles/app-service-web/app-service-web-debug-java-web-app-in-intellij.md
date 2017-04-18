<properties 
    pageTitle="Depurar uma aplicação Web do Java no Azure no IntelliJ | Microsoft Azure" 
    description="Este tutorial mostra-lhe como utilizar o Toolkit de Azure para IntelliJ para depurar um Java Web App em execução no Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Depurar uma aplicação Web do Java no Azure no IntelliJ

Este tutorial mostra como depurar um Java Web App em execução no Azure utilizando o [Toolkit de Azure para IntelliJ]. Por razões de simplificar, que irá utilizar um exemplo de página de servidor de Java (JSP) básica para este tutorial, mas os passos seria semelhantes para uma servlet Java quando estiver a depurar no Azure.

Quando tiver concluído neste tutorial, a aplicação terá uma aspeto semelhante a ilustração seguinte quando estiver a depurá-lo no IntelliJ:

![][01]
 
## <a name="prerequisites"></a>Pré-requisitos

* Um Java programador Kit (JDK), v 1,8 ou posterior.
* IntelliJ IDEIA Ultimate Edition. Isto pode ser transferido das <https://www.jetbrains.com/idea/download/index.html>.
* Uma distribuição de um servidor web baseadas em Java ou servidor de aplicações, como Apache Tomcat ou cais.
* Uma subscrição Azure, que pode ser adquirida a partir do <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* O Toolkit Azure para IntelliJ. Para mais informações, consulte o artigo [instalar o Toolkit de Azure para IntelliJ].
* Um projecto da Web dinâmicas criadas e implementado a aplicação de serviço de Azure; Por exemplo, consulte [criar um Olá mundo Web App para o Azure no IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Depurar uma aplicação Web do Java no Azure

Para concluir estes passos nesta secção, pode utilizar um projeto de Web dinâmico existente que já tiver implementado como uma aplicação Web do Java no Azure, transferir um [Projecto de Web dinâmica de exemplo] e siga os passos no artigo [criar um Olá mundo Web App para o Azure no IntelliJ] implementá-lo no Azure. 

1. Abra o projeto Java Web App implementado Azure no IntelliJ.

1. Clique no menu **Executar** e, em seguida, clique em **Editar configurações...**.

    ![][02]

1. Quando abre a caixa de diálogo **Executar/depuração configurações** : 

    1. Selecione **Azure Web App**.
    1. Clique no **+** para adicionar uma nova configuração.
    1. Forneça um **nome** para a configuração.
    1. Aceite os valores predefinidos restantes que são sugeridos pelo Toolkit de Azure e, em seguida, clique em **OK**.

        ![][03]

1. Selecione a configuração de depuração de Azure Web App, que acabou de criar no canto superior direita do menu e clique no **Depurar**

    ![][04]

1. Quando lhe for pedido para **Activar depuração remota na aplicação Web remote agora?**, clique em **OK**.

1. Quando lhe for pedido que **a aplicação web do está agora pronta para depuração remota**, clique em **OK**.

    ![][05]

1. Selecione a configuração de depuração de Azure Web App que acabou de criar no canto superior direita do menu e, em seguida, clique no **Depurar**.

1. Uma linha de comandos do Windows ou a shell de Unix abrirá e preparar a ligação necessários para depuração; Pode ter de esperar até que a ligação para a sua aplicação Java Web remota é efetuada com êxito antes de continuar. Se estiver a utilizar o Windows, terá o seguinte aspeto a ilustração seguinte:

    ![][06]

1. Inserir um ponto de interrupção na sua página JSP, em seguida, abra o URL para a sua aplicação Web do Java num browser:

    1. Abrir o **Explorador do Azure** no IntelliJ.
    1. Navegue para a **Web Apps** e o que pretende depurar do Java Web App.
    1. Clique com o botão direito do rato sobre a aplicação Web e clique em **Abrir no Browser**.
    1. Agora irá introduzir IntelliJ no modo de depuração.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

Para obter informações adicionais sobre a criação de Azure Web Apps, consulte o artigo [Descrição geral de aplicações Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Toolkit Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Instalar o Toolkit Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Project Web dinâmico de exemplo]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro de programadores do Azure Java]: https://azure.microsoft.com/develop/java/
[Descrição geral de aplicações Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
