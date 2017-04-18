<properties
    pageTitle="Criar uma aplicação web do Java na aplicação de serviço de Azure | Microsoft Azure"
    description="Este tutorial mostra-lhe como implementar uma aplicação web do Java a aplicação de serviço de Azure."
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
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Criar uma aplicação web do Java no serviço de aplicação do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar uma Java [web app no serviço de aplicação do Azure] utilizando o [Portal do Azure]. O Portal do Azure é uma interface de web que pode utilizar para gerir os recursos do Azure.

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta do Microsoft Azure. Se não tiver uma conta, pode [Ativar os benefícios da sua Visual Studio subscritor] ou [Inscrever-se para uma avaliação gratuita].
>
> Se pretender começar com a aplicação de serviço de Azure antes de se inscrever para uma conta do Azure, aceda ao [Serviço de aplicação tente]. Não existem, imediatamente pode criar uma aplicação web do starter curto no serviço de aplicação; cartão de crédito não é necessário e não compromissos.

## <a name="java-application-options"></a>Opções da aplicação Java

Existem várias formas, que pode configurar uma aplicação de Java numa aplicação web do serviço de aplicação. 

1. Criar uma aplicação e, em seguida, configure **as definições da aplicação**.

    Aplicação de serviço fornece várias versões de Tomcat e cais, com configuração predefinida. Se a aplicação que vai ser alojamento irá trabalhar com uma das versões incorporadas, este método de configuração de um contentor web é o mais fácil e é perfeito quando tudo o que pretende fazer é carregar um ficheiro de guerra para um contentor web. Para este método, criar uma aplicação no Portal do Azure e, em seguida, aceda a pá as **definições da aplicação** para a sua aplicação escolher a sua versão do Java juntamente com o contentor de web Java pretendido. Quando utilizar este método Java e o contentor web são executados a partir de ficheiros do programa. Os outros métodos de colocar o contentor de web e potencialmente a JVM no seu espaço de disco. Quando utilizar este modelo, não tem acesso ao editar ficheiros nesta peça do sistema de ficheiros. Isto significa que não pode fazer coisas como configurar o ficheiro *server.xml* ou coloque ficheiros da biblioteca na pasta */lib* . Para obter mais informações, consulte o artigo o [criar e configurar uma aplicação web do Java](#appsettings) secção neste tutorial, mais adiante.
    
2. Utilize um modelo do Azure Marketplace.

    Azure Marketplace inclui modelos que criar e configurar aplicações web do Java com Tomcat ou cais contentores de web automaticamente. Os contentores de web que crie os modelos são configuráveis. Para mais informações, consulte a secção [utilizar um modelo de Java do Azure Marketplace](#marketplace) deste tutorial.
  
3. Criar uma aplicação e, em seguida, copie manualmente e editar ficheiros de configuração 

    Poderá pretender alojar uma aplicação Java personalizada que não implementar em qualquer um dos contentores fornecidos pela aplicação de serviço web. Por exemplo:
    
    * A aplicação de Java necessita de uma versão de Tomcat ou cais que não não suportado pelo serviço de aplicação diretamente ou fornecidos na galeria.
    * A aplicação de Java assume pedidos de HTTP e implementar não como uma guerra para um contentor web pré-existentes.
    * Pretende configurar o contentor web de raiz si mesmo. 
    * Pretende utilizar uma versão do Java que não é suportada na aplicação de serviço e pretende carregar o seu próprio.

    Casos como estes, pode criar uma aplicação do utilizando o Portal do Azure e, em seguida, forneça os ficheiros de runtime adequado manualmente. Neste caso, os ficheiros vão ser contados contra as quotas de espaço de armazenamento para o seu plano de serviço de aplicação. Para mais informações, consulte o artigo [carregar uma aplicação web do Java personalizada para Azure].

## <a name="portal"></a>Criar e configurar uma aplicação web do Java

Esta secção mostra como criar uma aplicação web e configure-o para Java utilizando o pá **definições da aplicação** do portal.

1. Inicie sessão no [Portal do Azure].

2. Clique em **Novo > Web + Mobile > Web App**.

    ![Nova aplicação Web][newwebapp]

4. Introduza um nome para a aplicação web na caixa **do Web app** .

    Este nome tem de ser exclusivo no domínio de azurewebsites.net porque o URL da aplicação web serão {nome}. azurewebsites.net. Se o nome que introduziu não exclusivo, um ponto de exclamação vermelho é apresentada na caixa de texto.

5. Selecione um **Grupo de recursos** ou crie um novo.

    Para mais informações sobre os grupos de recursos, consulte o artigo [utilizar o Portal do Azure para gerir os seus recursos Azure].

6. Selecione uma **Aplicação de serviço de plano/localização** ou crie um novo.

    Para mais informações sobre os planos do serviço de aplicação, consulte o artigo [Descrição geral de planos do serviço de aplicação do Azure].

7. Clique em **Criar**.

    ![Criar Web App][newwebapp2]
 
8. Quando a aplicação web foi criada, clique em **Web Apps > {a aplicação web do}**.
 
    ![Selecione Web App][selectwebapp]

9. No pá **Web app** , clique em **Definições**.

10. Clique em **definições da aplicação**.

11. Escolha a **versão Java**pretendida. 

12. Escolha a **versão secundária Java**pretendida. Se selecionar **mais recente**, a sua aplicação irá utilizar a versão secundária mais recente disponível na aplicação de serviço para essa versão principal Java. O item **mais recente** é exclusivo às aplicações Java criadas a partir das **definições da aplicação**. Se criar a sua aplicação Java a partir da galeria, em seguida, tem de gerir o seu próprio contentor e JVM alterações. 

12. Selecione o pretendido **contentor Web**. Se selecionar um nome de contentor que começa com **mais recente**, será mantida a aplicação a versão mais recente do que versão principal contentor web que está disponível na aplicação de serviço. 

    ![Versões de contentor Web][versions]

13. Clique em **Guardar**.

    Dentro de alguns minutos, a aplicação web do irão tornar-se com base em Java e configurado para utilizar o contentor de web que selecionou.

14. Clique em **Web aplicações > {a nova aplicação web do}**.

15. Clique no **URL** para navegar para o novo site.

    A página web confirmar que criou uma aplicação web baseada em Java.

## <a name="marketplace"></a>Utilizar um modelo de Java do Azure Marketplace

Esta secção mostra como utilizar o Azure Marketplace para criar uma aplicação web do Java. O mesmo fluxo geral pode também ser utilizado para criar um com base em Java mobile ou API aplicação. 

1. Inicie sessão no [Portal do Azure]

2. Clique em **Novo > Marketplace**.

    ![Novo Marketplace][newmarketplace]

3. Clique em **Web + Mobile**.

    Poderá ter de deslocar da esquerda para ver o pá **Marketplace** onde pode selecionar **Web + Mobile**.

4. Na caixa de texto de pesquisa, introduza o nome de um servidor de aplicação Java, tal como **Apache Tomcat** ou **cais**e, em seguida, prima Enter.

5. Nos resultados da pesquisa, clique no servidor de aplicação Java.

    ![Cais móvel da Web][webmobilejetty]

6. Na primeira pá **Apache Tomcat** ou **cais** , clique em **Criar**.

    ![Pá Portal marinha][jettyblade]

7. Na pá **Apache Tomcat** ou **cais** seguinte, introduza um nome para a aplicação web na caixa **do Web app** .

    Este nome tem de ser exclusivo no domínio de azurewebsites.net porque o URL da aplicação web serão {nome}. azurewebsites.net. Se o nome que introduziu não exclusivo, um ponto de exclamação vermelho é apresentada na caixa de texto.

8. Selecione um **Grupo de recursos** ou crie um novo.

    Para mais informações sobre os grupos de recursos, consulte o artigo [utilizar o Portal do Azure para gerir os seus recursos Azure].

9. Selecione uma **Aplicação de serviço de plano/localização** ou crie um novo.

    Para mais informações sobre os planos do serviço de aplicação, consulte o artigo [Descrição geral de planos do serviço de aplicação do Azure].

10. Clique em **Criar**.

    ![Criar Portal marinha][jettyportalcreate2]

    Uma hora abreviada, normalmente menores do que um minuto Azure acaba de criar a nova aplicação web.

11. Clique em **Web aplicações > {a nova aplicação web do}**.

12. Clique no **URL** para navegar para o novo site.

    ![URL marinha][jettyurl]

    Tomcat é fornecido com um conjunto predefinido de páginas; Se tiver optado por Tomcat, verá uma página semelhante ao exemplo seguinte.

    ![Web app através de Apache Tomcat][tomcat]

    Se tiver optado por cais, verá uma página semelhante ao exemplo seguinte. Cais não têm um conjunto de página predefinido, pelo que a mesma JSP que é utilizado para um site de Java vazio é reutilizada aqui.

    ![Web app através de cais][jetty]

Agora que criou a aplicação web com um contentor de aplicação, consulte a secção de [passos seguintes](#next-steps) para obter informações sobre como carregar a sua aplicação para a aplicação web.

## <a name="next-steps"></a>Próximos passos

Neste momento, se tiver um servidor de aplicação de Java a ser executada em sua aplicação web na aplicação de serviço de Azure. Para implementar o seu próprio código para a aplicação web, consulte o artigo [Adicionar uma aplicação ou página Web para a sua aplicação web do Java].

Para mais informações sobre como desenvolver aplicações Java no Azure, consulte o [Centro de programadores do Java].

<!-- URL List -->

[Adicionar uma aplicação ou página Web para a sua aplicação web do Java]: ./web-sites-java-add-app.md
[Descrição geral de planos do Azure de aplicação de serviço]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portal do Azure]: https://portal.azure.com/
[Ativar os benefícios da sua subscritor Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[inscrever-se para uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Experimente a aplicação de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751
[Web app no serviço de aplicação do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro de programadores do Java]: /develop/java/
[Utilizar o Portal do Azure para gerir os recursos do Azure]: ../azure-portal/resource-group-portal.md
[Carregar uma aplicação web do Java personalizada para Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
