<properties 
   pageTitle="O que são percursos do definidos pelo utilizador e reencaminhamento IP?"
   description="Saiba como utilizar rotas definidos pelo utilizador (UDR) e reencaminhamento IP reencaminhe o tráfego para virtuais eletrodomésticos no Azure de rede."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>O que são rotas de definidos pelo utilizador e reencaminhamento IP?
Quando adiciona máquinas virtuais (VMs) a uma rede virtual (VNet) no Azure, vai notar que a VMs são capazes de comunicar com os outros através da rede, automaticamente. Não tem de especificar um gateway, apesar das VMs estão no sub-redes diferentes. O mesmo se aplica-se para a comunicação a partir dos VMs à Internet pública e até mesmo para o seu local rede quando uma ligação de implementações do Azure ao seu próprio Centro de dados é apresentar.

Este fluxo de comunicação é possível porque o Azure utiliza uma série de rotas de sistema para definir a forma como os fluxos de tráfego IP. Percursos do sistema controlam o fluxo de comunicação nos cenários seguintes:

- A partir de dentro da mesma sub-rede.
- A partir de uma sub-rede para outro dentro de uma VNet.
- A partir do VMs à Internet.
- A partir de um VNet para outra VNet através de um gateway VPN.
- A partir de um VNet à sua rede no local através de um gateway VPN.

A figura seguinte mostra uma configuração simple com um VNet, sub-duas redes e alguns VMs, juntamente com as rotas de sistema que permitem o tráfego para um fluxo de IP.

![Percursos do sistema no Azure](./media/virtual-networks-udr-overview/Figure1.png)

Apesar da utilização de rotas sistema facilita tráfego automaticamente para a sua implementação, existem casos em que pretender para controlar o encaminhamento de pacotes através de uma aplicação virtual. Pode por isso, ao criar rotas definidas pelo utilizador que especificar próximo salto para pacotes a fluir para uma sub-rede específica para aceder ao seu aparelho virtual em vez disso e activar o reencaminhamento de IP para a VM em execução como aparelho virtual.

A figura seguinte mostra um exemplo de rotas definidas pelo utilizador e reencaminhamento de IP para forçar pacotes enviada para uma sub-rede de outro para percorrer um aparelho virtual numa sub-rede terceira.

