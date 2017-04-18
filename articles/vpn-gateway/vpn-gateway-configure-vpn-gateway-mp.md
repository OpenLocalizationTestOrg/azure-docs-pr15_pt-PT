<properties 
   pageTitle="Configurar um Gateway VPN no Portal do Azure clássico | Microsoft Azure"
   description="Este artigo apresenta-lhe através de configurar a sua rede virtual VPN gateway e alterar um tipo de encaminhamento de VPN do gateway."
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
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Configurar um gateway VPN para o modelo clássico de implementação


Se pretende criar uma ligação de publicação em local seguro entre Azure e a sua localização no local, terá de configurar uma ligação de gateway VPN. No modelo clássico de implementação, um gateway pode ser um dos dois tipos de encaminhamento de VPN: estática ou dinâmica. O tipo de que escolher depende do seu plano de estrutura de rede e o dispositivo VPN no local que pretende utilizar. 

Por exemplo, algumas opções de conectividade, tal como uma ligação ponto-para-site, necessitam de um gateway encaminhamento dinâmico. Se pretender configurar o seu gateway para suportar ligações ponto site (P2S) e uma ligação do site para o site (S2S), tem de configurar um gateway de encaminhamento dinâmico apesar de site para o site pode ser configurado com o tipo de encaminhamento de VPN do gateway. 

Para além disso, tem Certifique-se de que o dispositivo que pretende utilizar para a sua ligação suporta o tipo de encaminhamento de VPN ao qual pretende criar. Consulte o artigo [sobre os dispositivos VPN](vpn-gateway-about-vpn-devices.md).


**Sobre este artigo** 

