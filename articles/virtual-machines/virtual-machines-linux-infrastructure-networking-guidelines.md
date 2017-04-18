<properties
    pageTitle="Diretrizes de infraestrutura de rede | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para implementar o funcionamento em rede virtual nos serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="networking-infrastructure-guidelines"></a>Diretrizes de infraestrutura de rede

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo foca-se em Noções sobre os passos necessários planeamento de rede virtual dentro do Azure e conectividade entre ambientes no local existentes.


## <a name="implementation-guidelines-for-virtual-networks"></a>Diretrizes de implementação para redes virtuais

Decisões:

- Que tipo de rede virtual que precisa de alojar o seu carga de trabalho ou infraestrutura (apenas na nuvem ou cruzada local)?
- Para redes virtuais cruzada local, quanto espaço de endereços que precisa alojar sub-redes e VMs agora e para a expansão razoável no futuro?
- Vai para criar redes virtuais centralizadas ou criar redes virtuais individuais para cada grupo de recursos?

Tarefas:

- Defina o espaço de endereços para as redes virtuais seja criada.
- Defina o conjunto de sub-redes e o espaço de endereços para cada um.
- Para publicação em local redes virtuais, definir o conjunto de espaços de endereços de rede local para as localizações no local que precisar de entrar VMs na rede virtual.
- Trabalhar com os no local equipa de rede para garantir que o encaminhamento adequado está configurada quando criar cruzada local redes virtuais.
- Crie a rede virtual utilizando Convenção de nomenclatura.


## <a name="virtual-networks"></a>Redes virtuais

