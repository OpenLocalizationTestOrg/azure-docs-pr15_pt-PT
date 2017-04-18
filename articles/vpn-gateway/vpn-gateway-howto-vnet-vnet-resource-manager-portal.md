<properties
   pageTitle="Ligar VNets utilizando o modelo de implementação do Gestor de recursos e o portal do Azure | Microsoft Azure"
   description="Crie uma ligação de gateway VPN entre VNets utilizando o Gestor de recursos e o portal do Azure."
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
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurar uma ligação de VNet para VNet através do portal Azure

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clássico - clássico de Portal](virtual-networks-configure-vnet-to-vnet-connection.md)


Este artigo explica os passos para criar uma ligação entre VNets no modelo de implementação de Gestor de recursos utilizando o VPN Gateway e o portal Azure.

Quando utiliza o portal do Azure para ligar virtuais redes, tem de ser a VNets na mesma subscrição. Se estiverem redes virtuais em diferentes subscrições, ainda pode ligá-los através dos passos do [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) .

![Diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementação e métodos para VNet para VNet ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos de implementação atualmente disponíveis e os métodos para VNet para VNet configurações. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet efectuado

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações de VNet para VNet

(VNet para VNet) que liga uma rede virtual a outra rede virtual é semelhante a ligar-se uma VNet para uma localização do site no local. Ambos os tipos de conectividade utilizam um gateway Azure VPN para fornecer um túnel seguro através do IPsec/IKE. Pode ser VNets que se liga nas regiões diferentes, ou nas diferentes subscrições.

Ainda pode combinar VNet para VNet comunicação com configurações de múltiplos sites. Permite-estabelecer topologias de rede que combinam cruzada local conectividade com a conectividade de rede entre virtual, conforme mostrado no diagrama seguinte:

![Acerca das ligações] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca das ligações")

### <a name="why-connect-virtual-networks"></a>Por que motivo ligar redes virtuais?

Pretende ligar redes virtuais pelos seguintes motivos:

- **Cruzada região geo-redundância e geo presença**
    - Pode configurar o seu próprio geo replicação ou a sincronização com a conectividade segura sem ultrapassar pontos finais de acesso à Internet.
    - Com o Gestor de tráfego do Azure e Balanceador de carga, pode configurar o altamente disponível carga de trabalho com geo redundância entre as regiões Azure múltiplos. Um exemplo importante é configurar o SQL sempre com grupos de disponibilidade propagação entre as regiões Azure múltiplos.

- **Aplicações de várias camadas regionais com isolamento ou limite administrativo**
    - Dentro da mesma região, pode configurar as aplicações com várias camadas com múltiplas redes virtuais interligados devido a isolamento ou requisitos administrativos.

Para mais informações sobre ligações VNet para VNet, consulte as [FAQ VNet para VNet](#faq) no final deste artigo.

### <a name="values"></a>Definições de exemplo

Ao utilizar estes passos como um exercício, pode utilizar os valores de configuração de exemplo. Efeitos de exemplo, utilizamos vários espaços de endereço para cada VNet. No entanto, configurações de VNet para VNet não necessitam de vários espaços de endereço.

**Valores para TestVNet1:**

- Nome de VNet: TestVNet1
- Espaço de endereços: 10.11.0.0/16
    - Nome de sub-rede: front-end
    - Intervalo de endereços de sub-rede: 10.11.0.0/24
- Grupo de recursos: TestRG1
- Localização: Leste dos e.u.a.
- Espaço de endereços: 10.12.0.0/16
    - Nome de sub-rede: back-end
    - Intervalo de endereços de sub-rede: 10.12.0.0/24
- Nome do gateway sub-rede: GatewaySubnet (Isto irá preenchimento automático no portal do)
    - Intervalo de endereços do gateway sub-rede: 10.11.255.0/27
- Servidor DNS: Utilize o endereço IP do seu servidor DNS
- Nome do Gateway rede virtual: TestVNet1GW
- Tipo de gateway: VPN
- Tipo de VPN: com base em encaminhar
- SKU: Selecione o SKU de Gateway que pretende utilizar
- Nome do endereço IP público: TestVNet1GWIP
- Valores de ligação:
    - Nome: TestVNet1toTestVNet4
    - Chave partilhada: pode criar a chave partilhada sozinho. Neste exemplo, vamos utilizar abc123. O importante é que quando criar a ligação entre o VNets, o valor tem de corresponder.

**Valores para TestVNet4:**

- Nome de VNet: TestVNet4
- Espaço de endereços: 10.41.0.0/16
    - Nome de sub-rede: front-end
    - Intervalo de endereços de sub-rede: 10.41.0.0/24
- Grupo de recursos: TestRG1
- Localização: Oeste dos e.u.a.
- Espaço de endereços: 10.42.0.0/16
    - Nome de sub-rede: back-end
    - Intervalo de endereços de sub-rede: 10.42.0.0/24
- Nome de GatewaySubnet: GatewaySubnet (Isto irá preenchimento automático no portal do)
    - Intervalo de endereços GatewaySubnet: 10.41.255.0/27
- Servidor DNS: Utilize o endereço IP do seu servidor DNS
- Nome do Gateway rede virtual: TestVNet4GW
- Tipo de gateway: VPN
- Tipo de VPN: com base em encaminhar
- SKU: Selecione o SKU de Gateway que pretende utilizar
- Nome do endereço IP público: TestVNet4GWIP
- Valores de ligação:
    - Nome: TestVNet4toTestVNet1
    - Chave partilhada: pode criar a chave partilhada sozinho. Neste exemplo, vamos utilizar abc123. O importante é que quando criar a ligação entre o VNets, o valor tem de corresponder.


## <a name="CreatVNet"></a>1. criar e configurar TestVNet1

Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a estrutura de gateway VPN. Paga uma atenção especial para qualquer sub-redes que podem sobrepor-se noutras redes. Se tiver sobrepostos sub-redes, a sua ligação não irá funcionar corretamente. Se o seu VNet está configurado com as definições corretas, pode começar os passos na secção [especificar um servidor de DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. Adicionar espaço de endereços adicionais e criar sub-redes

Pode adicionar espaço de endereços adicionais e criar sub-redes assim que a sua VNet foi criada.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. criar uma sub-rede do gateway

Antes de ligar à rede virtual para um gateway, primeiro tem de criar a sub-rede de gateway para a rede virtual ao qual pretende ligar. Se possível, é melhor criar uma sub-rede de gateway utilizando CIDR ou um bloco de /28 /27 para fornecer suficiente endereços IP para acomodar os requisitos de configuração de futuros adicionais.

Se estiver a criar esta configuração como um exercício, consulte estas [definições de exemplo](#values) ao criar a sua sub-rede de gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Especifique um servidor de DNS (opcional)

Se pretender que a resolução de nomes para máquinas virtuais que são implementada no seu VNets, deverá especificar um servidor de DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. criar um gateway de rede virtual

Neste passo, crie o gateway de rede virtual para sua VNet. Este passo pode demorar até 45 minutos a concluir. Se estiver a criar esta configuração como um exercício, pode referir-se para as [definições de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. criar e configurar TestVNet4

Depois de ter configurado TestVNet1, crie TestVNet4 repetindo os passos anteriores, substituindo as do TestVNet4 os valores. Não é necessário Aguarde até que o gateway de rede virtual para TestVNet1 concluiu a criação antes de configurar TestVNet4. Se estiver a utilizar o seus próprio valores, certifique-se de que os espaços de endereço não se sobrepõe com qualquer um da VNets ao qual pretende ligar.


## <a name="TestVNet1Connection"></a>7. configurar a ligação de TestVNet1

Quando tem concluído os gateways de rede virtual para TestVNet1 e TestVNet4, pode criar a rede virtual ligações de gateway. Nesta secção, irá criar uma ligação a partir do VNet1 para VNet4.

1. Em **todos os recursos**, navegue para o gateway de rede virtual para sua VNet. Por exemplo, **TestVNet1GW**. Clique em **TestVNet1GW** para abrir o pá de gateway rede virtual.

    ![Pá ligações] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Pá ligações")

2. Clique em **+ Adicionar** para abrir o pá **Adicionar ligação** .

3. No pá **Adicionar ligação** , no campo nome, escreva um nome para a sua ligação. Por exemplo, **TestVNet1toTestVNet4**.

    ![Nome da ligação] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nome da ligação")

4. Para **tipo de ligação**. Selecione **VNet para VNet** na lista pendente.

5. O valor do campo **primeiro gateway de rede virtual** é preenchido automaticamente uma vez que está a criar esta ligação de gateway a rede virtual especificado.

6. O campo do **segundo rede virtual gateway** é o gateway de rede virtual da VNet ao qual pretende criar uma ligação para. Clique em **Selecione outra rede virtual gateway** para abrir o pá **Selecione rede virtual gateway** .

    ![Adicionar ligação] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Adicionar uma ligação")

7. Ver os gateways de rede virtual que sejam encontram listados neste pá. Repare que estão listados apenas os gateways de rede virtual que estão na sua subscrição. Se pretender ligar a um gateway de rede virtual que não esteja na sua subscrição, utilize o [PowerShell artigo](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Clique no gateway de rede virtual ao qual pretende ligar.
 
9. No campo de **chave partilhada** , escreva uma chave partilhada para a sua ligação. Pode gerar ou criar esta tecla sozinho. Numa ligação de site para o site, a chave que utiliza seria exatamente o mesmo para o seu dispositivo no local e a sua ligação de gateway rede virtual. O conceito é semelhante aqui, exceto que em vez de ligar para um dispositivo VPN, estiver a ligar a outra gateway de rede virtual.

    ![Chave partilhado] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Chave partilhado")

10. Clique em **OK** na parte inferior da pá para guardar as alterações.

## <a name="TestVNet4Connection"></a>8. configurar a ligação de TestVNet4

Em seguida, crie uma ligação a partir do TestVNet4 TestVNet1. Utilize o método mesmo que utilizou para criar a ligação da TestVNet1 para TestVNet4. Certifique-se de que utilize a mesma chave partilhada.

## <a name="VerifyConnection"></a>9. Verifique a ligação

Verifique a ligação. Para cada gateway de rede virtual, faça o seguinte:

1. Localize pá para o gateway de rede virtual. Por exemplo, **TestVNet4GW**. 
2. No pá de gateway rede virtual, clique em **ligações** para ver o pá ligações para o gateway de rede virtual.

Ver as ligações e verifique o estado. Quando a ligação estiver criada, irá ver **com êxito** e **ligado** como os valores de estado.

![Foi concluída com êxito] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Foi concluída com êxito")

Pode fazer duplo clique cada ligação separadamente para ver mais informações sobre a ligação.

![Tópicos essenciais] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Tópicos essenciais")

## <a name="faq"></a>Perguntas mais frequentes do VNet para VNet

Ver os detalhes de perguntas mais frequentes para obter informações adicionais sobre VNet para VNet ligações.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Próximos passos

Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.
