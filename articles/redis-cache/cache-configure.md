<properties 
    pageTitle="Como configurar a Cache de Redis Azure | Microsoft Azure"
    description="Compreender a configuração de Redis predefinida para Azure Redis Cache e saiba como configurar o seu instâncias de Cache Redis do Azure"
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
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Como configurar a Cache de Redis Azure

Este tópico descreve como rever e atualizar a configuração para o seu instâncias de Cache Redis do Azure e aborda a configuração do servidor Redis predefinido para instâncias de Cache Redis do Azure.

>[AZURE.NOTE] Para mais informações sobre como configurar e utilizar funcionalidades premium do cache, consulte o artigo [como configurar persistente para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md), [como configurar clusters para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md)e [como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Configurar definições de cache Redis

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis Cache fornece as seguintes definições pá as **Definições** .

![Redis definições de Cache](./media/cache-configure/redis-cache-settings.png)



-   [Suporte e resolução de problemas de definições](#support-amp-troubleshooting-settings)
-   [Definições gerais](#general-settings)
    -   [Propriedades](#properties)
    -   [Teclas de acesso](#access-keys)
    -   [Definições avançadas](#advanced-settings)
    -   [Redis Cache Advisor](#redis-cache-advisor)
-   [Definições de escala](#scale-settings)
    -   [Preços de camadas](#pricing-tier)
    -   [Redis tamanho cluster](#cluster-size)
-   [Definições de gestão de dados](#data-management-settings)
    -   [Redis recorrência de dados](#redis-data-persistence)
    -   [Importar/exportar](#importexport)
-   [Definições de administração](#administration-settings)
    -   [Reiniciar o computador](#reboot)
    -   [Atualizações de agenda](#schedule-updates)
-   [Definições dos diagnósticos do](#diagnostics-settings)
-   [Definições de rede](#network-settings)
-   [Definições de gestão de recursos](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Suporte e resolução de problemas de definições

As definições na secção de **suporte + resolução de problemas de** fornecem-lhe opções para resolver problemas com a cache.

![Suporte + resolução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

Clique em **diagnosticar e resolver problemas de** a ser fornecido com estratégias e problemas comuns para os resolver.

Clique em **registo de atividade** para ver ações executadas na cache. Também pode utilizar a filtragem para expandir a esta vista para incluir outros recursos. Para mais informações sobre como trabalhar com registos de auditoria, consulte o artigo [Ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) e de [auditoria operações com o Gestor de recursos](../resource-group-audit.md). Para mais informações sobre como monitorizar eventos de Azure Redis Cache, consulte o artigo [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

**Estado de funcionamento do recurso** está a observar seu recurso e mostra-lhe se está a funcionar como esperado. Para mais informações sobre o serviço de estado de funcionamento do Azure recurso, consulte o artigo [Descrição geral de estado de funcionamento do Azure recurso](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Estado de funcionamento do recurso é atualmente não é possível comunicar o estado de funcionamento de instâncias do Azure Redis Cache alojado numa rede virtual. Para obter mais informações, consulte o artigo [todas as funcionalidades de cache funcionam quando o alojamento uma cache num VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Clique em **novo pedido de suporte** para abrir um pedido de suporte para a sua cache.

## <a name="general-settings"></a>Definições gerais

As definições na secção **Geral** permitem-lhe aceder e configurar as seguintes definições para a sua cache.

![Definições gerais](./media/cache-configure/redis-cache-general-settings.png)

-   [Propriedades](#properties)
-   [Teclas de acesso](#access-keys)
-   [Definições avançadas](#advanced-settings)
-   [Redis Cache Advisor](#redis-cache-advisor)

### <a name="properties"></a>Propriedades

Clique em **Propriedades** para ver informações sobre a cache, incluindo todas as portas e o ponto final de cache.

![Redis propriedades de Cache](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Teclas de acesso

Clique em **teclas de acesso** para ver ou gerar as teclas de acesso para a sua cache. Estas teclas são utilizadas juntamente com o nome do anfitrião e portas, impedindo o pá **Propriedades** pelos clientes ligar-se para a cache.

![Redis teclas de acesso de Cache](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Definições avançadas

As seguintes definições são configurar a pá **Definições avançadas** .

-   [Portas de acesso](#access-ports)
-   [Política de Maxmemory e reservados maxmemory](#maxmemory-policy-and-maxmemory-reserved)
-   [Notificações de Keyspace (definições avançadas)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Portas de acesso

Por predefinição, o access não SSL está desativado para caches novos. Para ativar a porta de que não sejam SSL, clique em **não** para **Permitir o acesso apenas através de SSL** no **pá definições avançadas** e, em seguida, clique em **Guardar**.

![Portas de acesso de Cache redis](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Política de Maxmemory e reservados maxmemory

As definições **Maxmemory política** e **reservada maxmemory** a pá **Definições avançadas de** configurar as políticas de memória para a cache. A definição de **política maxmemory** configura a política de eviction para a cache e **reservada maxmemory** configura memória reservada para que não sejam cache processos.

![Redis Cache Maxmemory política](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Política de Maxmemory** permite-lhe escolher a partir das seguintes políticas de eviction.

-   lru voláteis - esta é a predefinição.
-   allkeys lru
-   voláteis aleatórios
-   allkeys aleatórios
-   ttl voláteis
-   noeviction

Para mais informações sobre as políticas de maxmemory, consulte o artigo [políticas Eviction](http://redis.io/topics/lru-cache#eviction-policies).

A definição **reservada maxmemory** configura a quantidade de memória em MB que está reservada para operações de que não sejam cache, tais como a replicação durante activação pós-falha. Também podem ser utilizada quando tiver um rácio fragmentação alta. A definição deste valor permite-lhe para que tenha uma experiência de servidor mais consistente Redis quando o carregamento varia. Este valor deve ser definido superior para das cargas de trabalho são escrever grossa. Quando memória reservada para essas operações não está disponível para o armazenamento de dados em cache.

>[AZURE.IMPORTANT] A definição **reservada maxmemory** só está disponível para padrão e Premium coloca em cache.

### <a name="keyspace-notifications-advanced-settings"></a>Notificações de Keyspace (definições avançadas)

Redis keyspace notificações estão configuradas na pá **Definições avançadas** . Notificações de Keyspace permitir que os clientes receber notificações quando ocorrem determinados eventos.

![Redis Cache definições avançada](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Notificações de Keyspace e a definição de **Notificar-keyspace-eventos** só estão disponíveis para padrão e Premium coloca em cache.

Para mais informações, consulte o artigo [Redis notificações Keyspace](http://redis.io/topics/notifications). Código de exemplo, consulte o ficheiro [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) no exemplo [Olá a todos](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor

A **recomendações** pá apresenta recomendações para a cache. Durante operações normais, são apresentadas sem recomendações. 

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se todas as condições ocorrerem durante a operações da cache como a utilização de memória alta, largura de banda de rede ou carga do servidor, um alerta é apresentado no pá a **Redis Cache** .

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Podem encontrar mais informações sobre a pá **recomendações** .

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Pode monitorizar nestas métricas das secções de [monitorização gráficos](cache-how-to-monitor.md#monitoring-charts) e [gráficos de utilização](cache-how-to-monitor.md#usage-charts) de pá a **Redis Cache** .

Cada camada comparar tem limites diferentes para ligações de cliente, memória e largura de banda. Se a cache abordagens capacidade máxima para estes métricas através de um período de tempo constante, é criada uma recomendação. Para mais informações sobre os métricas e os limites revistos pela ferramenta de **recomendações** , consulte a tabela seguinte.

| Redis métrica Cache      | Para obter mais informações, consulte                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Utilização da largura de banda de rede | [Cache desempenho - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes ligados       | [Configuração do servidor Redis predefinida - maxclients](#maxclients)            |
| Carga do servidor             | [Gráficos de utilização - Redis carga do servidor](cache-how-to-monitor.md#usage-charts)  |
| Utilização de memória            | [Cache desempenho - tamanho](cache-faq.md#cache-performance)                |

Para atualizar a cache, clique em **atualizar agora** para alterar os [níveis de preços](#pricing-tier) e dimensionar a cache. Para obter mais informações sobre como escolher uma camada comparar, consulte o artigo [que oferta Redis Cache e o tamanho devo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Definições de escala

As definições na secção de **escala** permitem-lhe aceder e configurar as seguintes definições para a sua cache.

![Rede](./media/cache-configure/redis-cache-scale.png)

-   [Preços de camadas](#pricing-tier)
-   [Redis tamanho cluster](#cluster-size)

### <a name="pricing-tier"></a>Preços de camadas

Clique em **níveis de preços** para ver ou alterar a definição de preços camada para a sua cache. Para mais informações sobre dimensionamento, consulte o artigo [como escala Azure Redis Cache](cache-how-to-scale.md).

![Redis Cache preços de camadas](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Redis tamanho Cluster

Clique em **Tamanho do Cluster Redis (pré-visualização)** para alterar o tamanho de computação para executar um cache premium com clusters ativado.

>[AZURE.NOTE] Note que enquanto a camada Azure Redis Cache Premium foi lançada para disponibilidade geral, a funcionalidade de tamanho de Cluster Redis está atualmente na pré-visualização.

![Redis tamanho cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho de cluster, utilize o controlo de deslize ou escreva um número entre 1 e 10 na caixa de texto de **contagem de Shard** e clique em **OK** para guardar.

>[AZURE.IMPORTANT] Redis clusters só está disponível para Premium caches. Para mais informações, consulte o artigo [como configurar clusters para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).


## <a name="data-management-settings"></a>Definições de gestão de dados

As definições na secção **Gestão de dados** permitem-lhe aceder e configurar as seguintes definições para a sua cache.

![Gestão de dados](./media/cache-configure/redis-cache-data-management.png)

-   [Redis recorrência de dados](#redis-data-persistence)
-   [Importar/exportar](#importexport)

### <a name="redis-data-persistence"></a>Redis recorrência de dados

Clique em **Redis recorrência de dados** para ativar, desativar ou configurar a recorrência de dados para a sua cache premium.

![Redis recorrência de dados](./media/cache-configure/redis-cache-persistence-settings.png)

Para ativar Redis persistente, clique em **ativado** para ativar a cópia de segurança do RDB (Redis base de dados). Para desativar Redis persistente, clique em **desativado**.

Para configurar o intervalo de cópia de segurança, selecione uma **Cópia de segurança frequência** a partir da lista pendente. Opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Este intervalo inicia contando para baixo, depois da operação de cópia de segurança anterior é concluída com êxito e quando-decorrida é iniciada uma nova cópia de segurança.

Clique em **Conta de armazenamento** para selecionar a conta de armazenamento para utilizar e selecione a **chave primária** ou **chave secundária** para utilizar a partir do menu pendente **Chave de armazenamento** . Tem de escolher uma conta de armazenamento na mesma região como a cache e uma conta de **Armazenamento de Premium** é recomendada porque armazenamento premium tem débito superior. Sempre a chave de armazenamento da sua conta persistente é geradas novamente, tem de escolher voltar a chave pretendida a partir do menu pendente **Chave de armazenamento** .

Clique em **OK** para guardar a configuração de persistente.

>[AZURE.IMPORTANT] Recorrência de dados do redis só está disponível para Premium caches. Para mais informações, consulte o artigo [como configurar persistente para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).

### <a name="importexport"></a>Importar/exportar

Importar/exportar é uma operação de gestão de dados de Cache de Redis Azure que permite-lhe importar dados para o Azure Redis Cache ou exportar dados a partir do Azure Redis Cache por importar e exportar um instantâneo de base de dados de Cache Redis (RDB) a partir de uma cache premium para um blob de página numa conta de armazenamento do Azure. Permite-lhe migrar entre instâncias Azure Redis Cache diferentes ou preencher a cache com dados antes do utilizar.

Importar pode ser utilizado para trazer Redis compatíveis RDB (s) de qualquer servidor Redis a ser executada em qualquer na nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outras pessoas. A importação de dados é uma forma fácil de criar uma cache com dados preenchidos. Durante o processo de importação Azure Redis Cache carrega os ficheiros RDB a partir do armazenamento Azure na memória e, em seguida, insere as teclas a cache.

Exportar permite-lhe exportar os dados armazenados na Cache Redis Azure para Redis compatíveis RDB ficheiros específicos. Pode utilizar esta funcionalidade para mover os dados a partir de uma instância do Azure Redis Cache para outra ou para outro servidor Redis. Durante o processo de exportação que é criado um ficheiro temporário na VM que aloja a instância de servidor do Azure Redis Cache e o ficheiro é carregado para a conta de armazenamento designado. Quando a operação de exportação é concluída com um de um Estado de sucesso ou falha, o ficheiro temporário é eliminado.

>[AZURE.IMPORTANT] Importar/exportar apenas está disponível para caches de camada Premium. Para obter mais informações e instruções, consulte o artigo [Importar e exportar os dados na Cache de Redis Azure](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Definições de administração

As definições na secção **Administração** de permitem-lhe executar as seguintes tarefas administrativas para a sua cache premium. 

![Administração](./media/cache-configure/redis-cache-administration.png)

-   [Reiniciar o computador](#reboot)
-   [Atualizações de agenda](#schedule-updates)

>[AZURE.IMPORTANT] As definições nesta secção só estão disponíveis para caches de camada Premium.

### <a name="reboot"></a>Reiniciar o computador

O pá **reinicie** permite-lhe um ou mais nós da cache de reiniciar. Permite-lhe testar a sua aplicação de RDP em caso de falha.

![Reiniciar o computador](./media/cache-configure/redis-cache-reboot.png)

Se tiver uma cache premium com clusters ativado, pode selecionar quais shards da cache para reiniciar o computador.

![Reiniciar o computador](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar uma ou mais nós da cache, selecione os nós pretendido e clique em **reiniciar o computador**. Se tiver uma cache premium com clusters ativado, selecione shard(s) para reiniciar o computador e, em seguida, clique em **reiniciar**. Depois de alguns minutos, o reinício nós selecionado e estiver novamente online alguns minutos mais tarde.

>[AZURE.IMPORTANT] Reinicie só está disponível para caches de camada Premium. Para obter mais informações e instruções, consulte o artigo [Administração Azure Redis Cache - reiniciar o computador](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Atualizações de agenda

A **atualizações de agenda** pá permite-lhe designar uma janela de manutenção para Redis actualizações do servidor para a sua cache. 

>[AZURE.IMPORTANT] Tenha em atenção que a janela de manutenção só se aplica a Redis atualizações do servidor e não para qualquer Azure actualizações ou atualizações para o sistema operativo das VMs que alojam a cache.

![Atualizações de agenda](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias pretendidos e especificar a hora de início de janela de manutenção para cada dia e clique em **OK**. Tenha em atenção que a hora da janela de manutenção está no UTC. 

>[AZURE.IMPORTANT] Atualizações de agenda só está disponível para caches de camada Premium. Para obter mais informações e instruções, consulte o artigo [Azure Redis Cache administration - atualizações de agenda](cache-administration.md#schedule-updates).

## <a name="diagnostics-settings"></a>Definições dos diagnósticos do

A secção **Diagnósticos** permite-lhe configurar diagnósticos para a sua Cache Redis.

![Diagnósticos](./media/cache-configure/redis-cache-diagnostics.png)

Clique em **diagnóstico** para [Configurar a conta de armazenamento](cache-how-to-monitor.md#enable-cache-diagnostics) utilizada para armazenar diagnósticos de cache.

![Redis diagnósticos de Cache](./media/cache-configure/redis-cache-diagnostics-settings.png)

Clique em **Redis métricas** para [Ver métricas](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) sobre a cache e o **alertas regras** configurar [regras de alertas](cache-how-to-monitor.md#operations-and-alerts).

Para mais informações sobre diagnósticos do Azure Redis Cache, consulte o artigo [como monitorizar o Azure Redis Cache](cache-how-to-monitor.md).


## <a name="network-settings"></a>Definições de rede

As definições na secção **rede** permitem-lhe aceder e configurar as seguintes definições para a sua cache.

![Rede](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Definições de rede virtual só estão disponíveis para caches premium que tenham sido configurados com suporte VNET durante a criação de cache. Para obter informações sobre a criação de uma cache premium com VNET de suporte e atualizar as suas definições, consulte o artigo [como configurar a rede Virtual suporte para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Definições de gestão de recursos

![Gestão de recursos](./media/cache-configure/redis-cache-resource-management.png)

A secção **etiquetas** ajuda-o a organizar os seus recursos. Para mais informações, consulte o artigo [utilizar etiquetas para organizar os recursos do Azure](../resource-group-using-tags.md).

A secção **bloqueios** permite-lhe bloquear uma subscrição, grupo de recursos ou recurso para impedir que outros utilizadores na sua organização a partir de eliminar ou modificar recursos críticos acidentalmente. Para mais informações, consulte o artigo [recursos de bloqueio com o Gestor de recursos do Azure](../resource-group-lock-resources.md).

Secção **utilizadores** fornece suporte para o controlo de acesso baseado em funções (RBCA) no portal do Azure para o ajudar a cumprir as suas necessidades de gestão de acesso simples e precisa de organizações. Para obter mais informações, consulte o [controlo de acesso baseado em funções no portal do Azure](../active-directory/role-based-access-control-configure.md).

Clique em **Exportar modelo** para criar e exportar um modelo dos seus recursos implementados para implementações futuras. Para mais informações sobre como trabalhar com modelos, consulte o artigo [Implementar recursos com modelos de Gestor de recursos do Azure](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Configuração do servidor Redis predefinida

Novas instâncias do Azure Redis Cache estão configuradas com os seguintes valores de configuração de Redis predefinidos.

>[AZURE.NOTE] As definições nesta secção não podem ser alteradas utilizando o `StackExchange.Redis.IServer.ConfigSet` método. Se este método chama-se com um dos comandos nesta secção, é devolvida uma exceção semelhante ao seguinte:  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Todos os valores que são configuráveis, tal como a **política de memória máximo**, são configuráveis através das ferramentas de gestão de portal ou linha de comandos Azure como Azure clip ou PowerShell.

|Definição|Valor predefinido|Descrição|
|---|---|---|
|bases de dados|16|O número predefinido de bases de dados é 16 mas pode configurar um número diferente com base na camada comparar. <sup>1</sup> a base de dados predefinido é DB 0, pode selecionar um diferente num utilizando base por ligação `connection.GetDatabase(dbid)` onde dbid é um número entre `0` e `databases - 1`.|
|maxclients|Depende da etapa comparar<sup>2</sup>|Este é o número máximo de clientes ligados permitido ao mesmo tempo. Assim que o limite é atingido Redis irá fechar todas as novas ligações Enviar um erro 'número máximo de clientes atingido'.|
|política de maxmemory|lru voláteis|Política de Maxmemory é a definição para como Redis irá selecione-o que remova quando é atingido maxmemory (o tamanho da cache de perguntar se que tiver seleccionado quando criou a cache do). Com o Azure Redis Cache a predefinição é voláteis-lru, que remove as teclas com um conjunto utilizando um algoritmo LRU de expirar. Esta definição pode ser configurada no portal do Azure. Para obter mais informações, consulte o artigo [Maxmemory política e reservados maxmemory](#maxmemory-policy-and-maxmemory-reserved).|
|Exemplos de maxmemory|3|LRU e algoritmos TTL mínimos não são precisos algoritmos mas aproximadas algoritmos (para poupar memória), para que também pode selecionar o tamanho da amostra para verificar. Por exemplo, para predefinido Redis verifica três teclas e escolha a que foi utilizado menos recentemente.|
|limite de tempo lua|5.000|Tempo de execução de máximo de um script Lua em milissegundos. Se o tempo de execução máximo for atingido Redis registará que um script ainda está em execução depois do máximo permitido tempo e irá iniciar para responder às consultas com um erro.|
|limite de evento lua|500|Este é o tamanho máximo de fila de eventos de script.|
|memória intermédia de limite de cliente-saída memória intermédia de limite de normalclient-saída pubsub|0 0 032mb 8mb 60|Os limites de memória intermédia de saída do cliente podem ser utilizados para forçar interrupção de clientes que não são leitura de dados do servidor com a rapidez necessária por algum motivo (um motivo comum é que um cliente Pub/Sub não é possível consumir mensagens tão rápidas quanto o publisher pode produzi-los). Para mais informações, consulte o artigo [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> O limite para `databases` é diferente para cada Azure Redis Cache de camadas de preços e pode ser definida na criação da cache. Se não `databases` definição for especificada durante a criação de cache, a predefinição é 16.

-   Básicas e de padrão
    -   C0 cache (250 MB) - até 16 bases de dados
    -   C1 cache (1 GB) - até 16 bases de dados
    -   C2 cache (2,5 GB) - até 16 bases de dados
    -   C3 (6 GB) cache - até 16 bases de dados
    -   C4 cache (13 GB) - até 32 bases de dados
    -   C5 cache (26 GB) - até 48 bases de dados
    -   C6 cache (53 GB) - até 64 bases de dados
-   Premium coloca em cache
    -   P1 (6 GB - 60 GB) - até 16 bases de dados
    -   P2 (13 GB - 130 GB) - até 32 bases de dados
    -   P3 (26 GB - 260 GB) - até 48 bases de dados
    -   E4 (53 GB - 530 GB) - até 64 bases de dados
    -   Todas as caches premium com cluster Redis ativado - Redis cluster só suporta a utilização da base de dados 0, para que o `databases` limitar para qualquer cache premium com cluster Redis ativado eficazmente é 1 e o comando [Select](http://redis.io/commands/select) não é permitido. Para obter mais informações, consulte o artigo [é preciso Efetue alterações às minhas aplicações de cliente para utilizar clusters?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] O `databases` definição pode ser configuradas apenas durante a criação da cache e apenas utilizar o PowerShell, clip ou outros clientes do gestão. Para obter um exemplo de configurar `databases` durante a criação de cache através do PowerShell, consulte o artigo [AzureRmRedisCache novo](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada Azure Redis Cache preços de camadas.

-   Básicas e de padrão
    -   C0 cache (250 MB) - até 256 ligações
    -   C1 cache (1 GB) - até 1.000 ligações
    -   C2 cache (2,5 GB) - até 2000 ligações
    -   C3 (6 GB) cache - até 5.000 ligações
    -   C4 cache (13 GB) - até 10.000 ligações
    -   C5 cache (26 GB) - até 15.000 ligações
    -   C6 cache (53 GB) - até 20.000 ligações
-   Premium coloca em cache
    -   P1 (6 GB - 60 GB) - até 7,500 ligações
    -   P2 (13 GB - 130 GB) - até 15.000 ligações
    -   P3 (26 GB - 260 GB) - até 30.000 ligações
    -   E4 (53 GB - 530 GB) - até 40.000 ligações

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis comandos não suportados no Azure Redis Cache

>[AZURE.IMPORTANT] Uma vez que a configuração e gestão de instâncias do Azure Redis Cache é gerida pela Microsoft os seguintes comandos são desativados. Se tentar invocá-los receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIGURAÇÃO
>-  DEPURAR
>-  MIGRAR
>-  GUARDAR
>-  ENCERRAMENTO
>-  SLAVEOF
>-  CLUSTER - Cluster escrever os comandos estão desactivados, mas só de leitura Cluster comandos são permitidos.

Para mais informações sobre Redis comandos, consulte o artigo [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis consola

Em segurança pode emitir comandos para o seu instâncias de Cache de Redis Azure utilizando a **Consola Redis**, que está disponível para padrão e Premium coloca em cache.

>[AZURE.IMPORTANT] Consola do Redis não funciona com VNET, clusters e de bases de dados que não 0. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - quando a cache faz parte de um VNET, apenas os clientes a VNET podem aceder a cache. Uma vez que a consola Redis utiliza o cliente de redis cli.exe alojado no VMs que não fazem parte da sua VNET, não consegue ligar à sua cache.
>-  [Clusters](cache-how-to-premium-clustering.md) - a consola Redis utiliza o cliente de redis cli.exe que não suporta clusters neste momento. O utilitário redis clip ramo [instável](http://redis.io/download) do repositório Redis na GitHub implementa suporte básico quando iniciado com o `-c` mudar. Para obter mais informações consulte o artigo [em reprodução com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [http://redis.io](http://redis.io) no [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial).
>-  Consola do Redis faz com que uma nova ligação à base de dados 0 sempre que submeter um comando. Não é possível utilizar o `SELECT` comando para selecionar uma base de dados diferente, porque a base de dados é reposta para 0 com cada comando. Para obter informações sobre como executar Redis comandos, incluindo alterar a uma base de dados diferente, consulte o artigo [como executar Redis comandos?](cache-faq.md#how-can-i-run-redis-commands)

Para aceder a consola Redis, clique em **consola** de pá a **Redis Cache** .

![Redis consola](./media/cache-configure/redis-console-menu.png)

Emitir comandos contra a instância de cache, basta escreva no comando pretendido na consola do.

![Redis consola](./media/cache-configure/redis-console.png)

Para a lista de comandos de Redis que são desativados para Azure Redis Cache, consulte a secção anterior [Redis comandos não suportados no Azure Redis Cache](#redis-commands-not-supported-in-azure-redis-cache) . Para mais informações sobre Redis comandos, consulte o artigo [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Deslocar-se a cache para uma nova subscrição

Pode mover a cache para uma nova subscrição ao clicar em **Mover**.

![Deslocar-se Redis Cache](./media/cache-configure/redis-cache-move.png)

Para obter informações sobre como mover recursos de grupo de um recurso para outro e, a partir de uma subscrição para outra, consulte o artigo [Mover recursos para novo grupo de recursos ou subscrição](../resource-group-move-resources.md).

## <a name="next-steps"></a>Próximos passos
-   Para obter mais informações sobre como trabalhar com comandos Redis, consulte o artigo [como executar Redis comandos?](cache-faq.md#how-can-i-run-redis-commands).
