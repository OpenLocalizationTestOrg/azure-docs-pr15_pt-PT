<properties
    pageTitle="Criar uma aplicação web do Azure Marketplace | Microsoft Azure"
    description="Saiba como criar uma aplicação web do WordPress do Azure Marketplace, utilizando o Portal do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Criar uma aplicação web do Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace disponibiliza uma vasta gama de aplicações web populares desenvolvidos pela Microsoft, empresas de terceiros e iniciativas de software de abrir origem. Por exemplo, WordPress, Umbraco CMS, Drupal, etc. Estas aplicações web foram criadas numa vasta gama de quadros populares, tal como [PHP] neste WordPress exemplo, [.NET], [Node.js], [Java]e [Python], para atribuir um nome algumas. Para criar uma aplicação web do Azure Marketplace o software de só que precisa de é o browser que utiliza para o [Portal do Azure].

Neste tutorial vai aprender como:

* Localizar e criar do web app no Azure aplicação de serviço de que é baseada num modelo Azure Marketplace.
* Configure definições de serviço de aplicação do Azure para a nova aplicação web.
* Iniciação e gerir a sua aplicação web.

Para este tutorial, irá implementar um site de blogue WordPress do Azure Marketplace. Quando tiver concluído os passos neste tutorial, terá o seu próprio site WordPress para cima e a execução de na nuvem.

![Dashboard do exemplo WordPress wep aplicação][WordPressDashboard1]

O site de WordPress que irá implementar neste tutorial utiliza MySQL para a base de dados. Se pretender utilizar em vez disso, base de dados SQL para a base de dados, consulte o artigo [Nami de projeto], que também estão disponível através do Azure Marketplace.

> [AZURE.NOTE]
> Para concluir este tutorial, é necessária uma conta do Microsoft Azure. Se não tiver uma conta, pode [Ativar os benefícios da sua Visual Studio subscritor] [ activate] ou [Inscreva-se para uma avaliação gratuita][free trial].
>
> Se pretender começar com a aplicação de serviço de Azure antes de se inscrever para uma conta do Azure, aceda ao [Serviço de aplicação tente]. A partir desse local imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço — sem cartão de crédito é necessário, e não existem sem compromissos.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Localizar e criar uma aplicação Web na aplicação de serviço de Azure

1. Inicie a sessão [Portal do Azure].

1. Clique em **Novo**.
    
    ![Criar um novo recurso Azure][MarketplaceStart]
    
1. Procurar **WordPress**e, em seguida, clique em **WordPress**. (Se pretender utilizar a base de dados SQL em vez de MySQL, procure **Nami de projeto**.)

    ![Procurar WordPress no mercado][MarketplaceSearch]
    
1. Depois de ler a descrição da aplicação WordPress, clique em **Criar**.

    ![Criar WordPress web app][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configurar definições de serviço de aplicação Azure para a nova aplicação Web

1. Depois de ter criado uma nova aplicação web, o pá definições WordPress será apresentado, que irá utilizar para concluir os passos seguintes:

    ![Configurar definições do WordPress web app][ConfigStart]

1. Introduza um nome para a aplicação web na caixa **do Web app** .

    Este nome tem de ser exclusivo no domínio de azurewebsites.net porque o URL da aplicação web serão *{nome}*. azurewebsites.net. Se o nome que introduziu não exclusivo, um ponto de exclamação vermelho é apresentada na caixa de texto.

    ![Configurar o nome da aplicação web WordPress][ConfigAppName]

1. Se tiver mais do que uma subscrição, escolha aquele que pretende utilizar. 

    ![Configurar a subscrição para a aplicação web][ConfigSubscription]

1. Selecione um **Grupo de recursos** ou crie um novo.

    Para obter mais informações sobre os grupos de recursos, consulte o artigo [Descrição geral do Gestor de recursos do Azure][ResourceGroups].

    ![Configurar o grupo de recursos para a aplicação web][ConfigResourceGroup]

1. Selecione uma **Aplicação de serviço de plano/localização** ou crie um novo.

    Para mais informações sobre os planos do serviço de aplicação, consulte o artigo [Descrição geral de planos do serviço de aplicação do Azure][AzureAppServicePlans]. 

    ![Configurar o plano de serviço para a aplicação web][ConfigServicePlan]

1. Clique em **base de dados**e, em seguida, no pá a **Nova base de dados do MySQL** forneça os valores necessários para configurar a sua base de dados do MySQL.

    um. Introduza um novo nome ou deixar o nome predefinido.

    b. Deixe o **Tipo de base de dados** definido para **partilhado**.

    c. Selecione na mesma localização que aquele que escolheu para a aplicação web.

    d. Selecione uma camada comparar. **Mercúrio** - o que é gratuito com ligações mínimas e espaço em disco - está bem formatado para este tutorial.

    "e". Na pá **Nova base de dados do MySQL** , aceite os termos legais e, em seguida, clique em **OK**. 

    ![Configurar as definições de base de dados para a aplicação web][ConfigDatabase]

1. Na pá **WordPress** , aceite os termos legais e, em seguida, clique em **Criar**. 

    ![Concluir as definições de aplicação web e clique em OK][ConfigFinished]

    Aplicação de serviço de Azure cria a aplicação web, normalmente no menos de um minuto. Pode ver o progresso ao clicar no ícone de campainha na parte superior da página de portal.

    ![Indicador de progresso][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciação e gerir a sua aplicação web do WordPress
    
1. Quando tiver terminada a criação de aplicação web, navegue no Portal do Azure ao grupo de recursos em que criou a aplicação e, pode ver a aplicação web e a base de dados.

    O recurso extra com o ícone de lâmpada é [Informações de aplicação][ApplicationInsights], que fornece serviços de monitorização para a sua aplicação web.

1. No pá **grupo de recursos** , clique na linha de aplicação web.

    ![Selecione a aplicação web do WordPress][WordPressSelect]

1. Na pá de aplicação Web, clique em **Procurar**.

    ![Navegue para a aplicação web do WordPress][WordPressBrowse]

1. Se lhe for pedido para selecionar o idioma para o seu blogue WordPress, selecione o idioma pretendido e, em seguida, clique em **continuar**.

    ![Configurar o idioma para o seu WordPress web app][WordPressLanguage]

1. Na página WordPress **Bem-vindo** , introduza as informações de configuração, uma ferramenta necessária ao WordPress e, em seguida, clique em **Instalar WordPress**.

    ![Configurar as definições da aplicação web do WordPress][WordPressConfigure]

1. Inicie sessão com as credenciais que criou na página de **boas-vindas** .  

1. A página de Dashboard do site irá abrir e apresentar as informações que forneceu.    

    ![Ver o seu dashboard WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Próximos passos

Neste tutorial visualizou como criar e implementar uma aplicação web do exemplo do Azure Marketplace.

Para mais informações sobre como trabalhar com a aplicação de serviço Web Apps, consulte as ligações no lado esquerdo da página (para windows do browser e uma altura) ou na parte superior da página (para windows do browser estreita).

Para obter mais informações sobre como desenvolver WordPress web apps no Azure, consulte o artigo [Desenvolver WordPress na aplicação de serviço de Azure][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[NODE.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Experimente a aplicação de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portal do Azure]: https://portal.azure.com/
[Nami de projeto]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
