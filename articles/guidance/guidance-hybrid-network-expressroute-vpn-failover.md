<properties
   pageTitle="Implementar uma arquitetura de rede híbrido altamente disponível | Microsoft Azure"
   description="Como implementar uma arquitetura de rede de site para o site seguro que abrange uma rede virtual Azure e uma rede no local ligado ao utilizar ExpressRoute com VPN activação pós-falha de gateway."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Implementar uma arquitetura de rede híbrido altamente disponíveis

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as melhores práticas para ligar uma rede no local à redes virtuais no Azure utilizando ExpressRoute, com uma site para o site rede privada virtual (VPN) como uma ligação de activação pós-falha. O tráfego flua entre à rede no local e uma rede virtual Azure (VNet) através de uma ligação de ExpressRoute.  Se existir uma perda de conectividade no circuito ExpressRoute, o tráfego será encaminhado através de um túnel IPSec VPN.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Este plano utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Casos de utilização típica para esta arquitectura incluem:

- Aplicações híbridas onde das cargas de trabalho são distribuídas entre uma rede no local e o Azure.

- Aplicações em execução em grande escala, críticos das cargas de trabalho que requerem um elevado grau de escalabilidade.

- Em grande escala meios de cópia de segurança e restauro de dados que tem de ser guardados noutro local.

- Processamento Big Data das cargas de trabalho.

- Utilizar o Azure como um site de recuperação de falhas.

Tenha em atenção que se o circuito ExpressRoute estiver disponível, a rota VPN processará apenas ligações efectuadas privada. Efectuado públicas e Microsoft ligações de efectuado irão transmitir através da Internet.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

>[AZURE.NOTE] O [serviço Azure VPN Gateway] [ azure-vpn-gateway] implementa dois tipos de gateways de rede virtual; Os gateways de rede virtual de VPN e gateways de rede virtual ExpressRoute. Em todo o documento, o termo *VPN Gateway* refere-se ao serviço Azure, enquanto as frases *gateway de rede virtual VPN* e *gateway de rede virtual ExpressRoute* são utilizadas para consultar as implementações VPN e ExpressRoute do gateway respetivamente.

O diagrama seguinte realça os componentes importantes nesta arquitectura:

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está na "híbrido rede - ER VPN" página.

![[0]][0]

- **Redes virtuais Azure (VNets).** Cada VNet reside numa única região Azure e pode alojar várias camadas de aplicação. Camadas de aplicação podem ser segmentadas utilizar sub-redes em cada VNet e/ou grupos de segurança (NSGs) de rede.

- **Rede de empresa no local.** Esta é uma rede de computadores e dispositivos, ligados através de uma rede privada de rede local em execução no interior de uma organização.

- **Aparelho VPN.** Este é um dispositivo ou serviço que fornece conectividade externa à rede no local. Aparelho VPN pode ser um dispositivo de hardware ou pode ser uma solução de software como o encaminhamento e o serviço de acesso remoto (RRAS) no Windows Server 2012.

> [AZURE.NOTE] Para obter uma lista de eletrodomésticos de VPN suportados e informações sobre como configurar selecionados eletrodomésticos de VPN para ligar a um Gateway de VPN Azure, consulte as instruções para o dispositivo adequado na [lista de dispositivos VPN suportados pelo Azure][vpn-appliance].

- **Gateway de rede virtual VPN.** O gateway de rede virtual VPN permite VNet ligar ao aparelho VPN na rede no local. O gateway de rede virtual VPN está configurado para aceitar pedidos de rede no local apenas através de aparelho VPN. Para mais informações, consulte o artigo [ligar uma rede no local para uma rede virtual do Microsoft Azure][connect-to-an-Azure-vnet].

- **ExpressRoute gateway de rede virtual.** O gateway de rede virtual ExpressRoute permite VNet ligar ao circuito ExpressRoute utilizado para a conectividade com a sua rede no local.

