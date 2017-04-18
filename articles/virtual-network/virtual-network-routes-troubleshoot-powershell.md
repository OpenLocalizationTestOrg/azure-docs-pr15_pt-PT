<properties 
   pageTitle="Resolver problemas de rotas - PowerShell | Microsoft Azure"
   description="Saiba como resolver problemas de rotas no modelo de implementação de Gestor de recursos do Azure através do Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-azure-powershell"></a>Resolver problemas de rotas através do PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Se ocorrerem problemas de conectividade de rede para ou a partir do seu Máquina Virtual Azure (VM), rotas podem ser que afetam os fluxos de tráfego VM. Este artigo fornece uma descrição geral das capacidades de diagnóstico para rotas ajudar a resolver ainda mais.

Encaminhar tabelas estão associadas a sub-redes e são eficazes em todas as interfaces rede (NIC) nessa sub-rede. Os seguintes tipos de rotas podem ser aplicados a cada interface de rede:

- **Rotas de sistema:** Por predefinição, todas as sub-rede criado numa rede Virtual Azure (VNet) tem de tabelas de rota de sistema que permitem tráfego VNet local, o tráfego através de VPN gateways no local e o tráfego da Internet. Também existem rotas de sistema para VNets dispõe.
- **BGP rotas:** Propagadas para interfaces de rede através de ExpressRoute ou ligações de VPN do site para o site. Saiba mais sobre o encaminhamento de BGP lendo os artigos [BGP com VPN gateways](../vpn-gateway/vpn-gateway-bgp-overview.md) e [Descrição geral de ExpressRoute](../expressroute/expressroute-introduction.md) .
- **Rotas definidos pelo utilizador (UDR):** Se estiver a utilizar, eletrodomésticos virtual rede ou que são túnel forçada tráfego para uma rede no local através de VPN site para o site, poderá ter definido pelo utilizador rotas (UDRs) associadas com a tabela de rota de sub-rede. Se não estiver familiarizado com UDRs, leia o artigo [rotas definidos pelo utilizador](virtual-networks-udr-overview.md#user-defined-routes) .

Com as rotas vários que podem ser aplicadas a uma interface de rede, pode ser difícil determinar quais as rotas agregação são eficazes. Para ajudar a resolver problemas de conectividade de rede VM, pode ver todas as rotas eficazes para uma interface de rede no modelo de implementação de Gestor de recursos do Azure.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Utilizar rotas eficaz para resolver problemas de fluxo de tráfego VM

Este artigo utiliza o cenário seguinte de exemplo para ilustrar como resolver problemas de rotas eficazes para uma interface de rede:

Uma VM (*VM1*) ligado a VNet (*VNet1*, o prefixo: 10.9.0.0/16) falha ligar a um VM(VM3) num VNet recentemente dispõe (*VNet3*, prefixo 10.10.0.0/16). Existem sem UDRs ou BGP encaminha aplicados a interface de rede de VM1 NIC1 ligada para a VM, são aplicadas apenas as rotas do sistema.

Este artigo explica como determinar a causa a falha na ligação, com capacidade de rotas eficaz no modelo de implementação do Azure a gestão de recursos.
Enquanto o exemplo que utiliza apenas as rotas do sistema, os mesmos passos podem ser utilizados para determinar falhas de ligação de entrada e saída qualquer tipo de encaminhar.

>[AZURE.NOTE] Se a VM tiver mais do que um NIC anexado, selecione rotas eficazes para cada um dos NIC para diagnosticar problemas de conectividade de rede de e para a uma VM.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Vista rotas eficazes para uma máquina virtual

Para ver as rotas agregação que são aplicadas a uma VM, conclua os passos seguintes:

### <a name="view-effective-routes-for-a-network-interface"></a>Vista rotas eficazes para uma interface de rede

Para ver as rotas agregação que são aplicadas a uma interface de rede, conclua os passos seguintes:

1.  Inicie uma sessão do PowerShell do Azure e iniciar sessão no Azure. Se não estiver familiarizado com o Azure PowerShell, leia o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .

2.  O seguinte comando devolve todas as rotas aplicadas a uma interface de rede com o nome *VM1 NIC1* no grupo de recursos *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Se não souber o nome de uma interface de rede, escreva o seguinte comando para obter os nomes de todas as interfaces de rede no group.* recurso

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    O seguinte resultado é semelhante a exportação para cada rota aplicada à sub-rede que NIC está ligada ao:

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Tenha em atenção o seguinte procedimento no resultado:
    - **Nome**: nome da rota eficaz poderá estar vazio, a menos que explicitamente especificada, para rotas definidos pelo utilizador. 
    - **Estado**: indica o estado da rota eficaz. Os valores possíveis são "Activo" ou "Inválido"
    - **AddressPrefixes**: Especifica o prefixo do endereço da rota eficaz na notação CIDR. 
    - **nextHopType**: indica o próximo salto para a rota determinado. Valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Nulo*. Um valor *Nulo* de para **nextHopType** num UDR pode indicar uma rota inválida. Por exemplo, se for **nextHopType** *VirtualAppliance* e a rede virtual aparelho VM não está num Estado aprovisionado/em execução. Se **nextHopType** é *VPNGateway* e não existe nenhum gateway aprovisionado/em execução na determinado VNet, a rota pode tornar-se inválidas.
    - **NextHopIpAddress**: Especifica o endereço IP do próximo salto da rota eficaz.
    
    O comando seguinte devolve as rotas uma mais fácil de ver tabela:

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    O seguinte resultado é algumas da saída recebida para o cenário descrito anteriormente:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. Não existe nenhuma rota listada para o *WestUS VNet3* VNet (prefixo 10.10.0.0/16)* * a partir do *WestUS VNet1* (prefixo 10.9.0.0/16) no resultado do passo anterior. Como é mostrado na seguinte imagem, a ligação peering VNet com o VNet *WestUS VNet3* encontra no estado *desligado* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    A ligação bidirecional para o efectuado é quebrada, que explica por que motivo VM1 não foi possível ligar ao VM3 na VNet *WestUS VNet3* . Configurar uma ligação de peering VNet bidirecional novamente para *WestUS VNet1* e *WestUS VNet3* VNets. O resultado devolvido após a ligação peering VNet corretamente é estabelecida segue:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Assim que determinar o problema, pode adicionar, remover, ou alterar rotas e encaminhar tabelas. Escreva o seguinte comando para ver uma lista dos comandos utilizados para fazê-lo:

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Considerações sobre

Algumas coisas a ter em conta durante a revisão à lista de rotas devolvido:

- Encaminhamento baseia-se na célula mais longa do prefixo CORRESP (LPM) entre UDRs, rotas BGP e de sistema. Se existir mais do que uma rota com a mesma correspondência LPM, em seguida, uma rota está selecionada com base na sua origem pela seguinte ordem:
    - Encaminhar definidos pelo utilizador
    - Encaminhar BGP
    - Rota do sistema (predefinição)

    Com rotas eficazes, só consegue ver rotas eficazes que são LPM correspondência com base em todas as rotas disponível em. Por que mostra como as rotas realmente são avaliadas para uma determinado NIC, isto torna muitas mais fácil de resolução de problemas rotas específicas que podem ser que afetam conectividade para a sua VM.

- Se tiver UDRs e estiver a enviar tráfego para um aparelho virtual com *VirtualAppliance* como **nextHopType**rede (NVA), certifique-se de que reencaminhamento IP está ativado no NVA receber o tráfego ou pacotes desaparecem. 
- Se forçado túnel estiver ativada, todo o tráfego de saída da Internet será encaminhado para no local. RDP/SSH a partir da Internet para o seu VM poderá não funcionar com esta definição, dependendo de como no local trata este tráfego. 
  Pode ser activada túnel forçada:
    - Se utilizar o site para o site VPN, definindo uma rota definidos pelo utilizador (UDR) com nextHopType como VPN Gateway
    - Se uma rota predefinida é anunciada sobre BGP
- VNet efectuado o tráfego funcionar corretamente, tem de existir uma rota de sistema com **nextHopType** *VNetPeering* para o intervalo de prefixo a VNet dispõe. Se não existe uma rota essa e a ligação peering VNet parece OK:
    - Aguarde alguns segundos e repetir se for uma ligação peering recentemente estabelecida. Ocasionalmente demora mais tempo a serem propagadas rotas para todas as interfaces de rede, numa sub-rede.
    - As regras de grupo de segurança de rede (NSG) podem ser que afetam os fluxos de tráfego. Para mais informações, consulte o artigo [Resolver problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-powershell.md) .
