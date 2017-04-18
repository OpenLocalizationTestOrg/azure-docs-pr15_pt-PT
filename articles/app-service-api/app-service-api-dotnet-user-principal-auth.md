<properties
    pageTitle="Autenticação de utilizador para as aplicações de API na aplicação de serviço de Azure | Microsoft Azure"
    description="Saiba como pode proteger uma aplicação do API na aplicação de serviço de Azure ao permitir acesso apenas aos utilizadores autenticados."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="user-authentication-for-api-apps-in-azure-app-service"></a>Autenticação de utilizador para as aplicações de API na aplicação de serviço do Azure

## <a name="overview"></a>Descrição geral

Este artigo mostra como pode proteger uma aplicação do Azure API para que apenas os utilizadores autenticados podem ligá-lo. O artigo assume que já leu um [Descrição geral do Azure aplicação de serviço de autenticação](../app-service/app-service-authentication-overview.md).

Que vai aprender:

* Como configurar um fornecedor de autenticação, com detalhes do Azure Active Directory (Azure AD).
* Como consumir uma aplicação de API protegida utilizando o [Active Directory autenticação biblioteca (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Este artigo contém duas secções:

* A secção [como configurar a autenticação de utilizador na aplicação de serviço de Azure](#authconfig) geral explica como configurar autenticação de utilizador para qualquer aplicação do API e aplica-se igualmente para todos os quadros suportados pelo serviço de aplicação, incluindo .NET, Node.js e Java.

* Começar com a secção [continuar os tutoriais .NET API aplicações](#tutorialstart) , as guias de artigo-o através da configuração da aplicação de exemplo com um .NET back-end e de fim de uma parte da frente AngularJS para que possa utilizar o Azure Active Directory para autenticação de utilizador. 

## <a id="authconfig"></a>Como configurar a autenticação de utilizador na aplicação de serviço do Azure

Esta secção fornece instruções gerais que se aplicam a qualquer aplicação do API. Para obter passos específicos para a aplicação de exemplo para o fazer de .NET de lista, vá para [continuar os tutoriais .NET introdução](#tutorialstart).

1. No [portal do Azure](https://portal.azure.com/), navegue para a pá **Definições** da aplicação API que pretende proteger, localize a secção de **funcionalidades** e, em seguida, clique em **autenticação / autorização**.

    ![Portal Azure/autorização de autenticação](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. No **autenticação / autorização** pá, **clique em**.

4. Selecione uma das opções a partir da lista pendente de **ação a tomar quando não é autenticado pedido** .

    * Se quiser que apenas as chamadas autenticadas para alcançar a sua aplicação API, escolha uma das opções de **Iniciar sessão com...** . Esta opção permite-lhe proteger a aplicação de API sem escrever qualquer código que é executada no mesmo.

    * Se quiser que todas as chamadas para alcançar a sua aplicação API, selecione **Permitir pedidos (nenhuma acção)**. Pode utilizar esta opção para direcionar não autenticados os autores das chamadas para uma variedade de fornecedores de autenticação. Com esta opção tem de escrever código para processar autorização.

    Para mais informações, consulte o artigo [autenticação e autorização para aplicações de API na aplicação de serviço de Azure](app-service-api-authentication.md#multiple-protection-options).

5. Selecione uma ou mais dos **Fornecedores de autenticação**.

    A imagem que mostra opções que requerem todos os autores de chamadas autenticados por Azure AD.
 
    ![Azure pá de autenticação/autorização de portal](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

    Quando escolher um fornecedor de autenticação, o portal apresenta uma pá configuração para esse fornecedor. 

    Para obter instruções detalhadas que explicam como configurar as lâminas de configuração do fornecedor de autenticação, consulte o artigo [como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (A ligação vai para um artigo sobre como Azure AD, mas artigo propriamente dito contém os separadores que ligação para artigos para os outros fornecedores de autenticação.)

7. Quando tiver terminado a pá de configuração do fornecedor de autenticação, clique em **OK**.

7. No **autenticação / autorização** pá, clique em **Guardar**.

Quando este estiver concluída, o serviço de aplicação autentica todas as chamadas de API antes de chegar à aplicação de API. Os serviços de autenticação funcionam da mesma para todos os idiomas que suporta a aplicação de serviço, incluindo .NET, Node.js e Java. 

Para efetuar chamadas de API autenticadas, o autor da chamada inclui 2.0 token de ligação OAuth o fornecedor de autenticação no cabeçalho da autorização de pedidos de HTTP. Pode ser adquirido o token utilizando SDK o fornecedor de autenticação.

## <a id="tutorialstart"></a>Se continuar os tutoriais .NET API aplicações

Se está a seguir os tutoriais Node.js ou Java para aplicações API, avance para o seguinte artigo, [autenticação principal para as aplicações de API do serviço](app-service-api-dotnet-service-principal-auth.md). 

Se está a seguir a série de tutoriais .NET para as aplicações de API e se já tiver implementado a aplicação de exemplo conforme mencionado nos tutoriais [primeira](app-service-api-dotnet-get-started.md) e a [segunda](app-service-api-cors-consume-javascript.md) , vá para a secção [Configurar autenticação na aplicação de serviço e Azure AD](#azureauth) .

Se pretender siga este tutorial sem ter de aceder a tutoriais primeiros e a segundo, execute os seguintes passos que explicam como começar a utilizando um processo automatizado para implementar a aplicação de exemplo.

>[AZURE.NOTE] Os passos seguintes começar ao mesmo ponto de partida como se fez os dois primeiros tutoriais, com uma exceção: Visual Studio já não têm como saber que web app ou a aplicação de API que cada projeto é implementado. Isto significa que não terá exatas instruções neste tutorial que explicam como implementar os destinos à direita. Se não estiver habituado a perceber como efetuar os passos de implementação sozinho, é melhor seguir a série de tutoriais a partir do [primeiro tutorial](app-service-api-dotnet-get-started.md) que para começar com este processo de implementação automatizada.

1. Certifique-se de que tem todos os pré-requisitos listados no [tutorial primeiro](app-service-api-dotnet-get-started.md). Para além dos pré-requisitos listados, nestes tutoriais de autenticação partem do princípio de que tem trabalhou com a aplicação de serviço web apps e apps de API no portal do Azure e no Visual Studio.

2. Clique no botão **implementar para Azure** no [ficheiro Leia-me do lista fazer exemplo repositório](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) para implementar as aplicações de API e a aplicação web. Tome nota do grupo de recursos Azure que é criado, tal como pode utilizar esta opção posterior para procurar web app e nomes de aplicação API.
 
3. Transferir ou clonar do [repositório de exemplo para fazer a lista](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) para obter o código que terá de trabalhar com localmente no Visual Studio.

## <a id="azureauth"></a>Configurar autenticação na aplicação de serviço e Azure AD

Agora tem a aplicação em execução no serviço de aplicação do Azure sem necessidade de que os utilizadores ser autenticados. Nesta secção Adicionar autenticação ao efetuar as seguintes tarefas:

* Configure o serviço de aplicação para exigir autenticação do Azure Active Directory (Azure AD) para chamar a aplicação de camada API.
* Crie uma aplicação do Azure AD.
* Configure a aplicação do Azure AD para enviar a ligação OAuth após início de sessão para o AngularJS front-end. 

Se ocorrer problemas ao seguir as instruções de iniciação, consulte a secção de [resolução de problemas](#troubleshooting) no fim do tutorial. 
 
### <a name="configure-authentication-for-the-middle-tier-api-app"></a>Configurar a autenticação para a aplicação de API camada

1. No [portal do Azure](https://portal.azure.com/), navegue para a pá **Definições** da aplicação API que criou para o projeto ToDoListAPI, localize a secção de **funcionalidades** e, em seguida, clique em **autenticação / autorização**.

    ![Portal Azure/autorização de autenticação](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. No **autenticação / autorização** pá, **clique em**.

4. Na lista pendente de **ação a tomar quando não é autenticado pedido** , selecione **Iniciar sessão com o Azure Active Directory**.

    Esta opção garante que não existem pedidos unauathenticated irão chegar a aplicação de API. 

5. Em **Fornecedores de autenticação**, clique em **Azure Active Directory**.

    ![Azure pá de autenticação/autorização de portal](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. No pá **Definições do Azure Active Directory** , clique em **Express**

    ![Azure opção portal de autenticação/autorização Express](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

    Com a opção **Express** aplicação de serviço automaticamente pode criar uma aplicação do Azure AD no seu [inquilino](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)do Azure AD. 

    Não tem de criar um inquilino, uma vez que cada conta Azure automaticamente tem uma.

7. Em **modo de gestão**, clique em **Criar nova aplicação de AD** se ainda não estiver selecionada e repare o valor que se encontra na caixa de texto **Criar aplicação** ; irá procurar esta aplicação AAD no portal do Azure clássica mais tarde.

    ![Definições do portal Azure Azure AD](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

    Azure irá criar uma aplicação do Azure AD automaticamente com o nome indicado no inquilino do Azure AD. Por predefinição, a aplicação do Azure AD chama-se o mesmo que a aplicação de API. Se preferir, pode introduzir um nome diferente.
 
7. Clique em **OK**.

7. No **autenticação / autorização** pá, clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

Agora, apenas os utilizadores no seu inquilino do Azure AD podem ligar a aplicação de API.

### <a name="optional-test-the-api-app"></a>Opcional: Teste a aplicação de API

1. Num browser, aceda ao URL da aplicação API: na pá de **aplicação API** no portal do Azure, clique na ligação em **URL**.  

    É redirecionado para um ecrã de início de sessão porque não autenticados pedidos não são permitidos para alcançar a aplicação de API.

    Se o seu browser acede à página do "foi criada com sucesso", o browser poderá já ter sessão iniciado no – nesse caso, abra uma janela InPrivate ou janela e vá para URL a aplicação de API.

2. Inicie sessão com as credenciais para um utilizador no inquilino do Azure AD.

    Quando tem sessão iniciada, a página "criada com êxito" é apresentado no browser.

9. Feche o browser.

### <a name="configure-the-azure-ad-application"></a>Configurar a aplicação do Azure AD

Quando tiver configurado a autenticação do Azure AD, o serviço de aplicação criado uma aplicação do Azure AD para si. Por predefinição, o Azure AD nova aplicação foi configurada para fornecer a ligação OAuth para URL a aplicação de API. Nesta secção Configurar a aplicação do Azure AD para fornecer a ligação OAuth ao AngularJS front-end em vez de diretamente para a aplicação de camada API. O AngularJS front-end irá enviar o token de para a aplicação de API quando liga-lo para a aplicação de API.

>[AZURE.NOTE] Para manter o processo como simples possível, este tutorial utiliza uma única Azure AD aplicação para o front-end e meio camada API aplicação. Outra opção é utilizar as aplicações do Azure AD duas. Nesse caso teria que conceder a permissão de aplicação o front-end Azure AD para chamar Azure AD aplicação a camada. Esta abordagem de múltiplas aplicações seria dar-lhe controlo mais granular sobre permissões para cada camada.

11. No [portal clássica Azure](https://manage.windowsazure.com/), aceda a **Azure Active Directory**.

    Tem de utilizar o portal do clássico porque determinadas que precisa de aceder às definições do Azure Active Directory ainda não estão disponíveis no portal do Azure atual.

12. No separador do **diretório** , clique em seu inquilino AAD.

    ![Azure AD no portal clássico](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Clique em **aplicações > aplicações proprietário da minha empresa**e, em seguida, clique na marca de verificação.

    Também poderá ter de atualizar a página para ver a nova aplicação.

15. Na lista de aplicações, clique no nome do que Azure criado por si quando activou autenticação para a sua aplicação API.

    ![Separador do Azure AD aplicações](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Clique em **Configurar**.

    ![Separador do Azure AD configurar](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Configurar o **início de sessão do URL** para o URL para a sua aplicação web do AngularJS, sem barra final.

    Por exemplo: https://todolistangular.azurewebsites.net

    ![URL de início de sessão](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Configurar o **URL de resposta** para o URL para a sua aplicação web, sem barra final.

    Por exemplo: https://todolistsangular.azurewebsites.net

16. Clique em **Guardar**.

    ![URL de resposta](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. Na parte inferior da página, clique em **manifesto gerir > Transferir manifesto**.

    ![Transferir manifesto](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Transferir o ficheiro para uma localização onde pode editar.

16. No ficheiro transferido manifesto, procure o `oauth2AllowImplicitFlow` propriedade. Altere o valor da propriedade a partir de `false` para `true`e, em seguida, guarde o ficheiro.

    Esta definição é necessária para aceder a partir de uma aplicação de página única JavaScript. Permite-o de ligação OAuth Oauth 2.0 ser devolvido no fragmento de URL.

16. Clique em **manifesto gerir > carregar manifesto**e carregue o ficheiro que atualizou no passo anterior.

    ![Carregar manifesto](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)

17. Copie o valor de **ID de cliente** e guardá-lo algures que pode obtê-lo a partir de mais tarde.

## <a name="configure-the-todolistangular-project-to-use-authentication"></a>Configurar o projecto ToDoListAngular utilize autenticação

Nesta secção alterar o AngularJS front-end para que possa utilizar o Active Directory autenticação biblioteca (ADAL) para JS para adquirir uma ligação OAuth para o utilizador tem sessão iniciada na partir do Azure AD. O código irá incluir o token nos pedidos HTTP enviados para a camada, conforme mostrado no seguinte diagrama. 

![Diagrama de autenticação de utilizador](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Efetue as seguintes alterações a ficheiros do projeto ToDoListAngular.

1. Abra o ficheiro *Index* .

2. Remova os comentários as linhas que fazem referência a Active Directory autenticação biblioteca (ADAL) scripts JS.

        <script src="app/scripts/adal.js"></script>
        <script src="app/scripts/adal-angular.js"></script>

1. Abra o ficheiro *app/scripts/app.js* .

2. Comentar o bloco de código marcado para "sem autenticação" e remova os comentários o bloco de código marcado para "com autenticação".

    Esta alteração referencia o fornecedor de autenticação ADAL JS e fornece os valores de configuração para o mesmo. Os passos seguintes define os valores de configuração do seu aplicação API e a aplicação do Azure AD.

8. O código que define o `endpoints` variável, definir o URL de API para o URL da aplicação API criados para o projeto ToDoListAPI e configurar o ID da aplicação Azure AD para o ID do cliente que copiou do portal do Azure clássico.

    O código agora é semelhante ao seguinte exemplo.

        var endpoints = {
            "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };

9. Na chamada para `adalProvider.init`, defina `tenant` para o seu nome de inquilino e `clientId` para o mesmo valor que utilizou no passo anterior.

    O código agora é semelhante ao seguinte exemplo.

        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );

    Estas alterações para `app.js` especificar que o código de chamada e a API denominada são na aplicação do Azure AD mesmo.

1. Abra o ficheiro *app/scripts/homeCtrl.js* .

2. Comentar o bloco de código marcado para "sem autenticação" e remova os comentários o bloco de código marcado para "com autenticação".

1. Abra o ficheiro *app/scripts/indexCtrl.js* .

2. Comentar o bloco de código marcado para "sem autenticação" e remova os comentários o bloco de código marcado para "com autenticação".

### <a name="deploy-the-todolistangular-project-to-azure"></a>Implementar o projeto ToDoListAngular Azure

8. No **Explorador de soluções**, com o botão direito do projecto ToDoListAngular e, em seguida, clique em **Publicar**.

9. Clique em **Publicar**.

    Visual Studio implementa o projecto e é aberta num browser para URL base do web app. Isto vai mostrar uma página de 403 erro, que é normal para uma tentativa Ir para um URL de base da Web API a partir de um browser.

    Ainda tem de efetuar uma alteração à aplicação de API camada antes de testar a aplicação.

10. Feche o browser.

## <a name="configure-the-todolistapi-project-to-use-authentication"></a>Configurar o projecto ToDoListAPI utilize autenticação

Atualmente o projeto ToDoListAPI envia "*" como o `owner` valor a ToDoListDataAPI. Nesta secção altere o código para enviar o ID do utilizador com sessão iniciada.

Efetue as seguintes alterações no projeto ToDoListAPI.

1. Abra o ficheiro *Controllers/ToDoListController.cs* e remova os comentários da linha em cada método de ação que define `owner` para o Azure AD `NameIdentifier` reclamar valor. Por exemplo:

        owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

    **Importante**: não remova os comentários código na `ToDoListDataAPI` método; fazê-lo irá mais tarde para o tutorial de autenticação principal de serviço.

### <a name="deploy-the-todolistapi-project-to-azure"></a>Implementar o projeto ToDoListAPI Azure

8. No **Explorador de soluções**, com o botão direito do projecto ToDoListAPI e, em seguida, clique em **Publicar**.

9. Clique em **Publicar**.

    Visual Studio implementa o projecto e é aberta num browser para base URL a aplicação de API.

10. Feche o browser.

### <a name="test-the-application"></a>Testar a aplicação

9. Aceda ao URL da aplicação web, **utilizando HTTPS, não HTTP**.

8. Clique no separador **Lista de tarefas** .

    Lhe for pedido para iniciar sessão.

9. Inicie sessão com as credenciais de um utilizador no seu inquilino AAD.

10. É apresentada a página de **Lista de tarefas** .

    ![Para a página de lista](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

    Não existem itens de acções a fazer são apresentadas, porque até agora todos foram para proprietário "*". Agora a camada está a pedir itens para o utilizador tem sessão iniciada no e nenhum ter sido criado ainda.

11. Adicione novos itens de acções a fazer para verificar se a aplicação está a funcionar.

12. Na outra janela do browser, vá para o URL de IU Swagger para a aplicação ToDoListDataAPI API e clique em **fazer > obter**. Introduza um asterisco para o `owner` parâmetro e, em seguida, clique em **experimentar saída**.

    A resposta mostra que os novos itens de acções a fazer têm o valor real ID de utilizador do Azure AD na propriedade do proprietário.

    ![ID do proprietário em resposta JSON](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## <a name="building-the-projects-from-scratch"></a>Criar projetos de raiz

Os dois projetos da Web API foram criados utilizando o modelo de projeto de **Aplicação de API do Azure** e substituir o controlador de valores predefinidos com um controlador de fazer. 

Para obter informações sobre como criar uma aplicação de página única AngularJS com um back-end da Web API 2, consulte o artigo [mãos no laboratório: criar uma aplicação de página única (SPA) com a API de Web do ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obter informações sobre como adicionar o código de autenticação do Azure AD, consulte os seguintes recursos:

* [Proteger AngularJS única página aplicações com o Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introdução ao ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## <a name="troubleshooting"></a>Resolução de problemas

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Certifique-se de que não confunda ToDoListAPI (camada) e ToDoListDataAPI (camada de dados). Por exemplo, certifique-se de que adicionou a autenticação para a aplicação de camada API, não a camada de dados. 
* Certifique-se de que o código de origem AngularJS referencia o URL da aplicação de camada API (ToDoListAPI, não ToDoListDataAPI) e o Azure correto ID de cliente do AD. 

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como utilizar a aplicação de serviço autenticação para uma aplicação de API e como ligar a aplicação de API utilizando a biblioteca de ADAL JS. O próximo tutorial saberá como [acesso seguro para a sua aplicação de API para cenários de serviço de serviço](app-service-api-dotnet-service-principal-auth.md).