Este artigo foi escrito para o modelo de implementação clássica utilizando o [portal clássico](https://manage.windowsazure.com) (não o portal do Azure). 

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Descrição geral de configuração

Os passos seguintes ajudá-lo através de configurar o gateway da VPN no portal do Azure clássico. Estes passos aplicam-se para gateways para redes virtuais que foram criadas utilizando o modelo de implementação clássica. Atualmente, não de todas as definições de configuração de gateways estão disponíveis no portal do Azure. Quando estiverem, podemos irá criar um novo conjunto de instruções que se aplicam ao portal do Azure.


1. [Criar um gateway VPN para o seu VNet](#create-a-vpn-gateway)

1. [Recolher informações para a configuração de dispositivo VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurar o seu dispositivo VPN](#configure-your-vpn-device)

1. [Confirme a sua intervalos de rede local e o endereço IP do gateway VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Antes de começar

Antes de configurar o seu gateway, primeiro tem de criar a sua rede virtual. Para obter os passos criar uma rede virtual para a conectividade de publicação em local, consulte o artigo [Configurar uma rede virtual com uma ligação VPN do site para o site](vpn-gateway-site-to-site-create.md)ou [Configurar uma rede virtual com uma ligação VPN do ponto-para-site](vpn-gateway-point-to-site-create.md). Em seguida, utilize os seguintes passos para configurar o gateway VPN e reunir as informações necessárias configurar o seu dispositivo VPN. 

Se já tiver um gateway VPN e que pretende alterar o tipo de encaminhamento de VPN, consulte o artigo [como alterar o tipo de encaminhamento de VPN para o gateway](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Criar um gateway VPN

1. No [portal clássica Azure](https://manage.windowsazure.com), na página de **redes** , certifique-se de que a coluna de estado para a sua rede virtual é **criado**.

1. Na coluna **nome** , clique no nome da sua rede virtual.

1. Na página do **Dashboard** , repare que este VNet não tem um gateway ainda configurado. Verá este estado à medida que avança através dos passos para configurar o gateway.

![Gateway não criado](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Em seguida, na parte inferior da página, clique em **Criar Gateway**. Pode selecionar em *Encaminhamento estático* ou *Encaminhamento dinâmico*. O tipo de encaminhamento de VPN que selecionar depende alguns fatores. Por exemplo, o que suporta o seu dispositivo VPN e se é necessário suportar ligações ponto-para-site. Selecione [Sobre dispositivos de VPN para a conectividade da rede Virtual](vpn-gateway-about-vpn-devices.md) para verificar o tipo de encaminhamento de VPN que necessita. Assim que o gateway foi criado, não é possível alterar entre os tipos de encaminhamento de VPN de gateway sem eliminar e voltar a criar o gateway. Quando o sistema pede para confirmar que pretende que o gateway criado, clique em **Sim**.

![Tipo de encaminhamento de VPN do gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Quando estiver a criar o gateway, repare o gráfico de gateway na página muda para a amarelo e indica a *Criação de Gateway*. Poderá demorar até 45 minutos para o gateway criar. Aguarde até que o gateway está concluído antes de poder avançar com outras definições de configuração.

![Criação de gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Quando altera o gateway para *Ligar*, pode reunir as informações que precisa para o seu dispositivo VPN.

![Ligar-se do gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Recolher informações para a configuração de dispositivo VPN

Depois do gateway foi criado, recolha informações para a configuração de dispositivo VPN. Esta informação encontra-se na página **Dashboard** para a sua rede virtual:

1. **Endereço IP do gateway-** O endereço IP pode ser encontrado na página do **Dashboard** . Não poderá vê-la até depois do gateway concluiu a criação.

1. **Chave partilhado-** Clique em **Gerir chave** na parte inferior do ecrã. Clique no ícone junto a tecla para copiá-lo para a área de transferência e, em seguida, cole e guarde a chave. Este botão só funciona quando existe um único túnel S2S VPN. Se tiver vários túneis S2S VPN, utilize a *Obter Virtual rede partilhadas chave de Gateway* API ou o cmdlet do PowerShell.

![Gerir a chave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Configurar o seu dispositivo VPN

Depois de concluir os passos anteriores, utilizador ou o administrador de rede terão configurar o dispositivo VPN para poder criar a ligação. Consulte o artigo [Sobre dispositivos de VPN para Virtual a conectividade da rede](vpn-gateway-about-vpn-devices.md) para obter mais informações sobre os dispositivos VPN.

Depois de ter sido configurado o dispositivo VPN, pode ver as informações de ligação atualizados na página Dashboard para sua VNet.

Também pode executar um dos seguintes comandos para testar a ligação:

|                      | Cisco ASA             | Cisco ISR/recuperação automática         | Zimbro SSG/ISG | Zimbro SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Verificar SA de modo principal**  | Mostrar crypto isakmp sa | Mostrar crypto isakmp sa | obter ike cookie  | Mostrar segurança associação de segurança ike   |
| **Verificar SA de modo rápido** | Mostrar crypto ipsec sa  | Mostrar crypto ipsec sa  | obter sa          | Mostrar a associação de segurança do ipsec segurança |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Confirme a sua intervalos de rede local e o endereço IP do gateway VPN

### <a name="verify-your-vpn-gateway-ip-address"></a>Verificar o seu endereço IP de gateway VPN

Para o gateway ligar corretamente, o endereço IP do seu dispositivo VPN tem de ser configurado corretamente para a rede Local que especificou para a sua configuração cruz local. Normalmente, isto é configurado durante o processo de configuração de site para o site. No entanto, se tiver utilizado anteriormente esta rede local com um dispositivo diferente ou o endereço IP foi alterado para esta rede local, edite as definições para especificar o endereço IP do Gateway correto.

1. Para verificar o seu endereço IP do gateway, clique em **redes** no painel esquerdo do portal e, em seguida, selecione **Redes locais** na parte superior da página. Verá o endereço do Gateway VPN para cada rede local que tenha criado. Para editar o endereço IP, selecione o VNet e clique em **Editar** na parte inferior da página.

1. Na página **especificar os detalhes do seu local de rede** , edite o endereço IP e, em seguida, clique na seta na parte inferior da página seguinte.

1. Na página **especificar o espaço de endereços** , clique em marca de verificação no canto inferior direito para guardar as definições.

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Verifique se os intervalos de endereços para redes locais

Para o tráfego correto para fluxo através do gateway para a sua localização no local, tem de confirmar que é especificado cada intervalo de endereços IP. Cada intervalo tem de estar listado na configuração Azure **Redes locais** . Dependendo da configuração de rede da sua localização no local, isto pode ser uma tarefa um pouco grande. O tráfego de que está vinculado para um endereço IP que encontra-se os intervalos listados será enviado através do gateway VPN rede virtual. Os intervalos lista não têm de ser intervalos privados, apesar de que pretende verificar se a sua configuração no local pode receber o tráfego de entrada.

Para adicionar ou editar os intervalos para uma rede Local, utilize os passos seguintes.

1. Para editar os intervalos de endereços IP para uma rede local, clique em **redes** no painel esquerdo do portal e, em seguida, selecione **Redes locais** na parte superior da página. No portal do, a maneira mais fácil de ver os intervalos que tenha listado está na página **Editar** . Para ver os intervalos especificados, selecione o VNet e clique em **Editar** na parte inferior da página.

1. Na página **especificar os detalhes do seu local de rede** , não faça as alterações. Clique na seta na parte inferior da página seguinte.

1. Na página **especificar o espaço de endereços** , faça as alterações de espaço de endereços de rede. Em seguida, clique em marca de verificação para guardar a configuração.

## <a name="how-to-view-gateway-traffic"></a>Como ver o tráfego do gateway

Pode ver o seu gateway e o tráfego de gateway a partir da sua página de rede Virtual **Dashboard** .

Na página **Dashboard** pode ver o seguinte procedimento:

- A quantidade de dados que estão a fluir através do seu gateway, dados e dados de saída.

- Os nomes dos servidores DNS que são especificados para a sua rede virtual.

- A ligação entre o seu gateway e o seu dispositivo VPN.

- A chave partilhada que é utilizada para configurar a sua ligação de gateway para o seu dispositivo VPN.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Como alterar o tipo de encaminhamento de VPN para o gateway

Uma vez que algumas configurações de conectividade só estão disponíveis para determinados tipos de encaminhamento de gateway, poderá constatar que precisa de alterar o tipo de encaminhamento de VPN de um gateway VPN existente do gateway. Por exemplo, poderá querer adicionar a conectividade de ponto-para-site para uma ligação de site para o site já existente, que tem um estático gateway. Ligações ponto-para-site necessitam de um gateway dinâmico. Isto significa configurar uma ligação de P2S, tem de alterar o tipo de encaminhamento de VPN gateway de estático para dinâmicos.

Se precisar de alterar um tipo de encaminhamento de VPN gateway, irá eliminar o gateway existente e, em seguida, crie um novo gateway com o novo tipo de encaminhamento. Não tem de eliminar toda a rede virtual para alterar o tipo de encaminhamento de gateway.

Antes de alterar o tipo de encaminhamento de VPN gateway, certifique-se de que verifique se o seu dispositivo VPN suporta o tipo de encaminhamento que pretende utilizar. Para transferir novas amostras de configuração de encaminhamento e verifique os requisitos do dispositivo VPN, consulte o artigo [Sobre dispositivos de VPN para a conectividade da rede Virtual](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Quando elimina um gateway VPN rede virtual, é disponibilizada VIP atribuído para o gateway. Quando recriar o gateway, um novo VIP é atribuído à mesma.

1. **Elimine o gateway VPN existente.**

    Na página **Dashboard** para a sua rede virtual, navegue para a parte inferior da página e clique em **Eliminar o Gateway**. Aguarde que a notificação de que o gateway foi eliminado. Quando receber a notificação no ecrã do seu gateway foi eliminado, pode criar um novo gateway.

1. **Crie um novo gateway VPN.**

    Utilize o procedimento na parte superior da página para criar um novo gateway: [criar um gateway VPN](#create-a-vpn-gateway).


## <a name="next-steps"></a>Próximos passos

Pode adicionar máquinas virtuais à sua rede virtual. Veja [como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se quiser configurar uma ligação VPN do ponto de sites, consulte o artigo [Configurar uma ligação VPN ponto para local](vpn-gateway-point-to-site-create.md).

 
