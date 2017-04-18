<properties 
   pageTitle="Como ligar clássicas redes virtuais ao Gestor de recursos redes virtuais no portal do | Microsoft Azure"
   description="Saiba como criar uma ligação VPN entre clássico VNets e VNets Gestor de recursos utilizando o VPN Gateway e o portal"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Ligar redes virtuais a partir de modelos de implementação diferente no portal

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure atualmente tem dois modelos de gestão: clássica e Gestor de recursos (RM). Se tiver utilizado o Azure há algum tempo, provavelmente tem Azure VMs e funções de instância a ser executada numa VNet clássica. O mais recentes VMs e instâncias de função poderão estar em execução um VNet criado no Gestor de recursos. Este artigo explica o ligar VNets clássica VNets Gestor de recursos para permitir que os recursos localizados nos modelos de implementação separada para comunicar com os outros através de uma ligação de gateway. 

Pode criar uma ligação entre o VNets que estão na diferentes subscrições e em regiões diferentes. Também pode ligar VNets que já tenham ligações a redes no local, desde que o gateway que tenham sido configurados com é dinâmico ou com base em encaminhar. Para mais informações sobre ligações VNet para VNet, consulte as [FAQ VNet para VNet](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementação e métodos para VNet para VNet ligações

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Estamos a atualizar a tabela seguinte como novos artigos e ferramentas adicionais disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir da tabela.<br><br>

**VNet para VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet efectuado

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Antes de começar

Os passos seguintes orientam as definições necessárias para configurar um gateway dinâmico ou com base em encaminhar para cada VNet e criar uma ligação VPN entre os gateways. Esta configuração não suporta estáticas ou baseada em políticas gateways. 

Neste artigo, utilizamos portal clássico, Azure portal e PowerShell. Atualmente, não é possível criar esta configuração utilizando apenas o portal Azure.

### <a name="prerequisites"></a>Pré-requisitos

 - Ambas as VNets já foram criadas.
 - Os intervalos de endereços para o VNets não sobrepor-se entre si, ou se sobrepõem com qualquer um dos intervalos para outras ligações que os gateways poderão estar ligados ao.
 - Tiver instalado o mais recente cmdlets do PowerShell (1.0.2 ou posterior). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações. Certifique-se de que instale a gestão de serviço (s) e os cmdlets do Gestor de recursos (RM). 

### <a name="values"></a>Definições de exemplo

Pode utilizar as definições de exemplo como referência.

**Definições de VNet clássicas**

Nome VNet = ClassicVNet <br>
Localização = US Ocidental <br>
Espaços de endereços de rede virtual = 10.0.0.0/24 <br>
1 de sub-rede = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome de rede local = RMVNetLocal <br>

**Definições de VNet Gestor de recursos**

Nome VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereços IP de rede virtual = 192.168.0.0/16 <br>
1 de sub-rede = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = Leste dos EUA <br>
Nome do gateway rede virtual = RMGateway <br>
Nome do gateway público IP = gwpip <br>
Tipo de gateway = VPN <br>
Tipo de VPN = baseado em encaminhar <br>
O gateway de rede local = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Secção 1: Configurar definições de VNet clássicas

Nesta secção, criamos à rede local e o gateway para a sua VNet clássica. As instruções nesta secção utilizam o portal do clássico. Atualmente, o portal do Azure não oferecer todas as definições que pertencem a um VNet clássica.

### <a name="part-1---create-a-new-local-network"></a>Parte 1 - criar uma nova rede local

Abra o [portal clássica](https://manage.windowsazure.com) e inicie sessão com a sua conta Azure.

1. No canto inferior esquerdo do ecrã, clique em **Novo** > **Serviços de rede** > **Rede Virtual** > **Adicionar de rede local**.

2. Na janela de **especificar os detalhes do seu local de rede** , escreva um nome para o VNet RM que pretende ligar. Na caixa **endereço IP de dispositivo VPN (opcional)** , escreva qualquer endereço IP público válido. Este é apenas um marcador de posição temporário. Alterar este endereço IP mais tarde. No canto inferior direito da janela, clique no botão de seta.
 
3. Na página **especificar o espaço de endereços** , na caixa de texto **IP inicial** , escreva o prefixo de rede e bloquear CIDR para VNet o Gestor de recursos que pretende ligar. Esta definição é utilizada para especificar o espaço de endereços para encaminhar para o VNet RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Parte 2 - associar à rede local para o seu VNet

1. Clique em **Redes virtuais** na parte superior da página para mudar para ecrã redes virtuais e, em seguida, clique para selecionar o seu VNet clássica. Na página do seu VNet, clique em **Configurar** para navegar para a página de configuração.

2. Na secção de ligação de **conectividade de site para o site** , selecione a caixa de verificação **ligar à rede local** . Em seguida, selecione a rede local que criou. Se tiver múltiplas redes locais que criou, certifique-se de que selecione aquele que criou para representar VNet seu Gestor de recursos a partir da lista pendente.

3. Clique em **Guardar** na parte inferior da página.

### <a name="part-3---create-the-gateway"></a>Parte 3 - criar um gateway

1. Depois de guardar as definições, clique em **Dashboard** na parte superior da página para alterar para a página do Dashboard. Na parte inferior da página do Dashboard, clique em **Criar Gateway**, em seguida, clique em **Encaminhamento dinâmico**. Clique em **Sim** para começar a criar o gateway. Um gateway encaminhamento dinâmico é necessário para esta configuração.

2. Aguarde que o gateway para ser criado. Por vezes, pode demorar 45 minutos ou mais para concluir.

### <a name="ip"></a>Parte 4 - ver o endereço IP público do gateway

Depois do gateway foi criado, pode ver o endereço IP do gateway na página do **Dashboard** . Este é o endereço IP público do seu gateway. Escreva ou copie o endereço IP público. Utilizar passos posteriores ao criar a rede local para a sua configuração VNet Gestor de recursos.


## <a name="creatermgw"></a>Secção 2: Configurar as definições de VNet Gestor de recursos

Nesta secção, vamos criar o gateway de rede virtual e de rede local para VNet seu Gestor de recursos. Não pode começar os seguintes passos até depois de ter obtido o endereço IP público para o gateway a VNet clássica.

As capturas de ecrã são fornecidas como exemplos. Certifique-se de que substitua os valores com o seu próprio. Se estiver a criar esta configuração como um exercício, consulte estes [valores](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Parte 1 - criar uma sub-rede do gateway

Antes de ligar à rede virtual para um gateway, primeiro tem de criar a sub-rede de gateway para a rede virtual ao qual pretende ligar. Criar uma sub-rede de gateway com contagem CIDR de /28 polegadas ou superior (/ 27, / 26, etc.)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

A partir de um browser, navegue para o [portal do Azure](http://portal.azure.com) e inicie sessão com a sua conta Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2 - criar um gateway de rede virtual


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Parte 3 - criar um gateway de rede local

O gateway' rede local' refere-se normalmente a sua localização no local. Indica Azure endereço IP que intervalos a encaminhar para a localização e o endereço IP público do dispositivo para essa localização. No entanto, neste caso,-refere-se para o intervalo de endereço e o endereço IP público associada ao seu VNet clássica e gateway de rede virtual.

Dê um nome pelo qual, Azure pode referir-se para a mesma ao gateway de rede local. Pode criar o gateway de rede local enquanto está a ser criado o gateway de rede virtual. Para esta configuração, utilize o endereço IP público que foi atribuído ao seu gateway VNet clássico na [secção anterior](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>Parte 4 - copie o endereço IP público

Assim que o gateway de rede virtual concluiu a criação, copie o endereço IP público associado o gateway. Utilizá-lo quando configurar as definições de rede local para o seu VNet clássica. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Secção 3: Modificar a rede local para o VNet clássico

Abra o [portal clássica](https://manage.windowsazure.com).

2. No portal do clássico, desloque para baixo no lado esquerdo e clique em **redes**. Na página de **redes** , clique em **Redes locais** na parte superior da página. 

3. Clique para selecionar a rede local que configurou na parte 1. Na parte inferior da página, clique em **Editar**.

4. Na página **especificar os detalhes do seu local de rede** , substitua o endereço IP do marcador de posição com o endereço IP público para o gateway de Gestor de recursos que criou na secção anterior. Clique na seta para mover para a secção seguinte. Verifique se o **Espaço de endereços** está correta e, em seguida, clique em marca de verificação para aceitar as alterações.

## <a name="connect"></a>Secção 4: Criar a ligação

Nesta secção, vamos criar a ligação entre o VNets. Os passos para este requerem PowerShell. Não é possível criar esta ligação em qualquer um dos portais. Certifique-se de que transferiu e instalou o clássico (s) e os cmdlets do PowerShell do Gestor de recursos (RM).


1. Inicie sessão na sua conta Azure na consola do PowerShell. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta Azure. Depois de início de sessão, as definições de conta são transferidas para que fiquem disponíveis para Azure PowerShell.

        Login-AzureRmAccount 

    Obter uma lista das suas subscrições Azure se tiver mais do que uma subscrição.

        Get-AzureRmSubscription

    Especifique a subscrição que pretende utilizar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Adicione a sua conta do Azure para utilizar os cmdlets do PowerShell clássicos. Para fazê-lo, pode utilizar o seguinte comando:

        Add-AzureAccount

3. Defina a chave partilhada, executando o seguinte exemplo. Neste exemplo, `-VNetName` é o nome da VNet clássica e `-LocalNetworkSiteName` é o nome que especificou para a rede local quando configurou no portal do clássico. O `-SharedKey` for um valor que pode gerar e especificar. O valor que especificar aqui tem de ser o mesmo valor que especificou no passo seguinte quando criar a sua ligação.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Crie a ligação VPN executando os seguintes comandos:
    
    **Definir as variáveis**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Criar a ligação**<br> Tenha em atenção que a `-ConnectionType` é 'IPsec', não 'Vnet2Vnet'. Neste exemplo, `-Name` é o nome que pretende ligar a sua ligação. O exemplo seguinte cria uma ligação com o nome '*rm para clássico ligação*'.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Verifique se a sua ligação

Pode verificar a sua ligação utilizando o portal clássico, para o Azure portal ou PowerShell. Pode utilizar os seguintes passos para confirmar a sua ligação. Substitua os valores com o seu próprio.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>Perguntas mais frequentes do VNet para VNet

Ver os detalhes de perguntas mais frequentes para obter informações adicionais sobre VNet para VNet ligações.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


