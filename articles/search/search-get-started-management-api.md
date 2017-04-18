<properties 
    pageTitle="Introdução ao Azure pesquisa gestão REST API | Microsoft Azure | Serviço de pesquisa alojado na nuvem" 
    description="Administrar a serviço de pesquisa do Azure utilizando uma gestão REST API de nuvem alojada" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="get-started-with-azure-search-management-rest-api"></a>Introdução ao Azure pesquisa gestão REST API
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST API](search-get-started-management-api.md)

A API de gestão do resto do Azure pesquisa é uma alternativa programação para desempenhar tarefas administrativas no portal. Operações de gestão de serviço incluem criar ou eliminar o serviço, o serviço de dimensionamento e gestão de teclas. Neste tutorial vem com uma aplicação de cliente de exemplo que mostra a mudança a API de gestão de serviço. Também inclui passos de configuração necessários para executar o exemplo no seu ambiente de desenvolvimento local.

Para concluir este tutorial, terá de:

- Visual Studio 2012http ou 2013
- a transferência de aplicação de cliente de exemplo

Durante a concluir o tutorial, serão aprovisionados dois serviços: Procurar do Azure e Azure Active Directory (AD). Para além disso, irá criar uma aplicação de AD estabelece fidedignidade entre a aplicação de cliente e o ponto final de Gestor de recursos no Azure.

Terá uma conta Azure para concluir este tutorial.


##<a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Neste tutorial baseia-se uma aplicação de consola do Windows escrita c#, que pode editar e executar no Visual Studio 2012http ou 2013

