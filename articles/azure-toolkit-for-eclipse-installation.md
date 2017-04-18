<properties
    pageTitle="Instalar o Toolkit Azure para Eclipse | Microsoft Azure"
    description="Saiba como instalar o Toolkit de Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalar o Toolkit Azure para Eclipse

O Toolkit de Azure para Eclipse fornece uma funcionalidade que permitem-lhe criar facilmente, desenvolver, testar e implementar aplicações Azure utilizando o ambiente de desenvolvimento Eclipse e modelos. O Toolkit de Azure para Eclipse é um projeto de abrir origem cujo código fonte está disponível ao abrigo da licença MIT a partir do site do projeto no GitHub no seguinte URL:

<https://github.com/Microsoft/Azure-Tools-for-Java>

Os passos seguintes mostram-lhe como instalar o Toolkit de Azure para Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Para instalar o Toolkit de Azure para Eclipse

1. Inicie o Eclipse.

1. Quando abre a Eclipse, clique no menu **Ajuda** e, em seguida, clique em **Instalar Software de novo**, conforme apresentado na seguinte ilustração.

    ![Instalar o Toolkit Azure para Eclipse][01]

1. Na caixa de diálogo **Software disponível** , na caixa de **trabalhar com** texto, escreva **http://dl.microsoft.com/eclipse** seguido a tecla **Enter** .

1. No painel de **nome** , verifique o **Toolkit de Azure para Eclipse**e desmarque **contacto atualizar todos os sites durante instalar para localizar o software necessário**. Ecrã deverá ser semelhante ao seguinte:

    ![Instalar o Toolkit Azure para Eclipse][02]

1. Se expandir o **Toolkit de Azure para Eclipse**, irá ver os seguintes itens:

    * **Informações da aplicação de plug-in para Java**: este componente permite-lhe utilizar de telemetria registo e análise dos serviços do Azure para as aplicações e instâncias do servidor.
    * **Filtro de serviços de controlo de acesso de Azure**: este componente fornece suporte para autenticar os utilizadores da aplicação com Azure ACS, permitindo-cenários de início de sessão únicos e externalizing lógica de identidade da aplicação.
    * **Plug-in comuns do Azure**: este componente fornece as funcionalidades comuns necessária por outros componentes toolkit.
    * **Azure Explorer para Eclipse**: este componente fornece as funcionalidades comuns necessária por outros componentes toolkit.
    * **Plug-in do Azure para Eclipse com Java**: este componente fornece suporte para desenvolver projetos que ajudam a criar, testar e implementar Java aplicações para o cloud do Microsoft Azure na Eclipse e através de linha de comandos.
    * **Azure Web Apps Plug-in com Java**: este componente fornece suporte para implementar aplicações web Java contentores do Microsoft Azure Web App.
    * **Microsoft JDBC controlador 4.2 para SQL Server**: este componente fornece JDBC API para SQL Server e base de dados do Microsoft Azure SQL para Java plataforma Enterprise Edition 8.
    * **Compactar para bibliotecas do cliente Qpid Apache para JMS**: este componente fornece o componente de cliente JMS do projeto Apache Qpid para ativar a sua aplicação para utilizar AMQP mensagens no Microsoft Azure.
    * **Pacote do Microsoft Azure bibliotecas para Java**: este componente fornece APIs para aceder a serviços do Microsoft Azure, como o armazenamento, bus de serviço, tempo de execução do serviço, etc.

1. Clique em **seguinte**. (Se ocorrer atrasos invulgares ao instalar o toolkit, certifique-se de que o **contacto atualizar todos os sites durante instalar para localizar o software necessário** está selecionada.)

1. Na caixa de diálogo **Detalhes da instalação** , clique em **seguinte**.

    ![Reveja os detalhes da instalação][03]

1. Na caixa de diálogo **Rever licenças** , reveja os termos de contratos de licença. Se aceitar os termos de contratos de licença, clique em **aceito os termos do contrato de licença** e, em seguida, clique em **Concluir**. (Os restantes passos partem do pressuposto que aceitar os termos de contratos de licença. Se não aceitar os termos de contratos de licença, saia o processo de instalação.)

    ![Licenças de revisão][04]

    Eclipse irá transferir e instalar pacotes de necessários.

    ![Progresso da instalação][05]

1. Se lhe for pedido para reiniciar Eclipse para concluir a instalação, clique em **Sim**.

    ![Reinicie o pedido][06]

## <a name="see-also"></a>Consulte também

Para mais informações sobre os conjuntos de ferramentas do Azure para comunicação de IDES: Java, consulte as ligações seguintes:

- [Toolkit Azure para Eclipse]
  - *Instalar o Toolkit Azure para Eclipse (Este artigo)*
  - [Criar uma aplicação de Web do mundo Olá para Azure no Eclipse]
  - [Quais são as novidades no Azure Toolkit para Eclipse]
- [Toolkit Azure para IntelliJ]
  - [Instalar o Toolkit Azure para IntelliJ]
  - [Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]
  - [Quais são as novidades no Azure Toolkit para IntelliJ]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

<!-- URL List -->

[Toolkit Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar uma aplicação de Web do mundo Olá para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalar o Toolkit Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Quais são as novidades no Azure Toolkit para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Quais são as novidades no Azure Toolkit para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de programadores do Azure Java]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

