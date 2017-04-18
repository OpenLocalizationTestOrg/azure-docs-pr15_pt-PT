<properties 
   pageTitle="Ligar uma rede virtual a um circuito ExpressRoute utilizando o PowerShell | Microsoft Azure"
   description="Este artigo fornece uma descrição geral sobre como ligar redes virtuais (VNets) ExpressRoute circuitos utilizando o modelo de implementação do Gestor de recursos e PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual a um circuito ExpressRoute

> [AZURE.SELECTOR]
- [Portal Azure - Gestor de recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gestor de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell - clássico](expressroute-howto-linkvnet-classic.md)


Este artigo irá ajudá-lo ligar redes virtuais (VNets) a Azure ExpressRoute circuitos ao utilizar o modelo de implementação do Gestor de recursos e PowerShell. Redes virtuais podem ser na mesma subscrição ou parte da subscrição do outra.

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Tem a versão mais recente dos módulos Azure PowerShell (, pelo menos, versão 1.0). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.
- Tem de rever a [Pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Tem de ter um circuito ExpressRoute ativo. 
    - Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e ter o circuito ativado pelo seu fornecedor de conectividade. 
    - Certifique-se de que tem efectuado privado do Azure configurado para o seu circuito. Consulte o artigo [configurar o encaminhamento](expressroute-howto-routing-arm.md) para encaminhar obter instruções. 
    - Certifique-se de que efectuado privado Azure está configurado e a efectuado BGP entre a rede e o Microsoft para cima, para que pode ativar a conectividade de fim para fim.
    - Certifique-se de que tem uma rede virtual e um gateway de rede virtual criado e totalmente aprovisionado. Siga as instruções para criar um [VPN gateway](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), mas não se esqueça de utilizar `-GatewayType ExpressRoute`.

Pode ligar até 10 redes virtuais a um circuito ExpressRoute padrão. Todas as redes virtuais tem de ser na mesma região geopolítica ao utilizar um circuito ExpressRoute padrão. 

Pode ligar um redes virtuais fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais ao seu circuito ExpressRoute se activou o suplemento do ExpressRoute premium. Consulte as [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição para um circuito

Pode ligar um gateway de rede virtual para um circuito ExpressRoute utilizando o cmdlet seguinte. Certifique-se de que o gateway de rede virtual é criado e está pronto para ligação antes de executar o cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual numa subscrição diferente para um circuito

Pode partilhar um circuito ExpressRoute em múltiplas subscrições. A figura seguinte mostra um simples esquemáticos de como funciona a partilha para ExpressRoute circuitos nos múltiplas subscrições.

Cada um das nuvens mais pequenas dentro da nuvem de grandes dimensões é utilizada para representar subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da empresa pode utilizar os seus próprios subscrição para implementar o dos seus serviços –, mas pode partilhar um único circuito de ExpressRoute para ligar novamente a sua rede no local. Um único departamento (neste exemplo: IT) podem ser proprietários de circuito ExpressRoute. Outras subscrições para dentro da empresa podem utilizar o circuito ExpressRoute.

>[AZURE.NOTE] Taxas de conectividade e de largura de banda para o circuito dedicado serão aplicadas ao proprietário de circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.

![Conectividade de subscrição de cruz](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração

O *proprietário de circuito* é um utilizador do power autorizados do recurso de circuito ExpressRoute. O proprietário de circuito pode criar autorizações que podem ser reembolsadas pelos *utilizadores de circuito*. *Utilizadores de circuito* são os proprietários de gateways de rede virtual (que não estão dentro da subscrição do mesmo como o circuito ExpressRoute). *Os utilizadores de circuito* pode resgatar autorizações (uma autorização por rede virtual).

O *proprietário de circuito* tem power para modificar e revogar autorizações em qualquer altura. Revogar a um resultados de autorização em todas as ligações de ligação a ser eliminados da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário de circuito 

#### <a name="creating-an-authorization"></a>Criar uma autorizações
    
O proprietário de circuito cria uma autorização. Isto resulta na criação de uma chave de autorização que pode ser utilizada por um utilizador de circuito para ligar os respetivos gateways de rede virtual ao circuito ExpressRoute. Uma autorização seja válida para apenas uma ligação.

O fragmento do cmdlet seguinte mostra como criar uma autorização:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

A resposta para este irá conter a chave de autorização e o estado:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>Rever autorizações

O proprietário de circuito pode rever autorizações todos os que são enviadas num determinado circuito ao executar o cmdlet seguinte:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>Adicionar autorizações

O proprietário de circuito pode adicionar autorizações utilizando o cmdlet seguinte:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>Eliminar autorizações

O proprietário de circuito pode revogar/eliminar autorizações ao utilizador ao executar o cmdlet seguinte:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Operações de circuito do utilizador

O utilizador de circuito tem o ID do ponto a ponto e uma tecla de autorização ao proprietário de circuito. A chave de autorização é um GUID.

ID de ponto a ponto for, pode ser dada a partir do seguinte comando.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Resgatar autorizações de ligação

O utilizador de circuito pode executar o cmdlet seguinte para resgatar uma autorização de ligação:

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Libertar autorizações de ligação

Pode Liberte uma autorização ao eliminar a ligação que liga o circuito ExpressRoute para a rede virtual.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre ExpressRoute, consulte as [ExpressRoute FAQ](expressroute-faqs.md).