Pode encontrar a aplicação de cliente no Github no [Azure pesquisa .NET gestão API demonstração](https://github.com/Azure-Samples/search-dotnet-management-api/).


##<a name="configure-the-application"></a>Configurar a aplicação

Antes de poder executar a aplicação de exemplo, tem de ativar a autenticação para que podem ser aceites pedidos enviados a partir da aplicação de cliente para o ponto final de Gestor de recursos. O requisito de autenticação é criado com o [Gestor de recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx), que é a base para todas as operações relacionadas com o portal pedida através de uma API, incluindo as relacionadas com a gestão de serviço de pesquisa. A API de gestão de serviço para a pesquisa do Azure é simplesmente uma extensão do Gestor de recursos do Azure e assim herda as respectivas dependências.  

Gestor de recursos do Azure requer o serviço do Azure Active Directory o seu fornecedor de identidade. 

Para obter um token de acesso que irá permitir os pedidos de contactar o Gestor de recursos, a aplicação de cliente inclui um segmento de código que chamadas do Active Directory. O segmento de código, bem como os pré-requisitos passos para utilizar o segmento de código, foram emprestados partir deste artigo: [pedidos de Gestor de recursos do Azure autenticar](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Pode siga as instruções na ligação acima, ou utilize os passos neste documento se preferir até o tutorial passo a passo.

Nesta secção, irá efetuar as seguintes tarefas:

1. Criar um serviço de AD
1. Criar uma aplicação de AD
1. Configurar a aplicação de AD por se ter registado detalhes sobre a aplicação de cliente de exemplo que transferiu
1. Carregar a aplicação de cliente de exemplo com valores que irá utilizar para ganhar autorização para os pedidos de

> [AZURE.NOTE] Estas ligações fornecem fundo sobre a utilização do Azure Active Directory para autenticar os pedidos de cliente para o Gestor de recursos: [Gestor de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), [pedidos de autenticação de Gestor de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx)e [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###<a name="create-an-active-directory-service"></a>Criar um serviço do Active Directory

1. Inicie sessão no [Portal do Azure](https://manage.windowsazure.com).

2. Desloque-se para baixo no painel de navegação do lado esquerdo e clique em **Do Active Directory**.

4. Clique em **Novo** para abrir os **Serviços de aplicação** | **Do Active Directory**. Neste passo, está a criar um novo serviço do Active Directory. Este serviço irá alojar a aplicação de AD que irá definir alguns passos a partir de agora. Criar um novo serviço ajuda-o a isolar o tutorial de outras aplicações que pode ser alojamento no Azure.

5. Clique **no diretório** | **Criar personalizado**.

6. Introduza um nome de serviço, o domínio e a localização de geo. O domínio tem de ser exclusivo. Clique na marca de verificação para criar o serviço.

     ![][5]

###<a name="create-a-new-ad-application-for-this-service"></a>Criar uma nova aplicação de AD para este serviço

1. Selecione o serviço do Active Directory "SearchTutorial" que acabou de criar.

2. No menu superior, clique em **aplicações**. 
 
3. Clique em **Adicionar uma aplicação**. Uma aplicação do AD armazena informações sobre as aplicações de cliente vai ser utilizá-lo como um fornecedor de identidades.  
 
4. Selecione **Adicionar uma aplicação que está a desenvolver a minha organização**. Esta opção fornece as definições de registo para as aplicações que não são publicadas para a Galeria de aplicação. Uma vez que a aplicação de cliente não fizer parte da Galeria de aplicação, esta é a escolha correta para este tutorial.

     ![][6]
 
5. Introduza um nome, tal como "Gestor de pesquisa de Azure".

6. Selecione **aplicação cliente nativo** do tipo de aplicação. Este é correta para a aplicação de exemplo Para isto acontecer ser uma aplicação de cliente (consola) do Windows, não uma aplicação web.

     ![][7]
 
7. Na URI de redirecionar, introduza "http://localhost/Azure-Search-Manager-App". Isto um URI para o qual Azure Active Directory irá redirecioná-user-agent em resposta a um pedido de autorização OAuth 2.0. O valor não tem de ser um ponto final físico, mas tem de ser um URI válido. 

    Para efeitos neste tutorial, o valor pode ser qualquer coisa, mas que introduzir torna-se uma entrada necessária para a ligação administrativa na aplicação de exemplo. 
 
7. Clique na marca de verificação para criar a aplicação do Active Directory. Deverá visualizar "Azure-pesquisa-Gestor de aplicação" no painel de navegação à esquerda.

###<a name="configure-the-ad-application"></a>Configurar a aplicação de AD
 
9. Clique na aplicação de AD "Azure-pesquisa-Gestor de aplicação", que acabou de criar. Deverá ver o que mesmo listado no painel de navegação à esquerda.

10. No menu superior, clique em **Configurar** .
 
11. Desloque para baixo para as permissões e selecione **API de gestão do Azure**. Neste passo, de API (neste caso, a API do Gestor de recursos do Azure) especificar que a aplicação de cliente necessita de aceder, juntamente com o nível de acesso que necessita.

12. Em permissões de delegado, clique em pendente lista e selecione **Gestão de serviços do Access Azure (pré-visualização**).
 
     ![][8]
 
13. Guarde as alterações. 

Mantenha a página de configuração da aplicação aberta. No próximo passo será copie os valores a partir desta página e introduzi-las na aplicação de exemplo.

###<a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>Carregar o programa de aplicação de exemplo com valores de registo e subscrição

Nesta secção, irá editar a solução no Visual Studio, substituindo valores válidos obtidos a partir do portal.
Os valores que irá adicionar aparecem na parte superior do Program.cs:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Se ainda não tiver [transferido a aplicação de exemplo a partir do Github](https://github.com/Azure-Samples/search-dotnet-management-api/), terá-lo para este passo.

1. Abra o **ManagementAPI.sln** no Visual Studio.

2. Abra Program.cs.

3. Fornecer `ClientId`. A partir da aplicação de AD configuração de página à esquerda abrir a partir do passo anterior, copie o ID de cliente da página de configuração da aplicação de AD no portal do e colá-la Program.cs.

4. Fornecer `RedirectUrl`. Copie redirecionar URI da mesma página de portal e colá-la Program.cs.

    ![][9]

5. Fornecer`TenantID.` 
    - Vá para o Active Directory | SearchTutorial (serviço). 
    - Clique em **aplicações** a partir da barra superior. 
    - Clique em **Pontos finais de vista** em parte inferior da página. 
    - Copie o ponto final autorização de 2.0 OAUTH na parte inferior da lista. 
    - Cole o ponto final TenantID, limitar o valor de todos os parâmetros URI exceto o ID do inquilino.

    Dada "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0", elimine tudo exceto "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

    ![][10]

6. Fornecer `SubscriptionID`.
    - Aceda à página principal do portal.
    - Clique em **Definições** na parte inferior do painel de navegação à esquerda.
    - No separador de subscrições, copie o ID da subscrição e colá-la Program.cs.

7. Guardar e, em seguida, crie a solução.


##<a name="explore-the-application"></a>Explorar a aplicação

Adicione um ponto de interrupção na primeira chamada método para que pode passo através do programa. Prima **F5** para executar a aplicação e, em seguida, prima **F11** para se deslocar pelo código.

A aplicação de exemplo cria um serviço gratuito da pesquisa do Azure para uma subscrição existente do Azure. Se já existe um serviço gratuito para a sua subscrição, a aplicação de exemplo irá falhar. Apenas um serviço gratuito da procura por subscrição é permitido.

1. Abrir Program.cs a partir do Explorador de solução e vá para a função de principais ([cadeia void). 
 
3. Repare que **ExecuteArmRequest** é utilizado para executar pedidos contra o ponto final de Gestor de recursos do Azure, `https://management.azure.com/subscriptions` para um especificado `subscriptionID`. Este método é utilizado em todo o programa para efetuar operações utilizando a API do Gestor de recursos do Azure ou API de gestão de pesquisa.

3. Pedidos para o Gestor de recursos do Azure tem de ser autenticados e autorizados. Isto é feito utilizando o método de **GetAuthorizationHeader** designado pelo método **ExecuteArmRequest** , emprestado a partir do [Gestor de recursos do Azure autenticar pedidos](http://msdn.microsoft.com/library/azure/dn790557.aspx). Repare que **GetAuthorizationHeader** chamadas `https://management.core.windows.net` para obter um token de acesso.

4. Lhe for pedido para iniciar sessão com um nome de utilizador e palavra-passe que seja válido para a sua subscrição.

5. Em seguida, um novo serviço de pesquisa do Azure está registado com o fornecedor de Gestor de recursos do Azure. Novamente, este é o método de **ExecuteArmRequest** , utilizado este período de tempo para criar o serviço de pesquisa no Azure para a sua subscrição através do `providers/Microsoft.Search/register`. 

6. O resto do programa utiliza a [Azure pesquisa gestão REST API](http://msdn.microsoft.com/library/dn832684.aspx). Repare que o `api-version` para esta API é a diferença entre a versão de api do Gestor de recursos do Azure. Por exemplo, `/listAdminKeys?api-version=2014-07-31-Preview` mostra o `api-version` da API resto de gestão de pesquisa do Azure.

    A seguinte série de operações de obter a definição do serviço que acabou de criar, as teclas de api do administrador, gera novamente e obtém teclas, altera o réplica e a partições e por fim, elimina o serviço.

    Quando alterar a contagem de réplica ou partição de serviço, é esperado que esta ação irá falhar se estiver a utilizar a edição gratuita. Pode tornar à standard edition utilização das partições adicionais e réplicas.

    Eliminar o serviço é a última operação.

##<a name="next-steps"></a>Próximos passos

Após ter concluído neste tutorial, poderá pretender obter mais informações sobre a gestão de serviços ou a autenticação do serviço do Active Directory:

- Saiba mais acerca da integração de uma aplicação de cliente com o Active Directory. Consulte o artigo [integrar aplicações no Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Saiba mais sobre outras operações de gestão de serviço no Azure. Consulte o artigo [Gerir os seus serviços](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 
