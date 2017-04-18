<properties
   pageTitle="Descrição geral das configurações altamente disponíveis com o Azure VPN Gateways | Microsoft Azure"
   description="Este artigo fornece uma descrição geral das opções de configuração altamente disponível com o Azure VPN Gateways."
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
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Local cruz altamente disponíveis e a conectividade de VNet para VNet

Este artigo fornece uma descrição geral das opções de configuração altamente disponíveis para as suas cruzada local e a conectividade de VNet para VNet utilizando gateways Azure VPN.

## <a name = "activestandby"></a>Sobre a redundância de gateway VPN do Azure

Cada gateway Azure VPN consiste em duas instâncias numa configuração suspensão ativo. Para qualquer manutenção planeada ou não planeada interrupção que acontece à instância ativa, a instância espera assumir o controlo (activação pós-falha) automaticamente e retomar a S2S VPN ou ligações de VNet para VNet. O parâmetro sobre provocará uma interrupção breve. Para a manutenção planeada, a ligação deve ser restaurada dentro de 10 a 15 segundos. Para problemas não planeados, a recuperação de ligação será mais longa, cerca de 1 minuto a 1 e meia minutos na pior. P2S VPN para ligações de cliente para o gateway, as ligações de P2S serão desligadas e os utilizadores terá de voltar a ligar a partir de computadores cliente.

