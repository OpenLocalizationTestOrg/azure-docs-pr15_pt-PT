<properties
   pageTitle="Ligar a sua rede no local ao Azure | Microsoft Azure"
   description="Explica e compara os diferentes métodos disponíveis para ligar aos serviços em nuvem Microsoft como Azure, Office 365 e o Dynamics CRM Online."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>Ligar a sua rede no local ao Azure

A Microsoft fornece vários tipos de serviços em nuvem. Enquanto pode ligar a todos os serviços através da Internet pública, também pode ligar a alguns dos serviços utilizando um túnel de rede privada virtual (VPN) através da Internet ou através de uma ligação direta e privada para a Microsoft. Este artigo ajuda a determinar qual a opção de conectividade melhor irá corresponder às suas necessidades com base nos tipos de serviços de nuvem da Microsoft consumir. A maioria das organizações utilizam vários tipos de ligação descritos abaixo.

## <a name="connecting-over-the-public-internet"></a>Ligar-se através da Internet pública

Este tipo de ligação oferece um acesso aos serviços em nuvem Microsoft diretamente através da Internet, conforme apresentado abaixo.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Esta ligação é normalmente o primeiro tipo utilizado para ligar aos serviços em nuvem da Microsoft. A tabela abaixo apresenta as vantagens e desvantagens deste tipo de ligação.



| **Benefícios**| **Considerações sobre**|
|---------|---------|
|Requer sem alteração à sua rede no local, desde que todos os dispositivos de cliente têm acesso ilimitado a todos os endereços IP e portas na Internet.|Apesar do tráfego frequentemente está encriptado através de HTTPS,-pode ser interceptada em trânsito desde que atravessam Internet pública.|
|Pode ligar a todos os serviços em nuvem da Microsoft divulgados na Internet pública.|Latência inesperada porque a ligação atravessa na Internet.|
|Utiliza a sua ligação à Internet existente.||
|Não necessita de gestão de dispositivos qualquer conectividade.||

Esta ligação não inclui conectividade nem os custos de largura de banda uma vez que utilizar a sua ligação à Internet existente. 

## <a name="connecting-with-a-point-to-site-connection"></a>Estabelecer ligação com uma ligação ponto-para-site

Este tipo de ligação oferece um acesso aos serviços em nuvem algumas Microsoft através de um túnel túnel Protocolo SSTP (Secure Socket) através da Internet, conforme apresentado abaixo.

![P2S] Ligação (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "ponto-para-site")

A ligação é tornada ao longo da sua ligação à Internet existente, mas requer a utilização de um Gateway de VPN Azure. A tabela abaixo apresenta as vantagens e desvantagens deste tipo de ligação.

| **Benefícios**| **Considerações sobre**|
|---------|---------|
|Requer sem alteração à sua rede no local, desde que todos os dispositivos de cliente têm acesso ilimitado a todos os endereços IP e portas na Internet.|Apesar do tráfego está encriptado utilizando IPSec,-pode ser interceptada em trânsito desde que atravessam Internet pública.|
|Utiliza a sua ligação à Internet existente.|Latência inesperada porque a ligação atravessa na Internet.|
|Débito de até 200 Mb por s por gateway.|Necessita de criação e gestão de separada ligações entre cada dispositivo na sua rede no local e cada gateway que cada dispositivo tem de ligar a.|
|Podem ser utilizados para ligar aos serviços Azure que podem ser ligados a um redes Virtual do Azure (VNet) como máquinas virtuais do Azure e serviços em nuvem Azure.|Necessita de administração em curso mínima de um Gateway de VPN Azure.|
||Não pode ser utilizado para ligar ao Microsoft Office 365 ou Dynamics CRM Online.
||Não pode ser utilizado para ligar aos serviços Azure que não podem estar ligados a um VNet.|

