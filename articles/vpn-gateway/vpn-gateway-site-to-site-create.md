<properties
   pageTitle="Criar uma rede virtual com uma ligação VPN Gateway de site para o site através do portal clássico Azure | Microsoft Azure"
   description="Criar um VNet com uma ligação de S2S VPN Gateway para publicação em local e configurações híbridas utilizando o modelo de implementação clássico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Criar um VNet com uma ligação de Site para o Site através do portal clássico Azure

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clássico - clássico de Portal](vpn-gateway-site-to-site-create.md)

Este artigo explica o criação de uma rede virtual e uma ligação de gateway VPN do site para o site à sua rede no local com o modelo clássico de implementação e o portal clássico. Ligações de site para o Site podem ser utilizadas para publicação em local e híbrido configurações.

![Diagrama de site para o Site] (./media/vpn-gateway-site-to-site-create/site2site.png "site para o site")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações do Site para o Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os métodos para configurações de Site para o Site e modelos de implementação encontra-se disponível. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais 

Se pretender ligar VNets em conjunto, consulte o artigo [Configurar uma ligação de VNet para VNet para o modelo de implementação clássica](virtual-networks-configure-vnet-to-vnet-connection.md). Se quiser adicionar uma ligação de Site para o Site a um VNet que já tem uma ligação, consulte o artigo [Adicionar uma ligação de S2S a um VNet com uma ligação de gateway VPN existente](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem os seguintes itens antes de iniciar configuração.

- Um dispositivo VPN compatível e alguém que consiga configurá-lo. Consulte o artigo [sobre os dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do seu dispositivo VPN ou se não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu no local, é necessário estão coordenados com alguém que pode fornecer essas detalhes por si.

- Um externamente oposto endereço IP público para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.

- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Criar a sua rede virtual

1. Inicie a sessão [portal clássica Azure](https://manage.windowsazure.com/).

2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de rede**e, em seguida, clique em **Rede Virtual**. Clique em **Criar personalizado** para iniciar o Assistente de configuração.

3. Para criar o seu VNet, introduza as definições de configuração nas seguintes páginas:

## <a name="Details"></a>Página de detalhes de rede virtual

Introduza as seguintes informações:

- **Nome**: atribuir um nome a sua rede virtual. Por exemplo, *EastUSVNet*. Irá utilizar este nome de rede virtual quando implementar as instâncias VMs e PaaS, pelo que não pretende tornar o nome demasiado complicado.
- **Localização**: A localização diretamente está relacionada com a localização física (região) onde pretende que os recursos (VMs) para residem. Por exemplo, se pretender que o VMs que implementar a esta rede virtual física localizados no *Leste dos EUA*, selecione essa localização. Não é possível alterar a região associada com a sua rede virtual depois de o criar.

## <a name="DNS"></a>Os servidores DNS e página de conectividade VPN

Introduza as seguintes informações e, em seguida, clique na seta seguinte na parte inferior direita.

- **Os servidores DNS**: introduza o nome de servidor DNS e o endereço IP ou selecione um servidor DNS registado anteriormente no menu de atalho. Esta definição não cria um servidor de DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução do nome para esta rede virtual.
- **Configurar o Site para o Site VPN**: selecione a caixa de verificação para **Configurar uma VPN do site para o site**.
- **Rede local**: uma rede local representa a sua localização física no local. Pode selecionar uma rede local que criou anteriormente, ou pode criar uma nova rede local. No entanto, se selecionar para utilizar uma rede local que criou anteriormente, aceda à página de configuração de **Redes locais** e verificar a exatidão o endereço de IP de dispositivo de VPN (oposto endereço IPv4 público) para o dispositivo VPN.

## <a name="Connectivity"></a>Página de conectividade de site para o site

Se estiver a criar uma nova rede local, verá a página de **Conectividade de Site para o Site** . Se pretender utilizar uma rede local que criou anteriormente, esta página não será apresentada no Assistente de e pode mover para a secção seguinte.

Introduza as seguintes informações e, em seguida, clique na seta seguinte.

-   **Nome**: O nome que pretende para ligar o seu local (no local) para sites de rede.
-   **Endereço de IP do dispositivo de VPN**: endereço IPv4 oposto público do seu dispositivo VPN no local que utilizar para ligar ao Azure. Não é possível localizar o dispositivo VPN atrás de um NAT.
-   **Espaço de endereços**: inclua começando IP e CIDR (contagem de endereço). Especifique o endereço range(s) que o utilizador pretende ser enviada através do gateway de rede virtual para a sua localização local no local. Se um endereço IP de destino se situar dentro dos intervalos que especificar aqui, é encaminhado através do gateway de rede virtual.
-   **Adicionar espaço de endereços**: Se tiver vários intervalos de endereços que pretende que sejam enviados através do gateway de rede virtual, especifique a cada intervalo de endereço adicional. Pode adicionar ou remover intervalos mais tarde na página de **Rede Local** .

## <a name="Address"></a>Página de espaços de endereços de rede virtual

Especifique o intervalo de endereços que pretende utilizar para a sua rede virtual. Estes são os endereços IP dinâmicos (DIMINUIÇÕES) serão atribuídos para os VMs e outras instâncias de função implementar a esta rede virtual.

É importante especialmente selecionar um intervalo que não se sobrepõe com qualquer um dos intervalos que são utilizados para a sua rede no local. Tem de estão coordenados com o seu administrador de rede. O administrador de rede poderá ter de excepção saída de um intervalo de endereços IP a partir do seu espaço de endereços de rede no local para utilizar para a sua rede virtual.

Introduza as seguintes informações e, em seguida, clique em marca de verificação no canto inferior direito para configurar a rede.

- **Espaço de endereços**: inclua começando IP e contagem de endereço. Certifique-se de que os espaços de endereço que especificou não se sobrepõe qualquer um dos espaços de endereço tem na sua rede no local.
- **Adicionar sub-rede**: contagem de endereço e incluir IP inicial. Sub-redes adicionais não são obrigatórios, mas pretende criar uma sub-rede separada para VMs terão DIMINUIÇÕES estáticas. Ou poderá ter os VMs no sub-rede que está separada do seu outras instâncias de função.
- **Adicionar sub-rede de gateway**: clique para adicionar a sub-rede de gateway. A sub-rede gateway é utilizada apenas para o gateway de rede virtual e é necessária para esta configuração.

Clique em marca de verificação na parte inferior da página e vai começar criar a sua rede virtual. Quando estiver concluído, irá ver **que criado** listado em **Estado** , na página de **redes** no Portal clássica do Azure. Depois de ter sido criada a VNet, em seguida, pode configurar o seu gateway de rede virtual.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Configurar o seu gateway de rede virtual

Configure o gateway de rede virtual para criar uma ligação segura do site para o site. Consulte [configurar um gateway de rede virtual no portal do Azure clássico](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Próximos passos

Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte a documentação de [máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais informações.
