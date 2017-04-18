<properties
   pageTitle="Descrição geral de BGP com o Azure VPN Gateways | Microsoft Azure"
   description="Este artigo fornece uma descrição geral do BGP com o Azure VPN Gateways."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Descrição geral de BGP com o Azure VPN Gateways

Este artigo fornece uma descrição geral do suporte BGP (protocolo de Gateway limite) no Azure VPN Gateways.

## <a name="about-bgp"></a>Sobre BGP

BGP é o protocolo de encaminhamento padrão era utilizado frequentemente em da Internet para o encaminhamento e acessibilidade informações entre duas ou mais redes do exchange. Quando utilizado no contexto de redes virtuais Azure, BGP permite que os Gateways de VPN do Azure e os dispositivos VPN no local, denominados BGP colegas ou vizinhos para "encaminha" do exchange que irão informá-ambos os gateways sobre a disponibilidade e a acessibilidade para esses prefixos até gateways ou routers envolvam. Também pode ativar BGP trânsito encaminhamento entre várias redes clicando no ícone propagação rotas que um gateway BGP aprende a partir de um ponto BGP para todos os outros colegas BGP.
 
### <a name="why-use-bgp"></a>Porquê utilizar BGP?

BGP é uma funcionalidade opcional, que pode utilizar com gateways Azure VPN baseada em encaminhar. Também deve certificar-se de que os seus dispositivos VPN no local de suporte BGP antes de ativar a funcionalidade. Pode continuar a utilizar gateways Azure VPN e os seus dispositivos VPN no local sem BGP. É o equivalente da utilização rotas estáticas (sem BGP) *vs.* utilizando o encaminhamento dinâmico com BGP entre o seu redes e Azure.

Existem várias vantagens e novas capacidades com BGP:

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Atualizações automáticas e flexível prefixo de suporte

Com BGP, só tem de declarar um prefixo mínimo para uma ponto a ponto BGP específica sobre o túnel IPsec S2S VPN. Pode ser mais pequeno como um prefixo anfitrião (/ 32) do endereço IP BGP ponto a ponto do seu dispositivo VPN no local. Pode controlar que no local prefixos de rede que pretende anunciar ao Azure para permitir que a sua rede Virtual Azure aceder a.
    
Pode também anunciar um prefixos maiores que podem incluir alguns dos seus prefixos de endereço VNet, tal como um grande privado espaço do endereço IP (por exemplo, 10.0.0.0/8). Tenha em atenção apesar dos prefixos não podem ser idênticos com qualquer um dos seus prefixos VNet. Essas rotas idênticas ao seu prefixos VNet serão rejeitadas.

>[AZURE.IMPORTANT] Atualmente, a rota de predefinida (0.0.0.0/0) para gateways Azure VPN de publicidade será bloqueada. Atualizar outra será fornecido assim que esta funcionalidade está ativa.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Suporta vários túneis entre um VNet e um site no local com activação automática pós-de falha, com base em BGP

Pode estabelecer várias ligações entre o seu VNet Azure e os seus dispositivos VPN no local na mesma localização. Esta funcionalidade fornece vários túneis (caminhos) entre as duas redes numa configuração activo activo. Se um dos túneis estiver desligado, as rotas correspondentes vai ser retiradas através do BGP e o tráfego irá são automaticamente movidas para os restantes túneis.
    
O diagrama seguinte mostra um exemplo simples desta configuração altamente disponível:
    
![Caminhos ativos múltiplos](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Suporte de encaminhamento de trânsito entre redes no local e várias VNets do Azure

BGP permite várias gateways saber e propagar prefixos de redes diferentes, se estiverem direta ou indiretamente ligados. Isto pode ativar o encaminhamento de trânsito com gateways Azure VPN entre os seus sites no local ou entre várias redes de Virtual do Azure.
    
O diagrama seguinte mostra um exemplo de uma topologia de salto com várias com vários caminhos pode tráfego entre as duas redes no local através de gateways Azure VPN dentro Microsoft Networks de trânsito:

![Trânsito Multi-hop](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Perguntas mais frequentes do BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Próximos passos

Consulte o artigo [introdução com BGP no Azure VPN gateways](./vpn-gateway-bgp-resource-manager-ps.md) para obter os passos para configurar BGP para o local cruzada e as ligações de VNet para VNet.

