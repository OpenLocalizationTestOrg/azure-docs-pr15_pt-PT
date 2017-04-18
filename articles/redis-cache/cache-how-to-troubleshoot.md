<properties 
    pageTitle="Como resolver problemas de Azure Redis Cache | Microsoft Azure" 
    description="Saiba como resolver problemas comuns com Azure Redis Cache." 
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Como resolver problemas de Cache Redis do Azure

Este artigo fornece orientações para as seguintes categorias de problemas do Azure Redis Cache de resolução de problemas.

-   [Resolução de problemas de lado do cliente](#client-side-troubleshooting) - esta secção fornece diretrizes sobre identificar e resolver problemas causados pela aplicação estabelecer ligação ao Azure Redis Cache.
-   [Resolução de problemas de lado do servidor](#server-side-troubleshooting) - esta secção fornece diretrizes sobre identificar e resolver problemas causados do lado do servidor de Azure Redis Cache.
-   [Exceções de tempo limite StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - esta secção fornece informações sobre resolução de problemas quando utilizar o cliente StackExchange.Redis.


>[AZURE.NOTE] Incluem vários os passos de resolução de problemas deste guia de instruções para executar Redis comandos e monitorizar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos na secção [informações adicionais](#additional-information) .

## <a name="client-side-troubleshooting"></a>Resolução de problemas de lado do cliente


Esta secção descreve problemas de resolução de problemas que ocorrer devido a uma condição sobre a aplicação de cliente.

-   [Pressão de memória no cliente](#memory-pressure-on-the-client)
-   [Rajada do tráfego de](#burst-of-traffic)
-   [Cliente alta utilização da CPU](#high-client-cpu-usage)
-   [Largura de banda do lado cliente excedida](#client-side-bandwidth-exceeded)
-   [Tamanho grande pedido/resposta](#large-requestresponse-size)
-   [O que aconteceu aos meus dados no Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Pressão de memória no cliente

#### <a name="problem"></a>Problema

Orienta pressão de memória no computador do cliente para todos os tipos de problemas de desempenho que podem atrasar processamento de dados que foram enviados pela instância Redis sem qualquer atraso. Quando acertos de pressão de memória, o sistema, normalmente, tem de dados de uma página da memória física para a memória virtual que se encontra no disco. Esta *página em falha* faz com que o sistema para diminuir a velocidade significativamente.

#### <a name="measurement"></a>Medida 

1.  Monitorizar a utilização de memória num computador para se certificar de que não exceda memória disponível. 
2.  Monitorizar o `Page Faults/Sec` contador de desempenho. A maioria dos sistemas será têm algumas falhas de página pares durante a operação normal, por isso, veja picos neste contador de desempenho de falhas de página que correspondem com tempos limite.

#### <a name="resolution"></a>Resolução

Atualizar o seu cliente para um cliente de grande tamanho da memória virtual com mais memória ou abordar o seu padrões de utilização de memória para reduzir consuption de memória.


### <a name="burst-of-traffic"></a>Rajada do tráfego de

#### <a name="problem"></a>Problema

Rajada de tráfego combinado com má `ThreadPool` definições podem resultar em atrasos na processamento de dados já enviadas pelo servidor Redis mas ainda não consumidas do lado do cliente.

#### <a name="measurement"></a>Medida 

Monitor como seu `ThreadPool` estatísticas alterar ao longo do tempo com o código [da seguinte forma](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Também pode consultar a `TimeoutException` mensagem a partir de StackExchange.Redis. Eis um exemplo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Na mensagem acima, existem vários problemas que são interessantes:

 1. Repare que, na `IOCP` secção e o `WORKER` secção que tem um `Busy` valor maior que o `Min` valor. Isto significa que o `ThreadPool` definições precisam de ajustar.
 2. Também pode ver `in: 64221`. Isto indica que 64211 bytes receberam na camada do socket kernel mas ainda não foram lidos pela aplicação (por exemplo, StackExchange.Redis). Normalmente, isto significa que a aplicação não está a ler dados da rede tão rapidamente como o servidor é enviarem para si.

#### <a name="resolution"></a>Resolução

Configure as [Definições do conjunto de threads](https://gist.github.com/JonCole/e65411214030f0d823cb) para se certificar de que o conjunto de tópico irá dimensionar rapidamente em cenários de rajada.


### <a name="high-client-cpu-usage"></a>Cliente alta utilização da CPU

#### <a name="problem"></a>Problema

Utilização da CPU alta no cliente é uma indicação de que o sistema não pode acompanhar o trabalho que tenha sido pedido para efetuar. Isto significa que o cliente poderá falhar processar uma resposta a partir do Redis de uma forma atempada apesar Redis enviadas muito rapidamente a resposta.

#### <a name="measurement"></a>Medida

Monitorize a utilização da CPU e uma altura de sistema através do Portal do Azure ou o contador de desempenho associado. Tenha cuidado para não monitorizar CPU do *processo de* uma vez que um único processo pode ter baixa utilização da CPU ao mesmo tempo que sistema global CPU pode ser elevado. Veja picos na utilização da CPU que correspondem com tempos limite. Estatístico como resultado da CPU alta, também poderá ver alto `in: XXX` os valores na `TimeoutException` as mensagens de erro, tal como descrito na secção de [rajada do tráfego](#burst-of-traffic) .

>[AZURE.NOTE] StackExchange.Redis 1.1.603 e mais tarde inclui o `local-cpu` métrica no `TimeoutException` mensagens de erro. Certifique-se estiver a utilizar a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existem erros constantemente corrigidos o código para o tornar mais robustas para tempos limite pelo tendo a versão mais recente é importante.

#### <a name="resolution"></a>Resolução

Atualizar para um tamanho de VM maior com capacidade de CPU mais ou investigar o que está a causar CPU picos. 



### <a name="client-side-bandwidth-exceeded"></a>Largura de banda do lado cliente excedida

#### <a name="problem"></a>Problema

Computadores cliente médias diferentes têm limitações na quantidade largura de banda de rede estão disponíveis. Se o cliente exceder a largura de banda disponível, em seguida, dados não serão processados no lado do cliente tão rapidamente como o servidor está a enviar. Isto pode originar tempos limite.

#### <a name="measurement"></a>Medida

Monitorize como a utilização da largura de banda alterar ao longo do tempo com o código [da seguinte forma](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Note que não pode executar este código com êxito em alguns ambientes com permissões restritas (como o Azure web sites).

#### <a name="resolution"></a>Resolução 

Aumentar o tamanho da memória virtual de cliente ou reduzir consumo de largura de banda de rede.


### <a name="large-requestresponse-size"></a>Tamanho grande pedido/resposta

#### <a name="problem"></a>Problema

Uma grande pedido/resposta podem causar os tempos limite. Por exemplo, suponha que o valor de tempo limite configurado no seu cliente é 1 segundo. A aplicação pedidos duas chaves (por exemplo, 'A' e 'B') ao mesmo tempo (utilizando a mesma ligação de rede física). Maioria dos clientes suporta "Pipelining" de pedidos de, por exemplo, que ambos os pedidos de 'A' e 'B' sejam enviados no fio para o servidor de um após a outra sem aguardar que as respostas. O servidor irá enviar respostas novamente na mesma ordem. Se for grande resposta 'A' suficiente-pode comer maior parte do limite de tempo para os pedidos subsequentes. 

O exemplo seguinte demonstra este cenário. Neste cenário, pedido 'A' e 'B' são enviadas rapidamente, o servidor é iniciado enviar respostas 'A' e 'B' rapidamente, mas devido a tempos de transferência de dados, "B" obter preso atrás o pedido e tempos de saída apesar do servidor respondeu rapidamente.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Medida

Este é um difícil medir. Basicamente, tem de instrumento seu código do cliente para controlar grandes solicitações e respostas. 

#### <a name="resolution"></a>Resolução

1.  Redis está optimizada para um grande número de valores pequenos, em vez de alguns valores de grandes dimensões. A solução preferida é a quebra dos seus dados para valores mais pequenos relacionados. Consulte o [o que é o intervalo de tamanho de valor ideal para redis? É 100KB demasiado grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publicar para obter mais detalhes à volta porque é que os valores mais pequenos são recomendadas.
2.  Aumente o tamanho da sua VM (para o cliente e servidor de Cache Redis), para obter mais elevadas capacidades de largura de banda, como reduzir o tempo de transferência de dados para as respostas maiores. Tenha em atenção que introdução mais largura de banda no apenas servidor ou apenas no cliente do poderão não ser suficientes. Medir a utilização da largura de banda e compará-la com as funcionalidades do tamanho da VM que possui atualmente.
3.  Aumentar o número de `ConnectionMultiplexer` objetos utilização e os pedidos de round robin sobre ligações diferentes.


### <a name="what-happened-to-my-data-in-redis"></a>O que aconteceu aos meus dados no Redis?

#### <a name="problem"></a>Problema

Posso esperado para determinados dados sejam na minha instância do Azure Redis Cache mas -não parece ser aí.

##### <a name="resolution"></a>Resolução

Consulte o artigo [o que aconteceu aos meus dados no Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para possíveis causas e resoluções.


## <a name="server-side-troubleshooting"></a>Resolução de problemas de lado do servidor

Esta secção descreve problemas de resolução de problemas que ocorrer devido a uma condição no servidor cache.

-   [Pressão de memória no servidor](#memory-pressure-on-the-server)
-   [Utilização da CPU alta / carregar o servidor](#high-cpu-usage-server-load)
-   [Largura de banda do lado servidor excedida](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Pressão de memória no servidor

#### <a name="problem"></a>Problema

Orienta pressão de memória do lado do servidor para todos os tipos de problemas de desempenho que podem atrasar processamento de pedidos de. Quando acertos de pressão de memória, o sistema, normalmente, tem de dados de uma página da memória física para a memória virtual que se encontra no disco. Esta *página em falha* faz com que o sistema para diminuir a velocidade significativamente. Existem várias causas possíveis desta pressão de memória: 

1.  Ter preenchido a cache para capacidade total com os dados. 
2.  Redis está a ver fragmentação de memória alta - com mais frequência causada pela armazenar objectos de grandes dimensões (Redis está optimizada para um objectos pequenos - consulte as [o que é o intervalo de tamanho de valor ideal para redis? É 100KB demasiado grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publicar para obter mais detalhes). 

#### <a name="measurement"></a>Medida

Redis expõe duas métricas que podem ajudar a identificar este problema. O primeiro é `used_memory` e o outro é `used_memory_rss`. [Estes métricas](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) estão disponíveis no Portal do Azure ou através do comando [Redis informações](http://redis.io/commands/info) .

#### <a name="resolution"></a>Resolução

Existem várias eventuais alterações que pode efetuar para ajudar a manter a utilização de memória Saudável:

1. [Configurar uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e definir horas de expiração em suas chaves. Tenha em atenção que este pode não ser suficiente se tiver fragmentação.
2. [Configurar um valor reservada maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que seja grande o Adquirente na íntegra para fragmentação de memória.
3. Divida os objetos em cache grandes para o menores objectos relacionados.
4. [Escala](cache-how-to-scale.md) para um tamanho de cache maior.
5. Se estiver a utilizar uma [cache premium com cluster Redis ativado](cache-how-to-premium-clustering.md) pode [aumentar o número de shards](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilização da CPU alta / carregar o servidor

#### <a name="problem"></a>Problema

Utilização da CPU alta pode vermelha que pode falhar do lado do cliente processar uma resposta a partir do Redis de uma forma atempada apesar Redis enviadas muito rapidamente a resposta.

#### <a name="measurement"></a>Medida

Monitorize a utilização da CPU e uma altura de sistema através do Portal do Azure ou o contador de desempenho associado. Tenha cuidado para não monitorizar CPU do *processo de* uma vez que um único processo pode ter baixa utilização da CPU ao mesmo tempo que sistema global CPU pode ser elevado. Veja picos na utilização da CPU que correspondem com tempos limite.

#### <a name="resolution"></a>Resolução

[Escala](cache-how-to-scale.md) para uma cache maior camada com capacidade de CPU mais ou investigar o que está a causar CPU picos. 

### <a name="server-side-bandwidth-exceeded"></a>Largura de banda do lado servidor excedida

#### <a name="problem"></a>Problema

Instâncias de cache de tamanho idêntico diferentes têm limitações na quantidade largura de banda de rede estão disponíveis. Se o servidor exceder a largura de banda disponível, dados não a ser enviados para o cliente tão rapidamente. Isto pode originar tempos limite.

#### <a name="measurement"></a>Medida

Pode monitorizar o `Cache Read` métrica, que é a quantidade de dados ler a partir da cache em Megabytes por segundo (MB/s) durante o intervalo de elaboração de relatórios especificado. Este valor corresponde à largura de banda de rede utilizada por esta cache. Se pretender configurar alertas para limites de largura de banda de rede de lado do servidor, pode criá-las utilização desta `Cache Read` contador. Compare as medições com os valores em [Esta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observados para vários cache de camadas e tamanhos de preços.

#### <a name="resolution"></a>Resolução

Se for forma consistente junto a largura de banda máxima observada para o tamanho da camada e cache comparar, considere o [dimensionamento](cache-how-to-scale.md) a uma camada ou tamanho que tem banda maior, utilizando os valores [nesta](cache-faq.md#cache-performance) tabela como guia comparar.


## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite StackExchange.Redis

StackExchange.Redis utiliza uma definição com nome de configuração `synctimeout` para operações de síncronas que tem um valor predefinido do 1000 ms. Se não concluir uma chamada síncrona da hora previsto, o cliente StackExchange.Redis inicia um erro de tempo excedido semelhante ao exemplo seguinte.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Esta mensagem de erro contém métricas de que o podem ajudar a apontar para a resolução de causa e possíveis do problema. A tabela seguinte contém detalhes sobre as métricas de mensagem de erro.

| Métrica do erro de mensagem | Detalhes                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Inst       | No último setor de tempo: tem sido emitidos 0 comandos                                                                                                                                                                                              |
| Gestor de        | Está a executar o Gestor de socket `socket.select` que significa que está a pedir o SO para indicar um socket que tem algo fazer; que mostra basicamente: o leitor é não ativamente de leitura da rede porque-não pense que não há nada fazer |
| fila de espera      | Existem 73 operações em curso total                                                                                                                                                                                                        |
| qu         | 6 das operações em curso são na fila de espera não enviada e ainda não foram escritas à rede de saída                                                                                                                                                           |
| Qs         | 67 de operações de em curso he ter sido enviada para o servidor mas uma resposta ainda não está disponível. A resposta possível `Not yet sent by the server` ou`sent by the server but not yet processed by the client.`                                                   |
| controlo de qualidade         | 0 das operações em curso viu respostas mas ainda não foi marcado como concluído devido à espera do ciclo de conclusão                                                                                                                                      |
| wR         | Existe um bytes/activewriters sénior ativa (o que significa que não estão a ser ignorados os pedidos de não enviados 6)                                                                                                                                                   |
| no         | Não existem sem leitores ativos e zero bytes estão disponíveis para ser continue a ler a NIC bytes/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Passos para investigar

1. Como prática recomendada Certifique-se estiver a utilizar o seguinte padrão para ligar ao utilizar o cliente StackExchange.Redis.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Para mais informações, consulte o artigo [ligar para a cache utilizando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Certifique-se de que a Cache do Azure Redis e a aplicação de cliente estão na mesma região no Azure. Por exemplo, poderá obter tempos limite quando a cache está no Leste dos EUA, mas o cliente é nos EUA oeste e o pedido não concluída dentro de `synctimeout` intervalo ou poderá obter tempos limite quando estiver a depurar a partir do seu computador de desenvolvimento local. 

    É recomendado altamente têm a cache e no cliente na mesma região Azure. Se tiver um cenário que inclui a chamadas de região cruzada, deve configurar o `synctimeout` intervalo para um valor superior ao intervalo de 1000 ms predefinido ao incluir um `synctimeout` propriedade na cadeia de ligação. O exemplo seguinte mostra um fragmento da cadeia de ligação de cache de StackExchange.Redis com um `synctimeout` do 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Certifique-se estiver a utilizar a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existem erros constantemente corrigidos o código para o tornar mais robustas para tempos limite pelo tendo a versão mais recente é importante.

4. Se existirem pedidos que estão introdução vinculados pelo limitações de largura de banda no servidor ou cliente, irá demorar mais tempo para concluir e portanto causar tempos limite. Para ver se o seu tempo limite devido a largura de banda de rede no servidor, consulte o artigo [largura de banda do lado servidor excedida](#server-side-bandwidth-exceeded). Para ver se o seu tempo limite devido a largura de banda de rede de cliente, consulte [as largura de banda de lado do cliente excedida](#client-side-bandwidth-exceeded).

6. Está que introdução CPU vinculados no servidor ou no cliente?
    -   Verificar se que está introdução vinculados por CPU no seu cliente de que pode causar o pedido de não ser processada dentro de `synctimeout` intervalo, por conseguinte, a causar um limite de tempo. Mover para um tamanho de cliente maior ou distribuir a carga pode ajudar a o controlo. 
    -   Verificar se está a obter CPU vinculado no servidor através da monitorização de `CPU` [métrica de desempenho da cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Pedidos que chegam enquanto Redis está CPU vinculada podem causar os pedidos para tempo de espera. Para resolver este problema pode distribuir a carga por vários shards na cache de premium ou atualizar para um tamanho de maior ou comparar camada. Para mais informações, consulte o artigo [Excedido de largura de banda de lado do servidor](#server-side-bandwidth-exceeded).

7. Existem comandos a demorar muito tempo a processar no servidor? Comandos que estão a demorar muito tempo a processar no servidor de redis de execução longa pode causar tempos limite. Alguns exemplos de comandos longa são `mget` com grandes quantidades de teclas, `keys *` ou mal escritas lua scripts. Pode ligar à sua instância de Cache de Redis Azure utilizando o cliente do clip redis ou utilizar a [Consola Redis](cache-configure.md#redis-console) e execute o comando [SlowLog](http://redis.io/commands/slowlog) para ver se existem pedidos demorar mais tempo do que o esperado. Servidor de redis e StackExchange.Redis estão otimizados para pedidos de pequenas muitos em vez de pedidos de grandes menos. Dividir os seus dados em blocos mais pequenos, pode melhorar coisas aqui. 

    Para obter informações sobre como ligar para o ponto final Azure Redis Cache SSL utilizando redis clip e stunnel, consulte a mensagem do blogue [Anunciar o ASP.NET sessão Estado fornecedor para Redis versão de pré-visualização](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Para mais informações, consulte o artigo [SlowLog](http://redis.io/commands/slowlog).

8. Redis servidor caso de carga elevado pode causar os tempos limite. Pode monitorizar a carga de servidor através da monitorização de `Redis Server Load` [métrica de desempenho da cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Uma carga de servidor de 100 (valor máximo) se trata de que o servidor de redis foi ocupado, com sem tempo de espera, processamento de pedidos. Para ver se determinados pedidos são ocupar todos a funcionalidade de servidor, execute o comando de SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, consulte o artigo [utilização da CPU alta / servidor carregar](#high-cpu-usage-server-load).

9. Estava lá qualquer outro evento do lado do cliente que possam ter causado blip uma rede? Verificar no cliente (web, a função de trabalho ou um VM Iaas) se Ocorreu um evento como o número de ocorrências de cliente de dimensionamento para cima ou para baixo ou implementar uma versão nova do cliente ou dimensionar automaticamente está ativado? Nos nossos testes que ter encontrámos que pode causar autoscale ou dimensionamento para cima/baixo a conectividade da rede saída pode perder durante vários segundos. Código de StackExchange.Redis é e são a esses eventos e irá restabelecer a ligação. Durante este período de tempo de ligação re quaisquer pedidos na fila de espera podem tempo limite.

10. Foi existe um pedido de grande precede vários pequenas pedidos para a Cache de Redis excedeu? O parâmetro `qs` o erro mensagem mostra-lhe quantos pedidos foram enviados a partir do cliente para o servidor, mas ainda não tiverem processada uma resposta. Este valor pode manter em crescimento porque StackExchange.Redis utiliza uma única ligação TCP e só pode ler uma resposta uma vez. Apesar da primeira operação excedeu, não parar os dados a ser enviados para ao servidor e outros pedidos estão bloqueados até esta está concluída, a causar tempos. É uma solução minimizar a hipótese de limites de tempo por garantir que a cache é grande o suficiente para sua carga de trabalho e dividir valores grandes em blocos mais pequenos. Outra solução possível é utilizar um conjunto de `ConnectionMultiplexer` objetos no seu cliente e selecione pelo menos, carregados `ConnectionMultiplexer` ao enviar um pedido de nova. Isto deve impedir que um único limite de tempo a causar outros pedidos para também tempo limite.

11. Se estiver a utilizar `RedisSessionStateprovider`, certifique-se tiver de definir o tempo limite repetir corretamente. `retrytimeoutInMilliseconds`deve ser mais elevados que `operationTimeoutinMilliseonds`, caso contrário, sem repetições irão ocorrer. No exemplo seguinte `retrytimeoutInMilliseconds` está definido para 3000. Para mais informações, consulte o artigo [ASP.NET fornecedor de estado de sessão para a Cache de Redis Azure](cache-aspnet-session-state-provider.md) e [como utilizar os parâmetros de configuração de fornecedor de estado de sessão e o fornecedor de Cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Verifique a utilização de memória no servidor Azure Redis Cache através da [monitorização](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se for uma política de eviction no local, inicia o Redis expulsar teclas quando `Used_Memory` atingir o tamanho da cache. Idealmente, `Used Memory RSS` deve ser apenas um pouco mais elevados que `Used memory`. Uma grande diferença significa existe fragmentação de memória (interno ou externo. Quando `Used Memory RSS` é menor que `Used Memory`, significa que a parte da memória cache tem foram trocada pelo sistema operativo. Se tal acontecer que pode esperar alguns latências significativas. Uma vez que Redis não tenham controlo sobre como respetivas alocações são mapeadas para páginas de memória, alta `Used Memory RSS` é, muitas vezes, o resultado de um coletor na utilização de memória. Quando Redis liberta memória, a memória é dado novamente para a atribuição e a atribuição de pode ou não pode dar a memória novamente para o sistema. Poderão existir uma diferença entre o `Used Memory` consumo de memória e valor, tal como comunicado pelo sistema operativo. Pode ser devido ao facto de memória tenha sido utilizada e disponibilizada por Redis, mas não dado anterior para o sistema. Para ajudar a atenuar problemas de memória pode executar os passos seguintes.
    -   Atualize a cache para um tamanho de maior, de modo a que não estiver a executar compara limitações de memória no sistema.
    -   Definir horas de expiração nas teclas de modo a que os valores mais antigos são retirados importante.
    -   Monitorizar o o `used_memory_rss` métrica em cache. Quando este valor aproxima o tamanho da sua cache, é provável comece a ver problemas de desempenho. Distribua os dados por vários shards se estiver a utilizar uma cache premium ou atualizar para um tamanho de cache maior.

    Para mais informações, consulte o artigo [Pressão de memória no servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informações adicionais

-   [O que oferta Redis Cache e o tamanho devo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [Como referência e testar o desempenho da minha cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Como pode executar os comandos Redis?](cache-faq.md#how-can-i-run-redis-commands)
-   [Como monitorizar o Azure Redis Cache](cache-how-to-monitor.md)