![Modo de espera ativo](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Conectividade de publicação em local altamente disponíveis

Para fornecer uma melhor disponibilidade para as ligações de local cruzada, existem algumas das opções disponíveis:

- Vários dispositivos VPN no local
- Gateway de Azure VPN activo activo
- Combinação de ambos

### <a name = "activeactiveonprem"></a>Vários dispositivos VPN no local

Pode utilizar vários dispositivos VPN da sua rede no local para ligar ao seu gateway Azure VPN, conforme mostrado no diagrama seguinte:

![Vários no local VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Esta configuração fornece várias túneis ativos a partir do mesmo gateway Azure VPN para os seus dispositivos no local na mesma localização. Existem alguns requisitos e restrições:

1. Tem de criar várias ligações S2S VPN seus dispositivos VPN para Azure. Quando se ligar vários dispositivos VPN a partir da mesma rede no local para Azure, tem de criar um gateway de rede local para cada dispositivo VPN e uma ligação a partir do seu gateway Azure VPN para o gateway de rede local.

2. Os gateways de rede local correspondente aos seus dispositivos VPN tem de ter os endereços IP públicos exclusivos na propriedade "GatewayIpAddress".

3. BGP é necessário para esta configuração. Cada gateway de rede local que representa um dispositivo VPN tem de ter um endereço IP do ponto a ponto BGP exclusivo especificado na propriedade "BgpPeerIpAddress".

4. O campo de propriedade AddressPrefix em cada gateway de rede local não sobrepor. Deverá especificar "BgpPeerIpAddress" no /32 formato CIDR no campo AddressPrefix, por exemplo, 10.200.200.254/32.

5. Deve utilizar BGP para anunciar o mesmo prefixos do mesmo no local prefixos de rede para o gateway da VPN do Azure e o tráfego será reencaminhado através destes túneis em simultâneo.

6. Cada ligação é contada contra o número máximo de túneis para o gateway Azure VPN, 10 para Basic e SKUs padrão e 30 para reversa SKU. 

Nesta configuração, o gateway Azure VPN ainda está em modo de espera de ativo, para que o mesmo comportamento activação pós-falha e interrupções breve ainda irão acontecer como descrito [acima](#activestandby). Mas esta configuração protege contra falhas ou interrupções no seu rede no local e dispositivos VPN.
 
### <a name="active-active-azure-vpn-gateway"></a>Gateway de Azure VPN activo activo

Agora pode criar um gateway Azure VPN numa configuração activo activo, onde ambas as instâncias do VMs gateway estabelecerão túneis S2S VPN para o seu dispositivo VPN no local, conforme o diagrama seguinte:

![Activo activo](./media/vpn-gateway-highlyavailable/active-active.png)

Nesta configuração, cada instância do Azure gateway terá um endereço IP público exclusivo e cada estabelecerão um túnel IPsec/IKE S2S VPN para o seu dispositivo VPN no local especificado no seu gateway de rede local e a ligação. Note que ambos os túneis VPN são realmente a parte da ligação do mesmo. Ainda precisa de configurar o seu dispositivo VPN no local para aceitar ou estabelecer duas túneis S2S VPN para esses duas Azure VPN públicos endereços IP do gateway.

Porque as instâncias de Azure gateway encontram-se na configuração activo activo, o tráfego da sua rede virtual Azure à sua rede no local será encaminhado através de ambos os túneis em simultâneo, mesmo se o seu dispositivo VPN no local poderá preferir um túnel cima da outra. Tenha em atenção apesar do mesmo fluxo TCP ou UDP irá percorrer sempre o mesmo túnel ou caminho, a menos que um evento de manutenção acontece das instâncias.

Quando uma manutenção planeada ou evento não planeado acontece a instância de um gateway, será desligado túnel IPsec essa instância para o seu dispositivo VPN no local. As rotas correspondentes nos seus dispositivos VPN devem ser removidas ou retiradas automaticamente para que o tráfego vai ser mudado ao longo para o outro túnel IPsec ativo. No lado Azure, o parâmetro sobre provocará automaticamente da instância afectado para a instância ativa.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Redundância duplo: gateways VPN activo activo redes Azure e no local

A opção mais fiável está combinar os gateways activo activo na sua rede e o Azure, conforme mostrado no diagrama abaixo.

![Redundância dupla](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Aqui criar e configurar o gateway Azure VPN numa configuração activo activo e criar dois gateways de rede local e duas ligações para os dois no local dispositivos VPN, tal como descrito acima. O resultado é uma conectividade malha completa de 4 túneis IPsec entre a rede de virtual Azure e a sua rede no local.

Todos os gateways e túneis estão ativos a partir do lado do Azure, por isso o tráfego será distribuído entre todos os 4 túneis em simultâneo, apesar de cada fluxo TCP ou UDP novamente irá seguir o mesmo túnel ou o caminho do Azure lado. Apesar de por propagarem o tráfego, poderá ver débito ligeiramente melhor sobre os túneis IPsec, é o objetivo principal desta configuração para elevada disponibilidade. E devido à natureza estatística a propagação, é difícil fornecer que a medida da forma como diferentes condições de tráfego de aplicação irá afetar o débito agregação.

Esta topologia irá necessitar de dois gateways de rede local e duas ligações para suportar o par de dispositivos VPN no local e BGP é necessário para permitir que as duas ligações à mesma rede no local. Estes requisitos são iguais [acima](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Conectividade de VNet para VNet altamente disponível através de Gateways Azure VPN

Também pode aplicar a mesma configuração activo activo para ligações do Azure VNet para VNet. Pode criar activo activo VPN gateways para ambas as redes virtuais e ligá-las em conjunto ao formulário a conectividade de malha completa mesmo de 4 túneis entre as duas VNets, conforme mostrado no diagrama abaixo:

![VNet para VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Este procedimento garante são sempre um par de túneis entre as duas redes virtuais para qualquer eventos de manutenção planeada, fornecendo disponibilidade ainda melhor. Apesar da mesma topologia para publicação em local conectividade requer duas ligações, a topologia de VNet para VNet mostrada acima terão apenas uma ligação para cada gateway. Para além disso, BGP é opcional, a menos que o encaminhamento de trânsito através da ligação VNet para VNet é necessário.


## <a name="next-steps"></a>Próximos passos

Consulte o artigo [Configurar o activo activo VPN Gateways para publicação em local e VNet para VNet ligações](vpn-gateway-activeactive-rm-powershell.md) para obter os passos para configurar o local da publicação em activo activo e VNet para VNet ligações.
