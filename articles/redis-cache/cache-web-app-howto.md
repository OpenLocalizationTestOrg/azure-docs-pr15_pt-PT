<properties 
    pageTitle="Como criar uma aplicação Web com a Redis Cache | Microsoft Azure" 
    description="Saiba como criar uma aplicação Web com a Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>Como criar uma aplicação Web com Redis Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [NODE.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tutorial mostra como criar e implementar uma aplicação web do ASP.NET para uma aplicação web na aplicação de serviço de Azure utilizando o Visual Studio 2015. A aplicação de exemplo apresenta uma lista de estatísticas de equipa a partir de uma base de dados e mostra diferentes maneiras de utilizar Azure Redis Cache para armazenar e obter dados a partir da cache. Quando concluir o tutorial terá uma aplicação web em execução que lê e escreve a uma base de dados, otimizado com o Azure Redis Cache e alojado no Azure.

Que vai aprender:

-   Como criar uma aplicação web do ASP.NET MVC 5 no Visual Studio.
-   Como dados do access a partir de uma base de dados utilizando Framework entidade.
-   Como melhorar a débito de dados e reduzir carregar base de dados ao armazenamento de dados e obter dados utilizando a Cache de Redis Azure.
-   Como utilizar uma Redis ordenados conjunto para obter as equipas de 5 superiores.
-   Como pode aprovisionar os Azure recursos para a aplicação através de um modelo de Gestor de recursos.
-   Como publicar a aplicação para Azure utilizando o Visual Studio.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, tem de ter os pré-requisitos seguintes.

