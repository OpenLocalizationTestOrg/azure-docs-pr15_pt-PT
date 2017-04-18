<properties
    pageTitle="Ligar no local SQL Server a partir de uma aplicação web no serviço de aplicação do Azure utilizando ligações de híbrido"
    description="Criar uma aplicação web no Microsoft Azure e ligue-a uma base de dados do SQL Server no local"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="cephalin"/>

# <a name="connect-to-on-premises-sql-server-from-a-web-app-in-azure-app-service-using-hybrid-connections"></a>Ligar no local SQL Server a partir de uma aplicação web no serviço de aplicação do Azure utilizando ligações de híbrido

Ligações de híbrido podem ligar [Azure aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps para recursos no local que utilizam uma porta TCP estática. Recursos suportados incluem o Microsoft SQL Server, MySQL, HTTP Web APIs, aplicação de serviço e a maioria dos serviços Web personalizados.

Neste tutorial, irá aprender a criar uma aplicação web do aplicação de serviço no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), ligar a aplicação web a sua base de dados local no local do SQL Server utilizando a nova funcionalidade de ligação de híbrido, criar uma aplicação de ASP.NET simple que irá utilizar a ligação híbrido e implementar a aplicação para o serviço de aplicação web app. A aplicação web concluída no Azure armazena as credenciais de utilizador numa base de dados de associação que está no local. O tutorial assume sem experiência prévia utilizando Azure ou ASP.NET.

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.
>
>Parte da Web Apps da funcionalidade híbrido ligações está disponível apenas no [Portal do Azure](https://portal.azure.com). Para criar uma ligação no BizTalk Services, consulte o artigo [Híbrido ligações](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## <a name="prerequisites"></a>Pré-requisitos ##

Para concluir este tutorial, terá os seguintes produtos. Todos os estão disponíveis nas versões livres, para que possa começar a desenvolver para o Azure completamente para livre.

- **Subscrição do azure** - para uma subscrição gratuita, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/).

