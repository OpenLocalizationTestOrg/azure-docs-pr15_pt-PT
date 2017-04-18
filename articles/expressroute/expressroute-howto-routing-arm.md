<properties
   pageTitle="Como configurar o encaminhamento para um circuito ExpressRoute | Microsoft Azure"
   description="Este artigo explica os passos para a criação e de aprovisionamento privado, público e Microsoft efectuado num circuito ExpressRoute. Este artigo também mostra-lhe como verificar o estado, atualizar ou eliminar peerings para sua circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Criar e modificar o encaminhamento para um circuito ExpressRoute


> [AZURE.SELECTOR]
[Portal Azure - Gestor de recursos](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gestor de recursos](expressroute-howto-routing-arm.md)
[PowerShell - clássico](expressroute-howto-routing-classic.md)



Este artigo explica os passos para criar e gerir a configuração de encaminhamento para um circuito ExpressRoute utilizando o PowerShell e o modelo de implementação do Azure o Gestor de recursos.  Os passos abaixo irão também mostrar-lhe como verificar o estado, atualizar ou eliminar e deprovision peerings para um circuito ExpressRoute. 


**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Terá a versão mais recente do módulos Azure PowerShell, versão 1.0 ou posterior. 
- Certifique-se de que reviu a página de [Pré-requisitos](expressroute-prerequisites.md) , a página de [Encaminhamento de requisitos](expressroute-routing.md) e página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Tem de ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e ter o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute tem de ser num Estado aprovisionado e ativado para que possam executar os cmdlets descritos abaixo.

Estas instruções apenas se aplicam a circuitos criados com fornecedores de serviço, que oferece serviços de conectividade a camada 2. Se estiver a utilizar um fornecedor de serviços que oferece serviços de Layer 3 geridos (normalmente uma IPVPN, como MPLS), o seu fornecedor de conectividade configurar e gerir o encaminhamento para.

>[AZURE.IMPORTANT] Vamos atualmente não anunciam peerings configurados pelo fornecedores de serviço através do portal de gestão do serviço. Estamos a trabalhar sobre ativar esta funcionalidade mais rapidamente. Verifique se existem com o seu fornecedor de serviços antes de configurar BGP peerings.

Pode configurar um, dois ou todos os três peerings (público privado, Azure Azure e Microsoft) para um circuito ExpressRoute. Pode configurar peerings por qualquer ordem que escolher. No entanto, deve certificar-se de que concluir a configuração de cada um deles peering cada vez. 

## <a name="azure-private-peering"></a>Efectuado privado Azure

Esta secção fornece instruções sobre como criar, aceder, atualizar e eliminar a configuração peering privada Azure para um circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para criar efectuado privado Azure