![Percursos do sistema no Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Rotas definidas pelo utilizador são aplicadas apenas para o tráfego de sair de uma sub-rede. Não é possível criar rotas para especificar como o tráfego é uma sub-rede da Internet, por exemplo. Além disso, o dispositivo que está a reencaminhar o tráfego para o não pode ser na mesma sub-rede o tráfego do qual origina. Crie sempre uma sub-rede separada para a sua eletrodomésticos. 

## <a name="route-resource"></a>Encaminhar recurso
Pacotes são encaminhados através de uma rede TCP/IP com base numa tabela de encaminhamento definida no cada nó na rede física. Uma tabela de rota é uma coleção de rotas individuais utilizado para decidir onde reencaminhar pacotes com base em endereço IP de destino. Uma rota consiste em dos seguintes procedimentos:

|Propriedade|Descrição|Restrições|Considerações sobre|
|---|---|---|---|
| Prefixo de endereço | O CIDR de destino à qual a rota aplica-se, tal como 10.1.0.0/16.|Tem de ser um intervalo CIDR válido que representa endereços na Internet pública, Azure rede virtual ou centro de dados no local.|Certifique-se que o **prefixo de endereço** não contém o endereço para o **endereço de salto seguinte**, caso contrário os pacotes irão introduzir um ciclo aceder a partir da origem para o próximo salto sem nunca atingir o destino. |
| Tipo de salto seguinte | O tipo de salto Azure o pacote deve ser enviado para. | Tem de ser um dos seguintes valores: <br/> **Rede virtual**. Representa a rede virtual local. Por exemplo, se tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16 na mesma rede virtual, a rota de cada sub-rede na tabela rota terá um valor de próximo salto de *Rede Virtual*. <br/> **Rede virtual Gateway**. Representa um Gateway VPN Azure S2S. <br/> **Internet**. Representa o gateway de Internet predefinido fornecido pela infraestrutura do Azure. <br/> **Aparelho virtual**. Representa um aparelho virtual que adicionou à sua rede virtual Azure. <br/> **Nenhum**. Representa um mudo. Pacotes reencaminhados para um mudo não serão reencaminhadas de todo.| Considere utilizar um tipo de **nenhum** para parar de pacotes a partir de a fluir para um determinado destino. | 
| Endereço de salto seguinte | O endereço salto seguinte contém o endereço IP deverão ser reencaminhados pacotes. Valores de salto seguintes só são permitidos em rotas onde o seguinte tipo de salto for *Aparelho Virtual*.| Tem de ser um endereço de IP que está acessível no interior da rede Virtual onde é aplicada a rota definidas pelo utilizador. | Se o endereço IP representa uma VM, certifique-se de que ativa [reencaminhamento IP](#IP-forwarding) no Azure para a VM. |

No Azure PowerShell alguns dos valores "NextHopType" têm nomes diferentes:
- Rede virtual é VnetLocal
- Virtual Gateway de rede é VirtualNetworkGateway
- Aparelho virtual é VirtualAppliance
- Internet é a Internet
- Nenhum haja nenhuma

### <a name="system-routes"></a>Percursos do sistema
Cada sub-rede criado numa rede virtual é automaticamente associado uma tabela de rota que contém as seguintes regras de encaminhamento de sistema:

- **Local Vnet regra**: esta regra é automaticamente criada para cada sub-rede numa rede virtual. Especifica que existe uma ligação direta entre VMs na VNet e que não haja nenhuma salto seguinte intermédio.
- **Regra no local**: esta regra é aplicada a todo o tráfego destinado ao intervalo de endereços no local e utiliza VPN gateway como o destino de salto seguinte.
- **Regra de Internet**: este alças regra todo o tráfego destinados à Internet pública (endereço prefixo 0.0.0.0/0) e utiliza o gateway para a internet infraestrutura como o próximo salto para todo o tráfego destinarem à Internet.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador
Para a maioria dos ambientes apenas terá das rotas sistema já definidas pela Azure. No entanto, poderá ter de criar uma tabela de encaminhamento e adicionar uma ou mais rotas em casos específicos, tais como:

- Forçar túnel à Internet através da sua rede no local.
- Utilização de eletrodomésticos virtuais no seu ambiente Azure.

Nos cenários, terá de criar uma tabela de encaminhamento e adicionar-lhe rotas definidas pelo utilizador. Pode ter várias tabelas encaminhar e, a mesma tabela rota pode ser associada a um ou mais sub-redes. E cada sub-rede só pode ser associado a uma tabela de rota única. Todos os VMs e serviços em nuvem numa utilização sub-rede a tabela rota associados nessa sub-rede.

Sub-redes dependem rotas sistema até uma tabela de rota está associada à sub-rede. Assim que existe uma associação, encaminhamento é feito com base na célula mais longa do prefixo CORRESP (LPM) entre rotas definidas pelo utilizador e percursos do sistema. Se existir mais do que uma rota com a mesma correspondência LPM uma rota está selecionada com base na sua origem pela seguinte ordem:

1. Encaminhar definido do utilizador
1. Encaminhar BGP (quando é utilizado ExpressRoute)
1. Rota do sistema

Para saber como criar rotas definidas pelo utilizador, consulte o artigo [como criar rotas e ativar o reencaminhamento de IP no Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Rotas definidas pelo utilizador são aplicadas apenas aos serviços Azure VMs e na nuvem. Por exemplo, se pretender adicionar uma aplicação virtual firewall entre o seu rede no local e o Azure, terá de criar uma rota definidas pelo utilizador para as suas tabelas encaminhar Azure que reencaminha todo o tráfego Ir para o espaço de endereços no local ao aparelho virtual. Também pode adicionar uma rota definidas pelo utilizador (UDR) para GatewaySubnet para reencaminhar todo o tráfego no local para Azure através do aparelho virtual. Esta é uma adição recente.

### <a name="bgp-routes"></a>BGP rotas
Se tiver uma ligação de ExpressRoute entre o seu rede no local e o Azure, pode ativar a BGP a serem propagadas rotas da sua rede no local para Azure. Estas rotas BGP são utilizadas em da mesma forma que percursos do sistema e rotas definidas pelo utilizador em cada sub-rede Azure. Para obter mais informações, consulte [Introdução ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Pode configurar o seu ambiente do Azure utilizar forçar túnel através da sua rede no local através da criação de uma rota definido de utilizador para 0.0.0.0/0 sub-rede que utiliza o gateway VPN como próximo salto. No entanto, esta só funciona se estiver a utilizar um gateway VPN, não ExpressRoute. Para ExpressRoute, túnel forçada é configurado através de BGP.

## <a name="ip-forwarding"></a>IP reencaminhamento de chamadas
À medida que descrevem acima, uma das principais razões para criar uma rota definido de utilizador é reencaminhar o tráfego para um aparelho virtual. Um aparelho virtual é mais do que uma VM que é executada uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT nada.

Este aparelho virtual VM têm de receber o tráfego de entrada que não é resolvido a própria. Para permitir que uma VM receber tráfego endereçado a outros destinos, tem de ativar IP de reencaminhamento para a VM. Este é um Azure definição, não uma definição num sistema operativo convidado.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar rotas no modelo de implementação de Gestor de recursos](virtual-network-create-udr-arm-template.md) e associá-las para sub-redes. 
- Saiba como [criar rotas no modelo de implementação clássica](virtual-network-create-udr-classic-ps.md) e associá-las para sub-redes.