- **Visual Studio 2013** - transferir uma versão de avaliação gratuita do Visual Studio 2013, consulte o artigo [Visual Studio transferências](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale este antes de continuar.

- Para o **Microsoft .NET Framework 3.5 Service Pack 1** - se for o seu sistema operativo Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 ou Windows Server 2008 R2, pode ativar a isto no painel de controlo > programas e funcionalidades > activar funcionalidades do Windows ou desativar. Caso contrário, pode transferi-la a partir do [Centro de transferências da Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014 Express com ferramentas** - transferir o Microsoft SQL Server Express gratuitamente na [página de base de dados do Microsoft Web plataforma](http://www.microsoft.com/web/platform/database.aspx). Escolha a versão **Express** (não LocalDB). A versão **Express com ferramentas** inclui SQL Server Management Studio, que irá utilizar neste tutorial.

- **SQL Server Management Studio Express** - este é incluído no SQL Server 2014 Express com transferência das ferramentas de mencionados acima, mas se necessitar de instalá-lo em separado, pode transferir e instalar a partir da [página de transferência do SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

O tutorial assume que tem uma subscrição do Azure, que tenha instalado o Visual Studio 2013 e que tiver instalado ou ativado .NET Framework 3.5. O tutorial mostra-lhe como instalar o SQL Server 2014 Express numa configuração que funciona bem com a funcionalidade de ligações de implementações do Azure (uma instância predefinida com uma porta TCP estática). Antes de iniciar o tutorial, transfira o SQL Server 2014 Express com ferramentas desde a localização mencionada acima, se não tiver o SQL Server instalado.

### <a name="notes"></a>Notas ###
Para utilizar uma base de dados do SQL Server ou do SQL Server Express no local com uma ligação de híbrido, TCP/IP tem de estar ativada numa porta estática. Instâncias de predefinidas no SQL Server utilizam porta estática 1433, Considerando que não fazem instâncias com nome.

O computador no qual instalou o agente de Gestor de ligação híbrido no local:

- Tem de ter conectividade de saída para Azure sobre:

Porta|Por que motivo
---|---
80|É **necessário** para porta HTTP para validação do certificado e, opcionalmente, para conetividade a dados.
443|**Opcional** para a conectividade de dados. Se não estiver disponível conectividade de saída para 443, porta TCP 80 é utilizada.
5671 e 9352|**Recomendado** mas opcional para a conectividade de dados. Tenha em atenção que deste modo normalmente rendimentos débito superior. Se a conectividade de saída para estas portas não estiver disponível, é utilizada porta 443 do TCP.
- Tem de conseguir alcançar o *nome do anfitrião*:*NúmeroPorta* do seu recurso no local.

Os passos neste artigo presumem que está a utilizar o browser do computador que será o anfitrião o agente de ligação de híbrido no local.

Se já tiver o SQL Server instalado numa configuração e um ambiente que cumprem as condições descritas acima, pode avançar e começar por [criar uma base de dados do SQL Server no local](#CreateSQLDB).

<a name="InstallSQL"></a>
## <a name="a-install-sql-server-express-enable-tcpip-and-create-a-sql-server-database-on-premises"></a>RESPOSTAS. Instalar o SQL Server Express, ativar TCP/IP e de criar uma base de dados do SQL Server no local ##

Esta secção mostra-lhe como instalar o SQL Server Express, ativar TCP/IP e criar uma base de dados para que a aplicação web funcionam com o Portal do Azure.

### <a name="install-sql-server-express"></a>Instalar o SQL Server Express ###

1. Para instalar o SQL Server Express, execute o ficheiro **SQLEXPRWT_x64_ENU.exe** ou **SQLEXPR_x86_ENU.exe** que transferiu. Aparece o Assistente do Centro de instalação do SQL Server.

    ![Instalação do SQL Server][SQLServerInstall]

2. Selecione **instalação autónoma novo do SQL Server ou adicionar funcionalidades a uma instalação existente**. Siga as instruções, aceitar as opções predefinidas definições e, até chegar à página de **Configuração da instância** .

3. Na página **Configuração da instância** , selecione **predefinido instância**.

    ![Selecione a instância predefinida][ChooseDefaultInstance]

    Por predefinição, a instância predefinida do SQL Server recebe para pedidos de clientes do SQL Server numa porta estática 1433, que é o que requer a funcionalidade de ligações de híbrido. Instâncias com nome utilizam portas dinâmicas e UDP, que não são suportadas pelo híbrido ligações.

4. Aceite as predefinições na página de **Configuração do servidor** .

5. Na página **Configuração do motor de base de dados** , em **Modo de autenticação**, selecione o **Modo mista (autenticação do SQL Server e a autenticação do Windows)**e uma palavra-passe.

    ![Escolher o modo misto][ChooseMixedMode]

    Neste tutorial, irá de estar a utilizar a autenticação do SQL Server. Certifique-se de que não se esqueça da palavra-passe que lhe fornece, uma vez que irá necessitar mais tarde.

6. Visualizar passo a passo o resto do Assistente para concluir a instalação.

### <a name="enable-tcpip"></a>Ativar TCP/IP ###
Para ativar TCP/IP, irá utilizar o Gestor de configuração do SQL Server, que foi instalado quando instalou o SQL Server Express. Siga os passos em [Ativar o protocolo de rede TCP/IP para o SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
### <a name="create-a-sql-server-database-on-premises"></a>Criar uma base de dados do SQL Server no local ###

A aplicação web do Visual Studio necessita de uma base de dados de associação que possa ser acedida por Azure. Isto requer um SQL Server ou do SQL Server Express da base de dados (não LocalDB base de dados que utiliza o modelo MVC por predefinição), para que irá criar a base de dados de associação seguinte.

1. No SQL Server Management Studio, ligar para o SQL Server após a instalação. (Se **ligar ao servidor de** caixa de diálogo não aparecer automaticamente, navegar para o **Explorador de objectos** no painel esquerdo, clique em **Ligar**e, em seguida, clique em **Motor de base de dados**).  ![Ligar ao servidor][SSMSConnectToServer]

    Para **tipo de servidor**, selecione **Motor de base de dados**. **Nome do servidor**, pode utilizar **localhost** ou o nome do computador que está a utilizar. Selecione a **autenticação do SQL Server**e, em seguida, iniciar sessão com o nome de utilizador sa e a palavra-passe que criou anteriormente.

2. Para criar uma nova base de dados utilizando o SQL Server Management Studio, com o botão direito **bases de dados** no Explorador do objeto e, em seguida, clique em **Nova base de dados**.

    ![Criar nova base de dados][SSMScreateNewDB]

3. Na caixa de diálogo **Nova base de dados** , introduza MembershipDB para o nome da base de dados e, em seguida, clique em **OK**.

    ![Fornecer o nome da base de dados][SSMSprovideDBname]

    Tenha em atenção que não faça as alterações à base de dados neste momento. As informações de associação são adicionadas automaticamente mais tarde pela sua aplicação web quando executa-lo.

4. No Explorador do objeto, se lhe expanda **bases de dados**, verá que a base de dados de associação foi criada.

    ![MembershipDB criado][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## <a name="b-create-a-web-app-in-the-azure-portal"></a>B. Criar uma aplicação web no Portal do Azure ##

> [AZURE.NOTE] Se já tiver criado uma aplicação web no Portal do Azure que pretende utilizar para este tutorial, pode avançar para [criar uma ligação de híbrido e um serviço de BizTalk](#CreateHC) e continuar a partir daí.

1. No [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Mobile** > **Web app**.

    ![Botão novo][New]

2. Configurar a sua aplicação web e, em seguida, clique em **Criar**.

    ![Nome do Web site][WebsiteCreationBlade]

3. Depois de alguns minutos, a aplicação web é criada e é apresentada a sua pá de aplicação web. O pá é um dashboard verticalmente deslocável que permite-lhe gerir a sua aplicação web.

    ![Web site em execução][WebSiteRunningBlade]

    Para verificar que a aplicação web é direto, pode clicar no ícone **Procurar** para apresentar a página predefinida.

Em seguida, irá criar uma ligação de híbrido e um serviço de BizTalk para a aplicação web.

<a name="CreateHC"></a>
## <a name="c-create-a-hybrid-connection-and-a-biztalk-service"></a>C. Criar uma ligação de híbrido e um serviço de BizTalk ##

1. Novamente no Portal do, vá para definições e clique em **à rede** > **Configurar a ligação de híbrido pontos finais**.

    ![Ligações de híbrido][CreateHCHCIcon]

2. Na pá de ligações híbrido, clique em **Adicionar** > **nova ligação de híbrido**.

3. No pá **Criar ligação de híbrido** :
    - **Nome**, forneça um nome para a ligação.
    - Para o **nome do anfitrião**, introduza o nome do computador do seu computador anfitrião do SQL Server.
    - **Porta**, introduza 1433 (a porta predefinida para o SQL Server).
    - Clique em **Serviço de BizTalk** > **Novo serviço BizTalk** e introduza um nome para o serviço de BizTalk.

    ![Criar uma ligação de híbrido][TwinCreateHCBlades]

5. Clique duas vezes em **OK** .

    Quando o processo for concluído, o irá de área de **notificações** flash verde **SUCESSO** e o irá pá de **ligação de híbrido** Mostrar a nova ligação de híbrida com o estado como **não ligado**.

    ![Ligação de um híbrido criada][CreateHCOneConnectionCreated]

Neste momento, tiver concluído uma parte importante infraestrutura de ligação de implementações na nuvem. Em seguida, irá criar um elemento no local correspondente.

<a name="InstallHCM"></a>
## <a name="d-install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>D. Instalar o Gestor de ligação de híbrido no local para concluir a ligação ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Agora que a infraestrutura de ligação Híbrido estiver concluída, irá criar uma aplicação web que utiliza-lo.

<a name="CreateASPNET"></a>
## <a name="e-create-a-basic-aspnet-web-project-edit-the-database-connection-string-and-run-the-project-locally"></a>E. Criar um projeto de web do ASP.NET básico, edite a cadeia de ligação de base de dados e executar o projeto localmente ##

### <a name="create-a-basic-aspnet-project"></a>Criar um projeto básico do ASP.NET ###
1. No Visual Studio, no menu **ficheiro** , crie um novo projeto:

    ![Novo projeto do Visual Studio][HCVSNewProject]

2. Na secção **modelos** da caixa de diálogo **Novo projeto** , selecione **Web** e selecione a **Aplicação Web do ASP.NET**e, em seguida, clique em **OK**.

    ![Selecione a aplicação Web do ASP.NET][HCVSChooseASPNET]

3. Na caixa de diálogo **Novo projeto de ASP.NET** , selecione **MVC**e, em seguida, clique em **OK**.

    ![Selecione MVC][HCVSChooseMVC]

4. Quando o projeto tiver sido criado, é apresentada a página de ficheiro Leia-me de aplicação. Ainda não executada o projecto da web.

    ![Leia-me página][HCVSReadmePage]

### <a name="edit-the-database-connection-string-for-the-application"></a>Editar a cadeia de ligação de base de dados para a aplicação ###

Neste passo, edite a cadeia de ligação que indica a sua aplicação onde localizar a sua base de dados SQL Server Express local. A cadeia de ligação está no ficheiro. a aplicação Web. config, que contém informações de configuração para a aplicação.

> [AZURE.NOTE] Para se certificar de que a aplicação utiliza a base de dados que criou no SQL Server Express e não na pasta predefinida Visual Studio LocalDB, é importante que concluir este passo antes de executar o seu projeto.

1. No Explorador de solução, faça duplo clique no ficheiro da Web. config.

    ![Web. config][HCVSChooseWebConfig]

2. Edite a secção **connectionStrings** para apontar para a base de dados do SQL Server no seu computador local, seguindo a sintaxe no exemplo seguinte:

    ![Cadeia de ligação][HCVSConnectionString]

    Quando estiver a compor a cadeia de ligação, tenha em atenção o seguinte:

    - Se estiver a ligar a uma instância com nome em vez de uma instância de predefinida (por exemplo, YourServer\SQLEXPRESS), tem de configurar o SQL Server para utilizar portas estáticas. Para obter informações sobre como configurar as portas estáticas, consulte o artigo [como configurar o SQL Server para ouvir numa porta específica](http://support.microsoft.com/kb/823938). Por predefinição, instâncias com nome utilizam UDP e portas dinâmicas que não são suportadas pelo híbrido ligações.

    - É recomendado que especifique a porta (1433 por predefinição, conforme mostrado no exemplo) numa cadeia de ligação, de modo a que possa ter tem a certeza que o SQL Server local tem TCP ativado e estiver a utilizar a porta correta.

    - Lembre-se utilizar a autenticação do SQL Server para ligar, especificando o ID de utilizador e palavra-passe na sua cadeia de ligação.

3. Clique em **Guardar** no Visual Studio para guardar o ficheiro da Web. config.

### <a name="run-the-project-locally-and-register-a-new-user"></a>Execute o projecto localmente e registar um novo utilizador ###

1. Agora, execute o novo projeto de web localmente ao clicar no botão Procurar em depurar. Este exemplo utiliza o Internet Explorer.

    ![Executar o project][HCVSRunProject]

2. No canto superior direito da página web predefinida, selecione **Registe-se** para registar uma nova conta:

    ![Registe-se uma nova conta][HCVSRegisterLocally]

3. Introduza um nome de utilizador e palavra-passe:

    ![Introduza o nome de utilizador e palavra-passe][HCVSCreateNewAccount]

    Isto cria automaticamente uma base de dados no SQL Server local que detém as informações de associação para a sua aplicação. Uma das tabelas (**dbo. AspNetUsers**) suspensões web como aqueles que acabou de introduzir as credenciais de utilizador do aplicação. Verá esta tabela mais tarde no tutorial.

4. Feche a janela do browser da página web predefinido. Isto, impede a aplicação no Visual Studio.

Agora está pronto para o próximo passo, o que é publicar a aplicação para Azure e testá-la.

<a name="PubNTest"></a>
## <a name="f-publish-the-web-application-to-azure-and-test-it"></a>F. Publicar a aplicação web do Azure e testá-la ##

Agora, irá publicar a sua aplicação para o seu serviço de aplicação web app e, em seguida, testá-lo para ver como a ligação híbrido que configurou anteriormente está a ser utilizada para ligar a sua aplicação web para a base de dados no seu computador local.

### <a name="publish-the-web-application"></a>Publicar a aplicação web ###

1. Pode transferir o seu perfil de publicação para o aplicação de serviço web app no Portal do Azure. No pá para a sua aplicação web, clique em **obter publicar perfil**e, em seguida, guarde o ficheiro para o seu computador.

    ![Transferir publicar perfil][PortalDownloadPublishProfile]

    Em seguida, irá importar este ficheiro para a aplicação web do Visual Studio.

2. No Visual Studio, com o botão direito no nome do projeto no Explorador de solução e selecione **Publicar**.

    ![Selecione publicar][HCVSRightClickProjectSelectPublish]

3. Na caixa de diálogo **Publicar Web** , no separador **perfil** , selecione **Importar**.

    ![Importar][HCVSPublishWebDialogImport]

4. Navegue até ao seu perfil de publicação transferido, selecione-o e, em seguida, clique em **OK**.

    ![Navegue até ao perfil][HCVSBrowseToImportPubProfile]

5. As informações de publicação são importadas e apresenta o separador **ligação** da caixa de diálogo.

    ![Clique em publicar][HCVSClickPublish]

    Clique em **Publicar**.

    Quando concluir a publicação, o seu browser iniciação e mostrar a sua aplicação do ASP.NET agora familiar –, exceto os agora está direto na nuvem Azure!

Em seguida, irá utilizar a aplicação web direto para ver a sua ligação de híbrido em ação.

### <a name="test-the-completed-web-application-on-azure"></a>Testar a aplicação web concluída no Azure ###

1. No canto superior direito da sua página web no Azure, selecione **Iniciar sessão**.

    ![Teste início de sessão][HCTestLogIn]

2. A aplicação web do serviço de aplicação está agora ligada à associação da base de dados sua aplicação web no seu computador local. Para verificar isto, inicie sessão com as mesmas credenciais que introduziu anteriormente na base de dados local.

    ![Olá a saudação][HCTestHelloContoso]

3. Para testar ainda mais a sua nova ligação de híbrido, a aplicação Azure web a sessão e registar como outro utilizador. Fornecer um novo nome de utilizador e palavra-passe e, em seguida, clique em **Registe-se**.

    ![Teste registar outro utilizador][HCTestRegisterRelecloud]

4. Para verificar que as novas credenciais do utilizador tem sido guardadas na base de dados local através da ligação de híbrido, abra SQL Management Studio no seu computador local. No Explorador do objeto, expanda a base de dados **MembershipDB** e, em seguida, expanda **tabelas**. Botão direito do rato a **dbo. AspNetUsers** associação tabela e selecione **linhas selecione 1000 de início** para ver os resultados.

    ![Ver os resultados][HCTestSSMSTree]

5. A tabela de associação local agora mostra ambas as contas - o que criou localmente e a que criou na nuvem Azure. Aquele que criou na nuvem ter sido guardado a sua base de dados no local através da funcionalidade de ligação de implementações do Azure.

    ![Utilizadores registados na base de dados no local][HCTestShowMemberDb]

Agora tiver criado e implementado uma aplicação web do ASP.NET que utiliza uma ligação de híbrido entre uma aplicação web na nuvem Azure e uma base de dados do SQL Server no local. Parabéns!

## <a name="see-also"></a>Consulte também ##
[Descrição geral das ligações de híbrido](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Joaquim torção apresenta ligações híbrido (9 de canal de vídeo)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Descrição geral das ligações de híbrido](/services/biztalk-services/)

[BizTalk serviços: Separadores de Dashboard, Monitor, escala, configurar e híbrido ligação](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Construir uma nuvem de híbrido reais com totalmente integrada aplicação portabilidade (9 de canal de vídeo)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Recursos do Access no local utilizando ligações de híbrido no serviço de aplicação do Azure](../app-service-web/web-sites-hybrid-connection-get-started.md)

[Descrição geral de identidade do ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
