<properties
   pageTitle="Públicas e privadas no Gestor de recursos do Azure endereçamento IP | Microsoft Azure"
   description="Saiba mais sobre públicas e privadas endereçamento IP no Gestor de recursos do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Endereços IP no Azure
Pode atribuir endereços IP para recursos Azure para comunicar com outros recursos Azure, à rede no local e na Internet. Existem dois tipos de endereços IP, que pode utilizar no Azure:

- **Endereços IP público**: utilizada para a comunicação com a Internet, incluindo os serviços do Azure para o público
- **Os endereços IP privados**: utilizadas para a comunicação dentro de uma rede virtual Azure (VNet) e suas no local de rede ao utilizar um VPN gateway ou circuito ExpressRoute para expandir a sua rede para Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](virtual-network-ip-addresses-overview-classic.md).

Se estiver familiarizado com o modelo clássico de implementação, verifique o [diferenças nos endereçamento entre clássica IP e o Gestor de recursos](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Endereços IP públicos
Endereços IP públicos permitir que os recursos Azure comunicar com o Internet e Azure direccionada serviços como o [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/), [bases de dados SQL](../sql-database/sql-database-technical-overview.md)e [armazenamento Azure](../storage/storage-introduction.md).

No Gestor de recursos do Azure, um endereço [IP público](resource-groups-networking.md#public-ip-address) é um recurso que tenha as suas próprias propriedades. Pode associar um recurso de endereço IP público com qualquer um dos seguintes recursos:

- Máquinas virtuais (VM)
- Balanceadores de carga de acesso à Internet
- Gateways VPN
- Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Existem dois métodos em que um endereço IP é atribuído a um recurso IP *público* - *dinâmico* ou *estático*. O método de atribuição predefinido é *dinâmicos*, sempre que um endereço IP **não** está atribuída no momento da sua criação. Em vez disso, o endereço IP público é atribuído ao iniciar (ou criar) o recurso associado (como um balanceador VM ou para carregar). O endereço IP é disponibilizado quando parar (ou eliminar) o recurso. Isto faz com que o endereço IP alterar quando parar e iniciar um recurso.

Para assegurar que o endereço IP para o recurso associado se mantém o mesmo, pode definir o método de alocação explicitamente para *estático*. Neste caso um endereço IP é atribuído imediatamente. Este é disponibilizada apenas quando eliminar o recurso ou alterar o seu método de alocação *dinâmicos*.

>[AZURE.NOTE] Mesmo quando definir o método de alocação para *estático*, não pode especificar o endereço IP real atribuído ao *recurso IP público*. Em vez disso, obtém atribuído a partir de um conjunto de endereços IP disponíveis na localização Azure que o recurso é criado.

Endereços IP públicos estáticos são frequentemente utilizados nos cenários seguintes:

- Os utilizadores finais necessita de atualizar as regras de firewall para comunicar com os recursos do Azure.
- Resolução do nome de DNS, sempre que uma alteração no endereço IP exijam um registos a atualizar.
- Os recursos do Azure comunicam com outras aplicações ou serviços que utilizam um modelo de segurança baseada em endereço IP.
- Utilize os certificados SSL associados a um endereço IP.

>[AZURE.NOTE] A lista de intervalos IP a partir do qual endereços IP públicos (dynamic/estático) estão alocados a recursos Azure é publicada no [intervalos IP do Centro de dados de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Resolução do nome do anfitrião DNS
Pode especificar uma etiqueta de nome de domínio DNS para um recurso IP público, que cria um mapeamento para *domainnamelabel*. *localização*. cloudapp.azure.com para o endereço IP público nos servidores Azure com gestão de DNS. Por exemplo, se criar um recurso IP público com **contoso** como um *domainnamelabel* nos **EUA Ocidental** Azure *localização*, de domínio completamente qualificado (FQDN) do nome **contoso.westus.cloudapp.azure.com** vai ser resolvido para o endereço IP público do recurso. Pode utilizar este FQDN para criar um registo CNAME de domínio personalizado apontar para o endereço IP público no Azure.

>[AZURE.IMPORTANT] Cada etiqueta de nome de domínio criada tem de ser exclusiva dentro da sua localização Azure.  

### <a name="virtual-machines"></a>Máquinas virtuais
Pode associar um endereço IP público com [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) VM atribuindo-a respectiva da **interface de rede**. No caso de uma interface de rede com várias VM, pode atribui-lo a apenas a interface de rede *principal* . Pode atribuir um dinâmico ou de um endereço IP público estático a uma VM.

### <a name="internet-facing-load-balancers"></a>Balanceadores de carga de acesso à Internet
Pode associar um endereço IP público do [Balanceador de carga Azure](../load-balancer/load-balancer-overview.md), atribuindo-lo para a configuração de **front-end** do Balanceador de carga. Este endereço IP público funciona como um balanceamento de carga endereço IP virtual (VIP). Pode atribuir um dinâmico ou de um endereço IP público estático a um balanceador de carga front-end. Também pode atribuir vários endereços IP públicos para um balanceador de carga front-end, que permite aos cenários de [Múltiplos VIP](../load-balancer/load-balancer-multivip.md) como um ambiente do inquilino com várias com sites públicos do baseadas em SSL.

### <a name="vpn-gateways"></a>Gateways VPN
[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) utilizado para ligar uma rede virtual Azure (VNet) para outras VNets Azure ou para uma rede no local. Precisa de atribuir um endereço IP público para a **configuração de IP** para ativá-lo comunicar com a rede remota. Atualmente, só pode atribuir um endereço IP público *dinâmico* para um gateway VPN.

### <a name="application-gateways"></a>Gateways de aplicação
Pode associar um endereço IP público com um [Gateway aplicação](../application-gateway/application-gateway-introduction.md)Azure, atribuindo-a configuração de **front-end** o gateway. Este endereço IP público funciona como um VIP balanceamento de carga. Atualmente, só pode atribuir um endereço IP público *dinâmico* para uma configuração da aplicação gateway front-end.

### <a name="at-a-glance"></a>Um relance
A tabela abaixo mostra a propriedade específica através da qual um endereço IP público pode ser associado a um recurso de nível superior e os métodos de alocação possíveis (estáticos ou dinâmicos) que podem ser utilizados.

|Recursos de nível superior|Associação do endereço IP|Dinâmicos|Estático|
|---|---|---|---|
|Máquina virtual|Interface da rede|Sim|Sim|
|Balanceador de carga|Configuração de front-end|Sim|Sim|
|VPN gateway|Configuração de IP do gateway|Sim|N|
|Gateway de aplicação|Configuração de front-end|Sim|N|

## <a name="private-ip-addresses"></a>Endereços IP privados
Os endereços IP privados permitir que os recursos Azure comunicar com outros recursos a uma [rede virtual](virtual-networks-overview.md) ou numa rede no local através de um VPN gateway ou ExpressRoute circuito, sem utilizar um endereço IP alcançáveis de Internet.

No modelo de implementação do Azure o Gestor de recursos, um endereço IP privado está associado aos seguintes tipos de recursos Azure:

- VMs
- Balanceadores de carga interna (ILBs)
- Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Um endereço IP privado é atribuído do intervalo de endereços da sub-rede à qual o recurso está ligado. O intervalo de endereços da sub-rede propriamente dito é uma parte do intervalo de endereços do VNet.

Existem dois métodos em que um endereço IP privado é atribuído: *dinâmico* ou *estático*. O método de atribuição predefinido é *dinâmicos*, onde o endereço IP é atribuído automaticamente a partir de sub-rede do recurso (utilizando DHCP). Pode alterar este endereço IP quando parar e iniciar o recurso.

Pode definir o método de alocação para *estático* para garantir que o endereço IP se mantém o mesmo. Neste caso, também tem de fornecer um endereço IP válido que faz parte de sub-rede do recurso.

Endereços IP estáticos privados são utilizados frequentemente para:

- VMs funcionam como controladores de domínio ou os servidores DNS.
- Recursos que precisam de regras de firewall utilizando endereços IP.
- Recursos acedidos por outras aplicações recursos através de um endereço IP.

### <a name="virtual-machines"></a>Máquinas virtuais
Um endereço IP privado é atribuído à **interface da rede** do [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) VM. Em caso de uma interface de rede com várias VM, cada interface obtém um endereço IP privado atribuído. Pode especificar o método de alocação como dinâmico ou estático para uma interface de rede.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Resolução de nome do anfitrião DNS interna (para VMs)
Todos os Azure VMs estão configurados com [servidores DNS gerido Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por predefinição, a menos que configure explicitamente os servidores DNS personalizados. Nestes servidores DNS fornecem resolução do nome interna para VMs que residem nos VNet mesmo.

Quando cria uma VM, um mapeamento para o nome do anfitrião para o seu endereço IP privado é adicionado para os servidores DNS gerido Azure. Em caso de uma interface de rede com várias VM, o nome do anfitrião é mapeado para o endereço IP privado da interface de rede principal.

VMs configurados com os servidores DNS Azure com gestão poderão resolver os nomes de anfitriões de todos os VMs dentro da sua VNet para os respetivos endereços IP privados.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga interna (ILB) e gateways de aplicação
Pode atribuir um endereço IP privado para a configuração de **front-end** de um [Balanceador de carga interno Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou um [Gateway de aplicação do Azure](../application-gateway/application-gateway-introduction.md). Este endereço IP privado funciona como um extremo interno, acessível apenas para os recursos no interior da rede virtual (VNet) e as redes remotas ligadas a VNet. Pode atribuir quer um endereço IP privado dinâmico ou estático na configuração de front-end.

### <a name="at-a-glance"></a>Um relance
A tabela abaixo mostra a propriedade específica através da qual um endereço IP privado pode ser associado a um recurso de nível superior e os métodos de alocação possíveis (estáticos ou dinâmicos) que podem ser utilizados.

|Recursos de nível superior|Associação do endereço IP|Dinâmicos|Estático|
|---|---|---|---|
|Máquina virtual|Interface da rede|Sim|Sim|
|Balanceador de carga|Configuração de front-end|Sim|Sim|
|Gateway de aplicação|Configuração de front-end|Sim|Sim|

## <a name="limits"></a>Limites

Os limites de impostos no endereçamento IP estão indicados no conjunto completo de [limites para à rede](azure-subscription-service-limits.md#networking-limits) no Azure. Estes limites estão por região e por subscrição. Pode [contactar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites predefinidos até limites máximos com base nas necessidades da sua empresa.

## <a name="pricing"></a>Preços

Endereços IP públicos poderão ter um encargo nominal. Para saber mais sobre preços de endereço IP no Azure, reveja a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Próximos passos
- [Implementar uma VM com um endereço IP público estático através do portal Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Implementar uma VM com um endereço IP público estático através de um modelo](virtual-network-deploy-static-pip-arm-template.md)
- [Implementar uma VM com um endereço IP privado estático através do portal Azure](virtual-networks-static-private-ip-arm-pportal.md)
