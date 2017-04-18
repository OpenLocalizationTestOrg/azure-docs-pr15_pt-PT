### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP é suportada em todos os SKUs de Gateway de VPN Azure?

Não, BGP é suportada no Azure gateways VPN **reversa** e **padrão** . **Básicas** SKU não é suportada.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Pode utilizar BGP com gateways Azure Policy-Based VPN?

Não, BGP é suportado apenas os gateways VPN baseada em encaminhar.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Pode utilizar ASNs privadas (números de sistema autónoma)?

Sim, pode utilizar o seu próprio ASNs públicos ou privados ASNs do seu redes no local e o Azure redes virtuais.

#### <a name="are-there-asns-reserved-by-azure"></a>Existem ASNs reservados pelo Azure?

Sim, as seguintes ASNs são reservados por Azure para peerings internas e externas:

- ASNs públicos: 8075, 8076, 12076
- Privado ASNs: 65515, 65517, 65518, 65519, 65520

Não é possível especificar estes ASNs para o local em dispositivos VPN quando se liga ao gateways Azure VPN.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Pode utilizar o mesmo ASN para redes VPN no local e Azure VNets?

Não, tem de atribuir ASNs diferentes entre redes no local e o seu VNets Azure se estiver a ligar juntamente com o BGP. Azure VPN Gateways ter uma predefinida ASN de 65515 atribuído, quer BGP estiver ativado para não para a conectividade de publicação em local. Pode substituir este predefinido, atribuindo um ASN diferente ao criar o gateway VPN ou alterar a ASN depois de criar o gateway. Terá de atribuir os ASNs no local para o correspondente Azure Local Gateways de rede.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>O que prefixos de endereço o gateways Azure VPN anunciar para mim?

Azure VPN gateway irá anunciar as seguintes rotas para os dispositivos de BGP no local:

- Os prefixos de endereço VNet
- Prefixos de endereço para cada Gateways de rede Local ligado ao gateway VPN do Azure
- Encaminha aprendida sessões de outros BGP peering ligados ao gateway Azure VPN, **exceto rota predefinida ou rotas sobrepostas com qualquer prefixo VNet**.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Pode anunciar rota predefinida (0.0.0.0/0) para gateways Azure VPN?

Não neste momento.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Pode anunciar os prefixos exatos como a minha rede Virtual prefixos?

Não, os mesma prefixos como qualquer uma da sua rede Virtual de publicidade prefixos de endereço vão ser bloqueados ou filtrados pela plataforma do Azure. No entanto pode anunciar um prefixo que seja um vasto conjunto de que tem dentro da sua rede Virtual. Por exemplo, a sua rede Virtual pode utilizar o 10.10.0.0/16 espaço endereço e poderia anunciar 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Pode utilizar BGP com as minhas ligações VNet para VNet?

Sim, pode utilizar BGP para ligações de publicação em local e ligações de VNet para VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso misturar BGP com ligações que não sejam BGP para minha gateways Azure VPN?

Sim, pode misturar BGP e que não sejam BGP ligações para o mesmo gateway Azure VPN.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Significa encaminhamento do Azure VPN gateway suporte BGP trânsito?

Sim, BGP trânsito encaminhamento é suportado, com exceção que **não** irá do Azure VPN gateways anuncia rotas predefinidas para outros colegas BGP. Para ativar o encaminhamento de trânsito em múltiplos gateways Azure VPN, tem de ativar BGP em todas as ligações de VNet para VNet intermédias.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>Pode ter mais do que um túneis entre gateway Azure VPN e a minha rede no local?

Sim, pode estabelecer mais do que um S2S VPN túneis entre um gateway VPN do Azure e a sua rede no local. Tenha em atenção que vão ser contados todas estas túneis contra o número total de túneis para sua gateways Azure VPN. Por exemplo, se tiver duas túneis redundantes entre o gateway da VPN do Azure e uma da sua rede no local, irá consumir 2 túneis terminar a quota de total do seu gateway Azure VPN (10 para padrão) e 30 para reversa.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Pode ter vários túneis entre dois Azure VNets com BGP?

Não, redundantes túneis entre um par de redes virtuais não são suportados.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Posso utilizar BGP para S2S VPN numa configuração coexistência ExpressRoute/S2S VPN?

Não neste momento.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Qual é o endereço que utiliza gateway Azure VPN para BGP Peer IP?

O gateway Azure VPN atribuirá um único endereço IP do intervalo GatewaySubnet definido para a rede virtual. Por predefinição, é o segundo último endereço do intervalo. Por exemplo, se o GatewaySubnet for 10.12.255.0/27, que se situa entre 10.12.255.0 para 10.12.255.31, em seguida, o endereço de IP de ponto a ponto BGP no gateway Azure VPN será 10.12.255.30. Pode encontrar estas informações quando que as informações de gateway Azure VPN da lista.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos para os endereços de IP de ponto a ponto BGP no meu dispositivo VPN?

O BGP no local Endereço peer **Não tem** de ter o mesmo que o endereço IP público do seu dispositivo VPN. Utilize um endereço IP diferente no dispositivo VPN para o seu IP de ponto a ponto BGP. Pode ser um endereço atribuído à interface loopback no dispositivo. Especifique este endereço em correspondente Gateway rede Local que representa a localização.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que deve posso especificar como o meu prefixos de endereço para o Gateway de rede Local quando utilizo BGP?

Azure Gateway de rede Local Especifica os prefixos de endereço inicial para a rede no local. Com BGP, tem de atribuir o prefixo anfitrião (/ 32 prefixo) do seu endereço de IP de ponto a ponto BGP como o espaço de endereços para essa rede no local. Se o seu IP de ponto a ponto BGP for 10.52.255.254, deverá especificar "10.52.255.254/32" como localNetworkAddressSpace do Gateway de rede Local que representa esta rede no local. Este é para se certificar de que o gateway Azure VPN estabelece a sessão BGP através do túnel S2S VPN.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar ao meu dispositivo VPN no local para a sessão peering BGP?

Deverá adicionar uma rota de anfitrião do endereço IP do Azure BGP ponto a ponto no seu dispositivo VPN apontar para o túnel IPsec S2S VPN. Por exemplo, se o Azure VPN Peer IP for "10.12.255.30", deve adicionar uma rota de anfitrião "10.12.255.30" com uma interface de salto seguinte da interface de túnel IPsec correspondente no seu dispositivo VPN.
