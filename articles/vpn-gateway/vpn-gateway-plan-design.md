<properties 
   pageTitle="Planeamento de VPN Gateway e estrutura | Microsoft Azure"
   description="Saiba mais sobre o planeamento de VPN Gateway e estrutura para publicação em local, híbrido e VNet para VNet ligações"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Planeamento e estrutura para o VPN Gateway

Planear e estruturar o seu cruzada local e VNet para VNet configurações podem ser simples ou complicado, consoante as suas necessidades de redes. Este artigo apresenta-lhe pela considerações de planeamento e estrutura básica.

## <a name="planning"></a>Planeamento


### <a name="compare"></a>Opções de conectividade de publicação em local

Se pretender ligar os seus sites no local em segurança a uma rede virtual, tem três formas diferentes para fazê-lo: Site para Site ponto para o Site e ExpressRoute. Compare as ligações de publicação em locais diferentes que estão disponíveis. A opção que escolher pode depender de vários considerações, tais como:


- Que tipo de débito requer a solução?
- Pretende comunicar através da Internet pública através de VPN seguro ou através de uma ligação privada?
- Tem um endereço IP público disponível para utilização?
- Planeia utilizar um dispositivo VPN? Se Sim, é compatível com?
- Está a ligar apenas alguns computadores ou pretende uma ligação persistente para o seu site?
- Que tipo de VPN gateway é necessário para a solução que pretende criar?
- Qual gateway SKU devo utilizar?