-   [Conta Azure](#azure-account)
-   [Visual Studio 2015 com o Azure SDK para .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Conta Azure

Precisa de uma conta Azure para concluir o tutorial. Pode:

* [Abrir uma conta Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obter créditos que podem ser utilizados para experimentar a serviços Azure pagos. Mesmo depois de créditos são utilizados para cima, pode manter a conta e utilização das funcionalidades e serviços Azure gratuitos.
* [Ativar o Visual Studio benefícios de subscritor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>Visual Studio 2015 com o Azure SDK para .NET

O tutorial está escrito para Visual Studio 2015 com o [Azure SDK para .NET](../dotnet-sdk.md) 2.8.2 ou posterior. [Transferir o SDK Azure mais recente de 2015 de Studio Visual aqui](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio é instalado automaticamente com o SDK se ainda não tiver.

Se tiver o Visual Studio 2013, pode [Transferir o SDK Azure mais recente do Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Alguns ecrãs poderão estar diferentes a partir de ilustrações mostradas neste tutorial.

>[AZURE.NOTE] Dependendo das dependências SDK quantos já tiver no seu computador, instalar o SDK poderá demorar muito tempo, a partir de alguns minutos para de meia hora ou mais.

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Abrir o Visual Studio e clique em **ficheiro**, **Novo** **projeto**.

2. Expanda o nó **Visual c#** na lista de **modelos** , selecione **na nuvem**e clique em **Aplicação Web do ASP.NET**. Certifique-se de que o **.NET Framework 4.5.2** está selecionada.  Escreva **ContosoTeamStats** na caixa de texto **nome** e clique em **OK**.
 
    ![Criar projeto][cache-create-project]

3. Selecione **MVC** como o tipo de projecto. Desmarque a caixa de verificação do **anfitrião na nuvem** . Verá a [aprovisionar os recursos Azure](#provision-the-azure-resources) e [publicar a aplicação para Azure](#publish-the-application-to-azure) nos passos subsequentes no tutorial. Para obter um exemplo de uma aplicação web do serviço de aplicação a partir do Visual Studio de aprovisionamento deixe **anfitrião na nuvem** selecionada, consulte o artigo [Introdução ao Web Apps no serviço de aplicação do Azure, utilizando ASP.NET e o Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Selecione o modelo do project][cache-select-template]

4. Clique em **OK** para criar o projeto.

## <a name="create-the-aspnet-mvc-application"></a>Criar a aplicação de MVC do ASP.NET

Nesta secção do tutorial, irá criar a aplicação básica que lê e apresenta as estatísticas de equipa a partir de uma base de dados.

-   [Adicionar o modelo](#add-the-model)
-   [Adicionar o controlador de](#add-the-controller)
-   [Configurar as vistas](#configure-the-views)

### <a name="add-the-model"></a>Adicionar o modelo

1. Com o botão direito de **modelos** no **Explorador de soluções**e selecione **Adicionar**, **escolares**. 

    ![Adicionar a modelo][cache-model-add-class]

2. Introduza `Team` para a classe de atribuir um nome e clique em **Adicionar**.

    ![Adicionar a classe de modelo][cache-model-add-class-dialog]

3. Substituir o `using` declarações no topo da `Team.cs` ficheiro com o seguinte utilizando instruções.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Substituir a definição da `Team` classe com o fragmento de código seguinte que contém uma atualizada `Team` classe definição, bem como algumas outras classes entidade Framework helper. Para mais informações sobre código primeira abordagem para Framework entidade que é utilizado neste tutorial, consulte o artigo [código pela primeira vez para uma nova base de dados](https://msdn.microsoft.com/data/jj193542).


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. No **Explorador de soluções**, faça duplo clique em **Web. config** para o abrir.

    ![Web. config][cache-web-config]

3.  Adicionar a cadeia de ligação seguinte para o `connectionStrings` secção. O nome da cadeia de ligação tem de corresponder o nome de classe de contexto de base de dados Framework entidade que é `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Depois de adicionar esta ação, a `connectionStrings` secção deverá ter um aspeto semelhante ao exemplo seguinte.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Adicionar o controlador de

1. Prima **F6** para construir o projeto. 
2. No **Explorador de soluções**, com o botão direito na pasta de **controladores** e selecione **Adicionar**, **controlador**.

    ![Adicionar controlador][cache-add-controller]

3. Escolha **Controlador de 5 MVC com vistas, através de estrutura de entidades**e clique em **Adicionar**. Se obtiver um erro depois de clicar em **Adicionar**, certifique-se de que o projeto ter criado pela primeira vez.

    ![Adicionar controlador classe][cache-add-controller-class]

5. Selecione **equipa (ContosoTeamStats.Models)** a partir da lista pendente de **classe do modelo** . Selecione **TeamContext (ContosoTeamStats.Models)** a partir da lista pendente de **classe de contexto de dados** . Tipo de `TeamsController` na **controlador de** caixa de texto nome (se o mesmo não é preenchido automaticamente). Clique em **Adicionar** para a classe de controlador de criar e adicionar as vistas predefinidas.

    ![Configurar controlador][cache-configure-controller]

4. No **Explorador de soluções**, expanda **asax** e faça duplo clique em **Global.asax.cs** para o abrir.

    ![Global.asax.CS][cache-global-asax]

5. Adicionar as duas seguintes utilizando as instruções na parte superior do ficheiro em de outras utilizando instruções.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Adicionar a linha seguinte de código no final da `Application_Start` método.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. No **Explorador de soluções**, expanda `App_Start` e faça duplo clique `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Substituir `controller = "Home"` no código seguinte na `RegisterRoutes` método com `controller = "Teams"` conforme mostrado no seguinte exemplo.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Configurar as vistas

1. No **Explorador de soluções**, expanda a pasta de **vistas** e, em seguida, a pasta **partilhada** e faça duplo clique em **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]

2. Alterar o conteúdo do `title` elemento e substituir `My ASP.NET Application` com `Contoso Team Stats` conforme mostrado no seguinte exemplo.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. No `body` secção, atualize o primeiro `Html.ActionLink` declaração e substituir `Application name` com `Contoso Team Stats` e substituir `Home` com `Teams`.
    -   Antes:`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Depois:`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Alterações de código][cache-layout-cshtml-code]

4. Prima **Ctrl + F5** para criar e executar a aplicação. Esta versão da aplicação lê os resultados diretamente a partir da base de dados. Tenha em atenção as ações **Criar novo**, **Editar**, **Detalhes**e **Eliminar** que foram adicionadas automaticamente para a aplicação por scaffold o **Controlador de 5 MVC com vistas, utilizando Framework entidade** . Na secção seguinte do tutorial vai adicionar Redis Cache para otimizar o acesso a dados e fornecem funcionalidades adicionais para a aplicação.

![Aplicação Starter][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurar a aplicação para utilizar Redis Cache

Nesta secção do tutorial, irá configurar a aplicação de exemplo para armazenar e obter as estatísticas de equipa da Contoso a partir de uma instância da Cache de Redis Azure utilizando o cliente de cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

-   [Configurar a aplicação para utilizar StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [Atualizar a classe de TeamsController para devolver resultados da cache ou a base de dados](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [Atualizar os métodos de criar, editar e eliminar a trabalhar com a cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [Atualizar a vista de equipas índice para trabalhar com a cache](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar a aplicação para utilizar StackExchange.Redis

1. Para configurar uma aplicação de cliente no Visual Studio utilizando o pacote de StackExchange.Redis NuGet, com o botão direito do projecto no **Explorador de soluções** e selecione **Gerir pacotes de NuGet**. 

    ![Gerir pacotes de NuGet][redis-cache-manage-nuget-menu]

2. Escreva **StackExchange.Redis** na caixa de texto de pesquisa, selecione a versão pretendida nos resultados de e clique em **instalar**.

    ![Pacote StackExchange.Redis NuGet][redis-cache-stack-exchange-nuget]

    O pacote de NuGet transferências e adiciona as referências de assemblagem necessária para a sua aplicação cliente ao aceder a Cache de Redis Azure, com o cliente de cache StackExchange.Redis. Se preferir utilizar uma versão forte-com o nome da biblioteca de cliente **StackExchange.Redis** de, selecione **StackExchange.Redis.StrongName**; caso contrário, escolha **StackExchange.Redis**.

3. No **Explorador de soluções**, expanda a pasta de **controladores** e faça duplo clique em **TeamsController.cs** para o abrir.

    ![Controlador de equipas][cache-teamscontroller]

4. Adicionar as duas seguintes declarações para **TeamsController.cs**a utilizar.

        using System.Configuration;
        using StackExchange.Redis;

5. Adicionar as seguintes propriedades de duas para o `TeamsController` escolares.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Criar um ficheiro no seu computador com o nome `WebAppPlusCacheAppSecrets.config` e coloque-o numa localização que não seja dada com o código de origem da sua aplicação de exemplo, pode decidir a disponibilizá-lo em qualquer lado. Neste exemplo a `AppSettingsSecrets.config` ficheiro está localizado em `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Editar o `WebAppPlusCacheAppSecrets.config` de ficheiros e adicione o seguinte conteúdo. Se executar a aplicação localmente estas informações são utilizadas para ligar à sua instância do Azure Redis Cache. Mais tarde no tutorial irá aprovisionar uma instância do Azure Redis Cache e atualizar o nome da cache e a palavra-passe. Se não planeia executar a aplicação de exemplo localmente pode ignorar a criação deste ficheiro e os passos subsequentes que fazem referência ao ficheiro, porque quando implementar Azure a aplicação obtém as informações de ligação de cache da definição de aplicação para a aplicação Web e não a partir deste ficheiro. Uma vez que o `WebAppPlusCacheAppSecrets.config` não está implementada para Azure com a aplicação, não precise do mesmo, a menos que passar para executar a aplicação localmente.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. No **Explorador de soluções**, faça duplo clique em **Web. config** para o abrir.

    ![Web. config][cache-web-config]

3. Adicione o seguinte `file` atributo para o `appSettings` elemento. Se utilizou um nome de ficheiro diferente ou localização, substitua esses valores para aqueles mostrado no exemplo.
    -   Antes:`<appSettings>`
    -   Depois:` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    O tempo de execução do ASP.NET intercala o conteúdo do ficheiro externo com a marcação na `<appSettings>` elemento. O tempo de execução ignora o atributo do ficheiro, se não é possível localizar o ficheiro especificado. Os segredos (a cadeia de ligação para a cache) não estão incluídos como parte do código de origem para a aplicação. Quando implementar a aplicação web do Azure, o `WebAppPlusCacheAppSecrests.config` ficheiro não ser implementado (que é o que pretende). Existem várias formas para especificar estes segredos no Azure e neste tutorial são configuradas automaticamente para si quando [aprovisionar os recursos Azure](#provision-the-azure-resources) num tutorial subsequente passo. Para mais informações sobre como trabalhar com segredos no Azure, consulte o artigo [melhores práticas para implementar palavras-passe e outros dados sensíveis a maiúsculas e o serviço de aplicação do Azure e ASP.NET](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Atualizar a classe de TeamsController para devolver resultados da cache ou a base de dados

Neste exemplo, as estatísticas de equipa podem ser recuperadas da base de dados ou a partir da cache. Estatísticas de equipa são armazenadas na cache como uma série `List<Team>`e também como um conjunto ordenado Redis tipos de dados. Quando a obter itens a partir de um conjunto ordenado, pode obter alguns, todos ou consulta para determinados itens. Neste exemplo irá consultar o conjunto ordenado para as equipas de 5 superiores classificados ao número de wins.

>[AZURE.NOTE] Não é necessária para armazenar as estatísticas de equipa em vários formatos na cache para poder utilizar o Azure Redis Cache. Neste tutorial utiliza vários formatos para demonstram algumas formas diferentes e diferentes tipos de dados que pode utilizar para dados em cache.



1. Adicione o seguinte utilizando instruções para o `TeamsController.cs` ficheiro na parte superior com o outro utilizando instruções.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Substituir atuais `public ActionResult Index()` método com implementação que se segue.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Adicionar os seguintes três métodos para o `TeamsController` escolares para implementar o `playGames`, `clearCache`, e `rebuildDB` tipos de ação da instrução de parâmetro adicionado o fragmento de código anterior.

    O `PlayGames` método atualiza as estatísticas de equipa através da simulação uma época dos jogos, guarda os resultados à base de dados e limpa os dados da cache de Desatualizadas agora.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    O `RebuildDB` método reinicializa a base de dados com o conjunto predefinido de equipas, gera estatísticas das mesmas e limpa os dados agora desatualizados da cache de.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    O `ClearCachedTeams` método Remove todas as estatísticas de equipa em cache da cache.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Adicionar os seguintes quatro métodos para o `TeamsController` escolares para implementar as várias formas de obter as estatísticas de equipa a partir da cache e a base de dados. Cada um dos seguintes métodos devolve um `List<Team>` que é apresentado, em seguida, pela vista.

    O `GetFromDB` método lê as estatísticas de equipa a partir da base de dados.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    O `GetFromList` método lê as estatísticas de equipa da cache como uma série `List<Team>`. Se existir uma falha de cache, as estatísticas de equipa são ler a partir da base de dados e, em seguida, armazenadas na cache para utilização posterior. Neste exemplo estamos a utilizar JSON.NET serialização serializar os objetos .NET para e da cache de. Para mais informações, consulte o artigo [como trabalhar com objetos .NET na Cache de Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    O `GetFromSortedSet` método lê as estatísticas de equipa a partir de um conjunto de ordenado em cache. Se existir uma falha de cache, as estatísticas de equipa são ler a partir da base de dados e armazenadas na cache como um conjunto ordenado.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    O `GetFromSortedSetTop5` método lê as equipas de 5 de início do conjunto de ordenado em cache. Desde o início ao selecionar a cache para a existência do `teamsSortedSet` chave. Se esta chave não existir, o `GetFromSortedSet` método denomina-se para ler as estatísticas de equipa e armazená-los na cache. Seguinte é consultado o conjunto de ordenado em cache para as equipas de 5 superiores que são devolvidas.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Atualizar os métodos de criar, editar e eliminar a trabalhar com a cache

O código de andaimes que foram gerado como uma parte desta amostra inclui métodos para adicionar, editar e eliminar equipas. Sempre que uma equipa é adicionada, editada ou removida, os dados na cache torna-se Desatualizadas. Nesta secção, vai modificar estes três métodos para desmarcar as equipas em cache de modo a que a cache não está sincronizada com a base de dados.

1. Navegue para a `Create(Team team)` método na `TeamsController` escolares. Adicionar uma chamada para o `ClearCachedTeams` método, conforme mostrado no seguinte exemplo.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Navegue para a `Edit(Team team)` método na `TeamsController` escolares. Adicionar uma chamada para o `ClearCachedTeams` método, conforme mostrado no seguinte exemplo.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Navegue para a `DeleteConfirmed(int id)` método na `TeamsController` escolares. Adicionar uma chamada para o `ClearCachedTeams` método, conforme mostrado no seguinte exemplo.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Atualizar a vista de equipas índice para trabalhar com a cache

1. No **Explorador de soluções**, expanda a pasta de **vistas** , em seguida, a pasta de **equipas** e faça duplo clique em **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Na parte superior do ficheiro, procure o elemento de parágrafo seguinte.

    ![Tabela de ação][cache-teams-index-table]

    Esta é a hiperligação para criar uma nova equipa. Substitua o elemento de parágrafo com a tabela seguinte. Esta tabela tem hiperligações da ação para criar uma nova equipa, reproduzir uma nova época dos jogos, limpar a cache, obter as equipas a partir da cache em vários formatos, as equipas a obter da base de dados e reconstruir a base de dados com dados de exemplo fresca.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Desloque-se para a parte inferior do ficheiro **Index.cshtml** e adicione o seguinte `tr` elemento, pelo que a mesma é da última linha da tabela última no ficheiro.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    Esta linha apresenta o valor de `ViewBag.Msg` que contém um relatório de estado sobre o funcionamento atual que está definido quando clicar das ligações de acção do passo anterior.   

    ![Mensagem de estado][cache-status-message]

4. Prima **F6** para construir o projeto.

## <a name="provision-the-azure-resources"></a>Aprovisionar os Azure recursos

Para alojar a aplicação no Azure, tem de aprovisionamento pela primeira vez os serviços Azure que requer a aplicação. A aplicação de exemplo neste tutorial, utiliza os seguintes serviços Azure.

-   Cache de Azure Redis
-   Aplicação de serviço Web App
-   Base de dados SQL

Para implementar estes serviços para um grupo de recursos nova ou existente à sua escolha, clique no botão de **implementar para Azure** seguinte.

[! [Implementar Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Este botão **implementar para Azure** utiliza o modelo de [criar uma aplicação Web plus Redis Cache plus base de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure guia de introdução](https://github.com/Azure/azure-quickstart-templates) para aprovisionar estes serviços e configurar a cadeia de ligação para a base de dados do SQL e a definição de aplicação para a cadeia de ligação do Azure Redis Cache.

>[AZURE.NOTE] Se não tiver uma conta Azure, pode [criar uma conta Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) apenas de duas minutos.

Clicar no botão **implementar para Azure** leva-o para o portal do Azure e inicia o processo de criação de recursos descritos pelo modelo.

![Implementar Azure][cache-deploy-to-azure-step-1]

1. No pá **implementação personalizadas** , selecione a subscrição Azure para utilizar e selecione um grupo de recursos existente ou criar um novo e especificar a localização do grupo de recursos.
2. No pá **parâmetros** , especifique um nome de conta de administrador (**ADMINISTRATORLOGIN** - não utiliza o **administrador**), palavra-passe início de sessão de administrador (**ADMINISTRATORLOGINPASSWORD**) e o nome da base de dados (**DATABASENAME**). Os outros parâmetros são configurados para um serviço gratuito da aplicação alojamento plano e inferiores custo opções para a base de dados SQL e o Azure Redis Cache, que não vêm com uma camada gratuita.
3. Alterar qualquer uma das outras definições caso deseje, ou mantenha as predefinições e clique em **OK**.


![Implementar Azure][cache-deploy-to-azure-step-2]

1. Clique em **Rever legais termos**.
2. Leia os termos no pá a **Compra** e clique em **comprar**.
3. Para começar a aprovisionamento os recursos, clique em **Criar** no pá a **implementação personalizada** .

Para ver o progresso da sua implementação, clique no ícone de notificação e clique em **implementação iniciado**.

![Implementação iniciada][cache-deployment-started]

Pode ver o estado da sua implementação na pá **Microsoft.Template** .

![Implementar Azure][cache-deploy-to-azure-step-3]

Quando aprovisiona o estiver concluída, pode publicar a aplicação para Azure a partir do Visual Studio.

>[AZURE.NOTE] Erros que podem ocorrer durante o processo de aprovisionamento são apresentados na pá **Microsoft.Template** . Erros comuns são demasiados SQL servidores ou demasiadas gratuito aplicação de serviço de alojamento planos por subscrição. Resolver erros e reinicie o processo ao clicar em **voltar a implementar** na pá **Microsoft.Template** ou no botão de **implementar para Azure** neste tutorial.

## <a name="publish-the-application-to-azure"></a>Publicar a aplicação para Azure

Neste passo do tutorial, irá publicar a aplicação para Azure e executá-la na nuvem.

1. Com o botão direito do projecto **ContosoTeamStats** no Visual Studio e escolha **Publicar**.

    ![Publicar][cache-publish-app]

2. Clique em **Serviço de aplicação do Microsoft Azure**.

    ![Publicar][cache-publish-to-app-service]

3. Selecione a subscrição utilizada quando criar os recursos Azure, expandir o grupo de recursos que contém os recursos, selecione a aplicação Web pretendida e clique em **OK**. Se tiver utilizado o botão de **implementar para Azure** inicia o seu nome do Web App com o **Web site** seguido de alguns carateres adicionais.

    ![Selecione Web App][cache-select-web-app]

4. Clique em **Validar ligação** para verificar as suas definições e, em seguida, clique em **Publicar**.

    ![Publicar][cache-publish]

    Depois de alguns minutos irá concluir o processo de publicação e num browser será iniciado com a execução aplicação de exemplo. Se obtiver um erro DNS quando validar ou de publicação e o processo de aprovisionamento para os recursos Azure para a aplicação foi concluída apenas recentemente, aguarde alguns momentos e tente novamente.

    ![Cache adicionado][cache-added-to-application]

A tabela seguinte descreve cada ligação de ação da aplicação de exemplo.

| Ação                  | Descrição                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criar novas              | Crie uma nova equipa.                                                                                                                                               |
| Reproduzir época             | Reproduzir uma época dos jogos, actualização do Estado de equipa e desmarque qualquer Desatualizadas dados a partir da cache de equipa.                                                                          |
| Limpar a Cache             | Desmarque as estatísticas de equipa a partir da cache.                                                                                                                             |
| Lista da Cache         | Obtêm do Estado de equipa a partir da cache. Se existir uma falha de cache, carregar do Estado da base de dados e guarde a cache para utilização posterior.                                        |
| Conjunto ordenado da Cache   | Obter as estatísticas de equipa da cache de utilizando um conjunto ordenado. Se existir uma falha de cache, carregar do Estado da base de dados e guarde a cache utilizando um conjunto ordenado.  |
| Início 5 equipas da Cache  | Obter as equipas de 5 superiores da cache de utilizando um conjunto ordenado. Se existir uma falha de cache, carregar do Estado da base de dados e guarde a cache utilizando um conjunto ordenado. |
| Carregar a partir da base de dados            | Obter as estatísticas de equipa a partir da base de dados.                                                                                                                       |
| Reconstruir a base de dados              | Reconstruir a base de dados e voltar a carregá-lo com dados de equipa de exemplo.                                                                                                        |
| Editar / detalhes / eliminar | Editar uma equipa, ver detalhes de uma equipa, eliminar uma equipa.                                                                                                             |


Clique em algumas das ações e experimentar a obter os dados de diferentes origens. Não as diferenças entre o tempo necessário para concluir as várias formas de obter os dados a partir da base de dados e a cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Eliminar os recursos, quando tiver terminado com a aplicação

Quando tiver terminado a aplicação de iniciação do exemplo, pode eliminar os recursos Azure utilizados para conservar de custos e recursos. Se utilizar o botão **implementar para Azure** na secção [aprovisionar os recursos Azure](#provision-the-azure-resources) e de todos os recursos estão contidos no mesmo grupo de recursos, pode eliminá-las em conjunto numa operação ao eliminar o grupo de recursos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **grupos de recursos**.
2. Escreva o nome do seu grupo de recursos para a caixa de texto **Filtrar itens...** .
3. Clique em **…** à direita do seu grupo de recursos.
4. Clique em **Eliminar**.

    ![Eliminar][cache-delete-resource-group]

5. Escreva o nome do seu grupo de recursos e clique em **Eliminar**.

    ![Confirmar a eliminação][cache-delete-confirm]

Depois de alguns minutos são eliminados o grupo de recursos e todos os seus recursos contidos.

>[AZURE.IMPORTANT] Tenha em atenção que a eliminação de um grupo de recursos é irreversível e que o grupo de recursos e todos os recursos no mesmo são eliminados permanentemente. Certifique-se de que não é acidentalmente eliminar o grupo de recursos mal ou recursos. Se criou os recursos para alojamento este exemplo dentro de um grupo de recursos existente, pode eliminar individualmente cada recurso a partir da suas pás respetivos.

## <a name="run-the-sample-application-on-your-local-machine"></a>Executar a aplicação de amostra no seu computador local

Para executar a aplicação localmente no seu computador, tem uma instância do Azure Redis Cache nas quais se os seus dados em cache. 

-   Se tiver publicado a aplicação para Azure, tal como descrito na secção anterior, pode utilizar a instância do Azure Redis Cache aprovisionada durante a esse passo.
-   Se tiver outra instância Azure Redis Cache existente, pode utilizá-la para executar este exemplo localmente.
-   Se precisar de criar uma instância do Azure Redis Cache, pode seguir os passos no [artigo criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Assim que tiver selecionado ou criado a cache para utilizar, navegue para a cache no portal do Azure e obter o [nome do anfitrião](cache-configure.md#properties) e [as teclas de acesso](cache-configure.md#access-keys) para a cache. Para obter instruções, consulte [Configurar Redis definições da cache](cache-configure.md#configure-redis-cache-settings).

1. Abrir o `WebAppPlusCacheAppSecrets.config` ficheiro que criou durante o passo de [Configurar a aplicação para utilizar Redis Cache](#configure-the-application-to-use-redis-cache) deste tutorial utilizando o editor da sua escolha.

2. Editar o `value` atributo e substituir `MyCache.redis.cache.windows.net` com o [nome do anfitrião](cache-configure.md#properties) da cache e especificar um da [chave primária ou secundária](cache-configure.md#access-keys) da cache como a palavra-passe.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Prima **Ctrl + F5** para executar a aplicação.

>[AZURE.NOTE] Tenha em atenção que uma vez que a aplicação, incluindo a base de dados, está a ser executado localmente e a Cache Redis está alojada no Azure, a cache poderão parecer em executar a base de dados. Para um melhor desempenho, a aplicação de cliente e instância do Azure Redis Cache devem ser na mesma localização. 

## <a name="next-steps"></a>Próximos passos

-   Saiba mais sobre a [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) no site do [ASP.NET](http://asp.net/) .
-   Para obter mais exemplos de criação de uma aplicação Web do ASP.NET na aplicação de serviço, consulte o artigo [criar e implementar uma aplicação web do ASP.NET na aplicação de serviço de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) a partir de ligar a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    -   Para obter mais tutoriais a partir de demonstração HealthClinic.biz, consulte o artigo [Tutoriais de ferramentas de programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
-   Saiba mais sobre a abordagem de [código em primeiro lugar, para uma nova base de dados](https://msdn.microsoft.com/data/jj193542) para Framework entidade que é utilizado neste tutorial.
-   Saiba mais sobre [aplicações web na aplicação de serviço de Azure](../app-service-web/app-service-web-overview.md).
-   Saiba como [monitor](cache-how-to-monitor.md) a cache no portal do Azure.

-   Explorar às funcionalidades premium do Azure Redis Cache
    -   [Como configurar persistente para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md)
    -   [Como configurar clusters para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md)
    -   [Como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md)
    -   Consulte as [FAQ do Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, débito e largura de banda com premium caches.



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

