<properties
   pageTitle="Descrição geral de rede Virtual Azure (VNet)"
   description="Saiba mais sobre redes virtuais (VNets) no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>Descrição geral de rede virtual

Uma rede virtual Azure (VNet) é uma representação da sua própria rede na nuvem.  É um isolamento lógico da nuvem Azure dedicada à sua subscrição. Pode controlar totalmente a blocos de endereços IP, definições de DNS, políticas de segurança e encaminhar tabelas dentro desta rede. Pode também ainda mais segmentar seu VNet em sub-redes e iniciação máquinas virtuais do Azure IaaS (VMs) e/ou [serviços em nuvem (instâncias de função PaaS)](../cloud-services/cloud-services-choose-me.md). Para além disso, pode ligar a rede virtual à rede no local através de uma das [Opções de conectividade](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponíveis no Azure. Na sua essência, pode expandir a sua rede para Azure, com controlo total no blocos de endereço IP com o benefício de escala enterprise que Azure fornece.

Para compreender melhor VNets, consulte o artigo na figura abaixo, que mostra uma rede simplificado no local.

![Rede no local](./media/virtual-networks-overview/figure01.png)

A figura acima mostra uma rede no local ligada à Internet pública através de um router. Também pode ver uma firewall entre o router e uma DMZ alojamento um servidor DNS e de um farm de servidores web. O farm de servidores web é utilizando um balanceador de carga de hardware que está divulgado na Internet e consome recursos a partir de sub-rede interna de balanceamento de carga. A sub-rede interna está separada da DMZ por outro firewall e os servidores de controlador de domínio do Active Directory anfitriões, os servidores de base de dados e os servidores de aplicações.

Da mesma rede pode ser alojada no Azure, conforme apresentado na figura seguinte.

![Azure rede virtual](./media/virtual-networks-overview/figure02.png)

Repare como infraestrutura do Azure leva-o até o papel de router, permissão do acesso a partir do seu VNet à Internet sem necessitar de qualquer configuração pública. Firewalls podem ser substituídos por grupos de segurança de rede (NSGs) aplicada a cada sub-rede individual. E Balanceadores de carga física sejam substituídos por balanceadores de carga de opostas e internos da internet no Azure.

>[AZURE.NOTE] Existem dois modos de implementação no Azure: clássico (também conhecido como o serviço de gestão) e o Gestor de recursos do Azure (processador). VNets clássica foi adicionado a um grupo de afinidade ou criados como um VNet regional. Se tiver um VNet num grupo afinidade, recomenda-se para [migrar-o para uma VNet regional](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Vantagens de rede virtual

- **Isolamento**. VNets são completamente isolados uns dos outros. Que permite-lhe criar redes distintas de desenvolvimento, testar e produção que utilizam os blocos de endereço CIDR mesmo.

- **Acesso à Internet público**. Todos os IaaS VMs e PaaS função as instâncias um VNet podem aceder à Internet pública por predefinição. Pode controlar o acesso ao utilizar grupos de segurança de rede (NSGs).

- **Acesso ao VMs dentro de VNet**. Instâncias de função PaaS e IaaS VMs podem ser iniciado na mesma rede virtual e pode ligar-se entre si utilizando endereços IP privados, mesmo se estiverem na sub-redes diferentes sem precisar de configurar um gateway ou utilize endereços IP públicos.

- **Resolução do nome**. Azure fornece resolução do nome interna para IaaS VMs e instâncias de função PaaS implementadas no seu VNet. Também pode implementar os seus servidores DNS e configurar VNet utilizá-las.

- **Segurança**. O tráfego de introduzir e sair máquinas virtuais e as instâncias função PaaS um VNet pode ser controlado utilizar grupos de segurança de rede.

- **Conectividade**. Podem ser ligados VNets umas às outras utilizando gateways de rede ou VNet efectuado. Podem ser ligados VNets aos centros de dados no local através de redes VPN do site para o site ou Azure ExpressRoute. Para saber mais sobre a conectividade de VPN do site para o site, visite [sobre VPN gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Para saber mais sobre ExpressRoute, visite [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md). Para saber mais sobre VNet efectuado, visite [VNet efectuado](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Certifique-se de que criar um VNet antes de implementar qualquer IaaS VMs ou instâncias de função PaaS ambiente do Azure. PROCESSADOR com base VMs exigem uma VNet e, se não especificar um VNet existente, Azure cria uma predefinida VNet que possam ter um conflito do bloco de endereços CIDR com a sua rede no local. Se tornou impossibilita para poder ligar o seu VNet à sua rede no local.

## <a name="subnets"></a>Sub-redes

É um intervalo de endereços IP na VNet, pode dividir uma VNet em várias sub-redes para a organização e segurança. VMs e instâncias de função PaaS implementadas sub-redes (mesmo ou diferentes) dentro de uma VNet podem comunicar com os outros sem qualquer configuração extra. Também pode configurar tabelas de encaminhamento e NSGs para uma sub-rede.

## <a name="ip-addresses"></a>Endereços IP


Existem dois tipos de endereços IP atribuídos para recursos no Azure: *públicos* e *privados*. Endereços IP público permitir que os recursos Azure comunicar com da Internet e de outros serviços do público Azure como [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure evento concentradores](https://azure.microsoft.com/documentation/services/event-hubs/). Endereços de IP privado permite a comunicação entre os recursos numa rede virtual, juntamente com as que se relacionam através de uma VPN, sem utilizar um IP Internet encaminháveis endereços.

Para saber mais sobre os endereços IP no Azure, visite [endereços IP na rede virtual](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Balanceadores de carga Azure

Máquinas virtuais e serviços em nuvem numa rede Virtual podem ser expostos à Internet com balanceadores de carga Azure. Linha de aplicações empresariais que estão direcionado para o público interno só pode ser utilizando Balanceador de carga interno de balanceamento de carga.

- **Balanceador de carga externos**. Pode utilizar um balanceador de carga externos para fornecer disponibilidade elevada IaaS VMs e PaaS instâncias de função acedidas a partir da Internet pública.

- **Balanceador de carga interna**. Pode utilizar um balanceador de carga interna para fornecer elevada disponibilidade para IaaS VMs e instâncias de função PaaS acedidas a partir de outros serviços no seu VNet.

Para saber mais sobre balanceamento de carga no Azure, visite [Panorâmica Balanceador de carga](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Grupo de segurança de rede (NSG)

Pode criar NSGs para controlar o acesso de entrada e saído para interfaces de rede (NIC), VMs e sub-redes. Cada NSG contém um ou mais regras especificando se ou não o tráfego não está aprovado ou negado com base em endereço IP de origem, porta de origem, endereço IP de destino e porta de destino. Para saber mais sobre NSGs, visite [o que é um grupo de segurança de rede](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Eletrodomésticos virtuais

Um aparelho virtual é apenas outra VM no seu VNet que é executada uma função de aparelho software com base, como firewall, otimização WAN ou deteção intrusos. Pode criar uma rota no Azure para encaminhar o tráfego da sua VNet através de um virtual aparelho para utilizar as suas capacidades.

Por exemplo, NSGs podem ser utilizados para fornecer a segurança no seu VNet. No entanto, NSGs fornecem camada 4 lista de controlo de acesso (ACL) para pacotes recebidas e enviadas. Se pretender utilizar um modelo de segurança 7 camada, terá de utilizar um dispositivo de firewall.

Eletrodomésticos virtuais dependem [definidas pelo utilizador rotas e reencaminhamento IP](virtual-networks-udr-overview.md).

## <a name="limits"></a>Limites
Existem limites no número de redes virtuais permitidos numa subscrição, consulte [limites Azure funcionamento em rede](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## <a name="pricing"></a>Preços
É não extra custo para utilizar redes virtuais no Azure. As instâncias de cluster iniciadas dentro de Vnet serão cobradas as taxas padrão conforme descrito no [Azure VM preços](https://azure.microsoft.com/pricing/details/virtual-machines/). Os [Gateways de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) e [endereços de IP público] (https://azure.microsoft.com/pricing/details/ip-addresses/) utilizado na VNet também serão cobradas taxas fixas.

## <a name="next-steps"></a>Próximos passos

- [Criar um VNet](virtual-networks-create-vnet-arm-pportal.md) e sub-redes.
- [Criar uma VM num VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre [definidas pelo utilizador rotas e reencaminhamento IP](virtual-networks-udr-overview.md).