Saiba mais sobre o serviço de [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) , os [preços](https://azure.microsoft.com/pricing/details/vpn-gateway)e dados de saída transferência [preços](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Estabelecer ligação com uma ligação de site para o site

Este tipo de ligação oferece um acesso aos serviços em nuvem algumas Microsoft através de um túnel IPSec através da Internet, conforme apresentado abaixo.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Ligação de site para o site")

A ligação é tornada ao longo da sua ligação à Internet existente, mas requer a utilização de um Gateway de VPN Azure com as respetivas preços associados e preços de transferência de dados de saída. A tabela abaixo apresenta as vantagens e desvantagens deste tipo de ligação.

| **Benefícios**| **Considerações sobre**|
|---------|---------|
|Todos os dispositivos na sua rede no local, podem comunicar com os serviços do Azure ligados a uma VNet para que não precisa de configurar ligações individuais para cada dispositivo.|Apesar do tráfego está encriptado utilizando IPSec,-pode ser interceptada em trânsito desde que atravessam Internet pública.|
|Utiliza a sua ligação à Internet existente.|Latência inesperada porque a ligação atravessa na Internet.|
|Pode ser utilizado para ligar aos serviços Azure que podem ser ligados a um VNet como máquinas virtuais e serviços em nuvem.|Tem de configurar e gerir uma validado VPN dispositivo * no local.|
|Débito de até 200 Mb por s por gateway.|Necessita de administração em curso mínima de um Gateway de VPN Azure.|
|Pode forçar o tráfego de saída iniciado a partir da nuvem virtual máquinas através da rede no local para inspeção e registo utilizando rotas definidos pelo utilizador ou o limite Gateway Protocol (BGP) * *.|Não pode ser utilizado para ligar ao Microsoft Office 365 ou Dynamics CRM Online.|
||Não pode ser utilizado para ligar aos serviços Azure que não podem estar ligados a um VNet.|
||Se utilizar os serviços que iniciem ligações para o dispositivos no local e as políticas de segurança exigirem-la, poderá ter uma firewall entre a rede no local e o Azure.|

- * Ver uma lista de [validado dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- * * Saiba mais sobre como utilizar [rotas definidos pelo utilizador](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) para forçar o encaminhamento a partir do Azure VNets para um dispositivo no local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Estabelecer ligação com uma ligação privada dedicada

Este tipo de ligação oferece um acesso para todos os serviços em nuvem da Microsoft através de uma ligação privada dedicada para a Microsoft não atravessa da Internet, conforme apresentado abaixo.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute ligação")

A ligação requer a utilização do serviço ExpressRoute e uma ligação a um fornecedor de conectividade. A tabela abaixo apresenta as vantagens e desvantagens deste tipo de ligação.

| **Benefícios**| **Considerações sobre**|
|---------|---------|
|Tráfego não pode ser interceptado em trânsito através da Internet pública de uma vez uma ligação dedicada através de um fornecedor de serviço é utilizada.|Necessita de gestão de router no local.|
|Largura de banda até 10 Gb por s por ExpressRoute circuito e débito até 2 Gb por s para cada gateway.|Requer uma ligação dedicada a um fornecedor de conectividade.|
|Latência previsível porque é uma ligação dedicada para a Microsoft não atravessa na Internet.|Precisem de administração em curso mínima de uma ou mais Gateways de VPN Azure (se a ligar o circuito VNets).|
|Não requer comunicação encriptada, apesar de pode encriptar o tráfego, se pretender.| Se estiver a utilizar serviços em nuvem que iniciem ligações para o dispositivos no local, poderá ter uma firewall entre a rede no local e o Azure.|
|Diretamente pode ligar a todos os serviços na nuvem da Microsoft, com algumas exceções *.|Requer a tradução de endereços de rede (NAT) dos endereços IP no local introduzir os centros de dados da Microsoft para os serviços que não podem ser ligados para VNet.* *|
|Pode forçar iniciado a partir da nuvem virtual máquinas através da rede no local para inspeção e registo utilizando BGP o tráfego de saída.|

- * Ver uma [lista dos serviços](../expressroute/expressroute-faqs.md#supported-services) que não podem ser utilizados com ExpressRoute. A sua subscrição Azure têm de ser aprovada para ligar para o Office 365.  Consulte o artigo [Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) para obter detalhes.
- * * Saiba mais sobre ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisitos.

Saiba mais sobre [ExpressRoute](../expressroute/expressroute-introduction.md), respetivo associado [preços](https://azure.microsoft.com/pricing/details/expressroute)e [fornecedores de conectividade](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Considerações adicionais

- Várias das opções acima tem vários limites máximos pode de suporte para ligações VNet, ligações de gateway e outros critérios. Recomendamos que reveja o Azure [limites de funcionamento em rede](../azure-subscription-service-limits.md#networking-limits) de compreender se qualquer uma delas afetar os tipos de conectividade que optar por utilizar. 
- Se planeia ligar um gateway a partir de uma ligação VPN do site para o site para o mesmo VNet como um gateway ExpressRoute, deverá familiarizar com as restrições de importantes pela primeira vez. Consulte o artigo [Configurar ExpressRoute e ligações coexistentes do Site para o Site](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) para obter mais detalhes.

## <a name="next-steps"></a>Próximos passos

Os recursos abaixo explicam como implementar os tipos de ligação abordados neste artigo.

-   [Implementar uma ligação ponto-para-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Implementar uma ligação de site para o site](guidance-hybrid-network-vpn.md)
-   [Implementar uma ligação privada dedicada](guidance-hybrid-network-expressroute.md)
-   [Implementar uma ligação privada dedicada com uma ligação de site para o site para elevada disponibilidade](guidance-hybrid-network-expressroute-vpn-failover.md)
