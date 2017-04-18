<properties 
   pageTitle="Sobre o VPN Gateway | Microsoft Azure"
   description="Saiba mais sobre ligações de Gateway de VPN para redes virtuais Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>Sobre VPN Gateway


Um gateway de rede virtual é utilizado para enviar tráfego de rede entre o Azure redes virtuais e localizações no local e também entre redes virtuais dentro Azure (VNet para VNet). Quando configura um gateway VPN, tem de criar e configurar um gateway de rede virtual e uma ligação de gateway rede virtual.

O modelo de implementação do Gestor de recursos, quando cria um recurso de gateway de rede virtual, pode especificar várias definições. Uma das definições necessárias é '-GatewayType'. Existem dois tipos de gateway de rede virtual: Vpn e ExpressRoute. 

Quando o tráfego de rede é enviado numa ligação privada dedicada, pode utilizar o tipo de gateway 'ExpressRoute'. Isto é também conhecido como um gateway ExpressRoute. Quando o tráfego de rede é enviado encriptado através de uma ligação pública, pode utilizar o tipo de gateway 'Vpn'. Isto é referido como um gateway VPN. Site para Site ponto para o Site e ligações VNet para VNet todas as utilizam um gateway VPN.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter uma rede virtual gateway que utiliza - GatewayType ExpressRoute e que utiliza - GatewayType Vpn. Este artigo foca-se principalmente no VPN Gateway. Para mais informações sobre ExpressRoute, consulte o artigo [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Preços

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>SKUs do gateway

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para mais informações sobre o gateway SKUs, consulte o artigo [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Estimado débito agregação por SKU

A tabela seguinte mostra os tipos de gateway e o débito agregação estimado. Esta tabela aplica-se para o Gestor de recursos e de modelos de implementação clássica.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Configurar um Gateway VPN

Quando configura um gateway VPN, as instruções em que utilizar variam consoante o modelo de implementação utilizado para criar a sua rede virtual. Por exemplo, se tiver criado o seu VNet utilizando o modelo clássico de implementação, utilizar as orientações e instruções para o modelo clássico de implementação para criar e configurar as definições de gateway VPN. Para mais informações sobre modelos de implementação, consulte [Noções sobre o Gestor de recursos e modelos de implementação clássica](../resource-manager-deployment-model.md).

Uma ligação de gateway VPN baseia-se em vários recursos que estão configurados com definições específicas. A maior parte dos recursos pode ser configuradas separadamente, embora tem de ser configurados numa ordem em alguns casos. Pode começar a criar e configurar recursos utilizando uma ferramenta de configuração, tal como o portal do Azure. Mais tarde, em seguida, pode decidir mudar para outra ferramenta, tal como PowerShell, para configurar recursos adicionais, ou para modificar recursos existentes sempre que aplicável. Atualmente, não é possível configurar cada recurso e de definição do recurso no portal do Azure. Especifique as instruções nos artigos para cada topologia da ligação quando é necessária uma ferramenta de configuração específicas. Para obter informações sobre as definições para o VPN Gateway e recursos individuais, consulte o artigo [definições de sobre VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

As secções seguintes contêm tabelas da lista:

- modelo de implementação disponíveis
- ferramentas de configuração disponíveis
- ligações que levá-lo diretamente para um artigo, se disponível

Utilize os diagramas e as descrições para ajudar a seleccionar a topologia de ligação para que correspondam aos requisitos. Os diagramas mostram as topologias do plano base principal, mas é possível criar configurações mais complexas utilizando os diagramas como orientação.

## <a name="site-to-site-and-multi-site"></a>Site para o Site e sites com várias

### <a name="site-to-site"></a>Site para o Site

Uma ligação de gateway VPN de Site para Site (S2S) é uma ligação ao longo do túnel VPN/IKE IPsec (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizada no local que tem um endereço IP público atribuído à mesma e não está localizado atrás de um NAT. Ligações de S2S podem ser utilizadas para publicação em local e híbrido configurações.   

![Ligação S2S] (./media/vpn-gateway-about-vpngateways/demos2s.png "site para o site")


### <a name="multi-site"></a>Múltiplos sites

Pode criar e configurar uma ligação de gateway VPN entre o seu VNet e múltiplas redes no local. Quando trabalha com várias ligações, tem de utilizar um tipo de RouteBased VPN (o gateway dinâmico para VNets clássico). Uma vez que um VNet só pode ter um gateway VPN, todas as ligações através do gateway partilham a largura de banda disponível. Esta opção é denominada frequentemente uma ligação de "com várias site".
 

![Ligação de múltiplos Site] (./media/vpn-gateway-about-vpngateways/demomulti.png "múltiplos sites")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implementação e métodos de Site para o Site e sites com várias

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet para VNet

(VNet para VNet) que liga uma rede virtual a outra rede virtual é semelhante a ligar-se uma VNet para uma localização do site no local. Ambos os tipos de conectividade utilizam um gateway VPN para fornecer um túnel seguro através do IPsec/IKE. Ainda pode combinar VNet para VNet comunicação com configurações de ligação de múltiplos sites. Permite-lhe estabelecer topologias de rede que combinam conectividade de publicação em local com a conectividade de rede entre virtual.

Pode ser VNets que ligar:

- nas regiões mesmo ou diferentes
- nas subscrições do mesmo ou diferentes 
- nos modelos de implementação diferentes mesmo


![VNet VNet ligação] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet para vnet")

#### <a name="connections-between-deployment-models"></a>Ligações entre os modelos de implementação

Azure atualmente tem dois modelos de implementação: clássica e o Gestor de recursos. Se tiver utilizado o Azure há algum tempo, provavelmente tem Azure VMs e funções de instância a ser executada numa VNet clássica. O mais recentes VMs e instâncias de função poderão estar em execução um VNet criado no Gestor de recursos. Pode criar uma ligação entre o VNets para permitir que os recursos num VNet para comunicar diretamente com recursos no outro.

#### <a name="vnet-peering"></a>VNet efectuado

Poderá conseguir utilizar VNet efectuado para criar a sua ligação, desde que a rede virtual cumpre determinados requisitos. VNet efectuado não utilize um gateway de rede virtual. Para mais informações, consulte o artigo [VNet efectuado](../virtual-network/virtual-network-peering-overview.md).


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementação e métodos para VNet para VNet

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Site de ponto

Uma ligação de gateway VPN de ponto-para-Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. P2S é uma ligação VPN sobre SSTP (Secure Socket túnel Protocol). Ligações de P2S não necessitam de um dispositivo VPN ou um endereço IP público para trabalhar. Estabelecer a ligação VPN por iniciá-lo a partir do computador cliente. Esta solução é útil quando pretende ligar à sua VNet a partir de uma localização remota, tais como a partir de casa ou numa conferência, ou quando tiver apenas alguns clientes que necessitam para ligar a um VNet. Ligações de P2S podem ser utilizadas em conjunto com ligações de S2S através do mesmo gateway VPN, desde que todos os requisitos de configuração para ambas as ligações são compatíveis.


Ligação ![ponto-para-site] (./media/vpn-gateway-about-vpngateways/demop2s.png "site de ponto")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Modelos de implementação e métodos para ponto-para-Site

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Uma ligação de ExpressRoute, um gateway de rede virtual está configurado com o tipo de gateway 'ExpressRoute', em vez de 'Vpn'. Para mais informações sobre ExpressRoute, consulte o artigo [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Ligações de coexistentes site para o Site e ExpressRoute

ExpressRoute é uma ligação direta, dedicada a partir do seu WAN (não através da Internet pública) e ao Microsoft Services, incluindo Azure. Site para Site VPN tráfego viagens encriptadas através da Internet pública. Ser capaz de configurar ligações VPN do Site para o Site e ExpressRoute para a mesma rede virtual tem várias vantagens.

Pode configurar uma VPN do Site para o Site como um caminho de activação pós-falha seguro para ExpressRoute ou utilizar VPNs de Site para o Site para ligar a sites que não fazem parte da sua rede, mas que está ligado através de ExpressRoute. Aviso que isto requer dois gateways de rede virtual para a mesma rede virtual, utilizando uma - GatewayType Vpn e o outro utilizando - GatewayType ExpressRoute.


![Ligação Coexist] (./media/vpn-gateway-about-vpngateways/demoer.png "expressroute site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modelos de implementação e métodos para S2S e ExpressRoute

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Próximos passos

Planear a configuração de gateway VPN. Consulte o artigo [planeamento de Gateway VPN e estrutura](vpn-gateway-plan-design.md) e [ligar a sua rede no local para Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
