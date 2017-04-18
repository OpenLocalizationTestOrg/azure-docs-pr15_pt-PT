<properties
   pageTitle="Criar VNet efectuado através do portal Azure | Microsoft Azure"
   description="Saiba como criar uma rede virtual através do portal Azure no Gestor de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Criar uma rede virtual efectuado através do portal Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet efectuado com base no cenário acima utilizando o portal do Azure, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Para estabelecer VNET efectuado, tem de criar duas ligações, uma para cada direcção, entre duas VNets. Pode criar ligação de efectuado VNET para VNET1 para VNET2 pela primeira vez. No portal de, clique em **Procurar** > **Selecione redes virtuais**

    ![Criar VNet efectuado no portal do Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. No pá redes virtuais, selecione VNET1, clique em Peerings, em seguida, clique em Adicionar

    ![Selecione efectuado](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. No pá adicionar efectuado, dê um nome de ligação peering LinkToVnet2, selecione a subscrição e o peer Virtual VNET2 de rede, clique em OK.

    ![Ligação para VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Assim que esta ligação peering VNET é criada. Pode ver o estado da ligação como seguinte:

    ![Estado da ligação](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Em seguida, crie a ligação peering VNET para VNET2 para VNET1. No pá redes virtuais, selecione VNET2, clique em Peerings, em seguida, clique em Adicionar

    ![Ponto a ponto a partir de outras VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. Na pá adicionar efectuado, dê um nome de ligação peering LinkToVnet1, selecione a subscrição e o peer rede Virtual, clique em OK.

    ![Criação de mosaico de rede virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Assim que esta ligação peering VNET é criada. Pode ver o estado da ligação como seguinte:

    ![Estado da ligação final](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Verificar o estado para LinkToVnet2 e transforma-se agora ligado também.  

    ![Estado da ligação final 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] Só é estabelecida VNET efectuado se ambas as ligações estão ligadas.

Existem algumas propriedades configuráveis para cada ligação:

|Opção|Descrição|Predefinido|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Se espaço de ponto a ponto VNet para ser incluídos como parte da etiqueta Virtual_network de endereços|Sim|
|AllowForwardedTraffic|Se o tráfego não proveniente de uma VNet dispõe está aceites ou largado|N|
|AllowGatewayTransit|Permite que o peer VNet para utilizar o seu gateway VNet|N|
|UseRemoteGateways|Utilize o gateway do seu ponto VNet. A ponto a ponto VNet tem de ter um gateway configurado e AllowGatewayTransit está selecionada. Não pode utilizar esta opção se tiver um gateway configurado|N|

Cada ligação no VNet efectuado tem um conjunto de acima propriedades. A partir do portal, pode clicar na ligação de efectuado VNet e alterar as opções disponíveis, clique em Guardar para tornar o efeito de alteração.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Neste exemplo estamos irá utilizar duas subscrições A e B e dois utilizadores utilizadora e UtilizadorB com os privilégios as subscrições respetivamente
3. No portal de, clique em Procurar, selecione redes virtuais. Clique na VNET e clique em Adicionar.

    ![Cenário 2 procurar](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. No pá de acesso adicionar, clique em selecionar uma função e selecione rede contribuinte, clique em Adicionar utilizadores, escreva o sinal de UtilizadorB em nome e clique em OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Não se trata de um requisito efectuado pode ser estabelecida mesmo se os utilizadores elevar individualmente efectuados pedidos para os respetivos Vnets respetivos desde que os pedidos de correspondem. Adicionar um utilizador com privilégios do outros VNet como os utilizadores no local VNet facilita a configuração no portal.

5. Em seguida, inicie sessão Azure portal com UtilizadorB quem é o utilizador privilégio para SubscriptionB. Siga acima passos para adicionar utilizadora como contribuinte de rede.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] Pode terminar a sessão e iniciar sessão em ambas as sessões de utilizador no browser para assegurar que a autorização é activada com êxito.

6. Início de sessão no portal como utilizadora, navegue para a pá VNET3, clique em Peering, verifique ' sei que o meu ID do recurso "caixa de verificação e escreva o recurso ID VNET5 no abaixo de formato.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![ID do recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Inicie sessão no portal como UtilizadorB e seguir acima passo para criar ligação efectuada da VNET5 para VNet3.

    ![ID do recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Efectuado será estabelecida e qualquer máquina Virtual VNet3 deverá conseguir comunicar com qualquer máquina virtual VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Como primeiro passo, VNET efectuados ligações a partir de HubVnet para VNET1. Tenha em atenção que a opção permitir o tráfego reencaminhadas não está selecionada para a ligação.

    ![Efectuado básicas](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Como um passo seguinte, podem ser criadas efectuadas ligações a partir de VNET1 para HubVnet. Tenha em atenção que a opção de tráfego de permitir reencaminhada está selecionada.

    ![Efectuado básicas](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Depois de efectuado é estabelecida, pode referir-se a este [artigo](virtual-network-create-udr-arm-ps.md) e definir Route(UDR) definidos pelo utilizador para redirecionar o tráfego de VNet1 através de um virtual aparelho para utilizar as suas capacidades. Quando especificar o endereço de salto seguinte nos artigos encaminhar, pode defini-la para o endereço IP do aparelho virtual no ponto a ponto VNet HubVNet


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.

2. Para estabelecer VNET efectuado neste cenário, tem de criar apenas uma ligação, da rede virtual no Gestor de recursos Azure para num clássica. Isto é, a partir do **VNET1** para **VNET2**. No portal de, clique em **Procurar** > escolher **Redes virtuais**

3. Na pá de redes Virtual, escolha **VNET1**. Clique em **Peerings**, em seguida, clique em **Adicionar**.

4. Pá adicionar efectuado, o nome da hiperligação. Aqui é chamado **LinkToVNet2**. Em detalhes de ponto a ponto, selecione **clássica**.

5. Em seguida, selecione a subscrição e o ponto de rede Virtual **VNET2**. Em seguida, clique em OK.

    ![Ligar Vnet1 a Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Quando esta ligação peering VNet estiver criada, as duas redes virtuais são dispõe e que poderá ver o seguinte:

    ![Verificar efectuada ligação](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Remover VNet efectuado

1.  A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2.  Aceda a pá de rede virtual, clique em Peerings, clique na ligação que pretende remover, clique em botão Eliminar.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Depois de remover uma ligação no VNET efectuado, o estado da ligação de ponto a ponto serão direcionadas para desligado.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. Neste estado, é possível criar novamente a ligação até se transformar o estado da ligação de ponto a ponto iniciadas. Recomendamos que remova as ambas as ligações antes de voltar a criar o VNET efectuado.
