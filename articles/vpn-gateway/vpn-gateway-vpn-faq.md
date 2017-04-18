<properties 
   pageTitle="Rede virtual VPN Gateway FAQ | Microsoft Azure"
   description="O Gateway VPN FAQ. FAQ para rede Virtual do Microsoft Azure cruzada local ligações, ligações de configuração híbrida e VPN Gateways"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>VPN Gateway perguntas mais frequentes

## <a name="connecting-to-virtual-networks"></a>Ligar a redes virtuais

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Pode ligar redes virtuais em diferentes regiões Azure?
Sim. Na verdade, não existe nenhuma restrição de região. Uma rede virtual pode ligar a outra rede virtual na mesma região ou numa região Azure diferente.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Pode ligar redes virtuais em diferentes subscrições?
Sim.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Posso ligar a vários sites a partir de uma única rede virtual?

Pode ligar a vários sites utilizando o Windows PowerShell e APIs do resto do Azure. Consulte a secção FAQ do [Site com várias e VNet para VNet conectividade](#multi-site-and-vnet-to-vnet-connectivity) .
## <a name="what-are-my-cross-premises-connection-options"></a>Quais são as opções de ligação de publicação em local?

As seguintes ligações de publicação em local são suportadas:

- [Site para o Site](vpn-gateway-site-to-site-create.md) – uma ligação VPN através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou RRAS.

- [Ponto-para-Site](vpn-gateway-point-to-site-create.md) – uma ligação VPN através de SSTP (Secure Socket túnel Protocol). Esta ligação não requer um dispositivo VPN.

- [VNet para VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – este tipo de ligação é igual a configuração de um Site para o Site. VNet para VNet é uma ligação VPN sobre IPsec (IKE v1 e IKE v2). Não requer um dispositivo VPN.

- [Múltiplos sites](vpn-gateway-multi-site.md) – este é uma variação de uma configuração de Site para o Site que permite-lhe ligar a vários sites no local para uma rede virtual.

- [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute é uma ligação direta para Azure a partir do seu WAN, não através da Internet pública. Consulte o artigo [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md) e as [Perguntas mais frequentes do ExpressRoute](../expressroute/expressroute-faqs.md) para obter mais informações.

Para mais informações sobre as ligações, consulte o artigo [Sobre VPN Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>O que é a diferença entre uma ligação de Site para o Site e o ponto para Site?

Ligações de **Site para o Site** permitem-lhe ligar-se entre qualquer um dos computadores localizados nas suas instalações a qualquer máquina virtual ou instância de funções na rede da sua virtual, dependendo de como escolher configurar o encaminhamento. É uma ótima opção para uma ligação local cruzada sempre disponíveis e é bem adequado para configurações híbridas. Este tipo de ligação depende de um aparelho IPsec VPN (hardware ou aparelho contornos), que tem de ser implementado na extremidade da sua rede. Para criar este tipo de ligação, tem de ter o hardware VPN necessário e endereços IPv4 externamente opostos.

**Ponto-para-Site** ligações permitem-lhe ligar a partir de um único computador a partir de qualquer lugar a qualquer elemento localizado na sua rede virtual. Utiliza o cliente VPN do Windows na caixa. Como parte da configuração do ponto-para-Site, instale um certificado e um pacote de configuração de cliente VPN, que contém as definições de que permitir que o computador ligar a qualquer máquina virtual ou instância de funções dentro da rede virtual. É ótimo quando pretende ligar a uma rede virtual, mas não estão localizado no local. Também é uma boa opção quando não tem acesso a VPN hardware ou um endereço de IPv4 oposto externamente, ambos que são necessários para uma ligação de Site para o Site. 

Pode configurar a sua rede virtual para utilizar o Site para o Site e ponto-para-Site em simultâneo, desde que pode cria a sua ligação de Site para o Site utilizando um tipo VPN com base em encaminhar para o gateway. Tipos VPN com base em encaminhar chamam gateways dinâmicos no modelo de implementação clássica.

### <a name="what-is-expressroute"></a>O que é ExpressRoute?

ExpressRoute permite-lhe criar ligações privada entre centros de dados do Microsoft e infraestrutura de que está no seu local ou num ambiente localização cocriação. Com ExpressRoute, pode estabelecer ligações a serviços em nuvem Microsoft como Microsoft Azure e no Office 365 numa funcionalidade de cocriação localização de parceiro ExpressRoute ou ligar diretamente da sua rede existente WAN (tal como uma VPN MPLS fornecida por um fornecedor de serviço de rede).

ExpressRoute ligações fornecem maior segurança, mais fiabilidade, largura de banda superior e inferiores latências que as ligações típicas através da Internet. Em alguns casos, utilizando ligações de ExpressRoute para transferir dados entre o seu rede no local e o Azure pode também lucro benefícios significativos custo. Se já tiver criado uma ligação de publicação em local da sua rede no local para Azure, pode migrar uma ligação a ExpressRoute enquanto mantém a sua rede virtual intacto.

Consulte as [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) para obter mais detalhes.

## <a name="site-to-site-connections-and-vpn-devices"></a>Ligações de site para o Site e dispositivos VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>O que posso considerar quando selecionar um dispositivo VPN?

Vamos validadas um conjunto de dispositivos de Site para o Site VPN padrão em parceria com fornecedores de dispositivo. Uma lista de dispositivos VPN compatíveis conhecidos, as instruções de configuração correspondente ou amostras e especificações de dispositivo pode ser encontrada [aqui](vpn-gateway-about-vpn-devices.md). Todos os dispositivos nas famílias dispositivo listados como compatíveis com o conhecidos devem trabalhar com rede Virtual. Para ajudar a configurar o seu dispositivo VPN, referir-se para o exemplo de configuração do dispositivo ou a ligação que corresponde ao família dispositivo apropriado.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>O que fazer se tiver um dispositivo VPN que não esteja na lista de dispositivos compatíveis conhecidos?

Se não vir o dispositivo listado como um dispositivo VPN compatível conhecido e pretende utilizá-lo para a sua ligação VPN, terá de confirmar que cumpre as suportados IPsec/IKE configuração opções e parâmetros listados [aqui](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Dispositivos que satisfaçam os requisitos mínimos devem funciona bem com VPN gateways. Contacte o fabricante do dispositivo para instruções de configuração e suporte adicionais.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Por que motivo é que o meu túnel VPN baseada em políticas descer quando tráfego está inactivo?

Este é o comportamento esperado para baseada em políticas (também conhecido como encaminhamento estático) VPN gateways. Quando o tráfego através do túnel está inactivo durante mais de cinco minutos, vai ser incompleta túnel para baixo. Quando o tráfego de inicia a fluir em qualquer direção, túnel será restabelecido imediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Pode utilizar o software VPNs para ligar ao Azure?

Suportamos o encaminhamento do Windows Server 2012 e os servidores de acesso remoto (RRAS) para a configuração de publicação em local do Site para o Site.

Outras soluções VPN software devem trabalhar com os nossos gateway, desde que satisfizerem para implementações do IPsec norma da indústria. Contacte o fornecedor do software para obter instruções de configuração e suporte.

## <a name="point-to-site-connections"></a>Ligações ponto-para-Site

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Que sistemas operativos pode utilizar com ponto-para-Site?

Os seguintes sistemas operativos são suportados:

- Windows 7 (32 bits e 64 bits)

- Windows Server 2008 R2 (apenas de 64 bits)

- Windows 8 (32 bits e 64 bits)

- Windows 8.1 (32 bits e 64 bits)

- Windows Server 2012 (apenas de 64 bits)

- Windows Server 2012 R2 (apenas de 64 bits)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Pode utilizar qualquer cliente VPN de software para o ponto-para-Site que suporta SSTP?

Não. O suporte está limitado apenas para as versões de sistemas operativos do Windows listadas em cima.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos finais cliente VPN podem ter na minha configuração ponto-para-Site?

Suportamos até 128 clientes VPN para conseguir ligar a uma rede virtual ao mesmo tempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso utilizar o meu próprio AC de raiz PKI interna para conectividade de ponto-para-Site?

Sim. Anteriormente, apenas os certificados de raiz autoassinado podem ser utilizados. Ainda pode carregar 20 certificados de raiz.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso percorrer proxies e firewalls utilizando a funcionalidade de ponto-para-Site?

Sim. Utilizamos SSTP (Secure Socket túnel Protocol) para túnel através de firewalls. Este túnel será apresentado como uma ligação HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para o ponto para o Site, irão VPN automaticamente restabelecer a ligação?

Por predefinição, o computador cliente vai não restabelecer a ligação VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Suporte ponto-para-Site automática-restabelecer a ligação e DDNS nos clientes VPN?

Restabelecer a ligação automática e DDNS atualmente não são suportadas no ponto-para-Site VPNs.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter Site para o Site e as configurações de ponto-para-Site coexistem para a mesma rede virtual?

Sim. Ambas as estas soluções irão funcionar se tiver um tipo de RouteBased VPN para o gateway. O modelo clássico de implementação, terá de um gateway dinâmico. Vamos fazer não ponto-para-Site de suporte para estáticos gateways VPN encaminhamento ou gateways utilizando - VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Pode configurar um cliente de ponto-para-Site para ligar a várias redes virtuais ao mesmo tempo?

Sim, é possível. Mas as redes virtuais não podem ter sobrepostos prefixos IP e os espaços de endereço de ponto-para-Site não devem sobrepor-se entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanto custa débito pode esperar através de ligações de Site para o Site ou Site de ponto

É difícil manter o débito exato de túneis VPN. IPsec e SSTP são protocolos de VPN crypto grossa. Débito também é limitado pela latência e a largura de banda entre o local e na Internet.

## <a name="gateways"></a>Gateways

### <a name="what-is-a-policy-based-static-routing-gateway"></a>O que é um gateway baseada em políticas (encaminhamento estática)?

Baseada em políticas gateways implementam VPNs baseadas na política. VPNs baseadas na política encriptar e direta pacotes através de túneis IPsec com base nas combinações de prefixos de endereço entre a sua rede no local e o Azure VNet. A política (ou Seletor de tráfego), normalmente é definida como uma lista de acesso a configuração de VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>O que é um gateway (encaminhamento dinâmico) baseado em encaminhar?

Com base em encaminhar gateways implementam as VPNs baseadas em encaminhar. VPNs baseadas em encaminhar utilizam "rotas" no período do inquérito a reencaminhar ou a tabela de encaminhamento para pacotes diretas para os respetivos interfaces de túnel correspondente. Em seguida, as interfaces de túnel encriptar ou desencriptar os pacotes e terminar os túneis. No selector de política ou o tráfego para encaminhar com base VPNs estão configurados como qualquer-para-qualquer (ou caracteres universais).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Posso obter o meu endereço IP de gateway VPN antes de posso criá-lo?

Não. Tem de criar o seu gateway pela primeira vez para obter o endereço IP. Altera o endereço IP se eliminar e recrie o gateway da VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Como é que o meu túnel VPN obter autenticado?

Azure VPN utiliza a autenticação de PSK (pré-partilhada chave). Vamos gerar uma chave previamente partilhada (PSK) quando criamos do túnel VPN. Pode alterar o PSK gerados automaticamente para o seu próprio com o cmdlet do PowerShell de chave de pré-partilhada definir ou REST API.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Pode utilizar a API para definir tecla pré-partilhada para configurar a minha política com base no gateway (encaminhamento estático) VPN?

Sim, o cmdlet definir API de chave de pré-partilhada e PowerShell pode ser utilizado para configurar o Azure baseada em políticas VPNs (estáticas) e (dinâmicas) VPNs encaminhamento baseadas em encaminhar.

### <a name="can-i-use-other-authentication-options"></a>Pode utilizar outras opções de autenticação?

Vamos são limitados ao utilizar teclas de pré-partilhadas (PSK) para autenticação.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>O que é a "sub-rede gateway" e por que motivo é necessário?

Temos um serviço de gateway que vamos executar para permitir a conectividade de publicação em local. 

Terá de criar uma sub-rede de gateway para a sua VNet configurar um gateway VPN. Todas as sub-redes gateway tem de ser nome GatewaySubnet funcione corretamente. Não atribuir um nome a sua sub-rede aceda gateway algo. E não implementar VMs ou mais nada à sub-rede de gateway.

O tamanho mínimo de sub-rede gateway depende completamente na configuração ao qual pretende criar. Apesar de é possível criar uma sub-rede de gateway tão pequenas quanto /29 para algumas configurações, recomendamos que cria uma sub-rede gateway de /28 polegadas ou superior (/ 28, /27, /26 etc.). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Posso implementar o máquinas virtuais ou instâncias de função à minha sub-rede gateway?

Não.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Como especificar quais tráfego acede através do gateway VPN?

Se estiver a utilizar o Portal clássica Azure, adicione cada intervalo que pretende enviado através do gateway para a sua rede virtual na página redes em redes locais.

### <a name="can-i-configure-forced-tunneling"></a>Posso configurar forçada túnel?

Sim. Consulte o artigo [Configurar forçada túnel](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Pode configurar o meu servidor VPN no Azure e utilizá-la para ligar à minha rede no local?

Sim, pode implementar os seus próprios VPN gateways ou servidores no Azure quer a partir do Azure Marketplace ou criar o seus próprio routers VPN. Terá de configurar rotas definidas pelo utilizador na sua rede virtual para garantir que o tráfego é encaminhado corretamente entre redes no local e o seu sub-redes rede virtual.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Por que motivo determinadas portas são abertas no meu gateway VPN?

São necessários para a comunicação de infraestrutura Azure. Estejam protegidos (bloqueado) por Azure certificados. Sem os certificados inicial, entidades externas, incluindo os clientes desses gateways, não poderá causar qualquer efeito sobre essas pontos finais.

Um gateway VPN bastante é um dispositivo hospedados multi com um tocar NIC para a rede privada de cliente e um NIC opostas da rede pública. Entidades de infraestrutura Azure não é possível Descubra redes privadas do cliente por razões de conformidade, pelo que precisam de utilizam os pontos finais públicos para a comunicação de infraestrutura. Os pontos finais públicos periodicamente são pesquisados por auditoria de segurança Azure.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Obter mais informações sobre tipos de gateway, requisitos e débito

Para mais informações, consulte o artigo [Sobre VPN as definições de Gateway](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Múltiplos sites e a conectividade de VNet para VNet

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Qual o tipo de gateways pode suportar múltiplos sites e VNet para VNet conectividade?

Apenas encaminhar com base em VPNs (encaminhamento dinâmico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Pode ligar um VNet com um tipo de VPN para outra VNet RouteBased com um tipo de PolicyBased VPN?

Não, ambas as redes virtuais tem de utilizar com base em encaminhar (encaminhamento dinâmico) VPNs.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>É o tráfego de VNet para VNet segura?

Sim, é protegida por encriptação IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>VNet para VNet tráfego viaja sobre a estrutura principal Azure?

Sim.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Como vários sites no local e redes virtuais pode uma rede virtual ligar?

Máximo. 10 combinados para os gateways Basic e encaminhamento dinâmico padrão 30 para os gateways de alto desempenho VPN.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Posso utilizar ponto-para-Site VPNs com a minha rede virtual com vários túneis VPN?

Sim, VPNs ponto-para-Site (P2S) podem ser utilizadas com os gateways VPN ligar a vários sites no local e outras redes virtuais.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Pode configurar vários túneis entre a minha rede virtual e o meu site no local através de múltiplos site VPN?

Não, redundantes túneis entre uma rede virtual Azure e um site no local não são suportados.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Não existem a pode ser sobrepostos espaços de endereços entre as redes virtuais ligadas e sites de locais no local?

Não. Sobreposição de espaços de endereços irão provocar o carregamento de ficheiro de configuração de rede ou "Criar rede Virtual" falha.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Posso obter mais largura de banda com mais VPNs de Site para o Site que para uma única rede virtual?

Não, todos os túneis VPN, incluindo ponto-para-Site VPNs, partilham o mesmo gateway Azure VPN e a largura de banda disponível.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Pode utilizar gateway Azure VPN para o tráfego de trânsito entre os meus sites no local ou a outra rede virtual?

**Modelo de implementação clássico**<br>
O tráfego de trânsito através do gateway Azure VPN é possível utilizar o modelo clássico de implementação, mas depende do espaços de endereços estática definido no ficheiro de configuração de rede. BGP ainda não é suportada com redes virtuais do Azure e gateways VPN utilizando o modelo de implementação clássica. Sem BGP, definir manualmente espaços de endereços de trânsito muito erro está sujeito e não recomendado.<br>
**Modelo de implementação do Gestor de recursos**<br>
Se estiver a utilizar o modelo de implementação do Gestor de recursos, consulte a secção [BGP](#bgp) para obter mais informações.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure gera a mesma chave previamente partilhada IPsec/IKE para todas as minhas ligações de VPN para a mesma rede virtual?

Não, Azure por predefinição gera chaves pré-partilhadas diferentes para ligações de VPN diferentes. No entanto, pode utilizar o cmdlet definir VPN Gateway chave API REST ou o PowerShell para definir o valor de chave que preferir. A chave tem de ser alfanumérico cadeia de comprimento entre 1 a 128 caracteres.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure cobra para o tráfego entre redes virtuais?

Para o tráfego entre diferentes redes virtuais Azure, Azure encargos apenas para o tráfego percorrer a partir de uma região Azure para outra. A taxa de juros está listada na página do Azure [VPN preços de Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) .


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Posso ligar de uma rede virtual com IPsec VPNs ao meu circuito ExpressRoute?

Sim, isto é suportado. Para mais informações, consulte o artigo [Configurar ExpressRoute e ligações de VPN de Site para o Site que coexistem](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>Conectividade de publicação em local e VMs

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se o meu máquina virtual for numa rede virtual e tenho de ter uma ligação de publicação em local, como deve me posso ligar a VM?

Tem algumas opções. Se tiver RDP ativado e se tiver criado um ponto final, pode ligar-se a sua máquina virtual utilizando o VIP. Nesse caso, é especificar o VIP e a porta ao qual pretende ligar. Terá de configurar a porta no seu computador virtual para o tráfego. Normalmente, teria aceda ao Portal clássica do Azure e guardar as definições para a ligação RDP para o seu computador. As definições contêm as informações de ligação necessárias.

Se tiver uma rede virtual com a conectividade de publicação em local configurada, pode ligar-se a sua máquina virtual utilizando a DIP interno ou o endereço IP privado. Também pode ligar ao seu máquina virtual por DIP interno a partir de outra máquina virtual que se encontra na mesma rede virtual. Não consegue RDP para o seu computador virtual utilizando DIP se estiver a ligar de uma localização fora da sua rede virtual. Por exemplo, se tiver uma rede virtual do ponto-para-Site configurada e não estabelecer uma ligação do seu computador, não consegue ligar à máquina virtual por DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se estiver minha máquina virtual numa rede virtual com a conectividade de publicação em local, todo o tráfego do meu VM percorrer essa ligação?

Não. Apenas o tráfego que tem um destino IP contidas nos intervalos de endereços de IP de rede Local rede virtual que especificou subscreve através do gateway de rede virtual. O tráfego tem um destino IP localizado dentro da rede virtual permanece no interior da rede virtual. Outro tráfego é enviado através do Balanceador de carga para redes públicas ou, se for utilizado túnel forçada, enviado através do gateway Azure VPN. Se estiver a resolver problemas, é importante para se certificar de que tem todos os intervalos listados na sua rede Local que pretende enviar através do gateway. Certifique-se de que os intervalos de endereços de rede Local não se sobrepõem com qualquer um dos intervalos de endereços na rede virtual. Além disso, que pretende Certifique-se de que o servidor de DNS que estiver a utilizar está resolução do nome para o endereço IP adequado.


## <a name="virtual-network-faq"></a>Rede virtual perguntas mais frequentes

Ver informações de rede virtual adicionais nas [FAQ sobre a rede Virtual](../virtual-network/virtual-networks-faq.md).
 