Redes virtuais são necessários para suportar as comunicações entre máquinas virtuais (VMs). Pode definir sub-redes, endereço IP personalizado, as definições de DNS, filtragem de segurança e balanceamento de carga, tal como acontece com redes física. Ao utilizar um [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [Express rota circuito](../expressroute/expressroute-introduction.md), pode ligar redes virtuais Azure às suas redes no local. Pode ler mais sobre [redes virtuais e os respetivos componentes](../virtual-network/virtual-networks-overview.md).

Ao utilizar grupos de recursos, tem de flexibilidade no modo de estruturação componentes de rede virtual. VMs podem ligar a redes virtuais fora da sua própria grupo de recursos. Uma abordagem de estrutura comuns seria criar grupos de recursos centralizada que contêm a sua infraestrutura de rede core pode ser gerida por uma equipa comuns. VMs e suas aplicações implementadas a grupos de recursos em separado. Esta abordagem permite o acesso de proprietários de aplicação para o grupo de recursos que contém os respetivos VMs sem abrir o acesso à configuração dos mais largo virtuais redes recursos.

## <a name="site-connectivity"></a>Conectividade de site

### <a name="cloud-only-virtual-networks"></a>Redes virtuais apenas na nuvem
Se a utilizadores no local e computadores não necessitam de conectividade em curso ao VMs numa rede virtual Azure, a estrutura da rede virtual aqui diretamente reencaminhar:

![Diagrama de rede base de virtual apenas na nuvem](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Esta abordagem é normalmente das cargas de trabalho de acesso à Internet, tal como um servidor web baseados na Internet. Pode gerir estas VMs utilizando SSH ou ligações de VPN ponto-para-site.

Porque é que se ligam à sua rede no local, redes virtuais Azure só podem utilizar qualquer parte do espaço de endereços IP privado. O espaço de endereços pode ser o mesmo espaço privado que está a utilizar no local.


### <a name="cross-premises-virtual-networks"></a>Redes virtuais cruz local
Se a utilizadores no local e computadores necessitam de conectividade em curso ao VMs numa rede virtual Azure, crie uma rede virtual cruzada local. Ligar a rede virtual a sua rede no local com uma ligação de VPN do site para o site ou ExpressRoute.

![Diagrama de rede virtual cruzada local](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

Nesta configuração, a rede virtual Azure é, essencialmente, uma baseado na nuvem extensão da sua rede no local.

Uma vez que se ligam a sua rede no local, cruzada local redes virtuais tem de utilizar uma parte do espaço de endereços utilizada pela sua organização que seja exclusiva. Da mesma forma que diferentes localizações da empresa estão atribuídas uma sub-rede IP específica, Azure torna-se outra localização como expandir fora da sua rede.

Para permitir que os pacotes de viagem da sua rede virtual cruzada local à sua rede no local, tem de configurar o conjunto de prefixos de endereços relevantes no local como parte da definição de rede local para a rede virtual. Consoante o espaço de endereços da rede virtual e o conjunto de localizações relevantes no local, podem existir vários prefixos de endereço na rede local.

Pode converter uma rede virtual apenas na nuvem para uma rede virtual cruzada local, mas é provável requer que re IP o espaço de endereços de rede virtual e recursos Azure. Por conseguinte, considere cuidadosamente se uma rede virtual tem de estar ligado à rede no local ao atribuir uma sub-rede IP.

## <a name="subnets"></a>Sub-redes
Sub-redes permitem-lhe organizar os recursos que estão relacionados, quer logicamente (por exemplo, uma sub-rede para VMs associados à mesma aplicação), ou física (por exemplo, uma sub-rede por grupo de recursos). Também pode utilizar técnicas de isolamento de sub-rede para segurança adicional.

Para redes virtuais cruzada local, deve conceber sub-redes com as mesmas convenções de que utiliza para recursos no local. **Azure sempre utiliza os três primeiros endereços IP do espaço de endereços para cada sub-rede**. Para determinar o número de endereços necessários para a sub-rede, comece por contagem do número de VMs que precisa de agora. Estimar para futuro crescimento e, em seguida, utilize a tabela seguinte para determinar o tamanho da sub-rede.

Número de VMs necessário | Número de bits do sistema anfitrião conforme necessário | Tamanho da sub-rede
--- | --- | ---
1-3 | 3 | / 29
4 – 11     | 4 | / 28
12 – 27 | 5 | / 27
28 – 59 | 6 | / 26
60 – 123 | 7 | / 25

> [AZURE.NOTE] Para sub-redes normal no local, o número máximo de endereços do anfitrião para uma sub-rede com bits do sistema anfitrião n é 2<sup>n</sup> – 2. Para uma sub-rede Azure, o número máximo de endereços do anfitrião para uma sub-rede com bits do sistema anfitrião n é 2<sup>n</sup> -5 (2 mais 3 para os endereços que utiliza o Azure em cada sub-rede).

Se escolher um tamanho de sub-rede é demasiado pequeno, que tem de re IP e implementar VMs na sub-rede.


## <a name="network-security-groups"></a>Grupos de segurança de rede
Pode aplicar as regras de filtragem para o tráfego que flua através de redes virtuais ao utilizar grupos de segurança de rede. Pode criar regras de filtragem granulares para proteger o seu ambiente de funcionamento em rede virtual, controlar entrados e saídos tráfego, origem e destino intervalos IP, permitido portas, etc. Grupos de segurança de rede podem ser aplicados para sub-redes dentro de uma rede virtual ou diretamente para uma interface de rede virtual determinado. Recomenda-se de ter algum nível de grupo de segurança de rede filtragem tráfego nas suas redes virtuais. Pode ler mais sobre os [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).


## <a name="additional-network-components"></a>Componentes de rede adicionais
Tal como acontece com no local física rede infraestrutura, a rede virtual Azure pode conter mais do que apenas sub-redes e endereçamento IP. Como estruturar sua infraestrutura de aplicação, poderá pretender incorporar alguns destes componentes adicionais:

- [VPN gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md) - ligar Azure redes virtuais para outras redes virtuais Azure, ou ligar a redes no local através de uma ligação VPN do Site para o Site. Implementar ligações de rota Express para ligações seguras e dedicadas. Também pode fornecer acesso direto aos utilizadores com ligações ponto-para-Site VPN.
- [Balanceador de carga](../load-balancer/load-balancer-overview.md) - fornece balanceamento de carga de tráfego para o tráfego de interno e externo conforme pretender.
- [Aplicação Gateway](../application-gateway/application-gateway-introduction.md) - Balanceador de carga de HTTP balanceamento de carga na camada de aplicação, fornecendo alguns benefícios adicionais relacionados para aplicações web em vez de implementar o Azure.
- [Gestor de tráfego](../traffic-manager/traffic-manager-overview.md) - distribuição de tráfego com base no DNS para direcionar os utilizadores finais para o ponto final aplicações disponíveis mais próximo, permitindo-lhe alojar o seu aplicação fora do Azure centros de dados de diferentes regiões.


## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 