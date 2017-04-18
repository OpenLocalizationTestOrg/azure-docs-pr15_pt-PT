<properties 
   pageTitle="Como ligar clássicas redes virtuais ao Gestor de recursos redes virtuais através do PowerShell | Microsoft Azure"
   description="Saiba como criar uma ligação VPN entre clássico VNets e VNets Gestor de recursos utilizando VPN Gateway e PowerShell"
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
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Ligar redes virtuais a partir de modelos de implementação diferentes através do PowerShell

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure atualmente tem dois modelos de gestão: clássica e Gestor de recursos (RM). Se tiver utilizado o Azure há algum tempo, provavelmente tem Azure VMs e funções de instância a ser executada numa VNet clássica. O mais recentes VMs e instâncias de função poderão estar em execução um VNet criado no Gestor de recursos. Este artigo explica o ligar VNets clássica VNets Gestor de recursos para permitir que os recursos localizados nos modelos de implementação separada para comunicar com os outros através de uma ligação de gateway.

Pode criar uma ligação entre o VNets que estão na diferentes subscrições e em regiões diferentes. Também pode ligar VNets que já tenham ligações a redes no local, desde que o gateway que tenham sido configurados com é dinâmico ou com base em encaminhar. Para mais informações sobre ligações VNet para VNet, consulte as [FAQ VNet para VNet](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Modelos de implementação e métodos para ligar VNets em modelos de implementação diferente

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Estamos a atualizar a tabela seguinte como novos artigos e ferramentas adicionais disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir da tabela.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet efectuado

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Antes de começar

Os passos seguintes orientam as definições necessárias para configurar um gateway dinâmico ou com base em encaminhar para cada VNet e criar uma ligação VPN entre os gateways. Esta configuração não suporta estáticas ou baseada em políticas gateways.

### <a name="prerequisites"></a>Pré-requisitos

 - Ambas as VNets já foram criadas.
 - Os intervalos de endereços para o VNets não sobrepor-se entre si, ou se sobrepõem com qualquer um dos intervalos para outras ligações que os gateways poderão estar ligados ao.
 - Tiver instalado o mais recente cmdlets do PowerShell (1.0.2 ou posterior). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações. Certifique-se de que instale a gestão de serviço (s) e os cmdlets do Gestor de recursos (RM). 

### <a name="exampleref"></a>Definições de exemplo

Pode utilizar as definições de exemplo como uma referência.

**Definições de VNet clássicas**

Nome VNet = ClassicVNet <br>
Localização = US Ocidental <br>
Espaços de endereços de rede virtual = 10.0.0.0/24 <br>
1 de sub-rede = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome de rede local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Definições de VNet Gestor de recursos**

Nome VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereços IP de rede virtual = 192.168.0.0/16 <br>
1 de sub-rede = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = Leste dos EUA <br>
Nome do gateway público IP = gwpip <br>
Rede local Gateway = ClassicVNetLocal <br>
Nome do Gateway de rede virtual = RMGateway <br>
Configuração do gateway IP endereçamento = gwipconfig


## <a name="createsmgw"></a>Secção 1 - configurar a VNet clássico

### <a name="part-1---download-your-network-configuration-file"></a>Parte 1 - Transferir o seu ficheiro de configuração de rede

1. Inicie sessão na sua conta Azure na consola do PowerShell com direitos elevados. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta Azure. Depois de início de sessão, transfere as definições de conta para que fiquem disponíveis para Azure PowerShell. Utilizar os cmdlets do PowerShell s para concluir esta parte da configuração.

        Add-AzureAccount

2. Exporte o ficheiro de configuração de rede Azure executando o seguinte comando. Pode alterar a localização do ficheiro a exportar para uma localização diferente, se necessário. Será editar o ficheiro e, em seguida, importá-lo para Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Abra o ficheiro. XML que transferiu para editá-lo. Para um exemplo de ficheiro de configuração de rede, consulte o [Esquema de configuração de rede](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Parte 2 - Verifique a sub-rede do gateway

No elemento de **VirtualNetworkSites** , adicione uma sub-rede gateway à sua VNet se um já não foi criado. Quando trabalha com o ficheiro de configuração de rede, a sub-rede gateway deve ser denominada "GatewaySubnet" ou Azure não é possível reconhecer e utilizá-la como uma sub-rede de gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemplo:**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Parte 3 - adicionar o site de rede local

O site de rede local que adicionar representa o VNet RM ao qual pretende ligar. Poderá ter de adicionar um elemento **LocalNetworkSites** para o ficheiro se ainda não exista. Neste momento na configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido porque podemos ainda não tiver criado o gateway para VNet o Gestor de recursos. Assim que recomendamos criar um gateway, podemos substituir este endereço IP do marcador de posição com o endereço IP público correto que foi atribuído ao RM gateway.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>Parte 4 - associar a VNet com o site de rede local

Nesta secção, vamos especificar o local de rede local que pretende ligar VNet para. Neste caso, é o Gestor de recursos VNet que é referenciado anterior. Certifique-se os nomes corresponder. Este passo não cria um gateway. Especifica que o gateway irá ligar à rede local.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Parte 5 - guardar o ficheiro e carregar

Guardar o ficheiro e, em seguida, importá-lo para Azure executando o seguinte comando. Certifique-se de que altera o caminho do ficheiro conforme necessário para o seu ambiente.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Deverá ver algo semelhante a este resultado que mostra que a importação bem sucedido.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Parte 6 - criar um gateway 

Pode criar o gateway VNet utilizando o portal clássico ou ao utilizar o PowerShell.

Utilize o exemplo seguinte para criar um gateway encaminhamento dinâmico:

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

Pode verificar o estado do gateway utilizando o `Get-AzureVNetGateway` cmdlet.


## <a name="creatermgw"></a>Secção 2: Configurar o gateway RM VNet

Para criar um gateway VPN para a VNet RM, siga as instruções seguintes. Não pode começar os passos até depois de ter obtido o endereço IP público para o gateway a VNet clássica. 

1. **Inicie sessão na sua conta Azure** na consola do PowerShell. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta Azure. Depois de início de sessão, as definições de conta são transferidas para que fiquem disponíveis para Azure PowerShell.

        Login-AzureRmAccount 

    Obter uma lista das suas subscrições Azure se tiver mais do que uma subscrição.

        Get-AzureRmSubscription

    Especifique a subscrição que pretende utilizar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Criar um gateway de rede local**. Numa rede virtual, o gateway de rede local normalmente refere-se a sua localização no local. Neste caso, o gateway de rede local refere-se a sua VNet clássica. Atribua um nome pelo qual Azure pode referência ao mesmo bem como especificar o prefixo do espaço de endereços. Azure utiliza o prefixo do endereço IP que especificar para identificar qual o tráfego para enviar a sua localização no local. Se precisar de ajustar aqui as informações mais tarde, antes de criar o seu gateway, pode modificar os valores e execute novamente a amostra.<br><br>
    - `-Name`é o nome que pretende atribuir a referir-se para o gateway de rede local.<br>
    - `-AddressPrefix`o espaço de endereços destina-se a sua VNet clássica.<br>
    - `-GatewayIpAddress`é o endereço IP público do gateway a VNet clássica. Certifique-se de que alterar o exemplo seguinte para refletir o endereço IP correto.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Pedir um endereço IP público** a serem atribuídos para o gateway de rede virtual para VNet o Gestor de recursos. Não é possível especificar o endereço IP que pretende utilizar. O endereço IP dinamicamente que é atribuído para o gateway de rede virtual. No entanto, isso não significa que irá alterar o endereço IP. A única vez que as alterações de endereço IP de gateway de rede virtual é quando o gateway é eliminado e recriado. Não altera ao longo do redimensionamento, repor ou outras manutenção/actualizações internas do gateway.<br>Neste passo, recomendamos também definir uma variável que é utilizada num passo posterior.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Verificar que a sua rede virtual tem uma sub-rede de gateway**. Se não existe uma sub-rede gateway não existir, adicione um. Certifique-se de que sub-rede gateway se chamar *GatewaySubnet*.

5. **Obter a sub-rede** utilizada para o gateway, executando o seguinte comando. Neste passo, recomendamos também definir uma variável para ser utilizado no próximo passo.
    - `-Name`é o nome do seu VNet Gestor de recursos.
    - `-ResourceGroupName`é o grupo de recursos que está associada a VNet. A sub-rede gateway tem de existir para este VNet e deve ser denominada *GatewaySubnet* funcione corretamente.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Criar a configuração de endereçamento de IP de gateway**. A configuração de gateway define a sub-rede e o endereço IP público para utilizar. Utilize o exemplo seguinte para criar a sua configuração de gateway.<br>Neste passo, o `-SubnetId` e `-PublicIpAddressId` parâmetros têm de ser passados a propriedade id a partir da sub-rede e objetos do endereço IP, respetivamente. Não pode utilizar uma cadeia simple. Estas variáveis estão definidas no passo para pedir um endereço IP público e o passo para obter a sub-rede.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Criar o gateway de rede virtual do Gestor de recursos** , executando o seguinte comando. O `-VpnType` tem de ser *RouteBased*. Pode demorar 45 minutos ou mais para esta opção para concluir.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Copie o endereço IP público** uma vez gateway VPN foi criada. Utilizá-lo quando configurar as definições de rede local para o seu VNet clássica. Pode utilizar o cmdlet seguinte para obter o endereço IP público. O endereço IP público está listado no retorno como *endereço IP*.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Secção 3: Modificar a rede local para o VNet clássico

O que pode fazer este passo quer exportar o ficheiro de configuração de rede, editá-la e, em seguida, guardando e importar o ficheiro de volta para Azure. Em alternativa, pode modificar esta definição no portal do clássico. 

### <a name="to-modify-in-the-portal"></a>Para modificar no portal

1. Abra o [portal clássica](https://manage.windowsazure.com).

2. No portal do clássico, desloque para baixo no lado esquerdo e clique em **redes**. Na página de **redes** , clique em **Redes locais** na parte superior da página. 

3. Na página de **Redes locais** , clique para selecionar a rede local que é configurado na parte 1 e, em seguida, vai para o fim da página e clique em **Editar**.

4. Na página **especificar os detalhes do seu local de rede** , substitua o endereço IP do marcador de posição com o endereço IP público para o gateway de Gestor de recursos que criou na secção anterior. Clique na seta para mover para a secção seguinte. Verifique se o **Espaço de endereços** está correta e, em seguida, clique em marca de verificação para aceitar as alterações.

### <a name="to-modify-using-the-network-configuration-file"></a>Para modificar a utilizar o ficheiro de configuração de rede

1. Exporte o ficheiro de configuração de rede.
2. Num editor de texto, modifique o endereço IP de gateway VPN.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Guardar as suas alterações e importar o ficheiro editado regressar ao Azure.


## <a name="connect"></a>Secção 4: Criar uma ligação entre os gateways

Criar uma ligação entre os gateways requer PowerShell. Precisa de adicionar a sua conta Azure para utilizar os cmdlets do PowerShell clássicos. Para fazê-lo, pode utilizar o cmdlet seguinte: 
        
    Add-AzureAccount

1. **Definir a chave partilhada** , executando o seguinte comando. Neste exemplo, `-VNetName` é o nome da VNet clássica e `-LocalNetworkSiteName` é o nome que especificou para a rede local quando configurou no portal do clássico. O `-SharedKey` for um valor que pode gerar e especificar. O valor que especificar aqui tem de ser o mesmo valor que especificou no passo seguinte quando criar a sua ligação. Deve ser devolvido para que este exemplo **Estado: bem sucedida**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Crie a ligação VPN executando os seguintes comandos.

    **Definir as variáveis**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Criar a ligação**<br> Tenha em atenção que a `-ConnectionType` é IPsec, não Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Pode ver a ligação no portal de um dos ou ao utilizar o `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Perguntas mais frequentes do VNet para VNet

Ver os detalhes de perguntas mais frequentes para obter informações adicionais sobre VNet para VNet ligações.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


