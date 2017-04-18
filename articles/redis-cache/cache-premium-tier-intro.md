<properties 
    pageTitle="Introdução à camada Azure Redis Cache Premium | Microsoft Azure" 
    description="Saiba como criar e gerir Redis persistente, Redis clusters VNET suporte e para o seu instâncias de Azure Redis Cache de camadas Premium" 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introdução à camada Azure Redis Cache Premium
Azure Redis Cache é uma cache distribuída, gerida que o ajuda a criar aplicações altamente dimensionáveis e responder, fornecendo um acesso rápido informação aos seus dados. 

A nova camada de Premium é uma camada pronta da empresa, que inclui todas as funcionalidades de camadas padrão e muito mais, tal como um melhor desempenho, maiores das cargas de trabalho, recuperação de falhas, importar/exportar e segurança melhorada. Continue a ler para saber mais sobre as funcionalidades adicionais da camada de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em comparação comparado padrão ou Basic camada
**Um melhor desempenho ao longo do padrão ou básicas camada.** Caches na camada Premium são implementados em hardware que tem processadores mais rápidos e dá-melhor desempenho em comparação comparado a Basic ou camada padrão. Premium camada Caches tem débito superior e inferiores latências. 

**Débito para a Cache de tamanho idêntico mesmo é mais alto no Premium em comparação com camadas padrão.** Por exemplo, o caudal de um 53 E4 GB (Premium) cache está pedidos de 250K por segundo em comparação com 150 K para C6 (padrão).

Para obter mais informações sobre o tamanho, débito e a largura de banda com premium caches, consulte o artigo [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis recorrência de dados
A camada Premium permite-lhe manter os dados de cache numa conta de armazenamento do Windows Azure. Na cache de Basic/padrão todos os dados são armazenados apenas na memória. Em caso de infraestrutura subjacente não existem problemas podem ser possível perda de dados. Recomendamos que utilize a funcionalidade de persistente Redis dados na camada Premium para aumentar RDP contra a perda de dados. Azure Redis Cache oferece RDB e AOF (brevemente) opções no [Redis persistente](http://redis.io/topics/persistence). 

Para obter instruções sobre como configurar persistente, consulte o artigo [como configurar persistente para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Redis cluster
Se pretender criar caches maior do que 53 GB ou pretende dados shard através de vários Redis nós, pode utilizar Redis clusters que se encontra disponível na camada Premium. Cada nó é constituída por um par de cache principal/réplica gerido pelo Azure para elevada disponibilidade. 

**Redis clusters lhe escala máxima e débito.** Débito linear aumenta à medida que aumenta o número de shards (nós) no cluster. Por ex. Se criar um cluster de E4 de 10 shards, em seguida, o débito disponível é 250K * 10 = pedidos de milhões de 2,5 por segundo. Consulte as [FAQ do Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, débito e largura de banda com premium caches.

Para começar com clusters, consulte o artigo [como configurar clusters para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Segurança melhorada e isolamento

Caches criados na camada Basic ou padrão são acessíveis na internet público. Acesso à Cache é restrito com base na tecla de acesso. Com a camada Premium ainda mais pode garantir que apenas os clientes dentro de uma rede especificada podem aceder a Cache. Pode implementar Redis Cache numa [Rede Virtual Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Pode utilizar todas as funcionalidades de VNet como sub-redes, políticas de controlo de acesso, e outras funcionalidades novas para restringir o acesso a Redis.

Para mais informações, consulte o artigo [como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importar/exportar

Importar/exportar é uma operação de gestão de dados de Cache de Redis Azure que permite-lhe importar dados para o Azure Redis Cache ou exportar dados a partir do Azure Redis Cache por importar e exportar um instantâneo de base de dados de Cache Redis (RDB) a partir de uma cache premium para um blob de página numa conta de armazenamento do Azure. Permite-lhe migrar entre instâncias Azure Redis Cache diferentes ou preencher a cache com dados antes do utilizar.

Importar pode ser utilizado para trazer Redis compatíveis RDB (s) de qualquer servidor Redis a ser executada em qualquer na nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outras pessoas. A importação de dados é uma forma fácil de criar uma cache com dados preenchidos. Durante o processo de importação, Azure Redis Cache carrega os ficheiros RDB a partir do armazenamento Azure na memória e, em seguida, insere as teclas a cache.

Exportar permite-lhe exportar os dados armazenados na Cache Redis Azure para Redis compatíveis RDB ficheiros específicos. Pode utilizar esta funcionalidade para mover os dados a partir de uma instância do Azure Redis Cache para outra ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário na VM que aloja a instância de servidor do Azure Redis Cache e o ficheiro é carregado para a conta de armazenamento designado. Quando a operação de exportação é concluída com um de um Estado de sucesso ou falha, o ficheiro temporário é eliminado.

Para mais informações, consulte o artigo [como importar dados para e exportar os dados a partir do Azure Redis Cache](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar o computador

A camada premium permite-lhe um ou mais nós do seu cache a pedido reiniciar o computador. Esta opção permite-lhe testar a sua aplicação de RDP em caso de falha. Pode reiniciar nós seguintes.

-   Nó do modelo global da cache
-   Nó subordinado da cache
-   Principal e secundária nós da cache
-   Quando utilizar uma cache premium com clusters, pode reiniciar o modelo global, secundário ou ambos os nós para shards individuais na cache

Para mais informações, consulte o artigo [reinicie](cache-administration.md#reboot) e [Reinicie o FAQ](cache-administration.md#reboot-faq).

## <a name="schedule-updates"></a>Atualizações de agenda

A funcionalidade de atualizações agendada permite-lhe designar uma janela de manutenção para a cache. Quando a janela de manutenção for especificada, quaisquer actualizações de servidor Redis são feitas durante esta janela. Para designar uma janela de manutenção, selecione os dias pretendidos e especificar a manutenção janela hora de início para cada dia. Tenha em atenção que a hora da janela de manutenção está no UTC. 

Para mais informações, consulte o artigo [Agendar actualizações](cache-administration.md#schedule-updates) e [Agendar actualizações FAQ](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Apenas Redis servidor atualizações forem efetuadas durante a janela de manutenção agendada. A janela de manutenção não se aplica ao Azure atualizações ou em atualizações para o sistema operativo VM.

## <a name="to-scale-to-the-premium-tier"></a>Para dimensionar para a camada premium

Para dimensionar para a camada premium, basta Selecione uma das camadas premium na pá **alterar preços de camadas** . Também pode dimensionar a cache na camada premium utilizar o PowerShell e o clip. Para obter instruções passo a passo, consulte o artigo [como escala Azure Redis Cache](cache-how-to-scale.md) e [como para automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Próximos passos

Criar uma cache e explore as novas funcionalidades de camada premium.

-   [Como configurar persistente para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md)
-   [Como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md)
-   [Como configurar clusters para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md)
-   [Como importar dados para e exportar dados da Cache Redis do Azure](cache-how-to-import-export-data.md)
-   [Como administrar a Cache de Redis Azure](cache-administration.md)
  

