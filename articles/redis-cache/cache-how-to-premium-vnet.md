<properties 
    pageTitle="Como configurar o suporte de rede Virtual para uma Cache do Premium Azure Redis | Microsoft Azure" 
    description="Saiba como criar e gerir o suporte de rede Virtual para sua instâncias de Azure Redis Cache de camadas Premium" 
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

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium
Azure Redis Cache tem ofertas de cache diferentes que disponibiliza a flexibilidade na escolha do tamanho da cache e funcionalidades, incluindo a nova camada de Premium.

As Azure Redis Cache de camadas às funcionalidades premium do incluem clusters, persistente e suporte de rede virtual (VNet). Um VNet é uma rede privada na nuvem. Quando uma instância do Azure Redis Cache está configurada com uma VNet, não é publicamente endereçável e só podem ser acedida a partir do máquinas virtuais e aplicações dentro de VNet. Este artigo descreve como configurar o suporte de rede virtual para uma instância do Azure Redis Cache premium.

>[AZURE.NOTE] Azure Redis Cache suporta ambos os clássica e processador VNets.

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução à camada Azure Redis Cache Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que motivo VNet?
Implementação do [Azure rede Virtual (VNet)](https://azure.microsoft.com/services/virtual-network/) fornece segurança melhorada e isolamento para a Cache Redis Azure, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades ainda mais restringir o acesso a Cache de Redis Azure.

## <a name="virtual-network-support"></a>Suporte de rede virtual
Suporte de rede (VNet) virtual está configurada no pá a **Nova Cache Redis** durante a criação de cache. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Assim que tiver selecionado um prémio preços de camadas, pode configurar integração do Azure Redis Cache VNet ao selecionar uma VNet que está na mesma localização, como a cache e subscrição. Para utilizar uma nova VNet, criá-lo pela primeira vez seguindo os passos em [criar uma rede virtual através do portal Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [criar uma rede virtual (clássico) utilizando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) e, em seguida, regressar ao pá a **Nova Cache Redis** para criar e configurar a cache de premium.

Para configurar VNet para a sua nova cache, clique em **Rede Virtual** no pá a **Nova Cache Redis** e selecione o VNet pretendido a partir da lista pendente.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede pretendida a partir da lista pendente de **sub-rede** e especifique o **endereço IP estático**de pretendida. Se estiver a utilizar um VNet clássica o campo **endereço IP estático** é opcional e, se não for especificado nenhum, um é escolhido de sub-rede seleccionada.

>[AZURE.IMPORTANT] Quando implementar uma Cache do Azure Redis VNet um processador, a cache tem de ser uma sub-rede dedicada que não contém outros recursos, à exceção instâncias de Cache Redis do Azure. Se for feita uma tentativa para implementar uma Cache do Azure Redis VNet um processador para uma sub-rede que contenha outros recursos, a implementação falha.

![Rede virtual][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Os endereços numa sub-rede primeiros quatro são reservados e não podem ser utilizados. Para obter mais informações, consulte o artigo [existem restrições sobre como utilizar endereços IP dentro estas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Quando a cache estiver criada, pode ver a configuração para o VNet clicando em **Rede Virtual** pá as **Definições** .

![Rede virtual][redis-cache-vnet-info]


Para ligar à sua instância de cache Azure Redis ao utilizar um VNet, especifique o nome do anfitrião da cache na cadeia de ligação, conforme mostrado no seguinte exemplo.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet perguntas mais frequentes

A lista seguinte contém respostas às perguntas mais frequentes sobre o dimensionamento Azure Redis Cache.

-   [O que são alguns problemas de configuração errada comuns com a Cache de Redis Azure e VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [Pode utilizar VNets com uma cache padrão ou básica?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [Por que motivo é que a criação de uma cache Redis falhar em algumas sub-redes mas não outros?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [Todas as funcionalidades de cache funcionam quando o alojamento uma cache num VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>O que são alguns problemas de configuração errada comuns com a Cache de Redis Azure e VNets?

Quando a Cache de Redis Azure está alojado num VNet, são utilizadas as portas na tabela seguinte. Se estas portas estão bloqueadas, a cache poderá não funcionar corretamente. Está a ter um ou mais dos seguintes portas bloqueadas é o problema de configuração errada mais comuns ao utilizar a Cache de Redis Azure num VNet.

| Porta (s)     | Direção        | Protocolo de transporte | Objetivo                                                                           | IP remoto                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | Saída         | TCP                | Redis dependências no Azure armazenamento/PKI (Internet)                                | *                                   |
| 53          | Saída         | TCP/UDP RECOMENDADAS            | Redis dependências no DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | Entrada          | TCP                | Comunicação do cliente para Redis, balanceamento de carga Azure                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | Entrada/saída | TCP                | Detalhe de implementação para Redis                                                   | VIRTUAL_NETWORK                     |
| 8500        | Entrada          | TCP/UDP RECOMENDADAS            | Balanceamento de carga Azure                                                              | AZURE_LOADBALANCER                  |
| 10221 10231 | Entrada/saída | TCP                | Detalhe de implementação para Redis (podem restringir o ponto final remoto a VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000 13999 | Entrada          | TCP                | Comunicação do cliente para Redis Clusters, balanceamento de carga Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000 15999 | Entrada          | TCP                | Comunicação do cliente para Redis Clusters, balanceamento de carga Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | Entrada          | TCP/UDP RECOMENDADAS            | Balanceamento de carga Azure                                                              | AZURE_LOADBALANCER                  |
| 20226       | Entrada + saída | TCP                | Detalhe de implementação para Redis Clusters                                          | VIRTUAL_NETWORK                     |


Existem requisitos de conectividade de rede para Azure Redis Cache de que não poderá ser correspondidos inicialmente numa rede virtual. Azure Redis Cache requer todos os itens seguintes para funcionar corretamente quando utilizada dentro de uma rede virtual.

-  Conectividade de rede de saída para os pontos finais de armazenamento do Windows Azure em todo o mundo. Isto inclui os pontos finais localizados na mesma região como a instância do Azure Redis Cache, bem como os pontos finais de armazenamento localizado na **outras** regiões Azure. Azure armazenamento os pontos finais resolver em seguintes domínios de DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*e *file.core.windows.net*. 
-  Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. É necessário para suportar a funcionalidade SSL este conectividade.
-  A configuração de DNS para a rede virtual tem de ser capaz de resolver todos os pontos finais e domínios mencionados os pontos anteriores. Estes requisitos de DNS podem ser correspondidos por garantir uma infraestrutura DNS válida é configurada e manter-se para a rede virtual.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Pode utilizar VNets com uma cache padrão ou básica?

VNets só podem ser utilizados com premium caches.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Por que motivo é que a criação de uma cache Redis falhar em algumas sub-redes mas não outros?

Se estiver a implementar uma Cache do Azure Redis VNet um processador, a cache tem de ser uma sub-rede dedicada que contém a outro tipo de recurso. Se for feita uma tentativa para implementar uma Cache do Azure Redis uma sub-rede processador VNet que contenha outros recursos, a implementação falha. Tem de eliminar os recursos existentes dentro da sub-rede antes de poder criar uma nova cache Redis.

Pode implementar os vários tipos de recursos para um VNet clássica desde tenha suficiente endereços IP disponíveis.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todas as funcionalidades de cache funcionam quando o alojamento uma cache num VNET?

Quando a cache fizer parte de um VNET, apenas os clientes a VNET podem aceder a cache. Como resultado, as seguintes funcionalidades de gestão da cache não funcionam neste momento.

-   Redis consola - porque Redis consola utiliza o cliente de redis cli.exe alojado no VMs que não fazem parte da sua VNET, não é possível ligar para a cache.


## <a name="use-expressroute-with-azure-redis-cache"></a>Utilizar ExpressRoute com Azure Redis Cache

Os clientes podem ligar um circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) para sua infraestrutura de rede virtual, assim expandir a sua rede no local para Azure. 

Por predefinição, um circuito ExpressRoute recentemente criado anuncia uma rota predefinida que permite a conectividade saída de Internet. Com esta configuração, aplicações de cliente são possível ligar a outras os pontos finais Azure incluindo Azure Redis Cache.

No entanto, uma configuração de cliente comum é definir os seus próprios rota de predefinida (0.0.0.0/0) força o tráfego de saída de Internet em vez disso fluxo no local. Este fluxo de tráfego Invariavelmente quebras de conectividade com Azure Redis Cache porque o tráfego de saída é quer bloqueado no local ou NAT utilizaria para um conjunto de endereços que já não funcionam com os pontos finais Azure vários reconhecido.

A solução é definir um (ou mais) rotas definidos pelo utilizador (UDRs) na sub-rede que contém a Cache do Azure Redis. Um UDR define rotas de sub-rede específicas que serão respeitadas em vez da rota predefinida.

Se possível, recomenda-se para utilizar a configuração seguinte:

- A configuração de ExpressRoute anuncia 0.0.0.0/0 e por predefinição forçar estabelece um túnel todo o tráfego de saída no local.
- UDR aplicado à sub-rede que contém a Cache do Azure Redis define 0.0.0.0/0 com um tipo de salto seguinte de Internet (um exemplo de isto é mais para baixo neste artigo).

O efeito combinado destes passos é que o nível de sub-rede UDR terá precedência relativamente ao ExpressRoute forçada túnel, garantindo assim o acesso à Internet saído da Cache de Redis do Azure.

Apesar de ligação a uma instância da Cache de Redis Azure a partir de um local aplicação utilizando ExpressRoute não é um cenário de utilização típica devido a razões de desempenho (para um melhor desempenho Azure Redis Cache clientes devem ser na mesma região como a Cache do Azure Redis), neste cenário, que o caminho de rede de saída não é possível viajam através de internos proxies da empresa, nem pode estar forçar túnel para no local. Fazê-lo altera o endereço NAT eficaz saída de tráfego de rede da Cache de Redis do Azure. Alterar o endereço NAT de uma Cache do Azure Redis tráfego de rede de saída da instância faz com que o falhas de conectividade a muitas dos pontos finais listados em cima. Isto resulta em tentativas falhadas de criação de Azure Redis Cache.

**Importante:**  Rotas definidas numa UDR **deve** ser específicas precedência sobre qualquer rotas anunciadas pela configuração ExpressRoute. O exemplo seguinte utiliza o intervalo de endereços 0.0.0.0/0 abrangentes e como tal pode potencialmente acidentalmente substituído por anúncios de rota utilizando intervalos de endereços mais específicos.

**Muito importante:**  Azure Redis Cache não é suportado com configurações ExpressRoute esse **incorretamente cruzada-anunciar rotas a partir do caminho peering público para o caminho peering privado**. ExpressRoute configurações que tenham efectuado público configurado, irá receber anúncios de rota da Microsoft para um conjunto maior de intervalos de endereços IP do Microsoft Azure. Se estes intervalos de endereços são incorretamente cruzada-anunciada no caminho peering privado, o resultado final é que todos os pacotes de rede de saída de sub-rede a instância Azure Redis Cache estão incorretamente forçar-túnel à infraestrutura de rede de um cliente no local. Este fluxo de rede quebras Azure Redis Cache. Solução para este problema é parar de publicidade cruzada rotas, a partir do caminho peering público para o caminho peering privada.

Obter informações gerais sobre vias definidos pelo utilizador estão disponíveis nesta [Descrição geral](../virtual-network/virtual-networks-udr-overview.md). 

Para mais informações sobre ExpressRoute, consulte o artigo [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Próximos passos
Saiba como utilizar mais funcionalidades de cache premium.

-   [Introdução à camada Azure Redis Cache Premium](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

