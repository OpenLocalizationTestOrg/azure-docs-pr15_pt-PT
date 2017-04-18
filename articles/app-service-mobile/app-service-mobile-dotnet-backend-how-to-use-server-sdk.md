<properties
    pageTitle="Como trabalhar com o servidor de back-end .NET SDK para aplicações Mobile | Azure de aplicação de serviço"
    description="Saiba como trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure aplicação serviço."
    keywords="aplicação de serviço, azure aplicação de serviço, aplicação móvel, serviço móvel, implementação de aplicações de implementação, azure escala, dimensionáveis, aplicação"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tópico mostra-lhe como utilizar o servidor de back-end .NET SDK em cenários chaves do Azure aplicação Serviço móvel aplicações. O SDK do Azure Mobile aplicações ajuda-o a trabalhar com os clientes móveis da aplicação de ASP.NET.

>[AZURE.TIP] O [servidor de .NET SDK para as aplicações móveis do Azure] [ 2] é abrir origem no GitHub. O repositório de contém todos os códigos de fonte, incluindo o conjunto de teste de unidade SDK todo o servidor e alguns projectos de exemplo.

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o servidor SDK se encontra aqui: [Azure Mobile aplicações .NET referência][1].

## <a name="create-app"></a>Como: criar um aplicação do .NET Mobile do back-end

Se estiver a iniciar um novo projeto, pode criar uma aplicação de serviço de aplicação utilizando o [Azure portal] ou Visual Studio. Pode executar a aplicação de serviço de aplicação localmente ou publique o projeto para a sua aplicação móvel de aplicação de serviço baseado na nuvem.  

