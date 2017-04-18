<properties
   pageTitle="Públicas e privadas endereçamento IP (clássico) no Azure | Microsoft Azure"
   description="Saiba mais sobre públicas e privadas endereçamento IP no Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Endereços IP (clássico) no Azure
Pode atribuir endereços IP para recursos Azure para comunicar com outros recursos Azure, à rede no local e na Internet. Existem dois tipos de endereços IP, pode utilizar no Azure: públicos e privados.

Endereços IP públicos são utilizados para a comunicação com a Internet, incluindo os serviços do Azure dirigido ao público.

Os endereços IP privados são utilizados para a comunicação dentro de uma rede virtual Azure (VNet), um serviço na nuvem e a sua rede no local ao utilizar um VPN gateway ou circuito ExpressRoute para expandir a sua rede para Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de implementação do Gestor de recursos](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Endereços IP públicos
Endereços IP públicos permitir que os recursos Azure comunicar com o Internet e Azure direccionada serviços como o [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/), [bases de dados SQL](../sql-database/sql-database-technical-overview.md)e [armazenamento Azure](../storage/storage-introduction.md).

Um endereço IP público está associado com os seguintes tipos de recursos:

- Serviços em nuvem
- Máquinas virtuais do IaaS (VMs)
- PaaS instâncias de função
- Gateways VPN
- Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Quando um endereço IP público tem de ser atribuída a um recurso Azure, ele está *dinamicamente* atribuída a partir de um conjunto de endereço IP público disponível dentro a localização de que um recurso é criado. Este endereço IP é disponibilizado quando o recurso está parado. No caso de um serviço de nuvem, isto acontece quando todas as instâncias de função são paradas, que pode ser evitado utilizando um endereço IP *estático* (reservado) (consulte [Serviços em nuvem](#Cloud-services)).

>[AZURE.NOTE] A lista de intervalos IP a partir do qual endereços IP públicos são atribuídos para recursos Azure é publicada no [intervalos IP do Centro de dados de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Resolução do nome do anfitrião DNS
Quando cria um serviço na nuvem ou um VM IaaS, tem de fornecer um nome DNS do serviço de nuvem que é exclusivo ao longo de todos os recursos no Azure. Esta ação cria um mapeamento nos servidores DNS gerido Azure para *NomeDNS*. cloudapp.net para o endereço IP público do recurso. Por exemplo, quando cria um serviço na nuvem com um nome DNS do serviço de nuvem da **contoso**, de domínio completamente qualificado (FQDN) do nome **contoso.cloudapp.net** resolverá para um endereço IP público (VIP) do serviço na nuvem. Pode utilizar este FQDN para criar um registo CNAME de domínio personalizado apontar para o endereço IP público no Azure.

### <a name="cloud-services"></a>Serviços em nuvem
Um serviço na nuvem sempre tem um endereço IP público designado de um endereço IP virtual (VIP). Pode criar os pontos finais num serviço de nuvem para associar portas diferentes no VIP para portas internas no VMs e instâncias de funções dentro do serviço de nuvem. 

Um serviço na nuvem pode conter várias IaaS VMs ou PaaS instâncias de função, todos os exposta através da mesma VIP de serviço de nuvem. Também pode atribuir [vários VIP num serviço na nuvem](../load-balancer/load-balancer-multivip.md), que permite aos cenários de múltiplos VIP como o ambiente do inquilino com várias com sites públicos do baseadas em SSL.

Pode assegurar que o endereço IP público de um serviço na nuvem se mantém o mesmo, mesmo quando estão paradas todas as instâncias de função, utilizando um público endereço IP *estático* , designado de [IP reservado](virtual-networks-reserved-public-ip.md). Pode criar um recurso IP estático (reservado) numa localização específica e atribuí-lo a qualquer serviço em nuvem nessa localização. Não é possível especificar o endereço IP real para o IP reservado, é-lhe atribuída a partir do conjunto de endereços IP disponíveis na localização que é criada. Este endereço IP não é disponibilizado até explicitamente eliminá-lo.

Estáticos endereços IP públicos (reservados) são utilizados frequentemente nos cenários sempre que um serviço na nuvem:

- necessita de regras de firewall para ser configuração pelos utilizadores finais.
- depende da resolução de nomes DNS externa, e um IP dinâmico seria necessário atualizar os registos.
- consome serviços web externo que utilizar o modelo de segurança IP com base.
- utiliza certificados SSL associados a um endereço IP.

>[AZURE.NOTE] Quando cria uma VM clássica, um contentor *serviço na nuvem* é criado pela Azure, que tem um endereço IP virtual (VIP). Quando concluir a criação através do portal, um valor predefinido RDP ou SSH *ponto final* é configurado o portal para que se pode ligar para a VM através de VIP do serviço de nuvem. Pode estar reservada este VIP do serviço de nuvem, que fornece um endereço IP reservado para ligar para a VM eficazmente. Pode abrir portas adicionais através da configuração mais pontos finais.

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VMs e PaaS instâncias de função
Pode atribuir um endereço IP público diretamente para um IaaS [VM](../virtual-machines/virtual-machines-linux-about.md) ou instância de função PaaS dentro de um serviço na nuvem. Isto é referido como um endereço IP público nível da instância ([ILPIP](virtual-networks-instance-level-public-ip.md)). Este endereço IP público só pode ser dinâmico.

>[AZURE.NOTE] Este é diferente do VIP do serviço na nuvem, que é um contentor para IaaS VMs ou PaaS instâncias de acesso função, uma vez que um serviço na nuvem, pode conter várias IaaS VMs ou instâncias de função PaaS, todas as expostas através da mesma VIP de serviço de nuvem.

### <a name="vpn-gateways"></a>Gateways VPN
Um [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser utilizado para ligar um VNet Azure para outros Azure VNets ou redes no local. Um gateway VPN é atribuído um público IP endereço *dinamicamente*, que permite a comunicação com à rede remota.

### <a name="application-gateways"></a>Gateways de aplicação
Um [gateway de aplicação](../application-gateway/application-gateway-introduction.md) de Azure pode ser utilizado para Layer7 balanceamento de carga para encaminhar tráfego de rede com base em HTTP. Gateway aplicação é atribuído um público IP endereço *dinamicamente*, que serve como o VIP balanceamento de carga.

### <a name="at-a-glance"></a>Relance
A tabela abaixo mostra cada tipo de recurso com os métodos de alocação possíveis (dynamic/estático) e a capacidade de atribuir vários endereços IP públicos.

|Recurso|Dinâmicos|Estático|Vários endereços IP|
|---|---|---|---|
|Serviço na nuvem|Sim|Sim|Sim|
|IaaS VM ou PaaS instância de função|Sim|N|N|
|VPN gateway|Sim|N|N|
|Gateway de aplicação|Sim|N|N|

## <a name="private-ip-addresses"></a>Endereços IP privados
Os endereços IP privados permitir que os recursos Azure comunicar com outros recursos de um serviço na nuvem ou uma [rede virtual](virtual-networks-overview.md)(VNet), ou rede no local (através de um VPN gateway ou ExpressRoute circuito), sem utilizar um endereço IP alcançáveis de Internet.

No modelo de implementação clássica Azure, um endereço IP privado pode ser atribuído para os seguintes recursos Azure:

- IaaS VMs e PaaS instâncias de função
- Balanceador de carga interno
- Gateway de aplicação

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VMs e PaaS instâncias de função
Máquinas virtuais (VMs) criadas com o modelo de implementação clássica são sempre colocadas num serviço de nuvem, semelhante ao PaaS instâncias de função. O comportamento de endereços IP privados, portanto, são semelhantes para estes recursos.

É importante ter em atenção que pode ser implementado num serviço na nuvem de duas maneiras:

- Como um *programa autónomo* serviço na nuvem, onde não é dentro de uma rede virtual.
- Como parte de uma rede virtual.

#### <a name="allocation-method"></a>Método de alocação
Em caso de um serviço de nuvem *autónomo* , recursos obtenha uma privado IP endereço atribuído *dinamicamente* do intervalo de endereços IP privado do Centro de dados Azure. Pode ser utilizada apenas para a comunicação com outros VMs dentro do serviço na nuvem mesmo. Pode alterar este endereço IP quando o recurso está parado e iniciado.

Em caso de um serviço na nuvem implementado dentro de uma rede virtual, recursos obtém privado endereços de IP atribuídas a partir do intervalo de endereço do subnet(s) associado (conforme especificado na sua configuração de rede). Este endereço IP privada (s) podem ser utilizados para a comunicação entre todos os VMs dentro de VNet.

Para além disso, em caso de serviços em nuvem dentro de uma VNet, um endereço IP privado é atribuído *dinamicamente* (utilizando DHCP) por predefinição. Tal pode alterar quando o recurso está parado e iniciado. Para certificar-se de que o endereço IP se mantém o mesmo, é necessário definir o método de alocação para *estático*e fornecer um endereço IP válido no intervalo endereço correspondente.

Endereços IP estáticos privados são utilizados frequentemente para:

 - VMs funcionam como controladores de domínio ou os servidores DNS.
 - VMs que requerem endereços IP de utilizar as regras de firewall.
 - VMs serviços acedido por outras aplicações através de um endereço IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolução do nome do anfitrião DNS interna
Todos os Azure VMs e instâncias de função PaaS estão configuradas com [servidores DNS gerido Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por predefinição, a menos que configure explicitamente os servidores DNS personalizados. Nestes servidores DNS fornecem resolução do nome interna para VMs e instâncias de função que se encontram dentro do serviço de VNet ou na nuvem mesmo.

Quando cria uma VM, um mapeamento para o nome do anfitrião para o seu endereço IP privado é adicionado para os servidores DNS gerido Azure. Em caso de uma VM multi-NIC, o nome do anfitrião é mapeado para o endereço IP privado do NIC principal. No entanto, esta informação de mapeamento é restrita para recursos dentro do serviço na nuvem mesmo ou VNet.

Em caso de um serviço de nuvem *autónomo* , será capaz de resolver nomes de anfitriões de todas as instâncias de VMs/função dentro do serviço na nuvem mesmo apenas. Em caso de um serviço na nuvem dentro de uma VNet, será capaz de resolver nomes de anfitriões de todas as instâncias de VMs/função dentro de VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga interna (ILB) e gateways de aplicação
Pode atribuir um endereço IP privado para a configuração de **front-end** de um [Balanceador de carga interno Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou um [Gateway de aplicação do Azure](../application-gateway/application-gateway-introduction.md). Este endereço IP privado funciona como um extremo interno, acessível apenas para os recursos no interior da rede virtual (VNet) e as redes remotas ligadas a VNet. Pode atribuir quer um endereço IP privado dinâmico ou estático na configuração de front-end. Também pode atribuir vários endereços IP privados para permitir multi vip cenários.

### <a name="at-a-glance"></a>Relance
A tabela abaixo mostra cada tipo de recurso com os métodos de alocação possíveis (dynamic/estático) e a capacidade de atribuir vários endereços IP privados.

|Recurso|Dinâmicos|Estático|Vários endereços IP|
|---|---|---|---|
|VM (num serviço de nuvem *autónomo* )|Sim|Sim|Sim|
|PaaS instância de função (num serviço de nuvem *autónomo* )|Sim|N|Sim|
|VM ou PaaS instância de função (num VNet)|Sim|Sim|Sim|
|Carga interno balanceador front-end|Sim|Sim|Sim|
|Aplicação gateway front-end|Sim|Sim|Sim|

## <a name="limits"></a>Limites

A tabela abaixo mostra os limites de impostos no IP endereçar no Azure por subscrição. Pode [contactar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites predefinidos até limites máximos com base nas necessidades da sua empresa.

||Limite predefinido|Limite máximo|
|---|---|---|
|Endereços IP públicos (dinâmico)|5|contactar o suporte|
|Endereços IP públicos reservados|20|contactar o suporte|
|Público VIP por implementação (serviço na nuvem)|5|contactar o suporte|
|Privado VIP (ILB) por implementação (serviço na nuvem)|1|1|

Certifique-se de que o conjunto completo de [limites para à rede](azure-subscription-service-limits.md#networking-limits) no Azure de leitura.

## <a name="pricing"></a>Preços

Na maioria dos casos, endereços IP públicos são gratuitos. Existe um encargo nominal para utilizar adicionais e/ou estáticos endereços IP públicos. Certifique-se de que compreende [preços a estrutura de IPs público](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferenças entre o Gestor de recursos e implementações clássicas
Abaixo encontra uma comparação de funcionalidades endereçamento de IP em Gestor de recursos e o modelo de implementação clássica.

||Recurso|Clássico|Gestor de recursos|
|---|---|---|---|
|**Endereço IP público**|VM|Designado por um ILPIP (apenas dinâmico)|Designados de um endereço IP público (dinâmico ou estático)|
|||Atribuídos a uma VM IaaS ou uma instância de função PaaS|Associado a VM NIC|
||Balanceador de carga oposta Internet|Designado (dinâmico) VIP ou IP reservado (estático)|Designados de um endereço IP público (dinâmico ou estático)|
|||Atribuído a um serviço na nuvem|Associado a configuração do Balanceador de carga front-end|
||||
|**Endereço IP privado**|VM|Designado por um DIP|Designados de um endereço IP privado|
|||Atribuídos a uma VM IaaS ou uma instância de função PaaS|Atribuído a VM NIC|
||Balanceador de carga interna (ILB)|Atribuído a ILB (dinâmico ou estático)|Atribuído a configuração de front-end a ILB (estática ou dinâmica)|

## <a name="next-steps"></a>Próximos passos
- [Implementar uma VM com um endereço IP estático privado](virtual-networks-static-private-ip-classic-pportal.md) através do portal clássico.
