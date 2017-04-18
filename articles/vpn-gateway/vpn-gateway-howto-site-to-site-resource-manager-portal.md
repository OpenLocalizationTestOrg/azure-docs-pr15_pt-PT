<properties
   pageTitle="Criar uma rede virtual com uma ligação VPN de Site para o Site utilizando o Gestor de recursos do Azure e o Portal do Azure | Microsoft Azure"
   description="Como criar VNet utilizando o modelo de implementação do Gestor de recursos e ligá-lo à sua local no local rede utilizando uma ligação de gateway S2S VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Criar um VNet com uma ligação de Site para o Site utilizando o portal do Azure

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clássico - clássico de Portal](vpn-gateway-site-to-site-create.md)


Este artigo explica o criação de uma rede virtual e uma ligação de gateway VPN de Site para o Site à sua rede no local com o modelo de implementação do Gestor de recursos do Azure e o portal do Azure. Ligações de site para o Site podem ser utilizadas para publicação em local e híbrido configurações.

![Diagrama](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações do Site para o Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os métodos para configurações de Site para o Site e modelos de implementação encontra-se disponível. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais 

Se pretende ligar VNets em conjunto, mas não estiver a criar uma ligação para uma localização no local, consulte o artigo [Configurar uma ligação de VNet para VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se quiser adicionar uma ligação de Site para o Site a um VNet que já tem uma ligação, consulte o artigo [Adicionar uma ligação de S2S a um VNet com uma ligação de gateway VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem os seguintes itens antes de iniciar a sua configuração:

- Um dispositivo VPN compatível e alguém que consiga configurá-lo. Consulte o artigo [sobre os dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do seu dispositivo VPN ou se não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu no local, é necessário estão coordenados com alguém que pode fornecer essas detalhes por si.

- Um externamente oposto endereço IP público para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.
    
- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Valores de configuração de exemplo para este exercício


Ao utilizar estes passos como um exercício, pode utilizar os valores de configuração de exemplo:

- **VNet nome:** TestVNet1
- **Espaço de endereços:** 10.11.0.0/16 e 10.12.0.0/16
- **Sub-redes:**
    - FrontEnd: 10.11.0.0/24
    - Back-end: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- **Grupo de recursos:** TestRG1
- **Localização:** Leste dos EUA
- **Servidor DNS:** 8.8.8.8
- **Nome do gateway:** VNet1GW
- **Endereço IP público:** VNet1GWIP
- **Tipo de VPN:** Com base em encaminhar
- **Tipo de ligação:** Site para site (IPsec)
- **Tipo de gateway:** VPN
- **Nome de rede local de Gateway:** Site2
- **Nome da ligação:** VNet1toSite2


## <a name="CreatVNet"></a>1. a criar uma rede virtual 

Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a estrutura de gateway VPN. Paga uma atenção especial para qualquer sub-redes que podem sobrepor-se noutras redes. Se tiver sobrepostos sub-redes, a sua ligação não irá funcionar corretamente. Se o seu VNet está configurado com as definições corretas, pode começar os passos na secção [especificar um servidor de DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. Adicionar espaço de endereços adicionais e sub-redes

Pode adicionar espaço de endereços adicionais e sub-redes a sua VNet depois de ter sido criada.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Especifique um servidor de DNS

### <a name="to-specify-a-dns-server"></a>Para especificar um servidor de DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. criar uma sub-rede do gateway

Antes de ligar à rede virtual para um gateway, primeiro tem de criar a sub-rede de gateway para a rede virtual ao qual pretende ligar. Se possível, é melhor criar uma sub-rede de gateway utilizando CIDR ou um bloco de /28 /27 para fornecer suficiente endereços IP para acomodar os requisitos de configuração de futuros adicionais.

Se estiver a criar esta configuração como um exercício, consulte estes [valores](#values) quando criar a sua sub-rede de gateway.

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. criar um gateway de rede virtual

Se estiver a criar esta configuração como um exercício, pode referir-se para os [valores de configuração de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. criar um gateway de rede local

O gateway' rede local' refere-se a sua localização no local. Dê um nome pelo qual, Azure pode referir-se para a mesma ao gateway de rede local. 

Se estiver a criar esta configuração como um exercício, pode referir-se para os [valores de configuração de exemplo](#values).

### <a name="to-create-a-local-network-gateway"></a>Para criar um gateway de rede local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. configurar o seu dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. criar uma ligação VPN do Site para o Site

Crie a ligação de Site para o Site VPN entre o seu gateway de rede virtual e o seu dispositivo VPN. Certifique-se de que substitua os valores com o seu próprio. A chave partilhada tem de corresponder o valor utilizado para a configuração de dispositivo VPN. 

Antes de iniciar nesta secção, certifique-se de que o gateway de rede virtual e gateways de rede local concluiu a criação. Se estiver a criar esta configuração como um exercício, consulte estes [valores](#values) quando criar a sua ligação.

### <a name="to-create-the-vpn-connection"></a>Para criar a ligação VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. Verifique se a ligação VPN

Pode verificar a ligação VPN no portal do ou ao utilizar o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Próximos passos

- Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo o máquinas virtuais [caminho de formação](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) para obter mais informações.

- Para obter informações sobre BGP, consulte a [Descrição geral de BGP](vpn-gateway-bgp-overview.md) e [como configurar BGP](vpn-gateway-bgp-resource-manager-ps.md).