Se estiver a adicionar funcionalidades de móveis a um projeto existente, consulte a secção de [Transferir e iniciar o SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Criar um .NET back-end, utilizando o portal Azure

Para criar um back-end móvel do serviço de aplicação, quer siga o [Guia de introdução tutorial] [ 3] ou siga estes passos:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Novamente na pá _começar_ , em **criar uma tabela API**, selecione **c#** como o **idioma de back-end**. Clique em **Transferir**, extrair os ficheiros de projeto comprimido o computador local e abrir a solução no Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Criar um .NET back-end, utilizar o Visual Studio 2013 e o Visual Studio 2015

Instalar o [SDK do Azure para .NET] [ 4] (versão 2.9.0 ou posterior) para criar um projeto de aplicações do Azure Mobile no Visual Studio. Assim que tiver instalado o SDK, crie uma aplicação do ASP.NET através dos seguintes passos:

1. Abrir a caixa de diálogo **Novo projeto** (a partir de *ficheiro* > **Novo** > **projecto...**).
2. Expanda **modelos** > **Visual c#**e selecione **Web**.
3. Selecione a **aplicação Web do ASP.NET**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Em _ASP.NET 4.5.2 modelos_, selecione a **Aplicação do Azure Mobile**. Verifique o **anfitrião na nuvem** para criar um back-end móvel na nuvem para o qual pode publicar este projeto.
6. Clique em **OK**.

## <a name="install-sdk"></a>Como: transferir e iniciar o SDK

O SDK está disponível no [NuGet.org]. Este pacote inclui a funcionalidade base necessária para começar a utilizar o SDK. Para iniciar o SDK, tem de efetuar ações no objeto **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalar o SDK

Para instalar o SDK, com o botão direito no projeto server no Visual Studio, selecione **Gerir pacotes de NuGet**, procurar o pacote de [Microsoft.Azure.Mobile.Server] , em seguida, clique em **instalar**.

###<a name="server-project-setup"></a>Iniciar o project server

Um projeto de servidor de back-end .NET está inicializado semelhante a outros projetos ASP.NET, incluindo uma classe de arranque OWIN. Certifique-se de que tem referenciados o pacote de NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar esta classe no Visual Studio, o botão direito do rato no seu projeto do servidor e selecione **Adicionar** > 
**Novo Item**, em seguida, **Web** > **gerais** > **classe de arranque OWIN**.  Uma classe é gerada com o atributo seguinte:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

No `Configuration()` método da sua aula de arranque OWIN, utilizar um objeto de **HttpConfiguration** para configurar o ambiente de aplicações do Azure Mobile.
O exemplo seguinte inicia o projeto de servidor com sem funcionalidades colaborativas:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para ativar funcionalidades individuais, tem de chamar métodos de extensão no objeto **MobileAppConfiguration** antes de chamar **abranger**. Por exemplo, o seguinte código adiciona as rotas predefinida para todos os controladores de API que têm o atributo `[MobileAppController]` durante a inicialização:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

O guia de introdução do servidor a partir do portal Azure chamadas **UseDefaultConfiguration()**. Este equivalente à configuração do seguinte:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Os métodos de extensão utilizados são:

* `AddMobileAppHomeController()`Fornece a home page predefinida Azure Mobile aplicações.
* `MapApiControllers()`Fornece capacidades personalizadas, de API para WebAPI controladores decorada com o `[MobileAppController]` atributo.
* `AddTables()`Fornece um mapeamento do `/tables` os pontos finais para controladores de tabela.
* `AddTablesWithEntityFramework()`é uma pequena mão para o mapeamento de `/tables` pontos finais utilizando entidade Framework com controladores de base.
* `AddPushNotifications()`Fornece um método simple de registar dispositivos para concentradores de notificação.
* `MapLegacyCrossDomainController()`Fornece cabeçalhos CORS padrão para desenvolvimento local.

### <a name="sdk-extensions"></a>Extensões SDK

Os pacotes de extensão com base em NuGet seguintes fornecem várias funcionalidades para dispositivos móveis que podem ser utilizadas pela aplicação. Activar extensões durante a inicialização utilizando o objeto **MobileAppConfiguration** .

- [Microsoft.Azure.Mobile.Server.Quickstart] suporta a configuração de aplicações Mobile básica. É adicionada à configuração de ao contactar o método de extensão **UseDefaultConfiguration** durante a inicialização. Esta extensão incluem os seguintes extensões: notificações, autenticação, entidade, tabelas, domínios e pacotes de base. Este pacote é utilizado pelo Guia de introdução do aplicações móveis disponíveis no portal do Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 
   implementa predefinido *esta aplicação móvel está a trabalhar página* para o web site de raiz. Adicione à configuração ao contactar o método de extensão   **AddMobileAppHomeController** .

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 
   inclui classes para trabalhar com dados e conjuntos de seta para cima pipeline de dados. Adicione à configuração ao contactar o método de extensão **AddTables** .

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 
   permite a arquitetura de entidade para aceder aos dados na base de dados SQL. Adicione à configuração ao contactar o método de extensão **AddTablesWithEntityFramework** .

- [Microsoft.Azure.Mobile.Server.Authentication] permite a autenticação e conjuntos de seta para cima o software OWIN intermédio utilizado para validar os tokens. Adicionar à configuração ao contactar o **AddAppServiceAuthentication**  
   e **IAppBuilder**. Métodos de extensão **UseAppServiceAuthentication** .

- [Microsoft.Azure.Mobile.Server.Notifications] permite notificações push e define um ponto final de registo de push. Adicione à configuração ao contactar o método de extensão **AddPushNotifications** .

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 
   cria um controlador que serve de dados em browsers legado da sua aplicação móvel. Adicione à configuração ao contactar o método de extensão   **MapLegacyCrossDomainController** .

- [Microsoft.Azure.Mobile.Server.Login] fornece o método de AppServiceLoginHandler.CreateToken(), que é um método estático utilizado durante cenários de autenticação personalizada.   

## <a name="publish-server-project"></a>Como: publicar o projeto de servidor

Esta secção mostra como publicar o projeto de back-end .NET do Visual Studio. Também pode implementar o projeto de back-end utilizando Git ou qualquer um dos outros métodos de abrangidos na [documentação de implementação do serviço de aplicação do Azure](../app-service-web/web-sites-deploy.md).

1. No Visual Studio, reconstrua o projeto para restaurar NuGet pacotes.

2. No Explorador de solução, com o botão direito do projeto, clique em **Publicar**. A primeira vez que publica, tem de definir um perfil de publicação. Quando já tem um perfil definido, pode selecioná-la e clique em **Publicar**.

2. Se lhe for perguntado para selecionar um destino de publicar, clique em **Serviço de aplicação do Microsoft Azure** > **seguinte**, em seguida, (se necessário) iniciar sessão com as suas credenciais Azure. 
   Visual Studio transferências e segura armazena as definições diretamente a partir do Azure de publicação.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Selecione a sua **subscrição**, selecione o **Tipo de recurso** a partir da **vista**, expanda a **Aplicação Mobile**e clique em seu back-end aplicação Mobile, em seguida, clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Verifique se as informações do perfil publicar e clique em **Publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Quando o seu back-end aplicação Mobile publicou com êxito, verá uma página de destino que indica o sucesso.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a>Como: definir um controlador de tabela

Defina um controlador de tabela para mostrar uma tabela SQL para clientes móveis.  Configurar um controlador de tabela necessita de três passos:

1. Crie uma classe de objeto de transferência de dados (DTO).
2. Configure uma referência de tabela da turma Mobile DbContext.
3. Crie um controlador de tabela.

Um objeto de transferência de dados (DTO) é um simples c# objeto que herda `EntityData`.  Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

O DTO é utilizado para definir a tabela na base de dados SQL.  Para criar a entrada de base de dados, adicionar um `DbSet<>` propriedade para DbContext estiver a utilizar.  No modelo de projeto predefinido para aplicações do Azure Mobile, chama-se a DbContext `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Se tiver instalado o SDK do Azure, agora pode criar um controlador de tabela do modelo da seguinte forma:

1. Botão direito do rato na pasta controladores e selecione **Adicionar** > **controlador de...**.
2. Selecione a opção de **Controlador de tabela do Azure Mobile aplicações** e, em seguida, clique em **Adicionar**.
3. Na caixa de diálogo **Adicionar controlador** :
    * Na lista pendente de **classe do modelo** , selecione o seu novo DTO.
    * Na lista pendente **DbContext** , selecione a classe de DbContext de serviço móvel.
    * O nome do controlador é criado por si.
4. Clique em **Adicionar**.

O projeto de servidor do guia de introdução contém um exemplo de uma simples **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a>Como: ajustar o tamanho de paginação de tabela

Por predefinição, as aplicações móveis do Azure devolve 50 registos por pedido.  Paginação assegura que o cliente não ocupar os respetivos tópico IU nem o servidor durante muito tempo, garantindo que uma boa experiência de utilizador. Para alterar o tamanho da tabela paginação, aumentar o lado do servidor "o tamanho da consulta permitido" e do lado do servidor de tamanho de página do lado do cliente "o tamanho da consulta permitido" é ajustado utilizando o `EnableQuery` atributo:

    [EnableQuery(PageSize = 500)]

Certifique-se de que o PageSize é igual ou maior do que o tamanho do pedido pelo cliente.  Referem-se para o cliente específico documentação de How to para obter detalhes sobre como alterar o tamanho da página de cliente.

## <a name="how-to-define-a-custom-api-controller"></a>Como: definir um controlador de API personalizado

O controlador de API personalizado fornece a funcionalidade mais básica para sua back-end aplicação Mobile por expor um ponto final. Pode registar um controlador de API mobile específicas utilizando o atributo [MobileAppController]. O `MobileAppController` regista a rota de atributo, configura o serializador JSON de aplicações móveis e ativa [verificação da versão de cliente](app-service-mobile-client-and-server-versioning.md).

1. No Visual Studio, com o botão direito na pasta de controladores, em seguida, clique em **Adicionar** > **controlador de**, selecione **controlador de 2 Web API&mdash;vazia** e clique em **Adicionar**.

2. Fornecer um **nome do controlador**, tais como `CustomController`e clique em **Adicionar**.

3. No novo ficheiro de classe do controlador, adicione o seguinte utilizando instrução:

        using Microsoft.Azure.Mobile.Server.Config;

4. Aplica o atributo **[MobileAppController]** para a definição de classe de controlador de API, tal como no exemplo seguinte:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }

4. No ficheiro de App_Start/Startup.MobileApp.cs, adicione uma chamada para o método de extensão **MapApiControllers** , tal como no exemplo seguinte:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Também pode utilizar o `UseDefaultConfiguration()` método extensão em vez de `MapApiControllers()`. Qualquer controlador de que não tem **MobileAppControllerAttribute** aplicado ainda pode ser acedido por clientes, mas -pode não ser corretamente média consumida por clientes utilizando qualquer cliente aplicação Mobile SDK.

## <a name="how-to-work-with-authentication"></a>Como: trabalhar com autenticação

Aplicações do Azure Mobile utiliza a autenticação de serviço de aplicação / autorização para proteger os seus dispositivos móvel back-end.  Esta secção mostra-lhe como realizar as seguintes tarefas relacionadas com autenticação no seu projeto de servidor de back-end .NET:

+ [Como: Adicionar autenticação a um projeto de servidor](#add-auth)
+ [Como: utilizar a autenticação personalizada para a sua aplicação](#custom-auth)
+ [Como: recuperar autenticados informações do utilizador](#user-info)
+ [Como: restringir o acesso de dados para os utilizadores autorizados](#authorize)

### <a name="add-auth"></a>Como: Adicionar autenticação a um projeto de servidor

Pode adicionar autenticação ao seu projeto de servidor por alargar o objeto **MobileAppConfiguration** e configurar o software OWIN intermédio. Quando instalar o pacote de [Microsoft.Azure.Mobile.Server.Quickstart] e chamar o método de extensão **UseDefaultConfiguration** , pode avançar para o passo 3.

1. No Visual Studio, instale o pacote de [Microsoft.Azure.Mobile.Server.Authentication] .

2. No ficheiro de projeto Startup.cs, adicione a linha seguinte de código no início do método de **configuração** :

        app.UseAppServiceAuthentication(config);

    Este componente de software intermédio OWIN valida tokens emitidos pelo gateway associado da aplicação de serviço.

3. Adicionar o `[Authorize]` atributo a qualquer controlador ou o método que requer autenticação. 

Para saber mais sobre como autenticar os clientes ao seu back-end aplicações Mobile, consulte o artigo [Adicionar autenticação para a sua aplicação](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como: utilizar a autenticação personalizada para a sua aplicação

Se não pretender utilizar um dos fornecedores de serviço de aplicação autenticação/autorização, pode implementar o seu próprio sistema de início de sessão. Instale o pacote de [Microsoft.Azure.Mobile.Server.Login] para ajudá-lo a geração token de autenticação.  Fornece o seu próprio código para validar as credenciais de utilizador. Por exemplo, poderá verificar contra salgadas hash palavras-passe e numa base de dados. No exemplo abaixo, o `isValidAssertion()` método (definido noutro local) é responsável por estas verificações.

A autenticação personalizada exposta através da criação de um ApiController e expor `register` e `login` ações. O cliente deve utilizar partes personalizadas da IU para recolher as informações do utilizador.  As informações, em seguida, são apresentadas à API com uma chamada de HTTP POST padrão. Assim que o servidor de valida a afirmação, é emitido um token utilizando o `AppServiceLoginHandler.CreateToken()` método.  A utilização de **não deve** ApiController a `[MobileAppController]` atributo. 

Um exemplo `login` ação:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

No exemplo anterior, LoginResult e LoginResultUser são objetos serializáveis expor as propriedades necessárias. O cliente espera obter respostas de início de sessão para ser devolvido como objetos JSON do formulário:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

O `AppServiceLoginHandler.CreateToken()` método inclui uma _audiência_ e um parâmetro _emissor_ . Ambos os parâmetros são definidos para o URL do seu raiz da aplicação, utilizando o esquema de HTTPS. Do mesmo modo deve definir _secretKey_ para ser que o valor da sua aplicação da chave de assinatura. Não distribua a chave de assinatura num cliente como pode ser utilizada para fazer teclas e representar utilizadores. Pode obter a chave de assinatura enquanto alojado no serviço de aplicação ao referenciar o _Web site\_AUTH\_assinatura\_chave_ variável de ambiente. Se for necessário num contexto depuração local, siga as instruções na secção [locais depuração com autenticação](#local-debug) para obter a chave e armazená-lo como uma definição de aplicação.

O token emitido também pode incluir outros créditos e uma data de expiração.  Mínimas, o token emitido tem de incluir um pedido de assunto (**sub**).

Pode suportar o cliente padrão `loginAsync()` método por sobrecarga a rota de autenticação.  Se liga para o cliente `client.loginAsync('custom');` para iniciar sessão, tem de ser a rota `/.auth/login/custom`.  Pode definir a rota para o controlador de autenticação personalizada utilizando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] Utilizar o `loginAsync()` abordagem assegura que o token de autenticação está ligado a todas as chamadas subsequentes para o serviço.

###<a name="user-info"></a>Como: recuperar autenticados informações do utilizador

Quando um utilizador é autenticado pelo serviço de aplicação, pode aceder ao ID de utilizador atribuídos e outras informações no seu código de back-end .NET. As informações do utilizador podem ser utilizadas para fazer decisões de autorização no back-end. O código seguinte obtém o ID de utilizador associado um pedido de:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

O SID deriva do ID de utilizador específicas do fornecedor e é estático para um determinado utilizador e um fornecedor de início de sessão.  O SID é nulo para tokens de autenticação inválido.

Aplicação de serviço também permite-lhe pedir afirmações específicas do fornecedor de início de sessão. Cada fornecedor de identidades pode fornecer mais informações, utilizando o fornecedor de identidade SDK.  Por exemplo, pode utilizar a API do Facebook Graph para obter informações de amigos.  Pode especificar afirmações que forem pedidas no pá fornecedor no portal do Azure. Em algumas afirmações requerem configuração adicional com o fornecedor de identidade.

O código seguinte chama o método de extensão **GetAppServiceIdentityAsync** para obter as credenciais de início de sessão, que incluem o token de acesso necessário para fazer pedidos contra a API do Facebook Graph:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Adicionar um utilizando declaração do `System.Security.Principal` para fornecer o método de extensão **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Como: restringir o acesso de dados para os utilizadores autorizados

Na secção anterior, podemos mostrava como obter o ID de utilizador de um utilizador autenticado. Pode restringir o acesso aos dados e outros recursos com base neste valor. Por exemplo, adicionar uma coluna de ID de utilizador a tabelas e filtrar os resultados da consulta pelo ID de utilizador são uma forma simple para limitar os dados devolvidos apenas para utilizadores autorizados. O código seguinte devolve linhas de dados, apenas quando o SID corresponde ao valor na coluna ID de utilizador na tabela TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

O `Query()` método devolve um `IQueryable` que pode ser manipulação por LINQ para processar filtragem.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Como: Adicionar push notificações para um projeto de servidor

Adicione as notificações push ao seu projeto de servidor por alargar o objeto **MobileAppConfiguration** e a criação de um cliente concentradores de notificação.

1. No Visual Studio, o project server com o botão direito e clique em **Gerir pacotes de NuGet**, procure `Microsoft.Azure.Mobile.Server.Notifications`, em seguida, clique em **instalar**. 

2. Repita este passo para instalar o `Microsoft.Azure.NotificationHubs` compactar, que inclui a biblioteca do cliente concentradores de notificação.

3. No App_Start/Startup.MobileApp.cs e adicionar uma chamada para o método de extensão **AddPushNotifications()** durante a inicialização:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);

4. Adicione o seguinte código que cria um cliente concentradores de notificação:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Agora pode utilizar o cliente de notificação concentradores para enviar notificações push para dispositivos registados. Para mais informações, consulte o artigo [Adicionar as notificações push para a sua aplicação](app-service-mobile-ios-get-started-push.md). Para saber mais sobre concentradores de notificação, consulte o artigo [Descrição geral de concentradores de notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Como: Activar direccionado push utilizar etiquetas

Notificação concentradores permite-lhe enviar notificações orientadas para os registos específicos ao utilizar etiquetas. Várias etiquetas são criadas automaticamente:

* O ID de instalação identifica um dispositivo específico.
* O Id de utilizador com base no autenticados SID identifica um utilizador específico.

O ID de instalação, pode ser acedido a partir da propriedade do **InstallationID, incluindo** o **MobileServiceClient**.  O exemplo seguinte mostra como utilizar um ID de instalação para adicionar uma etiqueta a uma instalação específica na notificação concentradores:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Quaisquer etiquetas fornecidas pelo cliente durante o registo de notificações push são ignoradas por back-end quando criar a instalação. Para permitir que um cliente para adicionar etiquetas à instalação, tem de criar uma API personalizada que adiciona etiquetas utilizando o padrão anterior. 

Consulte o artigo [etiquetas de notificações push adicionado cliente] [ 5] no exemplo aplicações do serviço de aplicação Mobile guia de introdução concluída para obter um exemplo.

##<a name="push-user"></a>Como: enviar as notificações push para um utilizador autenticado

Quando um utilizador autenticado regista para notificações push, uma etiqueta de ID de utilizador é automaticamente adicionada ao registo. Ao utilizar nesta etiqueta, pode enviar notificações push para todos os dispositivos registados por essa pessoa. O código seguinte obtém o SID do utilizador efetuar o pedido e envia uma notificação de push do modelo para cada registo de dispositivo para essa pessoa:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Quando registar-se para as notificações push a partir de um cliente não autenticado, certifique-se de que a autenticação estiver concluída antes de tentar registo. Para obter mais informações, consulte o artigo [emissão aos utilizadores] [ 6] no exemplo aplicações do serviço de aplicação Mobile guia de introdução concluída para .NET do back-end.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Como: depurar e resolver problemas o .NET Server SDK

Azure de aplicação de serviço fornece várias depuração e técnicas para aplicações do ASP.NET de resolução de problemas:

- [Monitorização de uma aplicação de serviço Azure](../app-service-web/web-sites-monitor.md)
- [Ativar o registo de diagnóstico no Azure de aplicação de serviço](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Resolver problemas de um serviço de aplicação Azure no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Funcionalidade de registo

Pode escrever para registos de diagnóstico de aplicação de serviço através da utilização do ASP.NET padrão de escrita de rastreio. Pode escrever os registos, tem de ativar diagnósticos no seu back-end aplicação Mobile.

Para activar diagnósticos e escrita para os registos:

1. Siga os passos no [artigo como activar diagnósticos](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Adicione o seguinte utilizando instrução no seu ficheiro de código:

        using System.Web.Http.Tracing;

3. Crie um gravador de rastreio de escrever a partir do back-end .NET registos de diagnóstico, da seguinte forma:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");

4. Voltar a publicar o projeto de servidor e aceder o aplicação Mobile back-end para executar o caminho de código com início de sessão.

5. Transferir e avaliar os registos, conforme descrito no [como: transferir registos](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Depuração com autenticação local

Pode executar a aplicação localmente para testar as alterações antes de publicá-los na nuvem. Para a maioria das aplicações do Azure Mobile back-ends, prima *F5* enquanto no Visual Studio. No entanto, existem algumas considerações adicionais quando utilizando a autenticação.

Tem de ter uma aplicação móvel baseado na nuvem com aplicação do serviço de autenticação/autorização configurada e o cliente tem de ter o ponto final de nuvem especificado como anfitrião do início de sessão alternativo. Consulte a documentação para a sua plataforma do cliente para obter passos específicos necessário.

Certifique-se de que o seu back-end móvel tem [Microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, em classe de arranque OWIN a aplicação, adicione o seguinte, após `MobileAppConfiguration` ser aplicado ao seu `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

No exemplo anterior, deve configurar as definições da aplicação _authAudience_ e _authIssuer_ do ficheiro da Web. config a cada ser o URL do seu raiz da aplicação, utilizando o esquema de HTTPS. Do mesmo modo deve definir _authSigningKey_ para ser que o valor da sua aplicação da chave de assinatura. Para obter a chave de assinatura:

1. Navegue para a sua aplicação no interior do [portal do Azure] 
2. Clique em **Ferramentas**, **Kudu**, **aceda**.
3. No site do Kudu gestão, clique em **ambiente**.
4. Encontrar o valor para _Web site\_AUTH\_assinatura\_chave_. 

Utilize a chave de assinatura para o parâmetro _authSigningKey_ no seu config aplicação local.  O back-end móvel agora esteja equipado para validar os tokens quando a ser executado localmente, o cliente obtém o token do ponto final baseado na nuvem.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portal do Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

