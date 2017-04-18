<properties 
    pageTitle="Cache de migrar para o Redis | Microsoft Azure"
    description="Saiba como migrar aplicações de serviço de Cache geridos para Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrar do serviço de Cache geridos para Azure Redis Cache

Migrar as suas aplicações que utilizar o serviço de Cache gerido Azure para Azure Redis Cache pode ser feito com mínimas alterações à aplicação, consoante as funcionalidades de serviço de Cache geridos utilizado pela sua aplicação de colocação em cache. Enquanto as APIs exatamente não são as mesmas são semelhantes e muito do seu código existente que utiliza o serviço de Cache geridos para aceder a uma cache pode ser reutilizado com alterações mínimas. Este tópico mostra como fazer a configuração necessárias e as alterações à aplicação para migrar as suas aplicações de serviço de Cache geridos para utilizar a Cache de Redis Azure e mostra como algumas das funcionalidades do Azure Redis Cache podem ser utilizadas para implementar a funcionalidade da cache de serviço de Cache gerido.

## <a name="migration-steps"></a>Passos de migração

Os passos seguintes são necessários para migrar uma aplicação de serviço de Cache geridos para utilizar a Cache de Redis Azure.

-   Mapear funcionalidades geridas serviço de Cache para Azure Redis Cache
-   Selecione uma oferta de Cache
-   Criar uma Cache
-   Configurar os clientes de Cache
    -   Remover a configuração do serviço de Cache geridos
    -   Configurar um cliente de cache utilizando o pacote de NuGet StackExchange.Redis
-   Migrar código gerido serviço de Cache
    -   Ligar a cache utilizando a classe de ConnectionMultiplexer
    -   Tipos de dados primitivos do Access na cache
    -   Trabalhar com objetos .NET na cache
-   Migrar o estado da sessão ASP.NET e a cache de saída para Azure Redis Cache 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mapear funcionalidades geridas serviço de Cache para Azure Redis Cache

Azure gerido serviço de Cache e Azure Redis Cache são semelhantes mas implementam algumas das suas funcionalidades de diferentes formas. Esta secção descreve alguns das diferenças e fornece orientações sobre como implementar as funcionalidades do serviço de Cache geridos na Cache de Redis Azure.

