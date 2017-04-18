<properties 
    pageTitle="Como monitorizar o Azure Redis Cache | Microsoft Azure" 
    description="Saiba como monitorizar o desempenho e estado de funcionamento do seu instâncias de Azure Redis Cache" 
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
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Como monitorizar o Azure Redis Cache

Azure Redis Cache fornece várias opções para monitorizar os seus instâncias de cache. Pode ver métricas, afixar gráficos de métricas para o Startboard, personalizar o intervalo de data e hora de monitorização de gráficos, adicionar e remover métricas dos gráficos e definir alertas quando forem cumpridas determinadas condições. Estas ferramentas permitem-lhe monitorizar o estado de funcionamento do seu instâncias de Cache Redis do Azure e ajudam a gerir as suas aplicações de colocação em cache.

Quando estão activados diagnósticos de cache, métricas de instâncias do Azure Redis Cache são recolhidas (Product Key) a cada 30 segundos e armazenadas para que possam ser apresentados nos gráficos métricas e avaliadas por regras de alertas.

Métricas de cache são recolhidas utilizando o Redis comando [INFO](http://redis.io/commands/info) . Para mais informações sobre as informações diferentes valores utilizados para cada métrica em cache, consulte o artigo [intervalos de elaboração de relatórios e métricas disponíveis](#available-metrics-and-reporting-intervals).

Para ver métricas de cache, [Navegue](cache-configure.md#configure-redis-cache-settings) para a instância de cache no [portal do Azure](https://portal.azure.com). Métricas de instâncias do Azure Redis Cache são acedidas na pá **Redis métricas** .

![Redis métricas][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Se for apresentada a seguinte mensagem na pá **Redis métricas** , siga os passos na secção [Ativar diagnósticos de cache](#enable-cache-diagnostics) para activar diagnósticos de cache.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

O pá **Redis métricas** tem **monitorização** gráficos que apresentam métricas de cache. Cada gráfico pode ser personalizado ao adicionar ou remover métricas e alterar o intervalo de elaboração de relatórios. Para ver e configurar alertas e operações, a **Redis Cache** pá tem uma secção de **operações** que apresenta a cache de **eventos** e as **regras de alertas**.

## <a name="enable-cache-diagnostics"></a>Activar diagnósticos de cache

Azure Redis Cache fornece a capacidade de ter diagnósticos dados armazenados numa conta de armazenamento para que possa utilizar as ferramentas que pretende aceder e processar os dados diretamente. Ordem para diagnósticos de cache para ser recolhidos, armazenado e apresentado no portal do Azure, uma conta de armazenamento tem de ser configurada. Caches na mesma região e subscrição partilham a mesma conta de armazenamento de diagnóstico e, quando a configuração é alterada aplica-se ao todas as caches a subscrição que estão nesse região.

Para ativar e configurar diagnósticos de cache, navegue para a pá **Redis Cache** para a instância da cache. Se diagnósticos ainda não estão activados, é apresentada uma mensagem em vez de um gráfico de diagnóstico.

![Activar diagnósticos de cache][redis-cache-enable-diagnostics]

Clique na mensagem para apresentar o pá **métrica** e clique em **definições de diagnóstico** para ativar e configurar as definições de diagnóstico para a instância do serviço de cache.

![Definições dos diagnósticos do][redis-cache-diagnostic-settings]

![Configurar diagnósticos][redis-cache-configure-diagnostics]

Clique no botão **** para activar diagnósticos de cache e apresentar a configuração de diagnóstico.

Clique na seta à direita da **Conta de armazenamento** para selecionar uma conta de armazenamento para manter os dados de diagnóstico. Para um melhor desempenho, selecione uma conta de armazenamento na mesma região como a cache.

Assim que as definições de diagnóstico estão configuradas, clique em **Guardar** para guardar a configuração. Tenha em atenção que poderá demorar alguns minutos para que as alterações sejam aplicadas.

>[AZURE.IMPORTANT] Caches na mesma região e subscrição partilham as mesmas definições de armazenamento de diagnóstico e, quando a configuração é alterados (diagnósticos activado/desactivado ou alterar a conta de armazenamento) aplica-se ao todas as caches a subscrição que estão nesse região.

Para ver as métricas armazenadas, examinar as tabelas na sua conta de armazenamento com nomes que começam com `WADMetrics`. Para obter mais informações sobre como aceder a métricas armazenadas fora do portal do Azure, consulte o exemplo de [dados Access Redis Cache de monitorização](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Apenas métricas que estão armazenadas na conta de armazenamento selecionado são apresentadas no portal do Azure. Se alterar contas de armazenamento, os dados na conta de armazenamento anteriormente configurado permanecem disponíveis para transferência, mas não é apresentada no portal do Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de elaboração de relatórios

Métricas de cache são reportadas com vários intervalos de elaboração de relatórios, incluindo **horas anteriores**, **hoje em dia**, **semana passada**e **personalizada**. O pá **métrica** para cada gráfico métricas apresenta os valores de média, mínimos e máximos para cada métrica no gráfico e algumas métricas de apresentam um total para o intervalo de elaboração de relatórios. 

Cada métrica inclui duas versões. Uma métrica medidas de desempenho para toda a cache de e para caches utilizam [clusters](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` de desempenho de medidas de nome para uma única shard numa cache. Por exemplo, se tiver de uma cache 4 shards, `Cache Hits` é a quantidade total de acertos para toda a cache e `Cache Hits (Shard 3)` está apenas os acertos para esse shard da cache.

>[AZURE.NOTE] Mesmo quando a cache está inactiva sem aplicações de cliente ativo ligado, poderá ver alguns atividade de cache, como clientes ligados, utilização de memória e operações a ser executadas. Esta actividade é normal durante a operação de uma instância do Azure Redis Cache.

| Métrica            | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Acertos na cache        | O número de pesquisas chaves bem sucedidas durante o intervalo de elaboração de relatórios especificado. Isto mapas para `keyspace_hits` a partir do Redis [informações](http://redis.io/commands/info) do comando.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Falhas na cache      | O número de pesquisas chaves falhadas durante o intervalo de elaboração de relatórios especificado. Isto mapas para `keyspace_misses` a partir do comando Redis INFO. Falhas na cache não significa necessariamente que existe um problema com a cache. Por exemplo, ao utilizar o padrão de cache anulação programação, uma aplicação procura primeira na cache para um item. Se o item não estiver lá (falha de cache), o item é obtido a partir da base de dados e adicionado à cache para utilização posterior. Falhas na cache são comportamento normal para o padrão de anulação da cache de programação. Se o número de falhas de cache for maior que o esperado, examine a lógica de aplicação que preenche e lê a partir da cache. Se estiverem a ser retirados itens da cache de devido a pressão de memória, em seguida, poderão existir algumas falhas na cache, mas uma métrica melhor para monitorizar a pressão de memória seria `Used Memory` ou `Evicted Keys`. |
| Clientes ligados | O número de ligações de cliente a cache durante o intervalo de elaboração de relatórios especificado. Isto mapas para `connected_clients` a partir do comando Redis INFO. Assim que o [limite de ligação](cache-configure.md#default-redis-server-configuration) é atingido tentativas de ligação subsequentes à cache irão falhar. Tenha em atenção que, mesmo se não nenhuma aplicação cliente ativa, pode ser instâncias alguns dos clientes ligadas devido ao processos internos e ligações.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Teclas retiradas      | O número de itens retirado da cache de durante o intervalo especificado elaboração de relatórios de conclusão para a `maxmemory` limite. Isto mapas para `evicted_keys` a partir do comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Teclas expiradas      | O número de itens expirou da cache de durante o intervalo de elaboração de relatórios especificado. Conjunto mapas deste valor para `expired_keys` a partir do comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Obtém              | O número de operações de obter da cache de durante o intervalo de elaboração de relatórios especificado. Este valor é a soma dos seguintes procedimentos valores a partir das informações do Redis comando tudo: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, e `cmdstat_getrange`e é equivalente a soma de cache acertos e falhas durante o intervalo de elaboração de relatórios.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis carga do servidor | A percentagem de ciclos em que o servidor de Redis está ocupado transformação e não a aguardar idle para mensagens. Se este contador atingir 100 significa que o servidor de Redis atingiu um limite máximo de desempenho e não consegue processar CPU trabalhe qualquer mais rapidamente. Se estiver a ver Redis servidor caso de carga elevada, em seguida, verá o tempo limite exceções no cliente. Neste caso, deverá tomar em consideração escalar para cima ou para criar a partições os seus dados em várias caches.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Conjuntos              | O número de operações de conjunto à cache durante o intervalo de elaboração de relatórios especificado. Este valor é a soma dos seguintes valores a partir das informações do Redis comando tudo: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, e `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Operações de total  | O número total de comandos processados pelo servidor cache durante o intervalo de elaboração de relatórios especificado. Conjunto mapas deste valor para `total_commands_processed` a partir do comando Redis INFO. Tenha em atenção que quando é utilizada a Cache de Redis Azure puramente para pub/sub haverá sem métricas para `Cache Hits`, `Cache Misses`, `Gets`, ou `Sets`, mas haverá `Total Operations` métricas refletem a utilização da cache para operações de pub/sub.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Memória utilizada       | A quantidade de memória cache utilizada para pares valor/chave na cache em MB durante o intervalo de elaboração de relatórios especificado. Conjunto mapas deste valor para `used_memory` a partir do comando Redis INFO. Isto não inclui metadados ou fragmentação.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Memória utilizada RSS   | A quantidade de memória cache utilizada em MB durante o intervalo especificado de elaboração de relatórios, incluindo fragmentação e metadados. Conjunto mapas deste valor para `used_memory_rss` a partir do comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CPU               | A utilização da CPU do servidor Azure Redis Cache como uma percentagem durante o intervalo de elaboração de relatórios especificado. Conjunto mapas deste valor para o sistema operativo `\Processor(_Total)\% Processor Time` contador de desempenho.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Cache de leitura        | Leia a quantidade de dados a partir da cache em Megabytes por segundo (MB/s) durante o intervalo de elaboração de relatórios especificado. Este valor é derivado de cartões de interface de rede que suportam a máquina virtual que aloja a cache e não Redis específico. **Este valor corresponde à largura de banda de rede utilizada por esta cache. Se pretender configurar alertas para limites de largura de banda de rede de lado do servidor, em seguida, crie-lo utilizando este `Cache Read` contador. Consulte [Esta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observados para vários cache de camadas e tamanhos de preços.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Cache de escrita       | A quantidade de dados escritos da cache em Megabytes por segundo (MB/s) durante a especificado reporte intervalo. Este valor é derivado de cartões de interface de rede que suportam a máquina virtual que aloja a cache e não Redis específico. Este valor corresponde a largura de banda de rede de dados enviados para a cache do cliente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Como ver métricas e personalizar gráficos

Pode ver uma descrição geral de métricas da cache na pá **Redis métricas** . Para aceder à **Redis métricas** pá selecione **todas as definições de** > **Redis métricas**.

![Redis métricas][redis-cache-redis-metrics]


O pá **Redis métricas** contém os seguintes gráficos.

| Gráfico de métricas de redis | Métricas apresentadas     |
|------------------|-------------------|
| Cache de leitura e de Cache de escrita | Cache de leitura |
|                            | Cache de escrita |
| Clientes ligados      | Clientes ligados |
| Acertos e falhas  | Acertos na cache        |
|                  | Falhas na cache      |
| Comandos total   | Operações de total  |
| Obtém e conjuntos    | Obtém              |
|                  | Conjuntos              |
| Utilização da CPU         | CPU           |
| Utilização de memória      | Memória utilizada   |
|                   | Memória utilizada RSS |
| Redis carga do servidor | Carga do servidor   |
| Contagem de tecla | Total de chaves |
|           | Teclas retiradas |
|           | Teclas expiradas |


Para uma vista mais detalhada de métricas num gráfico específico e para personalizar o gráfico, clique no gráfico pretendido a partir do pá **Redis métricas** para apresentar o pá **métrica** para esse gráfico.

![Métrica pá][redis-cache-metric-blade]

Todos os alertas que são definidos nas métricas apresentadas por um gráfico estão listados na parte inferior da pá **métrica** para esse gráfico.

Para adicionar ou remover métricas ou alterar o intervalo de elaboração de relatórios, escolha **Editar gráfico**.

Para adicionar ou remover métricas do gráfico, clique na caixa de verificação ao lado do nome da métrica. Para alterar o intervalo de elaboração de relatórios, clique no intervalo pretendido. Para alterar o **tipo de gráfico**, clique no estilo pretendido. Assim que são efetuadas as alterações pretendidas, clique em **Guardar**. 

![Editar gráfico][redis-cache-edit-chart]

Quando clica em **Guardar** as suas alterações vão permanecer até que deixe o pá **métrica** . Quando voltar mais tarde, verá a métrica original e o intervalo de tempo novamente. Para mais informações sobre a personalização de gráficos, consulte o artigo [métricas de serviço do Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para ver as métricas sobre um determinado período de tempo num gráfico, Paire o cursor do rato sobre uma das barras específicas ou pontos no gráfico que corresponde ao tempo pretendido e, são apresentadas as métricas para nesse intervalo.

![Ver detalhes do gráfico][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Como monitorizar uma cache premium com clusters

Caches Premium que tenham [clusters](cache-how-to-premium-clustering.md) activado podem ter até 10 shards. Cada shard tem as suas próprias métricas e estes métricas são agregadas para fornecer métricas para a cache como um todo. Cada métrica inclui duas versões. Uma métrica medidas de desempenho para toda a cache e uma segunda versão da métrica que inclui `(Shard 0-9)` de desempenho de medidas de nome para uma única shard numa cache. Por exemplo, se tiver de uma cache 3 shards, `Cache Hits` é a quantidade total de acertos para toda a cache e `Cache Hits (Shard 2)` está apenas os acertos para esse shard da cache.

Cada gráfico monitorização apresenta as métricas de nível superior para a cache juntamente com as métricas para cada shard cache.

![Monitor][redis-cache-premium-monitor]

Pairar o rato sobre os pontos de dados apresenta os detalhes desse ponto no tempo. 

![Monitor][redis-cache-premium-point-summary]

Os valores maiores normalmente são os valores agregados para a cache enquanto os valores mais pequenos são as métricas individuais para o shard. Repare que neste exemplo, existem três shards e os acertos estão distribuídos uniformemente as shards.

![Monitor][redis-cache-premium-point-shard]

Para ver mais detalhes, clique no gráfico para ver uma vista na pá **métrica** expandida.

![Monitor][redis-cache-premium-chart-detail]

Por predefinição a cada gráfico inclui o contador de desempenho da cache de nível superior, bem como os contadores de desempenho para os shards individuais. Pode personalizar estes no pá a **Editar o gráfico** .

![Monitor][redis-cache-premium-edit]

Para mais informações sobre os contadores de desempenho disponíveis, consulte o artigo [intervalos de elaboração de relatórios e métricas disponíveis](#available-metrics-and-reporting-intervals).


## <a name="operations-and-alerts"></a>Operações e alertas

A secção de **operações** no pá a **Redis Cache** tem secções **eventos** e **regras de alertas** .

![Oeprations][redis-cache-operations-events]

Para ver uma lista de operações de cache recentes, clique no gráfico de **eventos** para apresentar o pá **eventos** . Exemplos de operações incluem a obter e voltar a gerar teclas de acesso e a ativação e a resolução das regras de alertas. Para mais informações sobre cada evento, clique no evento na pá **eventos** .

Para mais informações sobre eventos, consulte o artigo [Ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Secção **regras de alertas** de apresenta a contagem de alertas para a instância da cache. Uma regra de alerta permite-lhe monitorizar a instância de cache e receber uma mensagem de e-mail, sempre que um determinado valor métrico atinge o limiar definido na regra. 

Regras de alertas são avaliadas (Product Key) a cada cinco minutos e, quando é activada uma regra de alerta, qualquer as notificações configuradas são enviadas. E regra de alerta e as notificações não são processadas instantaneamente; poderão existir um atraso de vários minutos antes de uma regra de alerta está ativada e notificações enviadas.

Regras de alertas possam ser visualizadas e definir a partir do pá **métrica** para um gráfico de monitorização específico, ou de pá as **regras de alertas** .

As regras alertas têm as seguintes propriedades.

| Propriedade regra de alerta                 | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recurso                            | O recurso avaliado pela regra de alerta. Ao criar uma regra de alerta a partir de uma cache Redis, a cache é um recurso.                                                                                                                                                                                                                                                                                                                                                  |
| Nome                                | Nome que identifica exclusivamente a regra alerta dentro da instância atual de cache.                                                                                                                                                                                                                                                                                                                                                                                       |
| Descrição                         | Descrição opcional a regra de alerta.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Métrica                              | Métrica monitorizadas pela regra de alerta. Para obter uma lista de métricas de cache, consulte o artigo intervalos de elaboração de relatórios e métricas disponíveis.                                                                                                                                                                                                                                                                                                                                             |
| Condição                           | O operador de condição para a regra de alerta. Escolhas possíveis são: maior, maior ou igual a, menor que, menor ou igual a                                                                                                                                                                                                                                                                                                                             |
| Limiar                           | O valor utilizado para comparar com a métrica utilizando o operador especificado pela propriedade condição. Consoante a métrica, este valor poderá estar no bytes/segundo bytes, % ou contagem.                                                                                                                                                                                                                                                                                     |
| Período                              | Especifica o período durante o qual o valor médio da métrica do é utilizado para a comparação de alerta regra. Por exemplo, se for o período de através de última hora, é utilizado o valor médio de métrica ao longo do intervalo de hora anterior para a comparação. Se quiser ser notificado sempre que o limiar de que existe correspondência devido a um coletor numa atividade, um período curto é adequado. Para ser notificado quando existe atividade constante acima do limiar, utilize um período. |
| Serviço de e-mail e coadministradores | Quando for VERDADEIRO, o administrador de serviços e o administrador cocriação são receber uma mensagem quando é activado o alerta.                                                                                                                                                                                                                                                                                                                                                                    |
| E-mail de administrador adicionais      | Endereço de e-mail opcional para um administrador adicional ser notificado quando o alerta está ativado.                                                                                                                                                                                                                                                                                                                                                                    |

Apenas uma notificação é enviada por ativação de alerta regra. Quando for excedido o limiar de para uma regra e uma notificação é enviada, a regra não for novamente avaliada até a métrica do determinado abaixo do limiar. Se a métrica do subsequentemente excede o limite, o alerta é reactivar e uma nova notificação é enviada.

Para ver todas as regras de alertas para a instância de cache, clique na parte do **alertas regras** na pá a **Redis Cache** . Para ver apenas as regras de alertas que utilizam uma métrica específica, navegue para a pá **métrica** para o gráfico que contém essa métrica.

![Regras de alertas][redis-cache-alert-rules]

Para adicionar uma regra de alerta, clique em **Adicionar alerta** da pá **métrica** ou pá as **regras de alertas** . 

Introduza os critérios de regra pretendida para o pá de regra de **Adicionar um alerta** e clique em **OK**. 

![Adicionar regra de alerta][redis-cache-add-alert]

>[AZURE.NOTE] Quando uma regra de alerta é criada ao clicar em **Adicionar alerta** a partir do pá **métrica** , apenas as métricas apresentadas no gráfico que pá aparecem no menu pendente **métrica** . Quando uma regra de alerta é criada ao clicar em **Adicionar alerta** de pá as **regras de alertas** , todas as métricas de cache estão disponíveis no menu pendente **métrica** .

Assim que uma regra de alerta seja guardada-é apresentada a pá de **regras de alertas** , bem como a pá **métrica** para qualquer gráficos que apresentam a métrica do utilizado na regra de alerta. Para editar uma regra de alerta, clique no nome da regra de alerta para apresentar o pá de **Editar regra** . O pá de **Editar regra de** pode editar as propriedades da regra, eliminar ou desativar a regra alerta ou reativar a regra se não tiver sido desativadas anteriormente.

>[AZURE.NOTE] Quaisquer alterações realizadas para as propriedades da regra poderão demorar um pouco a incorporá antes de estes serão refletidas no pá as **regras de alertas** ou a pá **métrica** .

Quando uma regra de alerta é activada, uma mensagem de e-mail é enviada consoante a configuração da regra alerta e é apresentado um ícone de alerta na parte **regras alertas** sobre a pá **Redis Cache** .

Considera-se uma regra de alerta para ser resolvido quando a condição alerta já não for avaliada como verdadeira. Assim que a condição da regra alerta for resolvida, o ícone de alerta é substituído por uma marca de verificação. Para obter detalhes sobre e alerta e resoluções, clique na parte de **eventos** no pá a **Redis Cache** para ver os eventos no pá **eventos** .

Para mais informações acerca de alertas no Azure, consulte o artigo [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Métricas no pá a Redis Cache

A **Redis Cache** pá apresenta as seguintes categorias de métricas.

-   [Gráficos de monitorização](#monitoring-charts)
-   [Gráficos de utilização](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitorização

Secção **monitorização** tem **de acertos e falha**, **obtém e define**, **ligações**e gráficos de **Comandos Total** .

![Gráficos de monitorização][redis-cache-monitoring-part]

Os gráficos de **monitorização** apresentam as seguintes métricas.

| Gráfico de monitorização | Métricas de cache     |
|------------------|-------------------|
| Acertos e falhas  | Acertos na cache        |
|                  | Falhas na cache      |
| Obtém e conjuntos    | Obtém              |
|                  | Conjuntos              |
| Ligações      | Clientes ligados |
| Comandos total   | Operações de total  |

Para obter informações sobre as métricas de visualização e personalizar os gráficos individuais nesta secção, consulte a secção de [como ver métricas e personalizar gráficos métricas](#how-to-view-metrics-and-customize-charts) seguinte.

### <a name="usage-charts"></a>Gráficos de utilização

A secção **utilização** tem **Redis carga do servidor**, **A utilização de memória**, **Largura de banda máxima**e **Utilização da CPU** gráficos e também apresenta a **preços camada** para a instância da cache.

![Gráficos de utilização][redis-cache-usage-part]

Apresenta a **camada de preços** a cache preços camada e podem ser utilizados para [escala](cache-how-to-scale.md) a cache para uma camada comparar diferentes.

Os gráficos de **utilização de** apresentam as seguintes métricas.

| Gráfico de utilização       | Métricas de cache |
|-------------------|---------------|
| Redis carga do servidor | Carga do servidor   |
| Utilização de memória      | Memória utilizada   |
| Largura de banda de rede | Cache de escrita   |
| Utilização da CPU         | CPU           |

Para obter informações sobre as métricas de visualização e personalizar os gráficos individuais nesta secção, consulte a secção de [como ver métricas e personalizar gráficos métricas](#how-to-view-metrics-and-customize-charts) seguinte.
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



