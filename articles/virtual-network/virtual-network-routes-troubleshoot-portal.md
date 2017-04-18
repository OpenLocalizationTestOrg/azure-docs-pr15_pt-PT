<properties 
   pageTitle="Resolver problemas de rotas - Portal | Microsoft Azure"
   description="Saiba como resolver problemas de rotas no modelo de implementação de Gestor de recursos do Azure utilizando o Portal do Azure."
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

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Resolver problemas de rotas utilizando o Portal do Azure

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

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**e, em seguida, clique em **máquinas virtuais** na lista que é apresentada.
3. Selecione uma VM para resolver a partir da lista que aparece e é apresentada uma pá VM com opções.
4. Clique em **diagnosticar & resolver problemas de** e, em seguida, selecione um problema comum. Neste exemplo, **não é possível ligo à minha VM Windows** está selecionada. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Passos aparecem no problema, conforme apresentado na seguinte imagem: 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Clique em *rotas eficazes* na lista de passos recomendados.

6. Aparece a pá **rotas eficazes** , conforme apresentado na seguinte imagem:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Se a sua VM tiver apenas uma imagem, está selecionada por predefinição. Se tiver mais do que uma imagem, selecione a imagem para o qual pretende ver as rotas eficazes.

    >[AZURE.NOTE] Se não estiver a VM associada a NIC num Estado de execução, rotas eficazes não serão apresentadas. As primeiro 200 rotas eficazes são apresentadas no portal. Para obter a lista completa, clique em **Transferir**. Pode filtrar ainda mais sobre os resultados do ficheiro. csv transferidos.

    Tenha em atenção o seguinte procedimento no resultado:
    - **Origem**: indica o tipo de encaminhar. Percursos do sistema são apresentados como *predefinido*, UDRs são apresentadas como rotas de *utilizador* e gateway (estático ou BGP) são apresentados como *VPNGateway*.
    - **Estado**: indica o estado da rota eficaz. Valores possíveis são *ativa* ou é *inválido*.
    - **AddressPrefixes**: Especifica o prefixo do endereço da rota eficaz na notação CIDR. 
    - **nextHopType**: indica o próximo salto para a rota determinado. Valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Nulo*. Um valor *Nulo* de para **nextHopType** num UDR pode indicar uma rota inválida. Por exemplo, se for **nextHopType** *VirtualAppliance* e a rede virtual aparelho VM não está num Estado aprovisionado/em execução. Se **nextHopType** é *VPNGateway* e não existe nenhum gateway aprovisionado/em execução na determinado VNet, a rota pode tornar-se inválidas.
    
7. Não existe nenhuma rota listada para o VNet *WestUS VNET3* (prefixo 10.10.0.0/16) a partir do *WestUS VNet1* (prefixo 10.9.0.0/16) na imagem no passo anterior. Na seguinte imagem, a ligação peering encontra no estado *desligado* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    A ligação bidirecional para o efectuado é quebrada, que explica por que motivo VM1 não foi possível ligar ao VM3 na VNet *WestUS VNet3* .

8. A imagem seguinte mostra as rotas após estabelecer a ligação peering bidirecional:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Para mais cenários de resolução de problemas de túnel forçada e avaliação de rota, consulte a secção [Considerações](virtual-network-routes-troubleshoot-portal.md#Considerations) deste artigo.

### <a name="view-effective-routes-for-a-network-interface"></a>Vista rotas eficazes para uma interface de rede

Se o fluxo de tráfego de rede é afectado para uma interface de rede em particular (NIC), pode ver uma lista completa de rotas efetivas numa NIC diretamente. Para ver as rotas agregação que são aplicadas a uma imagem, conclua os passos seguintes:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **interfaces de rede**
3. Procurar na lista para o nome de uma imagem ou selecione-a partir da lista que é apresentada. Neste exemplo, **VM1 NIC1** está selecionada.
4. Selecione **rotas eficazes** na pá **interface da rede** , conforme apresentado na seguinte imagem:
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    As predefinições do **âmbito** para a interface de rede selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Vista rotas eficazes para uma tabela de encaminhamento

Quando modificar rotas definidos pelo utilizador (UDRs) numa tabela rota, poderá querer rever o impacto das rotas a ser adicionado numa nomeadamente VM. Uma tabela de rota pode ser associada com qualquer número de sub-redes. Agora pode ver todas as rotas eficazes para todos os NIC que uma tabela de determinada rota é aplicada, sem ter de mudar o contexto da pá de tabela a rota determinado.

Neste exemplo, é especificado um UDR (*UDRoute*) numa tabela rota (*UDRouteTable*). Esta rota envia todo o tráfego da Internet do *Sub-rede1* no VNet *WestUS VNet1* , através de um aparelho virtual no *sub-rede2* da mesma VNet rede (NVA). A rota é apresentada na seguinte imagem:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Para ver as rotas agregação para uma tabela de rota, conclua os passos seguintes:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **encaminhar tabelas**
3. Procure na lista para a tabela de rota que pretende ver rotas agregação para e selecione-o. Neste exemplo, **UDRouteTable** está selecionada. Aparece uma pá para a tabela rota seleccionada, conforme apresentado na seguinte imagem:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Selecione **Rotas eficaz** pá a **tabela encaminhar** . O **âmbito** está definido para a tabela de rota que selecionar.
5. Uma tabela de rota pode ser aplicada a várias sub-redes. Selecione a **sub-rede** que pretende rever a partir da lista. Neste exemplo, **Sub-rede1** está selecionada.
6. Selecione uma **Interface de rede**. Todos os NIC ligado à sub-rede selecionada é listado. Neste exemplo, **VM1 NIC1** está selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Se o NIC não está associado uma VM em execução, sem rotas eficazes são apresentadas.

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
- VNet efectuado o tráfego funcionar corretamente, tem de existir uma rota de sistema com **nextHopType** *VNetPeering* para o intervalo de prefixo a VNet dispõe. Se não existe uma rota tal e a ligação peering VNet parece OK:
    - Aguarde alguns segundos e repetir se for uma ligação peering recentemente estabelecida. Ocasionalmente demora mais tempo a serem propagadas rotas para todas as interfaces de rede, numa sub-rede.
    - As regras de grupo de segurança de rede (NSG) podem ser que afetam os fluxos de tráfego. Para mais informações, consulte o artigo [Resolver problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md) .