A tabela seguinte pode ajudar a decidir a melhor opção de conectividade para a sua solução.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Requisitos de gateway ao tipo de VPN e SKU

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Para mais informações sobre o gateway SKUs, consulte o artigo [definições de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Agregado débito por tipo VPN e SKU

A tabela seguinte mostra os tipos de gateway e o débito agregação estimado. O estimado débito agregação pode ser um fator de decisão para a sua estrutura.
Preços diferem entre o gateway SKUs. Para obter informações sobre preços, consulte o artigo [VPN preços de Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabela aplica-se para o Gestor de recursos e de modelos de implementação clássica.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configurações suportadas por tipo VPN e SKU

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Fluxo de trabalho

A lista seguinte descreve o fluxo de trabalho comuns para a conectividade da nuvem:

1.  Estruturar e planear a topologia de conectividade e listam os espaços de endereço para todas as redes que pretende ligar.
2.  Crie uma rede virtual Azure. 
3.  Crie um gateway VPN para a rede virtual.
4.  Criar e configurar ligações a redes no local ou noutras redes virtuais (conforme necessário).
5.  Criar e configurar uma ligação ponto-para-Site para o gateway Azure VPN (conforme necessário).
 

## <a name="design"></a>Estrutura

### <a name="topologies"></a>Topologias de ligação

Comece por consultar os diagramas no artigo [Sobre VPN Gateway](vpn-gateway-about-vpngateways.md) . Este artigo contém diagramas básicos, os modelos de implementação para cada topologia (Gestor de recursos ou clássico) e qual implementação ferramentas podem utilizar para implementar a sua configuração.   

### <a name="designbasics"></a>Princípios básicos da estrutura

As secções seguintes abordam as noções básicas de gateway VPN. Além disso, considere [rede limitações de serviços](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Sobre sub-redes

Quando estiver a criar ligações, tem de considerar intervalos sua sub-rede. Não pode ter de intervalos de endereços de sub-rede sobrepostos. Uma sub-rede sobreposta é quando uma rede virtual ou localização no local contém o mesmo espaço de endereços que contém a outra localização. Isto significa que precisa de engenheiros sua rede para redes local no local para excepção saída de um intervalo que pode utilizar para o seu IP Azure endereçar espaço/sub-redes. Precisa de espaço de endereços que não está a ser utilizado na rede local no local. 

Evitar sobrepostos sub-redes é igualmente importante quando está a trabalhar com ligações de VNet para VNet. Se sobrepor-se a sua sub-redes e um endereço IP existe em enviar e destino VNets, ligações de VNet para VNet falham. Azure não é possível encaminhar os dados para a outras VNet porque o endereço de destino faz parte de VNet o envio. 

VPN Gateways requerem uma sub-rede específica denominada sub-rede um gateway. Todas as sub-redes gateway tem de ser nome GatewaySubnet funcione corretamente. Certifique-se de que não um nome diferente sua sub-rede aceda gateway e não implementar VMs ou mais nada à sub-rede de gateway. Consulte o artigo [sub-redes de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Sobre os gateways de rede local

O gateway de rede local refere-se normalmente, a sua localização no local. No modelo clássico de implementação, o gateway de rede local é referido como um Site de rede Local. Quando configura um gateway de rede local, atribua um nome, especifique o endereço IP público do dispositivo VPN no local e, especifique os prefixos de endereços que se encontram na localização no local. Azure analisa prefixos de endereço de destino para o tráfego de rede, consulta a configuração especificadas para o gateway de rede local e encaminha pacotes em conformidade. Pode modificar estes prefixos de endereço, conforme necessário. Para mais informações, consulte o artigo [gateways de rede Local](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>Informações sobre tipos de gateway

Selecionar o tipo de gateway correta para a sua topologia é crítica. Se selecionar o tipo errado, o gateway não irão funcionar corretamente. Especifica o tipo de gateway como o gateway propriamente dito liga-se e é uma definição de configuração necessários para o modelo de implementação do Gestor de recursos.

Os tipos de gateway são:

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>Sobre tipos de ligação

Cada configuração requer um tipo de ligação específicas. Os tipos de ligação são:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>Informações sobre tipos de VPN

Cada configuração requer um tipo específico de VPN. Se está a combinar duas configurações, tal como criar uma ligação de Site para o Site e uma ligação ponto-para-Site para o mesmo VNet, tem de utilizar um tipo de VPN que satisfaz ambos os requisitos de ligação.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

As tabelas seguintes mostram o tipo VPN como-mapas para cada configuração da ligação. Certifique-se que o tipo VPN para o gateway corresponde à configuração que pretende criar. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>Dispositivos VPN para ligações de Site para o Site

Para configurar uma ligação de Site para o Site, independentemente do modelo de implementação, tem os seguintes itens:

- Um dispositivo VPN que seja compatível com gateways VPN do Azure
- Um endereço de IPv4 IP direccionada que não esteja por trás de um NAT

Tem de ter experiência a configurar o seu dispositivo VPN ou se tiver uma pessoa que pode configurar o dispositivo para si. Para mais informações sobre os dispositivos VPN, consulte o artigo [sobre VPN dispositivos](vpn-gateway-about-vpn-devices.md). O artigo de dispositivos VPN contém informações sobre dispositivos validados, requisitos para dispositivos que não tenham sido validados e ligações para documentos de configuração do dispositivo se disponível.

### <a name="forcedtunnel"></a>Considere o encaminhamento de túnel forçada

Na maioria das configurações, pode configurar túnel forçada. Túnel forçada permite-lhe redirecionar ou "forçar" todo o tráfego de Internet vinculadas novamente para sua localização no local através de um túnel de Site para o Site VPN para auditoria e controlo. Isto é um requisito de segurança crítica para a maioria dos enterprise IT políticas. 

Sem túnel forçada, o tráfego de Internet vinculadas a partir do seu VMs no Azure sempre irá percorrer à Internet, sem a opção para permitir que inspecionar ou o tráfego de auditoria de infraestrutura de rede Azure diretamente saída. Acesso à Internet não autorizado potencialmente pode conduzir a divulgação de informações ou outros tipos de quebras de segurança.

**Forçada túnel diagrama**

![Ligação de túnel forçada] (./media/vpn-gateway-plan-design/forced-tunnel.png "forçada túnel")

Uma ligação de túnel forçada pode ser configurada em ambos os modelos de implementação e utilizando ferramentas diferentes. Consulte a tabela seguinte para obter mais informações. Esta tabela poderemos actualizar como novos artigos, modelos de implementação novos e ferramentas adicionais de ficam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir da tabela.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Próximos passos

Consulte os artigos [Perguntas mais frequentes do Gateway VPN](vpn-gateway-vpn-faq.md) e [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações para ajudá-lo com a estrutura.

Para mais informações sobre as definições de gateway específico, consulte o artigo [Sobre VPN as definições de Gateway](vpn-gateway-about-vpn-gateway-settings.md).