|Funcionalidade de serviço de Cache gerida|Suporte de serviço de Cache gerido|Suporte de Redis Cache Azure|
|---|---|---|
|Caches com nome|Uma cache predefinido está configurada e, na cache Standard e Premium ofertas, até nove adicionais com o nome caches podem ser configuradas se pretender.|Azure Redis caches tiverem um número configurável de bases de dados (predefinição de 16) que podem ser utilizados para implementar uma funcionalidade semelhante para caches com nome. Para mais informações, consulte o artigo [predefinido Redis configuração do servidor](cache-configure.md#default-redis-server-configuration).|
|Disponibilidade de alta|Fornece elevada disponibilidade para itens na cache as ofertas de cache Standard e Premium. Se forem perdidas devido a uma falha de itens, cópias de segurança dos itens na cache ainda estão disponíveis. Escritas à cache secundária são feitas de forma sincronizada.|Disponibilidade elevada está disponível no ofertas de cache Standard e Premium, que tem uma configuração de principal/réplica duas nó (cada shard na cache de Premium tem um par de principal/réplica). Escritas à réplica são feitas de forma assíncrona. Para mais informações, consulte o artigo [Azure Redis Cache preços](https://azure.microsoft.com/pricing/details/cache/).|
|Notificações|Permite que clientes receber notificações assíncronas quando uma variedade de operações de cache ocorrer numa cache com nome.|Aplicações de cliente podem utilizar Redis pub/sub ou [Keyspace notificações](cache-configure.md#keyspace-notifications-advanced-settings) para alcançar uma funcionalidade semelhante às notificações.|
|Local cache|Armazena uma cópia de objetos em cache localmente no cliente para um acesso rápido muito.|Aplicações de cliente seriam necessário implementar esta funcionalidade utilizando um dicionário ou estrutura de dados semelhantes.|
|Política de eviction|Nenhum ou LRU. A política predefinida é LRU.|Azure Redis Cache suporta as seguintes políticas de eviction: voláteis lru, allkeys lru, voláteis aleatório, allkeys-aleatório voláteis-ttl, noeviction. A política predefinida é voláteis lru. Para mais informações, consulte o artigo [predefinido Redis configuração do servidor](cache-configure.md#default-redis-server-configuration).|
|Política de expiração|A política de expiração predefinida é absoluto e o intervalo de expiração predefinido é dez minutos. Políticas de deslizante e nunca também estão disponíveis.|Por predefinição itens na cache não expiram, mas pode ser configurada uma expiração numa base de escrita por utilizar overloads do conjunto de cache. Para mais informações, consulte o artigo [Adicionar e obter objetos da cache de](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Regiões e etiquetagem|As regiões são subgrupos para itens em cache. Regiões também suportam a anotação de itens em cache com adicionais cadeias descritivas denominadas etiquetas. Regiões suportam a capacidade de executar operações de procura no quaisquer itens etiquetados nesse região. Todos os itens dentro de uma região estão localizados dentro de um único nó do cluster de cache.|Uma cache Redis é composta por um único nó (a não ser Redis cluster é activado) para que o conceito de regiões de serviço de Cache geridos não se aplica. Redis suporta a procura e operações de caracteres universais ao obter teclas para que marcas descritivas podem ser incorporadas dentro de nomes de chave e utilizadas para obter os itens mais tarde. Para obter um exemplo de implementar uma solução de etiquetas de rede utilizando Redis, consulte o artigo [Implementar a cache etiquetagem com Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Serialização|Cache gerido suporta NetDataContractSerializer, BinaryFormatter e a utilização de Serializadores personalizados. A predefinição é NetDataContractSerializer.|Cabe a aplicação de cliente para serializar objetos .NET antes de colocar em cache, com a escolha das serializador por excesso para o programador da aplicação de cliente. Para obter mais informações e exemplos de código, consulte o artigo [trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Emulador em cache | Cache gerido fornece um emulador local cache. | Azure Redis Cache não tem um emulador, mas pode [executar a compilação MSOpenTech do redis-server.exe localmente](cache-faq.md#cache-emulator) para fornecer uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Selecione uma oferta de Cache

Microsoft Azure Redis Cache está disponível nos seguintes camadas:

-   **Básicas** – nó único. Vários tamanhos 53 GB.
-   **Padrão** – dois nós principal/réplica. Vários tamanhos 53 GB. 99,9 SLA %.
-   **Premium** – dois nós principal/réplica com shards até 10. Vários tamanhos de 6 GB para 530 GB (contacte-nos para obter mais). Todas as funcionalidades da camada padrão e mais incluindo o suporte para [Redis cluster](cache-how-to-premium-clustering.md), [Redis persistente](cache-how-to-premium-persistence.md)e [Rede Virtual Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Cada camada difere em termos de funcionalidades e preços. As funcionalidades são abrangidas mais tarde deste guia de e para mais informações sobre preços, consulte os [Detalhes de preços de Cache](https://azure.microsoft.com/pricing/details/cache/).

Ponto de partida para migração é escolha o tamanho que corresponde ao tamanho da cache do serviço de Cache geridos anterior e, em seguida, dimensionar para cima ou para baixo consoante os requisitos da sua aplicação. Para obter mais orientações sobre como escolher a oferta Azure Redis Cache à direita, consulte o artigo [que oferta Redis Cache e o tamanho devo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar uma Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar os clientes de Cache

Depois da cache é criada e configurada, o próximo passo consiste em remover a configuração do serviço de Cache gerido e adicionar a adicionar a configuração do Azure Redis Cache e referências para que os clientes de cache podem aceder a cache.

-   Remover a configuração do serviço de Cache geridos
-   Configurar um cliente de cache utilizando o pacote de NuGet StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração do serviço de Cache geridos

Antes das aplicações de cliente podem ser configuradas para Azure Redis Cache, a configuração do serviço de Cache geridos existente e referências de assemblagem têm de ser removidas ao desinstalar o pacote de gerido NuGet de serviço de Cache.

Para desinstalar o pacote de gerido NuGet de serviço de Cache, com o botão direito do projecto do cliente no **Explorador de soluções** e selecione **Gerir pacotes de NuGet**. Selecione o nó **instalados pacotes** e escreva W**indowsAzure.Caching** na caixa de pacotes instalados de pesquisa. Selecione do **Windows** **Azure Cache** (ou o **Windows** **Azure colocação em cache** dependendo da versão do pacote NuGet), clique em **desinstalar**e, em seguida, clique em **Fechar**.

![Desinstalar o pacote de NuGet de serviço de Cache gerido Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote de NuGet de serviço de Cache gerido remove os conjuntos de serviço de Cache gerido e os movimentos do serviço de Cache geridos na App ou Web. config da aplicação cliente. Uma vez que algumas definições personalizadas não poderão ser removidas quando desinstalar o pacote de NuGet, abra a Web. config ou App e certifique-se de que os seguintes elementos são completamente removidos.

Certifique-se de que o `dataCacheClients` entrada é removida o `configSections` elemento. Não remova todo o `configSections` elemento; remover apenas o `dataCacheClients` entrada, caso esteja presente.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Certifique-se de que o `dataCacheClients` secção é removida. O `dataCacheClients` secção será semelhante ao seguinte exemplo.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Assim que a configuração do serviço de Cache geridos é removida, pode configurar o cliente de cache, tal como descrito na secção seguinte.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar um cliente de cache utilizando o pacote de NuGet StackExchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrar código gerido serviço de Cache

API para o cliente de cache StackExchange.Redis é semelhante ao serviço de Cache gerido. Esta secção fornece uma descrição geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ligar a cache utilizando a classe de ConnectionMultiplexer

No serviço de Cache gerido, ligações para a cache foram efetuadas pela `DataCacheFactory` e `DataCache` aulas. Na Azure Redis Cache, estas ligações são geridas pela `ConnectionMultiplexer` escolares.

Adicione o seguinte utilizando instrução para a parte superior de qualquer ficheiro a partir do qual pretende aceder a cache.

    using StackExchange.Redis
                                
Se não resolver este espaço de nomes, certifique-se de que adicionou o pacote de StackExchange.Redis NuGet conforme descrito no [configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Repare que o cliente StackExchange.Redis requer .NET Framework 4 ou superior.

Para ligar a uma instância do Azure Redis Cache, contacte o estática `ConnectionMultiplexer.Connect` método e passar no ponto final e a chave. Uma abordagem de partilha um `ConnectionMultiplexer` instância na sua aplicação deve ter uma propriedade estática que devolve uma instância ligada, semelhante ao exemplo seguinte. Este procedimento fornece uma forma de tópico seguros de inicialização apenas uma única ligada `ConnectionMultiplexer` instância. Neste exemplo `abortConnect` está definido como FALSO, o que significa que a chamada vai ter êxito mesmo que não é possível estabelecer uma ligação para a cache. Uma das principais funcionalidades do `ConnectionMultiplexer` é que irá restaurar automaticamente conectividade na cache de uma vez o problema de rede ou outras causas são resolvidas.

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

O ponto final de cache, chaves e portas podem ser obtidas junto a pá **Redis Cache** para a instância da cache. Para mais informações, consulte o artigo [Redis Cache propriedades](cache-configure.md#properties).

Assim que a ligação é estabelecida, devolver uma referência para a base de dados de cache Redis ao contactar o `ConnectionMultiplexer.GetDatabase` método. O objecto devolvido a partir do `GetDatabase` método é um objeto pass-through lightweight e não precisa de ser armazenado.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

O cliente StackExchange.Redis utiliza o `RedisKey` e `RedisValue` tipos para aceder a e armazenar itens na cache. Estes tipos de mapear para primitivos mais tipos de idioma, incluindo cadeia e muitas vezes não são utilizados diretamente. Redis cadeias são o tipo de valor Redis mais básico e podem conter vários tipos de dados, incluindo a série sequências binárias, e enquanto diretamente, não poderá utilizar o tipo, que irá utilizar métodos que contêm `String` no nome. Para tipos de dados mais primitivos armazenar e obter itens a partir da cache utilizando o `StringSet` e `StringGet` métodos, a menos que está a armazenar coleções de sites ou outros tipos de dados Redis na cache. 

`StringSet`e `StringGet` são muito semelhantes ao serviço de Cache gerido `Put` e `Get` métodos, com um principais diferença que antes de configurar e obter um objeto de .NET a cache de tem de serializá-la pela primeira vez. 

Quando chamar `StringGet`, se existir no objeto, é devolvido e se não tiver, é devolvido nulo. Neste caso pode obter o valor a partir da origem de dados pretendidos e armazená-lo na cache para utilização posterior. Isto é conhecido como o padrão de anulação da cache.

Para especificar a validade de um item na cache, utilize o `TimeSpan` parâmetro da `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Azure Redis Cache pode trabalhar com objetos .NET, bem como os tipos de dados primitivos, mas antes de um objeto de .NET pode ser colocadas em cache deve ser serializado. Este é a responsabilidade do programador da aplicação. Isto dá a flexibilidade de programador na escolha do serializador. Para obter mais informações e exemplos de código, consulte o artigo [trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrar o estado da sessão ASP.NET e a cache de saída para Azure Redis Cache

Azure Redis Cache tem fornecedores para o estado da sessão ASP.NET e saída da página colocação em cache. Para migrar a aplicação que utiliza as versões de serviço de Cache geridos destes fornecedores, primeiro, remova as secções existentes a partir do seu Web. config e, em seguida, configure as versões do Azure Redis Cache dos fornecedores. Para obter instruções sobre como utilizar os fornecedores de Azure Redis Cache ASP.NET, consulte o artigo [ASP.NET fornecedor de estado de sessão para a Cache de Redis Azure](cache-aspnet-session-state-provider.md) e [ASP.NET fornecedor de Cache de saída para Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Próximos passos

Explore a [documentação do Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) para tutoriais, amostras, vídeos e muito mais.