- **Sub-rede de gateway.** Os gateways de rede virtual são mantidos na mesma sub-rede.

- **Ligação VPN.** A ligação tem propriedades que especificar o tipo de ligação (IPSec) e a chave partilhado com aparelho VPN no local para encriptar o tráfego.

- **ExpressRoute circuito.** Esta é uma camada 2 ou circuito camada 3 fornecido pelo fornecedor de conectividade essa rede de associações no local com o Azure através de routers o limite. O circuito utiliza a infraestrutura de hardware gerida pelo fornecedor de conectividade.

- **Aplicação da nuvem de camadas.** Esta é a aplicação alojada no Azure. Pode incluir várias camadas, com várias sub-redes ligados através de balanceadores de carga Azure. O tráfego em cada sub-rede poderá estar sujeito a regras definidas ao utilizar [Grupos de segurança de rede do Azure][azure-network-security-group](NSGs). Para obter mais informações, consulte o artigo [Introdução à segurança do Microsoft Azure][getting-started-with-azure-security].

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="vnet-and-gatewaysubnet"></a>VNet e GatewaySubnet

Crie o gateway de rede virtual ExpressRoute e o gateway de rede virtual VPN no VNet mesmo. Isto significa que partilham a mesma sub-rede denominada **GatewaySubnet**

Se o VNet já inclui uma sub-rede denominada **GatewaySubnet**, certifique-se de que tem um /27 ou maior espaço de endereços. Se a sub-rede existente for demasiado pequena, em seguida, removê-la da seguinte forma e criar um novo conforme apresentado na marca seguinte:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Se o VNet não contiver uma sub-rede denominada **GatewaySubnet**, em seguida, crie um novo da seguinte forma:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>Gateways VPN e ExpressRoute

Certifique-se de que a sua organização cumpre os [requisitos de pré-requisito ExpressRoute] [ expressroute-prereq] para ligar à Azure.

Se já tiver um gateway de rede virtual VPN no seu VNet Azure, removê-lo, conforme apresentado abaixo.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Siga as instruções em [execução uma arquitetura de rede híbrida com o Azure ExpressRoute] [ implementing-expressroute] para estabelecer a ligação ExpressRoute.

Siga as instruções em [execução uma arquitetura de rede híbrida com o Azure e no local VPN] [ implementing-vpn] para estabelecer ligação VPN rede virtual gateway.

Depois de ter estabelecido as ligações de gateway rede virtual, teste o ambiente como seguinte:

1. Certifique-se de que pode ligar-se da sua rede no local para o seu VNet Azure.

2. Contacte o fornecedor para parar de conectividade de ExpressRoute para testar a ligação.

3. Certifique-se de que o que pode ainda ligar-se da sua rede no local para o seu VNet Azure utilizando a ligação de gateway de rede virtual VPN.

4. Contacte o fornecedor para reestabilish ExpressRoute conectividade.

## <a name="considerations"></a>Considerações sobre

Considerações ExpressRoute, consulte o artigo [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute] [ guidance-expressroute] orientações.

Considerações de VPN do Site para o Site, consulte o artigo [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN] [ guidance-vpn] orientações.

Considerações de segurança Azure gerais, consulte o artigo [serviços em nuvem da Microsoft e segurança da rede][best-practices-security].

## <a name="solution-deployment"></a>Implementação da solução

Se tiver uma infraestrutura no local existente já configurada com um dispositivo de rede adequadas, pode implementar a arquitetura de referência ao seguir estes passos:

1. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Aguarde que a ligação abra no portal do Azure e, em seguida, siga estes passos: 
  - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-hybrid-vpn-er-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

4. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Aguarde na ligação para o abrir no portal do Azure, em seguida, introduzir, em seguida, siga estes passos: 
  - Selecione **utilizar existentes** na secção do **grupo de recursos** e introduza `ra-hybrid-vpn-er-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Arquitetura de uma arquitetura de rede híbrido altamente disponível utilizando VPN e ExpressRoute gateway"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
