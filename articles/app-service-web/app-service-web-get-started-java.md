<properties 
    pageTitle="Implementar a aplicação web do Java primeira Azure dentro de cinco minutos | Microsoft Azure" 
    description="Saiba como é fácil executar aplicações web na aplicação de serviço ao implementar uma aplicação de exemplo. Iniciar a efetuar rapidamente desenvolvimento real e ver resultados imediatamente." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-java-web-app-to-azure-in-five-minutes"></a>Implementar a aplicação web do Java primeira Azure dentro de cinco minutos

Neste tutorial ajuda-o a implementar uma aplicação de web Java simple para a [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar a aplicação de serviço para criar aplicações web, [aplicação móvel trás extremidades](/documentation/learning-paths/appservice-mobileapps/)e [API apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Irá: 

- Crie uma aplicação web na aplicação de serviço de Azure.
- Implemente uma aplicação de Java de exemplo.
- Ver o seu código a executar o live de produção.

## <a name="prerequisites"></a>Pré-requisitos

- Obter um cliente de FTP/FTPS, tal como [FileZilla](https://filezilla-project.org/).
- Obter uma conta do Microsoft Azure. Se não tiver uma conta, pode [Inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios da sua Visual Studio subscritor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Pode [Tentar aplicação de serviço de](http://go.microsoft.com/fwlink/?LinkId=523751) sem uma conta Azure. Criar uma aplicação starter e reproduzir com o mesmo para até uma hora – sem cartão de crédito obrigatório, sem compromissos.

<a name="create"></a>
## <a name="create-a-web-app"></a>Criar uma aplicação web

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta Azure.

2. No menu à esquerda, clique em **Novo** > **Web + Mobile** > **Web App**.

    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)

3. No pá de criação de aplicação, utilize as seguintes definições para a sua nova aplicação:

    - **Nome da aplicação**: escreva um nome exclusivo.
    - **Grupo de recursos**: selecione **Criar nova** e dê um nome ao grupo de recursos.
    - **Aplicação de serviço de plano/localização**: clique na mesma para configurar, em seguida, clique em **Criar novo** para definir o nome, localização e comparar camada do plano de serviço de aplicação. Esteja à vontade utilizar **Free** preços de camadas.

    Quando terminar, seu pá de criação da aplicação deverá este aspeto:

    ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)

3. Na parte inferior, clique em **Criar** . Pode clicar no ícone de **notificação** na parte superior para ver o progresso.

    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)

4. Quando terminar de implementação, deverá ver esta mensagem de notificação. Clique na mensagem para abrir pá da sua implementação.

    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)

5. No pá **implementação com êxito** , clique na ligação de **recursos** para abrir pá da sua aplicação web novo.

    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## <a name="deploy-a-java-app-to-your-web-app"></a>Implementar uma aplicação de Java para a sua aplicação web

Agora, vamos implementar uma aplicação de Java para Azure utilizando FTPS.

5. No pá de aplicação web, desloque para baixo para **as definições da aplicação** ou procurá-lo, em seguida, clique na mesma. 

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

6. Na **versão de Java**, selecione **Java 8** e clique em **Guardar**.

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

    Quando receber a notificação de **actualizado com êxito as definições da aplicação web**, navegue até à http://*&lt;nome >*. azurewebsites.net para ver o servlet JSP predefinido em ação.

7. Novamente na pá de aplicação web, desloque para baixo para as **credenciais de implementação** ou procurá-lo, em seguida, clique na mesma.

8. Defina as suas credenciais de implementação e clique em **Guardar**.

7. Novamente na pá de aplicação web, clique em **Descrição geral**. Junto ao **nome de utilizador FTP/implementação** e **FTPS hostname**, clique no botão **Copiar** para copiar estes valores.

    ![](./media/app-service-web-get-started-languages/get-ftp-url.png)

    Agora está pronto para implementar a aplicação de Java com FTPS.

8. No seu cliente de FTP/FTPS, inicie sessão no servidor FTP da sua aplicação Azure web utilizando os valores que copiou no último passo. Utilize a palavra-passe de implementação que criou anteriormente.

    A seguinte captura de ecrã mostra o início de sessão utilizando FileZilla.

    ![](./media/app-service-web-get-started-languages/filezilla-login.png)

    Poderá ver avisos de segurança para o certificado SSL irreconhecíveis a partir do Azure. Vá para a frente e continuar.

9. Clique em [esta ligação](https://github.com/Azure-Samples/app-service-web-java-get-started/raw/master/webapps/ROOT.war) para transferir o ficheiro de guerra para o seu computador local.

9. No seu cliente de FTP/FTPS, navegue para **/site/wwwroot/webapps** no site remoto e arrastar o ficheiro transferido do guerra no seu computador local para esse directório remoto.

    ![](./media/app-service-web-get-started-languages/transfer-war-file.png)

    Clique em **OK** para substituir o ficheiro no Azure.

    >[AZURE.NOTE] Em conformidade com o comportamento de predefinido do Tomcat, nome do ficheiro **ROOT.war** /site/wwwroot/webapps dá-lhe o raiz web app (http://*&lt;nome >*. azurewebsites.net) e o nome de ficheiro ** * &lt;. anyname >*.war** dá-lhe uma aplicação web com nome (http://*&lt;nome >*.azurewebsites.net/*&lt;. anyname >*).

Já está! A aplicação Java está a ser executado em direto no Azure. No seu browser, navegue até à http://*&lt;nome >*. azurewebsites.net para visualizá-la em ação. 

## <a name="make-updates-to-your-app"></a>Fazer atualizações para a sua aplicação

Sempre que precisa de efetuar uma atualização, basta carregar o novo ficheiro de guerra para o mesmo directório remoto com o seu cliente de FTP/FTPS.

## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação web do Java a partir de um modelo no Azure Marketplace](web-sites-java-get-started.md#marketplace). Pode obter seu próprio contentor Tomcat totalmente personalizável e obter IU do Gestor de familiares. 

Depurar a aplicação Azure web, diretamente no [IntelliJ](app-service-web-debug-java-web-app-in-intellij.md) ou [Eclipse](app-service-web-debug-java-web-app-in-eclipse.md).

Em alternativa, largas à imaginação com a aplicação web do primeira. Por exemplo:

- Experimente [outras formas para implementar o seu código para Azure](../app-service-web/web-sites-deploy.md). 
- Tomar a sua aplicação Azure para o próximo nível. Autenticar os seus utilizadores. Escala com base na pedido. Configure o alguns alertas de desempenho. Tudo com alguns cliques. Consulte o artigo [Adicionar funcionalidade para a sua aplicação web primeira](app-service-web-get-started-2.md).

