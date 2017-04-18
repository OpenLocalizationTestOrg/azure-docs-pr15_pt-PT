<properties 
   pageTitle="Ligar uma rede virtual a múltiplos sites utilizando o VPN Gateway e PowerShell | Microsoft Azure"
   description="Este artigo irá guiá-lo através de vários sites local no local a ligar a uma rede virtual utilizando um Gateway de VPN para o modelo de implementação clássico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma ligação de Site para o Site a uma VNet com uma ligação de gateway VPN existente

> [AZURE.SELECTOR]
- [Gestor de recursos - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Clássico - PowerShell](vpn-gateway-multi-site.md)

Este artigo explica o utilizar o PowerShell para adicionar ligações do Site para Site (S2S) para um gateway VPN que tem uma ligação existente. Este tipo de ligação é muitas vezes como uma configuração "com várias site". 

Este artigo aplica-se ao redes virtuais criados utilizando o modelo de implementação clássico (também conhecido como o serviço de gestão). Estes passos não são aplicadas às configurações de ligação coexistentes ExpressRoute/Site-para-Site. Consulte o artigo [ExpressRoute/S2S coexistentes ligações](../expressroute/expressroute-howto-coexist-classic.md) para informações sobre ligações coexistentes.

### <a name="deployment-models-and-methods"></a>Modelos de implementação e métodos de

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Vamos atualizar esta tabela como novos artigos e ferramentas adicionais disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>Acerca da ligação

Pode ligar a vários sites no local para uma única rede virtual. Este é especialmente apelativo para construção de soluções de nuvem híbrido. Criar uma ligação de múltiplos site ao seu gateway Azure rede virtual é muito semelhante a criação de outras ligações de Site para o Site. Na verdade, pode utilizar um gateway Azure VPN existente, desde que o gateway está dinâmico (baseado em encaminhar).

Se já tiver um estático gateway ligado à rede virtual, pode alterar o tipo de gateway para dinâmico sem ser necessário especificá-las reconstruir a rede virtual para acomodar múltiplos sites. Antes de alterar o tipo de encaminhamento, certifique-se de que o gateway no local da VPN suporta configurações de VPN com base em encaminhar. 

![diagrama de vários sites] (./media/vpn-gateway-multi-site/multisite.png "múltiplos sites")

## <a name="points-to-consider"></a>Pontos a considerar

**Não poderá utilizar o Portal de clássica do Azure para efetuar alterações a esta rede virtual.** Nesta versão, terá de efetuar alterações ao ficheiro de configuração de rede em vez de utilizar o Portal do Azure clássica. Se efetuar alterações no Portal clássica do Azure, são irá substituir as definições de site com várias referência para esta rede virtual. 

Sentir-se deve muito à vontade com a utilizar o ficheiro de configuração de rede por hora que concluiu o procedimento de múltiplos site. No entanto, se tiver várias pessoas a trabalhar na sua configuração de rede, terá de Certifique-se de que todos saibam sobre esta limitação. Isto não significa que não é possível utilizar o Portal do Azure clássico de todo. Pode utilizá-lo para tudo o resto, exceto efetuar alterações à configuração para esta rede virtual específico.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, certifique-se de que tem o seguinte procedimento:

- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Compatível com hardware VPN para cada uma localização no local. Verifique a [Sobre dispositivos de VPN para a conectividade da rede Virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que pretende utilizar está algo que costuma ser compatíveis.

- Um externamente oposto IPv4 endereço IP público para cada dispositivo VPN. O endereço IP não pode ser localizado atrás de um NAT. Este é o requisito de registo.

- Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

- Alguém que esteja proficientes configuração do hardware VPN. Não será possível utilizar os scripts VPN gerados automaticamente a partir do Portal de clássica Azure para configurar os dispositivos de VPN. Isto significa que terá de ter uma forte compreensão de como configurar o seu dispositivo VPN ou se trabalha com alguém que é que.

- Intervalos de endereços IP que pretende utilizar para a sua rede virtual (se ainda não criou uma já). 

- Os intervalos de endereços IP para cada um dos sites de rede local que vai ser a ligar. Irá precisar para se certificar de que o endereço IP intervalos para cada um dos sites de rede local que pretende ligar ao não se sobrepõem. Caso contrário, o Portal do Azure clássico ou REST API irá rejeitar a configuração a ser carregada. 

    Por exemplo, se tiver dois locais de rede local que contêm o IP endereço intervalo 10.2.3.0/24 e tem um pacote com um endereço de destino 10.2.3.3, Azure não seria saber que site que pretende enviar o pacote porque os intervalos de endereços são sobrepostos. Para evitar problemas de encaminhamento, Azure não lhe permite carregar um ficheiro de configuração que tem intervalos sobrepostos.



## <a name="1-create-a-site-to-site-vpn"></a>1. Crie uma VPN do Site para o Site

Se já tiver uma VPN do Site para o Site com um gateway encaminhamento dinâmico, grandes! Pode prosseguir para [exportar as definições de configuração de rede virtual](#export). Caso contrário, efetue o seguinte procedimento:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se já tiver uma rede virtual do Site para o Site, mas tem um estático gateway encaminhamento (baseado em política):

1. Altere o tipo de gateway para o encaminhamento dinâmico. Está numa VPN múltiplos site requer um gateway de encaminhamento dinâmico (também conhecido como encaminhar baseado). Para alterar o tipo de gateway, terá primeiro eliminar o gateway existente, em seguida, crie um novo. Para obter instruções, consulte o artigo [como alterar o tipo de encaminhamento de VPN para o gateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Configurar o seu novo gateway e crie a sua túnel VPN. Para obter instruções, consulte o artigo [configurar um Gateway VPN no Portal clássica do Azure](vpn-gateway-configure-vpn-gateway-mp.md). Em primeiro lugar, altere o tipo de gateway para o encaminhamento dinâmico. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se não tiver uma rede virtual do Site para o Site:

1. Criar a sua rede virtual do Site para o Site utilizando estas instruções: [criar uma rede Virtual com uma ligação VPN do Site para o Site no Portal clássica do Azure](vpn-gateway-site-to-site-create.md).  

2. Configurar um gateway encaminhamento dinâmico utilizando estas instruções: [configurar um Gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md). Certifique-se de que selecione **encaminhamento dinâmico** para o seu tipo de gateway.

## <a name="export"></a>2. exportar o ficheiro de configuração de rede 

Exporte ficheiro de configuração de rede. O ficheiro que é exportar será utilizado para configurar as definições de sites com várias novas. Se precisar de instruções sobre como exportar um ficheiro, consulte a secção no artigo: [como criar um VNet com um ficheiro de configuração de rede no Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. abrir o ficheiro de configuração de rede

Abra o ficheiro de configuração de rede que transferiu no último passo. Utilize qualquer editor de xml que pretende. O ficheiro deverá ter um aspeto semelhante ao seguinte:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. adicionar várias referências de site

Quando adicionar ou remover as informações de referência do site, irá efetuar alterações à configuração para ConnectionsToLocalNetwork/LocalNetworkSiteRef. Adicionar um novo accionadores de referência do local site Azure para criar um novo túnel. No exemplo abaixo, a configuração de rede é de uma ligação de único site. Guarde o ficheiro depois de terminar de selecionar as suas alterações.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importar o ficheiro de configuração de rede

Importe o ficheiro de configuração de rede. Quando importar este ficheiro com as alterações, os novos túneis serão adicionados. Os túneis irão utilizar o gateway dinâmico que criou anteriormente. Se precisar de instruções sobre como importar o ficheiro, consulte a secção no artigo: [como criar um VNet com um ficheiro de configuração de rede no Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. Transferir teclas

Depois de terem sido adicionados a sua novos túneis, utilize o cmdlet do PowerShell `Get-AzureVNetGatewayKey` para obter as teclas de pré-partilhadas IPsec/IKE para cada túnel.

Por exemplo:

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Se preferir, também pode utilizar a API do resto *Obter Virtual rede partilhadas chave de Gateway* para obter as teclas de pré-partilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique as ligações

Verifique o estado de túnel múltiplos sites. Depois de transferir as teclas para cada túnel, irá querer verificar ligações. Utilizar `Get-AzureVnetConnection` para obter uma lista de túneis de rede virtual, conforme mostrado no exemplo abaixo. VNet1 é o nome da VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre os Gateways de VPN, consulte o artigo [Sobre VPN Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md).

