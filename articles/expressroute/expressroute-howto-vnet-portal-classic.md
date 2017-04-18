<properties
   pageTitle="Configurar um rede Virtual e o Gateway para ExpressRoute no portal do clássico | Microsoft Azure"
   description="Este artigo explica o configurar uma rede virtual para ExpressRoute utilizando o modelo clássico de implementação e o portal clássico."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Criar uma rede Virtual para ExpressRoute no portal do clássico

Os passos neste artigo irão guiá-lo através de configurar uma rede virtual e um gateway de rede virtual para utilização com ExpressRoute utilizando o modelo clássico de implementação e o portal clássico.

Se estiver à procura instruções para o modelo de implementação do Gestor de recursos, pode utilizar os seguintes artigos: [criar uma rede virtual utilizando o PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) e [Adicionar um Gateway de VPN para VNet um Gestor de recursos para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Criar uma VNet e gateway clássico

Os passos seguintes criam um VNet clássica e um gateway de rede virtual. Se já tiver uma VNet clássico, consulte a secção de [configurar um VNet clássica existente](#config) neste artigo.

1. Inicie a sessão [portal clássica Azure](http://manage.windowsazure.com).

2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de rede**e, em seguida, clique em **Rede Virtual**. Clique em **Criar personalizado** para iniciar o Assistente de configuração.

3. Na página **Detalhes da rede Virtual** , introduza o seguinte:

    - **Nome** – nome da sua rede virtual. Irá utilizar este nome de rede virtual quando implementar as instâncias VMs e PaaS, pelo que não pretende tornar o nome demasiado complicado.
    - **Localização** – a localização diretamente está relacionada com a localização física (região) onde pretende que os recursos (VMs) para residem. Por exemplo, se pretender que o VMs que implementar a esta rede virtual física localizados no Leste dos EUA, selecione essa localização. Não é possível alterar a região associada com a sua rede virtual depois de o criar.

4. Na página **DNS Servers e grupo análise** , introduza as seguintes informações e, em seguida, clique na seta seguinte na parte inferior direita. 

    - **Os servidores DNS** - introduza o nome de servidor DNS e o endereço IP ou selecione um servidor DNS registado anteriormente no menu de atalho. Esta definição não cria um servidor de DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução do nome para esta rede virtual.
    - **Conectividade de site para o Site** - selecione a caixa de verificação para **Configurar uma VPN do site para o site**.
    - **ExpressRoute** – selecione a caixa de verificação **Utilizar ExpressRoute**. Esta opção só aparece se tiver selecionado a **Configurar uma VPN do Site para o Site**.
    - **Rede local** - é necessários para ter um site de rede local para ExpressRoute. No entanto, no caso de uma ligação de ExpressRoute, serão ignorados prefixos de endereço especificados para o site de rede local. Em vez disso, os prefixos de endereço anunciados para a Microsoft através de circuito ExpressRoute serão utilizados para fins de encaminhamento.<BR>Se já tiver uma rede local criada para a sua ligação de ExpressRoute, pode selecioná-lo na lista pendente. Caso contrário, selecione **especificar uma nova rede Local**.

5. A página de **conectividade de Site para o Site** é apresentada se tiver selecionado para especificar uma nova rede local no passo anterior. Para configurar a sua rede local, introduza as seguintes informações e, em seguida, clique na seta seguinte. 

    - **Nome** - o nome que pretende para ligar o seu local (no local) para o site de rede.
    - **Espaço de endereços** - incluindo IP inicial e CIDR (contagem de endereço). Pode especificar intervalo qualquer endereço desde que não sobrepor-se com o intervalo de endereços para a sua rede virtual. Normalmente, isto de especificar os intervalos de endereços para redes no local, mas no caso de ExpressRoute, estas definições não são utilizadas. No entanto, esta definição for necessário para criar a rede local quando estiver a utilizar o portal clássico.
    - **Adicionar espaço de endereços** - esta definição não é relevante para ExpressRoute.


6. Na página **Virtual espaços de endereços de rede** , introduza as seguintes informações e, em seguida, clique em marca de verificação no canto inferior direito para configurar a sua rede. 

    - **Espaço de endereços** - incluindo a contagem de IP e o endereço inicial. Certifique-se de que os espaços de endereço que especificou não se sobrepõe qualquer um dos espaços de endereço tem na sua rede local.
    - **Adicionar sub-rede** - incluindo IP inicial e contagem de endereço. Sub-redes adicionais não são necessárias.
    - **Adicionar sub-rede de gateway** - clique para adicionar a sub-rede de gateway. A sub-rede gateway é utilizada apenas para o gateway de rede virtual e é necessária para esta configuração.<BR>A sub-rede gateway CIDR (contagem de endereço) para ExpressRoute tem de ser /28 polegadas ou superior (/ 27, / 26 etc.). Isto permite suficiente endereços IP nessa sub-rede para permitir que a configuração trabalhar. No portal do clássico, se tiver selecionado a caixa de verificação Utilizar ExpressRoute, o portal Especifica uma sub-rede gateway com /28.  Não é possível ajustar a contagem de endereço CIDR no portal do clássico. A sub-rede gateway aparecerá como **Gateway** no portal do clássico, apesar do nome real da sub-rede gateway que é criado é realmente **GatewaySubnet**. Pode ver este nome utilizando o PowerShell ou no portal do Azure.

7. Clique em marca de verificação na parte inferior da página e vai começar criar a sua rede virtual. Quando estiver concluído, irá ver **que criado** listado em **Estado** , na página de **redes** no portal do clássico.

## <a name="gw"></a>Criar um gateway

1. Na página de **redes** , clique na rede virtual que acabou de criar, em seguida, clique em **Dashboard** na parte superior da página.

2. Na parte inferior da página do **Dashboard** , clique em **Criar Gateway** e selecione **O encaminhamento dinâmico**. Clique em **Sim** para confirmar que pretende criar um gateway.

3. Quando o gateway começa a criar, verá enviarem uma mensagem que sabe que o gateway foi iniciado. Poderá demorar até 45 minutos para o gateway criar.

4. Ligar a sua rede para um circuito. Siga as instruções no artigo [como associar VNets ExpressRoute circuitos](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurar um VNet clássica existente para ExpressRoute

Se já tiver uma VNet clássico, pode configurar ligá-lo à ExpressRoute no portal do clássico. As definições são os mesmos como as secções acima, por isso, leia essas secções para se familiarizar com as definições necessárias. Se pretender criar uma ligação de coexistentes ExpressRoute/Site-para-Site, consulte [Este artigo](expressroute-howto-coexist-classic.md) para obter os passos. São diferentes que os passos neste artigo.
 
1. Tem de criar a rede local antes de atualizar o resto das suas definições de VNet. Para criar uma nova rede local, que é necessária ao configurar ExpressRoute através do portal do clássico, clique em **Novo** **>** **Serviços de rede** **>** **Rede Virtual** **>** **Adicionar de rede local**. Siga os passos do Assistente para criar a rede local.

2. Utilize **Configurar** página para atualizar o resto das definições para a sua VNet e para associar VNet para a rede local.

3. Depois de configurar as definições, aceda à secção [criar o gateway](#gw) deste artigo para criar o gateway.


## <a name="next-steps"></a>Próximos passos

- Se pretender adicionar máquinas virtuais à sua rede virtual, consulte o artigo [máquinas virtuais caminhos de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Se quiser saber mais sobre ExpressRoute, consulte o artigo [Descrição geral de ExpressRoute](expressroute-introduction.md).


 
