<properties 
    pageTitle="Cache de Azure Redis FAQ | Microsoft Azure" 
    description="Obter informações sobre as respostas a perguntas comuns, padrões e as melhores práticas para a Cache de Redis Azure" 
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
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Cache de Azure Redis perguntas mais frequentes

Saiba as respostas a perguntas comuns, padrões e as melhores práticas para Azure Redis Cache. 


## <a name="what-if-my-question-isnt-answered-here"></a>O que acontece se não for respondida aqui minha pergunta?

Se a sua pergunta não está listada aqui, diga-nos e iremos ajudá-lo a encontrar uma resposta.

-   Pode publicar uma pergunta no [tópico Disqus](#comments) no final deste FAQ e participar com a equipa do Azure Cache e outros membros da Comunidade acerca deste artigo.
-   Para chegar uma audiência maior, pode publicar uma pergunta no [Fórum MSDN do Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e participar com a equipa do Azure Cache e outros membros da Comunidade.
-   Se quiser fazer um pedido de funcionalidade, pode submeter os pedidos e ideias para [Voz do Azure Redis Cache de utilizador](https://feedback.azure.com/forums/169382-cache).
-   Também pode enviar uma mensagem de e-mail-no [Azure Cache externos comentários](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Noções básicas de Redis Cache Azure

As perguntas mais frequentes nesta secção de folha de rosto algumas das noções básicas da Cache de Redis Azure.

-    [O que é a Cache de Redis Azure?](#what-is-azure-redis-cache)
-    [Como posso começar a utilizar com a Cache de Redis Azure?](#how-can-i-get-started-with-azure-redis-cache)

As perguntas mais frequentes seguintes conceitos básicos e perguntas sobre o Azure Redis Cache de folha de rosto e são respondidas de uma das outras secções de perguntas mais frequentes.

-   [O que oferta Redis Cache e o tamanho devo utilizar?](#what-redis-cache-offering-and-size-should-i-use)
-   [Clientes de cache que Redis posso utilizar?](#what-redis-cache-clients-can-i-use)
-   [Existe um emulador para Azure Redis Cache local?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Como monitorizar o estado de funcionamento e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Planeamento perguntas mais frequentes

-   [O que oferta Redis Cache e o tamanho devo utilizar?](#what-redis-cache-offering-and-size-should-i-use)
-   [Desempenho do Azure Redis Cache](#azure-redis-cache-performance)
-   [O que região de localizar meu cache?](#in-what-region-should-i-locate-my-cache)
-   [Como estou faturada para Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
-   [Pode utilizar a Cache de Redis Azure com Azure Government Cloud ou Azure China nuvem?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Perguntas mais frequentes do desenvolvimento

-   [O que fazer as opções de configuração de StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [Clientes de cache que Redis posso utilizar?](#what-redis-cache-clients-can-i-use)
-   [Existe um emulador para Azure Redis Cache local?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Como pode executar os comandos Redis?](#how-can-i-run-redis-commands)
-   [Por que motivo o Azure Redis Cache não tem uma referência de biblioteca de classe MSDN como alguns dos outros serviços Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [Pode utilizar a Cache de Redis Azure como uma cache da sessão PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>FAQ de segurança

-   [Quando devo ativar a porta de que não sejam SSL para ligar à Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>FAQ de produção

-   [Quais são algumas produção das melhores práticas?](#what-are-some-production-best-practices)
-   [Quais são algumas das considerações ao utilizar os comandos comuns do Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [Como referência e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Detalhes importantes sobre o conjunto de threads crescimento](#important-details-about-threadpool-growth)
-   [Ativar GC de servidor obter mais débito no cliente ao utilizar StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorização e resolução de problemas de perguntas mais frequentes

As perguntas mais frequentes nesta secção abrangem monitorização e resolução de problemas de perguntas comuns. Para mais informações sobre como monitorizar e as instâncias do Azure Redis Cache de resolução de problemas, consulte o artigo [como monitorizar a Cache de Redis Azure](cache-how-to-monitor.md) e [como Azure Redis Cache de resolução de problemas](cache-how-to-troubleshoot.md).

-   [Como monitorizar o estado de funcionamento e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [Meu diagnósticos armazenamento conta definições de cache alteradas, o que aconteceu?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [Por que motivo o diagnósticos estão ativados para algumas caches novos mas não outros?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [Por que razão estou a ver os tempos limite?](#why-am-i-seeing-timeouts)
-   [Por que motivo o meu cliente foi desligada da cache de?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Perguntas mais frequentes do prévia oferta de Cache

-   [Qual oferta Azure Cache é ideal para mim?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>O que é a Cache de Redis Azure?

Azure Redis Cache baseia-se a origem-abrir popular [Redis cache](http://redis.io). -Dá-lhe acesso a uma segura, dedicada Redis cache, gerida pela Microsoft e acessível a partir de qualquer aplicação do Azure. Para obter uma descrição mais detalhada, consulte a página de produto do [Azure Redis Cache](https://azure.microsoft.com/services/cache/) no Azure.com.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Como posso começar a utilizar com a Cache de Redis Azure?

Existem várias formas, que pode começar a trabalhar com a Cache de Redis Azure.

-    Pode dar saída um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)e [Python](cache-python-get-started.md). 
-    Pode ver [como criar alto desempenho aplicações utilizando Microsoft Azure Redis Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Pode consultar a documentação do cliente para os clientes que corresponda ao idioma do desenvolvimento do seu projeto para ver como utilizar Redis. Existem muitos clientes Redis que podem ser utilizados com Azure Redis Cache. Para obter uma lista de clientes Redis, consulte o artigo [http://redis.io/clients](http://redis.io/clients).


Se ainda não tiver uma conta Azure, pode:

-    [Abrir uma conta Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obter créditos que podem ser utilizados para experimentar a serviços Azure pagos. Mesmo depois de créditos são utilizados para cima, pode manter a conta e utilização das funcionalidades e serviços Azure gratuitos.
-    [Ativar o Visual Studio benefícios de subscritor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>O que oferta Redis Cache e o tamanho devo utilizar?
Cada oferta Azure Redis Cache fornece níveis diferentes de **tamanho**, **largura de banda**, **elevada disponibilidade**e **SLA** opções.

Seguem-se considerações para escolher uma oferta de Cache.

-   **Memória**: camadas de básicas e padrão oferecem 250 MB – 53 GB. A camada Premium oferece 530 GB com mais disponíveis [no pedido](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para mais informações, consulte o artigo [Azure Redis Cache preços](https://azure.microsoft.com/pricing/details/cache/).
-   **O desempenho da rede**: Se tiver uma carga de trabalho que necessita de alto débito a camada Premium oferece mais largura de banda comparados com padrão ou Basic. Também dentro de cada camada maiores caches de tamanhos tem mais largura de banda devido a VM subjacente que aloja a cache. Consulte a [tabela seguinte](#cache-performance) para obter mais informações.
-   **Débito**: camada de Premium oferece o máximo débito disponível. Se o servidor de cache ou o cliente atingir os limites de largura de banda, receberá tempos limite do lado do cliente. Para obter mais informações, consulte a tabela seguinte.
-   **Disponibilidade de alta/SLA**: Azure Redis Cache garante que uma cache padrão/Premium estarão disponível, pelo menos, 99,9% das vezes. Para saber mais sobre os nossos SLA, consulte o artigo [Azure Redis Cache preços](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA apenas abrange conectividade com os pontos finais da Cache. O SLA não aborda a proteção contra perda de dados. Recomendamos que utilize a funcionalidade de persistente Redis dados na camada Premium para aumentar RDP contra a perda de dados.
-   **Redis recorrência de dados**: camada de Premium permite-lhe manter os dados de cache numa conta de armazenamento do Windows Azure. Na cache de Basic/padrão todos os dados são armazenados apenas na memória. Em caso de infraestrutura subjacente não existem problemas podem ser possível perda de dados. Recomendamos que utilize a funcionalidade de persistente Redis dados na camada Premium para aumentar RDP contra a perda de dados. Azure Redis Cache oferece RDB e AOF (brevemente) opções no Redis persistente. Para mais informações, consulte o artigo [como configurar persistente para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).
-   **Redis Cluster**: para criar coloca em cache maior do que 53 GB ou para dados shard através de vários Redis nós, pode utilizar Redis clusters, que se encontra disponível na camada Premium. Cada nó é constituída por um par de cache principal/réplica para elevada disponibilidade. Para mais informações, consulte o artigo [como configurar clusters para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).
-   **Segurança melhorada e isolamento de rede**: implementação do Azure Virtual rede (VNET) fornece segurança melhorada e isolamento para Azure Redis Cache, bem como sub-redes, políticas de controlo de acesso, e outras funcionalidades novas para restringem o acesso. Para mais informações, consulte o artigo [como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).
-   **Configurar Redis**: em camadas padrão tanto Premium, pode configurar Redis para Keyspace notificações.
-   **Número máximo de ligações de cliente**: camada de Premium oferece o número máximo de clientes que pode ligar a Redis, com um número mais alto de ligações para maiores caches médias. [Please referir-se para a página comparar para obter detalhes](https://azure.microsoft.com/pricing/details/cache/).
-   **Core dedicada para Redis Server**: na camada Premium todos os tamanhos de cache tem um núcleo dedicado para Redis. No Basic/padrão de camadas de C1 Dimensionar em acima tem um núcleo dedicado para Redis server.
-   **Redis é por tópicos único** por isso, está a ter mais do que dois núcleos não fornece benefícios adicionais sobre ter núcleos apenas dois, mas normalmente, os tamanhos VM maiores têm mais largura de banda de tamanho mais pequeno. Se o servidor de cache ou o cliente atingir os limites de largura de banda, em seguida, receberá tempos limite do lado do cliente.
-   **Melhorias de desempenho**: Caches na camada Premium são implementados em hardware que possui processadores mais rápidos e oferece-lhe um melhor desempenho em comparação comparado a camada Basic ou padrão. Premium camada Caches tem débito superior e inferiores latências.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Desempenho do Azure Redis Cache

A tabela seguinte mostra os valores de largura de banda máxima observados ao testar vários tamanhos do padrão e Premium coloca em cache utilizando `redis-benchmark.exe` a partir de um VM Iaas contra o ponto final Azure Redis Cache. Note que não são garante estes valores e não haja nenhuma SLA para estes números, mas deve ser típica. Deve ser carregado teste a sua própria aplicação para determinar o tamanho da cache direita para a sua aplicação.

A partir desta tabela podemos pode desenhar seguintes conclusões.

-   Débito para as caches que têm o mesmo tamanho é maior na camada Premium em comparação com a camada padrão. Por exemplo, com uma Cache GB 6, débito de P1 é 140K RPS em comparação com 49 K para C3.
-   Com Redis clusters, débito aumenta linear, à medida que aumenta o número de shards (nós) no cluster. Por exemplo, se criar um cluster de E4 de 10 shards, em seguida, o débito disponível é 250K * 10 = milhões de 2,5 RPS.
-   Débito para maiores tamanhos de chave é mais alto na camada Premium em comparação com a camada padrão.

| Preços de camadas             | Tamanho   | Núcleos CPU | Largura de banda disponível                                    | Tamanho da chave 1 KB                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Tamanhos de cache padrão** |        |           | **Megabits por sec (Mb/s) / Megabytes por sec (MB/s)** | **Pedidos de por segundo (RPS)**            |
| C0                       | 250 MB | Partilhado    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GB   | 1         | 100 / 12,5                                             | 12200                                    |
| C2                       | 2,5 GB | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GB   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GB  | 2         | 500 / 62.5                                             | 61000                                    |
| C5                       | 26 GB  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | 53 GB  | 8         | 2000 / 250                                             | 150000                                   |
| **Tamanhos de cache Premium**  |        | **Núcleos CPU por shard**  |                                         | **Pedidos por segundo (RPS) por shard** |
| P1                       | 6 GB   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | 26 GB  | 4         | 2000 / 250                                             | 220000                                   |
| E4                       | 53 GB  | 8         | 4000 / 500                                             | 250000                                   |


Para obter instruções sobre como transferir tais como as ferramentas de Redis `redis-benchmark.exe`, consulte o artigo o [como executar Redis comandos?](#cache-commands) secção.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>O que região de localizar meu cache?

Para obter o melhor desempenho e mais baixa latência, localize a Cache do Azure Redis na mesma região como a sua aplicação de cliente da cache.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>Como estou faturada para Azure Redis Cache?

Preços de Redis Cache Azure são [aqui](https://azure.microsoft.com/pricing/details/cache/). A página comparar lista preços como uma taxa de hora a hora. Caches são faturadas numa base por minuto da hora que é criada a cache até à hora que é eliminada uma cache de. Não existe nenhuma opção para parar ou interromper a faturação de uma cache.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>Pode utilizar a Cache de Redis Azure com Azure Government Cloud ou Azure China nuvem?

Sim, Azure Redis Cache está disponível no Azure Government Cloud e Azure China nuvem. Note que os URLs para aceder a e gerir a Cache de Redis Azure são diferentes na nuvem do Azure administração pública e Azure China nuvem comparada com a nuvem público Azure. Para mais informações sobre considerações sobre a utilização da Cache de Redis Azure com Azure Government Cloud e Azure China nuvem, consulte o artigo [Azure administração pública bases de dados - Azure Redis Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache) e [Azure China Cloud - Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/).

Para obter informações sobre como utilizar o Azure Redis Cache com PowerShell no Azure Government Cloud e Azure China nuvem, consulte o artigo [como ligar ao Azure Government Cloud ou Azure China nuvem](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que fazer as opções de configuração de StackExchange.Redis?

StackExchange.Redis tem várias opções. Esta secção fala sobre algumas das definições comuns. Para obter informações mais detalhadas sobre as opções de StackExchange.Redis, consulte o artigo [StackExchange.Redis configuração](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descrição|Recomendação
---|---|---
AbortOnConnectFail|Quando definido como VERDADEIRO, a ligação não irão restabelecer a ligação após uma falha de rede.|Definido como falso e permita que StackExchange.Redis reestabelecer ligação automaticamente.
ConnectRetry|Ligue o número de vezes a repetir tentativas de ligação durante inicial.| Veja as notas seguintes para obter orientações. |
ConnectTimeout|Limite de tempo em ms para ligar operações.| Veja as notas seguintes para obter orientações. |

Na maioria dos casos, os valores predefinidos do cliente são suficientes. Pode ajustar as opções com base na sua carga de trabalho.

-   **Número de tentativas**
    -   Para ConnectRetry e ConnectTimeout as informações gerais está a falhar rapidamente e tente novamente. Isto se baseia-se a sua carga de trabalho e quanto tempo no média demora para o seu cliente para emitir um comando Redis e recebe uma resposta.
    -   Permitir que StackExchange.Redis voltar a ligar automaticamente em vez de verificar o estado de ligação e si próprio reestabelecer ligação. **Evite utilizando a propriedade ConnectionMultiplexer.IsConnected**.
    -   Snowballing -, por vezes, poderá ocorrer um problema onde está a tentar aceder e este snowballs e nunca recupera. Neste caso, deverá tomar em consideração utilizando um algoritmo de repetição de duplicar exponencial, tal como descrito em [repetir informações gerais](best-practices-retry-general.md) publicados pelo grupo Microsoft Patterns e práticas.
-   **Valores de tempo limite**
    -   Considere a sua carga de trabalho e defina os valores em conformidade. Se estiver a armazenar valores grandes, defina o limite de tempo para um valor superior.
    -   Definir `AbortOnConnectFail` para FALSO e permitir que StackExchange.Redis restabelecer a ligação por si.
    -   Utilize uma única ocorrência de ConnectionMultiplexer para a aplicação. Pode utilizar uma LazyConnection para criar uma única ocorrência que é devolvida por uma propriedade de ligação, tal como apresentado em [ligar à cache utilizando a classe de ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Definir o `ConnectionMultiplexer.ClientName` propriedade para um nome exclusivo da instância aplicação para fins de diagnóstico.
    -   Utilizar múltiplos `ConnectionMultiplexer` instâncias para das cargas de trabalho personalizadas.
    -   Pode seguir este modelo se tiver carga variada na sua aplicação. Por exemplo:
    -   Pode ter um multiplexador para lidar com as teclas de grandes dimensões.
    -   Pode ter um multiplexador para lidar com as teclas pequenas.
    -   Pode definir valores diferentes para tempos limite de ligação e a lógica de repetição para cada ConnectionMultiplexer que utiliza.
    -   Definir o `ClientName` propriedade em cada Multiplexador para o ajudar com diagnósticos.
    -   Isto irá originar a mais optimizada latência por `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Clientes de cache que Redis posso utilizar?

Uma das melhores coisas sobre Redis é que não existem muitos clientes vários idiomas de desenvolvimento diferentes de suporte. Para obter uma lista atual de clientes, consulte o artigo [Redis clientes](http://redis.io/clients). Para a tutoriais que abrangem vários idiomas diferentes e clientes, veja [como utilizar a Cache de Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique no idioma pretendido a partir do comutador de idioma na parte superior deste artigo.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Existe um emulador para Azure Redis Cache local?

Não existe nenhuma emulador para Azure Redis Cache local, mas pode executar a versão de MSOpenTech da redis server.exe a partir do [Redis ferramentas de linha de comandos](https://github.com/MSOpenTech/redis/releases/) no computador local e ligar para obter uma experiência semelhante ao emulador de local cache, conforme mostrado no seguinte exemplo.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Opcionalmente, pode configurar um ficheiro de [redis.conf](http://redis.io/topics/config) mais atentamente corresponder as [definições de cache predefinido](cache-configure.md#default-redis-server-configuration) para a sua Azure Redis Cache online se pretender.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>Como pode executar os comandos Redis?

Pode utilizar qualquer um dos comandos listados no [Redis comandos](http://redis.io/commands#) exceto os comandos listados no [Redis comandos não suportados no Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para executar Redis comandos tem várias opções.

-   Se tiver uma cache padrão ou Premium, pode executar comandos Redis utilizando a [Consola Redis](cache-configure.md#redis-console). Isto fornece uma forma segura para executar Redis comandos no portal do Azure.
-   Também pode utilizar as ferramentas de linha de comandos Redis. Para utilizá-las, execute os passos seguintes.
-   Transferir o [Redis ferramentas de linha de comandos](https://github.com/MSOpenTech/redis/releases/).
-   Ligar a utilizando a cache `redis-cli.exe`. Passar no ponto final de cache utilizando que o -h mudar e a chave utilizando - a conforme mostrado no seguinte exemplo.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Nota que as ferramentas de linha de comandos Redis não funcionam com a porta de SSL, mas pode utilizar um utilitário tais como `stunnel` segura ligar as ferramentas à porta de SSL ao seguir as instruções na mensagem no blogue [Anunciar o ASP.NET sessão Estado fornecedor para Redis versão de pré-visualização](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Por que motivo o Azure Redis Cache não tem uma referência de biblioteca de classe MSDN como alguns dos outros serviços Azure?

Microsoft Azure Redis Cache baseia-se a origem de abrir popular Redis Cache e pode ser acedido por uma grande variedade de [Redis clientes](http://redis.io/clients) que estão disponíveis para muitas linguagens de programação. Cada cliente tem as suas próprias API que faz com que as chamadas para a instância de cache de Redis utilizando [Redis comandos](http://redis.io/commands).

Uma vez que cada cliente for diferente, não existe não uma referência de classe centralizada no MSDN; em vez disso, cada cliente mantém as suas próprias documentação de referência. Para além da documentação de referência, existem várias tutoriais que mostra como começar a utilizar idiomas diferentes e clientes de cache da Cache da Redis Azure. Para aceder a estes tutoriais, consulte o artigo [como utilizar o Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) e clique no idioma pretendido a partir do comutador de idioma na parte superior deste artigo.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Pode utilizar a Cache de Redis Azure como uma cache da sessão PHP?

Sim, para utilizar a Cache de Redis Azure como uma cache da sessão PHP, especifique a cadeia de ligação para a instância do Azure Redis Cache no `session.save_path`.

>[AZURE.IMPORTANT] Quando utilizar o Azure Redis Cache como uma cache da sessão PHP, tem de URL descodificar a chave de segurança utilizada para ligar para a cache, conforme mostrado no seguinte exemplo.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Se a chave não for codificada de URL, poderá receber uma exceção semelhante ao seguinte:`Failed to parse session.save_path`

Para mais informações sobre como utilizar Redis Cache como uma cache da sessão PHP com o cliente de PhpRedis, consulte o artigo [processador de PHP sessão](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando devo ativar a porta de que não sejam SSL para ligar à Redis?

Redis servidor não suporta SSL terminar a caixa de, mas não do Azure Redis Cache. Se estiver a ligar a Cache de Redis Azure e o cliente suporta SSL, como StackExchange.Redis, deve utilizar SSL.

Tenha em atenção que a porta de que não sejam SSL é desactivada por predefinição para novas instâncias do Azure Redis Cache. Se o cliente não suporta SSL, em seguida, tem de ativar a porta de que não sejam SSL ao seguir as instruções na secção de [portas de acesso](cache-configure.md#access-ports) do artigo [Configurar uma cache no Azure Redis Cache](cache-configure.md) .

Redis ferramentas tais como `redis-cli` não funcionam com a porta de SSL, mas pode utilizar um utilitário tais como `stunnel` segura ligar as ferramentas à porta de SSL ao seguir as instruções na mensagem no blogue [Anunciar o ASP.NET sessão Estado fornecedor para Redis versão de pré-visualização](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Para obter instruções sobre como transferir as ferramentas de Redis, consulte o artigo o [como executar Redis comandos?](#cache-commands) secção.



### <a name="what-are-some-production-best-practices"></a>Quais são algumas produção das melhores práticas?

-   [Práticas recomendadas StackExchange.Redis](#stackexchangeredis-best-practices)
-   [Configuração e conceitos](#configuration-and-concepts)
-   [Testes de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Práticas recomendadas StackExchange.Redis

-   Definir `AbortConnect` para FALSO, em seguida, deixe ConnectionMultiplexer reestabelecer ligação automaticamente. [Consulte aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Reutilizar a ConnectionMultiplexer - não fazer para criar um novo para cada pedido. O `Lazy<ConnectionMultiplexer>` padrão [é mostrado aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é vivamente recomendado.
-   Redis funciona melhor com valores inferiores, por isso, considere picadura o maiores dados em múltiplas teclas. [Redis debate](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb é considerada "grande". Leia [Este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para um problema de exemplo que pode ser causado por valores de grandes dimensões.
-   Configure as [definições do conjunto de threads](#important-details-about-threadpool-growth) para evitar tempos limite.
-   Utilize, pelo menos, o connectTimeout predefinido de 5 segundos. Isto seria dar StackExchange.Redis tempo suficiente restabelecer a ligação, em caso de uma blip de rede.
-   Tenha em atenção os custos de desempenho associados operações diferentes que estiver a executar. Por exemplo, o `KEYS` comando é uma operação de O(n) e deve ser evitado. O [redis.io site](http://redis.io/commands/) tem detalhes à volta a complexidade de tempo para cada operação que suporta. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos

-   Utilize padrão ou camada Premium para sistemas de produção. A camada básicas é um sistema de único nó com sem replicação de dados e não SLA. Além disso, utilize, pelo menos, uma cache C1. C0 caches destinam-se realmente a cenários de teste/Dev Center simples.
-   Lembre-se de que Redis é um arquivo de dados **Na memória** . Leia [Este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que esteja ciente de cenários onde pode ocorrer a perda de dados.
-   Desenvolva o seu sistema de forma a que possa tratar ligação sinais [devido a aplicação de patches e activação pós-falha](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testes de desempenho

-   Iniciar utilizando `redis-benchmark.exe` obter um funcionalidade para débito possível antes de escrever o seu próprio desempenho testa. Tenha em atenção que referência redis não suporta SSL, para que irá tem de [Ativar a porta SSL não através do portal do Azure](cache-configure.md#access-ports) antes de executar o teste. Para obter exemplos, consulte o artigo [como posso de referência e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   O cliente VM utilizado para testar a ligação deve estar na mesma região como a instância de cache Redis.
-   Recomendamos que utilize Dv2 VM série para o seu cliente, tal como terem um melhor hardware e irá dar-os melhores resultados.
-   Certifique-se do seu cliente VM escolher tem, pelo menos, muito computação e capacidade de largura de banda como a cache estiver a testar. 
-   Ativar VRSS no computador do cliente se estiver a trabalhar no Windows. [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
-   Instâncias de Redis camada Premium irão ter melhor e de rede latência débito porque estão a ser executados em hardware melhor para CPU e de rede.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao utilizar os comandos comuns do Redis?

-   Determinados comandos Redis que demorar muito tempo a concluir sem compreender o impacto destes comandos não deve ser executado.
    -   Por exemplo, não são executadas o comando de [teclas de](http://redis.io/commands/keys) produção como poderá demorar muito tempo a devolver consoante o número de teclas. Redis é um servidor por tópicos único e processa comandos, um de cada vez. Se tiver outros comandos emitidos depois de teclas, estes serão não processados até Redis processa o comando de teclas. O [redis.io site](http://redis.io/commands/) tem detalhes à volta a complexidade de tempo para cada operação que suporta. Clique em cada comando para ver a complexidade para cada operação.
-   Tamanhos de chave - devo utilizar/valores de chave pequenos ou grandes/valores de chave? Em geral depende do cenário. Se o seu cenário requer chaves maiores, em seguida, pode ajustar o ConnectionTimeout e volte a tentar valores e ajustar a lógica de repetição. A partir de uma perspetiva de servidor de Redis valores inferiores são observados para ter um melhor desempenho.
-   Isto significa que não pode armazenar valores maiores Redis; tem de ser em atenção as seguintes considerações. Latências serão mais altos. Se tiver um conjunto de dados que estão maiores e outra que é mais pequeno, pode utilizar várias instâncias de ConnectionMultiplexer, cada configurados com um conjunto diferente de valores de tempo de espera e volte a tentar, tal como descrito na secção [o que as opções de configuração de StackExchange.Redis fazer](#cache-configuration) anterior.



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como referência e testar o desempenho da minha cache?

-   [Activar diagnósticos de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que possa [Monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal do Azure e também pode [Transferir e reveja](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) -las utilizando as ferramentas da sua escolha.
-   Pode utilizar redis benchmark.exe para carregar o teste seu servidor de Redis.
-   Certifique-se de que a carga de testes cliente e a cache de Redis estão na mesma região.
-   Utilize redis cli.exe e monitorizar a cache utilizando o comando de informações.
-   Se a sua carga está a causar fragmentação de memória alta, em seguida, é deve Dimensionar para cima para um tamanho de cache maior.
-   Para obter instruções sobre como transferir as ferramentas de Redis, consulte o artigo o [como executar Redis comandos?](#cache-commands) secção.

Segue-se um exemplo de utilização de redis benchmark.exe. Para obter resultados precisos, execute este comando a partir de uma VM na mesma região como a cache.

-   Teste conduta definir pedidos utilizando uma carga de útil k 1

    redis benchmark.exe - h **yourcache**. redis.cache.windows.net - um **yourAccesskey** -t definir 1024 - P 50 - n 1000000 -d
    
-   Teste conduta obter pedidos utilizando uma carga de útil k 1. 
    Nota: Execute o conjunto de testar acima indicado pela primeira vez para preencher a cache
    
    redis benchmark.exe - h **yourcache**. redis.cache.windows.net - um **yourAccesskey** -t obter -n 1000000 - d 1024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o conjunto de threads crescimento

O conjunto de threads CLR inclui dois tipos de threads - "Trabalhador" e "Porta de conclusão e/s" (também conhecidos por IOCP) threads. 

-   Threads de trabalho são utilizadas quando para elementos como processamento `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)` métodos. Estes threads também são utilizados por vários componentes no CLR quando necessita de trabalho ocorrer um tópico de fundo.
-   IOCP threads são utilizadas quando IO assíncrona acontece (por exemplo, ler da rede). 

O conjunto de threads fornece novo threads de trabalho ou threads de conclusão e/s a pedido (sem qualquer limitação) até atingir a definição "Mínimo" para cada tipo de tópico. Por predefinição, o número mínimo de threads está definido para o número de processadores num sistema. 

Assim que o número de existente (ocupado) threads acertos que será o número de threads, o conjunto de threads "mínimo" borboleta a velocidade com que está injete threads novos para um tópico por 500 milissegundos. Isto significa que se o sistema obtém uma rajada do trabalho necessário especificá-las um tópico IOCP, processará que funcionam muito rapidamente. No entanto, se a rajada do trabalho é mais do que a definição "Mínimo" configurada, haverá algumas atraso no processamento algum do trabalho, tal como o conjunto de threads aguarda para um dos dois procedimentos para ocorrer.

1. Um tópico existente torna-se livre para processar o trabalho.
1. Sem tópico existente torna-se livre para 500ms, para que é criado um novo tópico.

Basicamente, significa que quando o número de threads ocupados for maior que threads mínimo, estiver provavelmente a pagar um atraso de 500ms antes do tráfego de rede é processado pela aplicação. Além disso, é importante ter em atenção que, quando um tópico existente permanece inactivo durante mais de 15 segundos (com base no não se esqueça), será possível limpar e este ciclo de crescimento e redução pode repetir.

Se olharmos para uma mensagem de erro de exemplo a partir do StackExchange.Redis (compilação 1.0.450 ou posterior), irá ver este imprime agora estatísticas do conjunto de threads (consulte IOCP e TRABALHADOR detalhes abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo acima, pode ver que para tópico IOCP existem 6 threads ocupados e o sistema estiver configurado para permitir 4 threads mínimos. Neste caso, o cliente seria provavelmente viu duas 500 ms atrasos porque 6 > 4.

Tenha em atenção que StackExchange.Redis pode acertar tempos limite se crescimento de threads IOCP ou TRABALHADOR obtém limitado.

### <a name="recommendation"></a>Recomendação

Dada esta informação, é fortemente recomendado que os clientes definido o valor mínimo configuração para threads IOCP e TRABALHADOR para algo maior que o valor predefinido. Não é possível apresentamos generalista orientações sobre o que este valor deve ser porque o valor direito de uma única aplicação irá ser demasiado alta/baixa para outra aplicação. Esta definição também pode afetar o desempenho do outras partes do complicada aplicações, para que precisa ajustar esta definição para as suas necessidades específicas de cada cliente. Um bom local de partida é 200 ou 300, em seguida, teste e aperfeiçoar conforme necessário.

Como configurar esta definição:

-   No ASP.NET, utilize a [definição de configuração de "minIoThreads"][] no `<processModel>` elemento de configuração na Web. config. Se estiver a executar dentro de Web sites do Azure, esta definição não é exposta as opções de configuração. No entanto, ainda deverá conseguir definir esta através de programação (ver abaixo) a partir do seu método de Application_Start global.asax.cs.

> **Nota importante:** o valor especificado no elemento configuração é uma definição de *-core* . Por exemplo, se tiver uma máquina de 4 core e pretender que a definição de minIOThreads para ser 200 o tempo de execução, utilizaria `<processModel minIoThreads="50"/>`.

-   Fora do ASP.NET, utilize o [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Ativar GC de servidor obter mais débito no cliente ao utilizar StackExchange.Redis

Ativar GC do servidor pode otimizar o cliente e forneça melhor desempenho e débito ao utilizar StackExchange.Redis. Para mais informações sobre servidor GC e como ativá-lo, consulte os artigos seguintes.

-   [Para ativar GC de servidor](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Noções básicas de recolha de lixo](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Recolha de lixo e desempenho](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorizar o estado de funcionamento e o desempenho do meu cache?

Instâncias de Cache Redis do Microsoft Azure podem ser monitorizadas no [portal do Azure](https://portal.azure.com). Pode ver métricas, afixar gráficos de métricas para o Startboard, personalizar o intervalo de data e hora de monitorização de gráficos, adicionar e remover métricas dos gráficos e definir alertas quando forem cumpridas determinadas condições. Para mais informações, consulte o artigo [Monitor Azure Redis Cache](cache-how-to-monitor.md).

Secção de **suporte + resolução de problemas** da pá **Definições** da Cache de Redis também contém várias ferramentas para o seu caches de resolução de problemas e monitorização. 

-   **Resolução de problemas** fornece informações sobre problemas e estratégias para os resolver comuns.
-   **Registos de auditoria** fornece informações sobre executadas na cache de ações. Também pode utilizar a filtragem para expandir a esta vista para incluir outros recursos.
-   **Estado de funcionamento do recurso** está a observar seu recurso e mostra-lhe se está a funcionar como esperado. Para mais informações sobre o serviço de estado de funcionamento do Azure recurso, consulte o artigo [Descrição geral de estado de funcionamento do Azure recurso](../resource-health/resource-health-overview.md).
-   **Novo pedido de suporte** fornece opções para abrir um pedido de suporte para a sua cache.

Estas ferramentas permitem-lhe monitorizar o estado de funcionamento do seu instâncias de Cache Redis do Azure e ajudam a gerir as suas aplicações de colocação em cache. Para mais informações, consulte o artigo [de suporte e resolução de problemas de definições](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>Meu diagnósticos armazenamento conta definições de cache alteradas, o que aconteceu?

Caches na mesma região e subscrição partilham as mesmas definições de armazenamento de diagnóstico e, se a configuração é alterados (diagnósticos activado/desactivado ou alterar a conta de armazenamento) aplica-se ao todas as caches a subscrição que estão nesse região. Se as definições de diagnóstico para a sua cache foram alteradas, verifique se as definições de diagnóstico para a cache de outro na mesma subscrição e região foram alteradas. Uma forma de verificar é ver os registos de auditoria para a sua cache para um `Write DiagnosticSettings` evento. Para mais informações sobre como trabalhar com registos de auditoria, consulte o artigo [Ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) e de [auditoria operações com o Gestor de recursos](../resource-group-audit.md). Para mais informações sobre como monitorizar eventos de Azure Redis Cache, consulte o artigo [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>Por que motivo o diagnósticos estão ativados para algumas caches novos mas não outros?

Caches na mesma região e subscrição partilham as mesmas definições de armazenamento de diagnóstico. Se criar uma nova cache na mesma subscrição como cache outra que tem diagnósticos ativados e região, diagnósticos está ativado na cache de novo utilizando as mesmas definições.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>Por que razão estou a ver os tempos limite?

Tempos limite ocorrer no cliente que utilizar para falar com Redis. A maior parte Redis server não não tempo limite. Quando um comando é enviado para o servidor de Redis, o comando ficar na fila de e servidor Redis eventualmente escolhe o comando e executa-lo. No entanto o cliente pode o tempo durante este processo e se que faz uma exceção é elevado no lado chamado. Para mais informações sobre resolução de problemas de tempo de espera, consulte o artigo [resolução de problemas de lado do cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [StackExchange.Redis tempo limite exceções](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que motivo o meu cliente foi desligada da cache de?

Seguem-se alguma razão comuns para desligar uma cache.

-   Faz com que o lado do cliente
    -   A aplicação de cliente foi novamente implementada.
    -   A aplicação de cliente executar uma operação de dimensionamento.
        -   No caso de serviços em nuvem ou Web Apps, isto pode ser devido a escala automática.
    -   Alteradas da camada de rede do lado do cliente.
    -   Ocorreram erros breves ao cliente ou em nós de rede entre o cliente e servidor.
    -   Os limites do limiar de largura de banda foram atingidos.
    -   CPU vinculado operações demorou demasiado tempo a concluir.
-   Faz com que o lado do servidor
    -   Na cache padrão perguntar se, o serviço de Cache de Redis Azure iniciados por um falha com sobre a partir do nó principal para o nó secundário.
    -   Azure foi aplicação de patches a instância onde foi implementada a cache
        -   Isto pode ser existência de actualizações de servidor de Redis ou manutenção VM geral.







### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual oferta Azure Cache é ideal para mim?

>[AZURE.IMPORTANT]Por último ano [anúncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), serviço de serviço de Cache gerido Azure e Azure na função Cache será de ser foi removido 30 de Novembro de 2016. A nossa recomendação é utilizar [A Cache de Redis Azure](https://azure.microsoft.com/services/cache/). Para obter informações sobre como migrar, consulte o artigo [migrar a partir do serviço de Cache geridos Azure Redis cache](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Cache de Azure Redis
Azure Redis Cache é disponibilizado no tamanhos 53 GB e tem uma disponibilidade SLA de 99,9%. A nova [camada premium](cache-premium-tier-intro.md) oferece tamanhos até 530 GB e suporte para clusters, VNET e persistente, com um SLA 99,9%.

Azure Redis Cache dá clientes a capacidade de utilizar uma cache de Redis segura, dedicada, gerida pela Microsoft. Com esta oferta obter tirar partido das conjunto de funcionalidades avançada e ecossistema fornecida pela Redis e fiável alojamento e monitorização da Microsoft.

Ao contrário caches tradicionais que lidar apenas com pares valor de chave, Redis é populares para suas altamente performant os tipos de dados. Redis também suporta a executar operações atómica nestes tipos, como acrescentar a uma cadeia; incrementar o valor num hash; conduza a uma lista; computação conjunto interseção, União e diferença; ou obter o membro com uma classificação mais elevada num conjunto ordenado. Outras funcionalidades incluem suporte para transações, pub/sub, Lua scripting, teclas com uma limitada time-para-live e as definições de configuração para tornar Redis comportam-se mais como uma cache tradicional.

Outro aspecto chave para o sucesso Redis é ecossistema saudável, fascinantes abrir origem compilado à volta do mesmo. Isto é refletido no conjunto diversificado de clientes Redis disponíveis em vários idiomas. Isto permite-lhe ser utilizado por praticamente qualquer carga de trabalho que iria cria dentro do Azure. 

Para mais informações sobre como começar a Cache de Redis Azure, consulte o artigo [como utilizar Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) e [documentação do Azure Redis Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="managed-cache-service"></a>Serviço de Cache gerido
[Geridas Cache serviço está definido para ser foi removida 30 de Novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>Na função Cache
[Na função Cache está definido para ser foi removida 30 de Novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[definição de configuração de "minIoThreads"]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
