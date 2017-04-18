<properties 
    pageTitle="Como utilizar o Azure Redis Cache | Microsoft Azure" 
    description="Aprenda a melhorar o desempenho das suas aplicações Azure com Azure Redis Cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Como utilizar o Azure Redis Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [NODE.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este guia mostra-lhe como começar a utilizar o **Azure Redis Cache**. Microsoft Azure Redis Cache baseia-se a origem de abrir popular Redis Cache. Dá-lhe acesso a uma cache de Redis segura, dedicada, gerida pela Microsoft. Uma cache criada utilizando a Cache de Redis Azure é acessível a partir de qualquer aplicação do Microsoft Azure.

Microsoft Azure Redis Cache está disponível nos seguintes camadas:

-   **Básicas** – nó único. Vários tamanhos 53 GB.
-   **Padrão** – dois nós principal/réplica. Vários tamanhos 53 GB. 99,9 SLA %.
-   **Premium** – dois nós principal/réplica com shards até 10. Vários tamanhos de 6 GB para 530 GB (contacte-nos para obter mais). Todas as funcionalidades da camada padrão e mais incluindo o suporte para [Redis cluster](cache-how-to-premium-clustering.md), [Redis persistente](cache-how-to-premium-persistence.md)e [Rede Virtual Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Cada camada difere em termos de funcionalidades e preços. Para obter informações sobre preços, consulte o artigo [Detalhes de preços da Cache][].

Este guia mostra-lhe como utilizar o cliente de [StackExchange.Redis][] utilizando C\# código. Os cenários abrangidos incluam **criar e configurar uma cache**, **configurar os clientes de cache**e **Adicionar e remover objetos da cache de**. Para mais informações sobre a utilização do Azure Redis Cache, consulte a secção [Os passos seguintes][] . Para obter um tutorial passo a passo básicos para construir um MVC ASP.NET web app com Redis Cache, consulte o artigo [como criar uma aplicação Web com Redis Cache](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Introdução ao Azure Redis Cache

Introdução ao Azure Redis Cache é fácil. Para começar, aprovisionar e configurar uma cache. Em seguida, configure os clientes de cache para que podem aceder à cache. Depois dos clientes de cache estão configurados, pode começar a trabalhar com os mesmos.

-   [Criar a cache][]
-   [Configurar os clientes de cache][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Criar uma cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Para aceder a cache após-é criado

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para mais informações sobre como configurar a cache, consulte o artigo [como configurar a Cache de Redis Azure](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Configurar os clientes de cache

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Depois do projeto de cliente é configurado para colocação em cache, pode utilizar técnicas descritas nas secções seguintes para trabalhar com a cache.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Trabalhar com Caches

Os passos nesta secção descrevem como executar tarefas comuns com a Cache.

-   [Ligar para a cache][]
-   [Adicionar e obtém objetos da cache][]
-   [Trabalhar com objetos .NET na cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Ligar para a cache

Para poder trabalhar através de programação com uma cache, precisa de uma referência para a cache. Adicione o seguinte para a parte superior de qualquer ficheiro a partir do qual pretende utilizar o cliente StackExchange.Redis para aceder a uma Cache de Redis Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] O cliente StackExchange.Redis requer .NET Framework 4 ou superior.

A ligação para a Cache do Azure Redis é gerida pela `ConnectionMultiplexer` escolares. Esta classe foi concebido para ser partilhada e reutilizadas em toda a sua aplicação cliente e não precisa de ser criado numa base por operação. 

Para ligar a uma Cache Redis do Azure e ser devolvido uma instância de um ligada `ConnectionMultiplexer`, chamada estática `Connect` método e fase no ponto final de cache e a chave como o exemplo seguinte. Utilize a tecla gerada a partir do Portal do Azure como o parâmetro de palavra-passe.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Aviso: Nunca de credenciais de arquivo no código fonte. Para manter este exemplo simples, posso estou a mostrá-las no código fonte. Consulte o artigo [como cadeias da aplicação e ligação cadeias de trabalho][] para obter informações sobre como armazenar credenciais.

Se não quiser utilizar SSL, quer definir `ssl=false` ou omitir o `ssl` parâmetro.

>[AZURE.NOTE] A porta de que não sejam SSL é desativada por predefinição para novas caches. Para obter instruções sobre como ativar a porta de que não sejam SSL, consulte as [Portas de acesso](cache-configure.md#access-ports)...

Uma abordagem de partilha um `ConnectionMultiplexer` instância na sua aplicação deve ter uma propriedade estática que devolve uma instância ligada, semelhante ao exemplo seguinte. Este procedimento fornece uma forma de tópico seguros de inicialização apenas uma única ligada `ConnectionMultiplexer` instância. Nestes exemplos `abortConnect` está definido como FALSO, o que significa que a chamada vai ter êxito mesmo que não é possível estabelecer uma ligação para a Cache do Azure Redis. Uma das principais funcionalidades do `ConnectionMultiplexer` é que irá restaurar automaticamente conectividade na cache de uma vez o problema de rede ou outras causas são resolvidas.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Para mais informações sobre as opções de configuração de ligação avançadas, consulte o artigo [StackExchange.Redis modelo de configuração][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Assim que a ligação é estabelecida, devolver uma referência para a base de dados de cache redis ao contactar o `ConnectionMultiplexer.GetDatabase` método. O objecto devolvido a partir do `GetDatabase` método é um objeto pass-through lightweight e não precisa de ser armazenado.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Agora que sabe como ligar a uma instância do Azure Redis Cache e devolver uma referência para a base de dados de cache, vamos olhar trabalhar com a cache.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Adicionar e obtém objetos da cache

Itens podem ser armazenados no e obtidos a partir de uma cache utilizando o `StringSet` e `StringGet` métodos.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis armazena a maior parte dos dados como cadeias Redis, mas estas cadeias podem conter vários tipos de dados, incluindo os dados binários série, que podem ser utilizados quando armazenar .NET objectos na cache.

Quando chamar `StringGet`, se existir no objeto, é devolvido, e se não, `null` é devolvido. Neste caso pode obter o valor a partir da origem de dados pretendidos e armazená-lo na cache para utilização posterior. Isto é conhecido como o padrão de anulação da cache.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Para especificar a validade de um item na cache, utilize o `TimeSpan` parâmetro da `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET na cache

Azure Redis Cache pode cache objetos .NET, bem como os tipos de dados primitivos, mas antes de um objeto de .NET pode ser colocadas em cache deve ser serializado. Esta é a responsabilidade do programador da aplicação e apresenta a flexibilidade de programador da escolha do serializador.

Uma forma simple de serializar objectos é utilizar a `JsonConvert` métodos de serialização no [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) e serializar de e para a JSON. O exemplo seguinte mostra um get e conjunto utilizando um `Employee` instância do objeto.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos, siga estas ligações para saber mais sobre a Cache de Redis Azure.

-   Consulte o artigo os fornecedores de ASP.NET para Azure Redis Cache.
    -   [Fornecedor de estado da sessão Redis Azure](cache-aspnet-session-state-provider.md)
    -   [Azure Redis fornecedor de Cache de saída do ASP.NET Cache](cache-aspnet-output-cache-provider.md)
-   [Activar diagnósticos de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que possa [Monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no Portal do Azure e também pode [Transferir e reveja](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) -las utilizando as ferramentas da sua escolha.
-   Consulte a [documentação do cliente StackExchange.Redis cache][].
    -   Azure Redis Cache podem ser acedida a partir de muitos clientes Redis e linguagens de programação. Para mais informações, consulte o artigo [http://redis.io/clients][].
-   Azure Redis Cache podem também ser utilizado com os serviços de terceiros e ferramentas, como Redsmin e Redis Gestor de ambiente de trabalho.
    -   Para mais informações sobre Redsmin, consulte o artigo [como obter uma cadeia de ligação do Azure Redis e utilizá-la com Redsmin][].
    -   Aceder e inspecionar os seus dados no Azure Redis Cache com uma interface gráfica utilizando [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Consulte a documentação [redis][] e leia acerca [redis tipos de dados][] e [uma introdução quinze minutos para Redis tipos de dados][].



<!-- INTRA-TOPIC LINKS -->
[Próximos passos]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Criar a cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurar os clientes de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Ligar para a cache]: #connect-to-cache
[Adicionar e obtém objetos da cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.IO/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Como obter uma cadeia de ligação do Azure Redis e utilizá-la com Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuração de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes do preços em cache]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentação do cliente StackExchange.Redis cache]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis tipos de dados]: http://redis.io/topics/data-types
[uma introdução quinze minutos a Redis tipos de dados]: http://redis.io/topics/data-types-intro

[Como funcionam cadeias da aplicação e cadeias de ligação]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


