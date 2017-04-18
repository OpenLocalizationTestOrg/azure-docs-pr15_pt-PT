<properties 
   pageTitle="Configurar túnel forçada para ligações de Site para o Site utilizando o modelo de implementação clássica | Microsoft Azure"
   description="Como redirecionar ou 'forçar' todo o tráfego de Internet vinculadas voltar a sua localização no local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar a utilizar o modelo de implementação clássico de túnel forçada

> [AZURE.SELECTOR]
- [PowerShell - clássico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestor de recursos](vpn-gateway-forced-tunneling-rm.md)

Túnel forçada permite-lhe redirecionar ou "forçar" todo o tráfego de Internet vinculadas novamente para sua localização no local através de um túnel de Site para o Site VPN para auditoria e controlo. Isto é um requisito de segurança crítica para a maioria dos enterprise IT políticas. 

Sem túnel forçada, o tráfego de Internet vinculadas a partir do seu VMs no Azure sempre irá percorrer à Internet, sem a opção para permitir que inspecionar ou o tráfego de auditoria de infraestrutura de rede Azure diretamente saída. Acesso à Internet não autorizado potencialmente pode conduzir a divulgação de informações ou outros tipos de quebras de segurança.

Este artigo irá guiá-lo através da configuração forçada túnel para redes virtuais criados utilizando o modelo de implementação clássica. 

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implementação e ferramentas para túnel forçada**

Uma ligação de túnel forçada pode ser configurada para o modelo clássico de implementação e o modelo de implementação do Gestor de recursos. Consulte a tabela seguinte para obter mais informações. Esta tabela poderemos actualizar como novos artigos, modelos de implementação novos e ferramentas adicionais de ficam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir da tabela.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Requisitos e considerações

Túnel forçada no Azure está configurado através do rotas definidas pelo utilizador de rede virtual (UDR). Redirecionar o tráfego para um site no local é expresso como uma rota predefinida para o gateway Azure VPN. A secção seguinte lista a limitação atual da tabela e rotas encaminhamento para uma rede Virtual Azure:


-  Cada sub-rede rede virtual tem uma tabela de encaminhamento incorporado, do sistema. A tabela de encaminhamento de sistema tem os seguintes três grupos de rotas:

    - **Local VNet encaminha:** Diretamente para o destino VMs na mesma rede virtual
    
    - **Em rotas local:** Para o gateway VPN do Azure
    
    - **Rota predefinida:** Diretamente à Internet. Pacotes destinados a endereços IP privados não abrangidos pelas anterior duas rotas serão ignorados.


-  Com a versão do rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento ao seu subnet(s) VNet para ativar túnel forçada nesses sub-redes.

- Tem de predefinir um "site" entre os sites local da publicação em local ligado à rede virtual.

- Tem de ser associada a um VNet que tem um gateway VPN encaminhamento (não um estático gateway) dinâmico túnel forçada.
 
- ExpressRoute forçada túnel não está configurado através deste mecanismo, mas em vez disso, é activado por publicidade uma rota predefinida através das sessões peering ExpressRoute BGP. Consulte a [Documentação de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.



## <a name="configuration-overview"></a>Descrição geral de configuração

No exemplo seguinte, túnel Frontend sub-rede não for forçada. As cargas de trabalho na sub-rede Frontend podem continuar a aceitar e responder a pedidos de cliente da Internet diretamente. As sub-redes camada meados e back-end são forçadas túnel. Quaisquer ligações de saída destas duas sub-redes à Internet vão ser forçadas ou redireccionadas para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar acesso à Internet a partir do seu máquinas virtuais ou cloud services no Azure, enquanto continua ativar a sua arquitetura de várias camadas serviço necessária. Também pode aplicar túnel forçada para as redes virtuais inteiras se não existirem das cargas de trabalho sem acesso à Internet no redes virtuais.


![Forçada túnel](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem os seguintes itens antes de iniciar configuração.

- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Uma rede virtual configurada. 

- A versão mais recente dos cmdlets do Azure PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="configure-forced-tunneling"></a>Configurar túnel forçada

O procedimento seguinte irá ajudá-lo especificar túnel forçada para uma rede virtual. Os passos de configuração correspondem ao ficheiro de configuração de rede VNet.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

Neste exemplo, a rede virtual "MultiTier VNet" tem três sub-redes: sub-redes *front-end*, *Midtier*e *back-end* , com ligações de quatro local cruzada: *DefaultSiteHQ*e três *ramificações*. 

Os passos predefinir a *DefaultSiteHQ* a ligação de site predefinida para forçada túnel e configurar o Midtier e sub-redes back-end a utilizar forçada túnel.


1. Crie uma tabela de encaminhamento. Utilize o cmdlet seguinte para criar a sua tabela encaminhar.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Adicione uma rota predefinida à tabela de encaminhamento. 

    O exemplo seguinte adiciona uma rota predefinida à tabela de encaminhamento que criou no passo 1. Tenha em atenção que a rota apenas suportada é o prefixo de destino de "0.0.0.0/0" para o salto "VPNGateway" seguinte.
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Associe a tabela de encaminhamento para as sub-redes. 

    Depois de uma tabela de encaminhamento é criada e adicionada uma rota, utilize o exemplo seguinte para adicionar ou associar a tabela encaminhar para uma sub-rede VNet. O exemplo adiciona a tabela de rota "MyRouteTable" para as sub-redes de VNet MultiTier-VNet Midtier e back-end.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Atribua um site predefinido para forçada túnel. 

    No passo anterior, os scripts de exemplo do cmdlet criado a tabela de encaminhamento em associados a tabela rota duas das sub-redes VNet. O restante passo é selecionar um site local entre as ligações de múltiplos sites da rede virtual como o site predefinido ou túnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Cmdlets do PowerShell adicionais

### <a name="to-delete-a-route-table"></a>Para eliminar uma tabela de encaminhamento

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>À lista de uma tabela de encaminhamento

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Para eliminar uma rota de uma tabela de encaminhamento

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota a partir de uma sub-rede

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>A tabela rota associada uma sub-rede de lista
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site predefinido de um gateway VNet VPN

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






