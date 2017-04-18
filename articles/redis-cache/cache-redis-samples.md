<properties 
    pageTitle="Exemplos de Redis Cache Azure | Microsoft Azure" 
    description="Saiba como utilizar a Cache de Redis Azure" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Exemplos de Redis Cache Azure 

Este tópico fornece uma lista das amostras do Azure Redis Cache, abrangendo cenários, tais como ligar a uma cache, leitura e escrita dados de e para a partir de uma cache e utilizar os fornecedores de ASP.NET Redis Cache. Algumas das amostras são transferíveis projetos e algumas fornecem orientações passo a passo e incluem fragmentos de código, mas não ligar a um projeto transferível.

## <a name="hello-world-samples"></a>Exemplos de mundo Olá

Os exemplos nesta secção mostram os princípios básicos para ligar a uma instância do Azure Redis Cache e leitura e escrita dados para a cache através de uma variedade de idiomas e Redis clientes.

O exemplo [Olá](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como efetuar várias operações de cache utilizando o cliente do .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

Este exemplo mostra como:

-   Utilizar várias opções de ligação
-   Ler e escrever objetos para e da cache utilizando operações síncronas e assíncronas
-   Utilizar comandos de Redis MGET/MSET para devolver valores de chaves especificadas
-   Efectuar operações transaccionais Redis
-   Trabalhar com listas de Redis e conjuntos de ordenado
-   Armazenar os objetos .NET utilizando JsonConvert Serializadores
-   Utilizar conjuntos Redis para implementar o etiquetagem
-   Trabalhar com Redis Cluster

Para mais informações, consulte a documentação [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) no github e para mais cenários de utilização, consulte os testes de unidade [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) .

[Como utilizar o Azure Redis Cache com Python](cache-python-get-started.md) mostra como começar a Cache de Redis Azure utilizando Python e o cliente [redis copiar](https://github.com/andymccurdy/redis-py) .

[Trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra-lhe uma forma de serializar objetos .NET para que possa escrevê-los para e lê-las a partir de uma instância do Azure Redis Cache. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utilizar Redis Cache como uma escala de saída de inserção para SignalR do ASP.NET

O exemplo de [Utilizar Cache Redis como uma escala de saída de inserção para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) demonstra como pode utilizar o Azure Redis Cache como um SignalR de inserção. Para mais informações acerca de inserção, consulte o artigo [SignalR Scaleout com Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Exemplo de consulta de cliente de Cache de redis

Este exemplo demonstra compara desempenho entre aceder aos dados a partir de uma cache e aceder aos dados a partir do armazenamento persistente. Este exemplo tem dois projetos.

-   [Demonstração como Redis Cache pode melhorar o desempenho ao colocação em cache de dados](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [Propagar a base de dados e a Cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado da sessão de ASP.NET e a cache de saída

O exemplo [Utilizar Cache de Redis Azure para armazenar ASP.NET SessionState e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como utilizar o Azure Redis Cache para armazenar ASP.NET sessão e utilizar os fornecedores de SessionState e OutputCache para Redis de Cache de saída.

## <a name="manage-azure-redis-cache-with-maml"></a>Gerir a Cache do Azure Redis com MAML

O exemplo [Gerir Azure Redis Cache utilizar bibliotecas de gestão do Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) demonstra como pode utilizar bibliotecas de gestão de Azure para gerir - (criar / actualizar / eliminar) a Cache. 

## <a name="custom-monitoring-sample"></a>Personalizar a monitorização de exemplo

O exemplo de [dados Access Redis Cache monitorização](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como pode aceder a dados de monitorização para a Cache do Azure Redis fora do Portal do Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Clonar um estilo de Twitter escrita usando PHP e Redis

O exemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) é Redis Olá mundo. É um clonar de rede social Twitter estilo mínima escrita usando Redis e PHP utilizando o cliente [Predis](https://github.com/nrk/predis) . O código de origem foi concebido para ser muito simples e ao mesmo tempo para mostrar diferentes Redis estruturas de dados.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda

O exemplo de [monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite-lhe monitorizar a largura de banda utilizada no cliente. Medir a largura de banda, execute o exemplo no computador do cliente de cache, efetuar chamadas para a cache e observar a largura de banda comunicada pela amostra de monitor de largura de banda.