1. Importe o módulo PowerShell para ExpressRoute.
    
    Tem de instalar o instalador do PowerShell mais recente da [Galeria de PowerShell](http://www.powershellgallery.com/) e importar os módulos Azure o Gestor de recursos para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos os módulos AzureRM.* no intervalo versão semântico conhecidos

        Import-AzureRM

    Pode importar também tem apenas um módulo selecione dentro do intervalo de versão semântico conhecidos 
        
        Import-Module AzureRM.Network 

    Início de sessão para a sua conta

        Login-AzureRmAccount

    Selecione a subscrição que pretende criar ExpressRoute circuito
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crie um circuito ExpressRoute.
    
    Siga as instruções para criar um [ExpressRoute circuito](expressroute-howto-circuit-arm.md) e ter aprovisionado pelo fornecedor de conectividade. 

    Se o seu fornecedor de conectividade oferecer serviços Layer 3 geridos, pode pedir o seu fornecedor de conectividade para ativar o Azure privado efectuado por si. Nesse caso, não é necessário seguir as instruções listadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, siga as instruções abaixo. 

3. Verifique o circuito ExpressRoute para garantir que está aprovisionado.

    Primeiro tem de verificar para ver o circuito ExpressRoute está aprovisionado e se também ativado. Veja o exemplo abaixo.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    A resposta será algo semelhante ao exemplo abaixo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Configure o Azure efectuado privado para o circuito.

    Certifique-se de que tem os seguintes itens antes de prosseguir com os passos seguintes:

    - Um /30 sub-rede para a ligação principal. Não tem de ser parte de qualquer espaço de endereços reservado para redes virtuais.
    - Um /30 sub-rede para a ligação secundária. Não tem de ser parte de qualquer espaço de endereços reservado para redes virtuais.
    - Um ID de VLAN válido para estabelecer este efectuado no. Certifique-se de que nenhuma efectuado no circuito utiliza o mesmo ID VLAN.
    - COMO número para efectuado. Pode utilizar 2 bytes com sinal e 4 bytes com sinal como números. Pode utilizar um privado como número para este efectuado. Certifique-se de que não está a utilizar 65515.
    - Um hash MD5 se optar por utilizar um. **Isto é opcional**.
    
    Pode executar o cmdlet seguinte para configurar o Azure efectuado privado para sua circuito.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Pode utilizar o cmdlet abaixo se optar por utilizar um hash MD5.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Certifique-se de que especifique o número como efectuado ASN, cliente ASN.

### <a name="to-view-azure-private-peering-details"></a>Para ver os detalhes Azure privados efectuados

Pode obter detalhes de configuração de utilizar o cmdlet seguinte

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>Para atualizar o Azure configuração privada efectuada

Pode atualizar qualquer parte da configuração utilizando o cmdlet seguinte. No exemplo abaixo, o ID da circuito está a ser actualizado a partir de 100 a 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Para eliminar efectuado privado Azure

Pode remover a sua configuração peering ao executar o cmdlet seguinte.

>[AZURE.WARNING] Tem de garantir que todas as redes virtuais estão não ligadas são iniciadas a partir do circuito ExpressRoute antes de executar este cmdlet. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Efectuado público Azure

Esta secção fornece instruções sobre como criar, aceder, atualizar e eliminar a configuração do peering pública Azure para um circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar efectuado público Azure

1. Importe o módulo PowerShell para ExpressRoute.
    
    Tem de instalar o instalador do PowerShell mais recente da [Galeria de PowerShell](http://www.powershellgallery.com/) e importar os módulos Azure o Gestor de recursos para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos os módulos AzureRM.* no intervalo versão semântico conhecidos

        Import-AzureRM

    Pode importar também tem apenas um módulo selecione dentro do intervalo de versão semântico conhecidos 
        
        Import-Module AzureRM.Network 

    Início de sessão para a sua conta

        Login-AzureRmAccount

    Selecione a subscrição que pretende criar ExpressRoute circuito
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crie um circuito ExpressRoute.
    
    Siga as instruções para criar um [ExpressRoute circuito](expressroute-howto-circuit-arm.md) e ter aprovisionado pelo fornecedor de conectividade. 

    Se o seu fornecedor de conectividade oferecer serviços Layer 3 geridos, pode pedir o seu fornecedor de conectividade para ativar o Azure público efectuado por si. Nesse caso, não é necessário seguir as instruções listadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, siga as instruções abaixo.

3. Verifique se circuito ExpressRoute para garantir que está aprovisionado.

    Primeiro tem de verificar para ver o circuito ExpressRoute está aprovisionado e se também ativado. Veja o exemplo abaixo.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    A resposta será algo semelhante ao exemplo abaixo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Configure o Azure efectuado público para o circuito.

    Certifique-se de que tem as seguintes informações antes de continuar ainda mais.

    - Um /30 sub-rede para a ligação principal. Tem de ser um prefixo IPv4 público válido.
    - Um /30 sub-rede para a ligação secundária. Tem de ser um prefixo IPv4 público válido.
    - Um ID de VLAN válido para estabelecer este efectuado no. Certifique-se de que nenhuma efectuado no circuito utiliza o mesmo ID VLAN.
    - COMO número para efectuado. Pode utilizar 2 bytes com sinal e 4 bytes com sinal como números.
    - Um hash MD5 se optar por utilizar um. **Isto é opcional**.
    
    Pode executar o cmdlet seguinte para configurar o Azure efectuado público para o seu circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Pode utilizar o cmdlet abaixo se optar por utilizar um hash MD5

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Certifique-se de que especificar o número como efectuado ASN e cliente ASN.

### <a name="to-view-azure-public-peering-details"></a>Para ver os detalhes efectuados de públicos Azure

Pode obter detalhes de configuração de utilizar o cmdlet seguinte

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Para atualizar o Azure configuração pública efectuada

Pode atualizar qualquer parte da configuração utilizando o cmdlet seguinte

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

ID da circuito está a ser actualizado de 200 para 600 no exemplo acima.

### <a name="to-delete-azure-public-peering"></a>Para eliminar efectuado público Azure

Pode remover a sua configuração peering ao executar o cmdlet seguinte

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft efectuado

Esta secção fornece instruções sobre como criar, aceder, atualizar e eliminar a configuração de peering da Microsoft para um circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para criar efectuado da Microsoft

1. Importe o módulo PowerShell para ExpressRoute.
    
    Tem de instalar o instalador do PowerShell mais recente da [Galeria de PowerShell](http://www.powershellgallery.com/) e importar os módulos Azure o Gestor de recursos para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos os módulos AzureRM.* no intervalo versão semântico conhecidos

        Import-AzureRM

    Pode importar também tem apenas um módulo selecione dentro do intervalo de versão semântico conhecidos 
        
        Import-Module AzureRM.Network 

    Início de sessão para a sua conta

        Login-AzureRmAccount

    Selecione a subscrição que pretende criar ExpressRoute circuito
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crie um circuito ExpressRoute.
    
    Siga as instruções para criar um [ExpressRoute circuito](expressroute-howto-circuit-arm.md) e ter aprovisionado pelo fornecedor de conectividade. 

    Se o seu fornecedor de conectividade oferecer serviços Layer 3 geridos, pode pedir o seu fornecedor de conectividade para ativar o Azure privado efectuado por si. Nesse caso, não é necessário seguir as instruções listadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, siga as instruções abaixo.

3. Verifique se circuito ExpressRoute para garantir que está aprovisionado.

    Primeiro tem de verificar para ver o circuito ExpressRoute está aprovisionado e se também ativado. Veja o exemplo abaixo.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    A resposta será algo semelhante ao exemplo abaixo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Configure o Microsoft efectuado para o circuito.

    Certifique-se de que tem as seguintes informações antes de continuar.

    - Um /30 sub-rede para a ligação principal. Tem de ser um público IPv4 prefixo válido propriedade por si e registada num RIR / TIR.
    - Um /30 sub-rede para a ligação secundária. Tem de ser um público IPv4 prefixo válido propriedade por si e registada num RIR / TIR.
    - Um ID de VLAN válido para estabelecer este efectuado no. Certifique-se de que nenhuma efectuado no circuito utiliza o mesmo ID VLAN.
    - COMO número para efectuado. Pode utilizar 2 bytes com sinal e 4 bytes com sinal como números.
    - Anunciada prefixos: tem de fornecer uma lista de todos os prefixos planear para anunciar sobre a sessão BGP. Apenas prefixos de endereço IP públicos são aceites. Pode enviar uma lista de separados por vírgulas se planeia enviar um conjunto de prefixos. Estes prefixos tem de estar registados para si num RIR / TIR.
    - Cliente ASN: Se forem prefixos de publicidade que não estão registados para efectuado como número, pode especificar o número de como a que estão registados. **Isto é opcional**.
    - Encaminhamento de registo de nome: Pode especificar o RIR / TIR contra a qual o número e prefixos são registados.
    - Hash MD5, se optar por utilizar um. **Isto é opcional.**
    
    Pode executar o cmdlet seguinte para configurar o Microsoft efectuado para sua circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>Para obter detalhes de efectuado da Microsoft

Pode obter detalhes de configuração de utilizar o cmdlet seguinte.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Para atualizar a configuração do Microsoft efectuada

Pode atualizar qualquer parte da configuração utilizando o cmdlet seguinte.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>Para eliminar efectuado da Microsoft

Pode remover a sua configuração peering ao executar o cmdlet seguinte.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Próximos passos

Próximo passo, [ligação um VNet para um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

-  Para mais informações sobre ExpressRoute fluxos de trabalho, consulte o artigo [ExpressRoute fluxos de trabalho](expressroute-workflows.md).

-  Para mais informações sobre circuito efectuado, consulte o artigo [ExpressRoute circuitos e encaminhamento de domínios](expressroute-circuit-peerings.md).

-  Para mais informações sobre como trabalhar com redes virtuais, consulte o artigo [Descrição geral de rede Virtual](../virtual-network/virtual-networks-overview.md).

