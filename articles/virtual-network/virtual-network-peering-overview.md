
<properties
   pageTitle="Azure rede virtual efectuado | Microsoft Azure"
   description="Saiba mais sobre VNet efectuado no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>VNet efectuado

VNet efectuado é um mecanismo que liga duas redes virtuais (VNets) na mesma região através da rede Azure estrutura principal. Assim que dispõe, as duas redes virtuais aparecem como um para todos os efeitos de conectividade. Ainda são geridos como recursos separados, mas máquinas virtuais nestes redes virtuais pode comunicar com os outros diretamente ao utilizar endereços IP privados.

O tráfego entre máquinas virtuais nas redes virtuais dispõe é encaminhado através de infraestrutura do Azure muito como o tráfego é encaminhado entre VMs na mesma rede virtual. Algumas das vantagens da utilização VNet efectuado incluem:

- Uma ligação de baixa latência e largura de banda elevada entre recursos no redes virtuais diferentes.
- A capacidade de utilizar recursos, tais como eletrodomésticos de rede e VPN gateways como pontos trânsito um VNet dispõe.
- A capacidade de ligar uma rede virtual que utiliza o Gestor de recursos do Azure a uma rede virtual que utiliza o modelo clássico de implementação do modelo e activar a conectividade completa entre recursos nestas redes virtuais.

Requisitos e chaves aspetos da VNet efectuado:

- As duas redes virtuais que são dispõe devem ser na mesma região Azure.
- As redes virtuais são dispõe que devem ter espaços de endereços IP não sobrepostos.
- VNet efectuado é entre duas redes virtuais e não existe nenhuma relação verbos derivada. Por exemplo, se rede virtual A é dispõe com a rede virtual B e, se rede virtual B é dispõe com a rede virtual C, não traduzir para virtual rede uma ser dispõe com a rede virtual C.
- Pode ser estabelecida a efectuado entre redes virtuais nas duas subscrições diferentes à medida tempo um utilizador com privilégios de ambas as subscrições autoriza o efectuado e as subscrições estão associadas ao mesmo inquilino do Active Directory. 
- Efectuado entre rede virtual no modelo de Gestor de recursos e o modelo de implementação clássica requer que a VNets deve ser na mesma subscrição.
- Uma rede virtual que utiliza o modelo de implementação do Gestor de recursos pode ser peered com outra rede virtual que utilize este modelo ou com uma rede virtual que utiliza o modelo de implementação clássica. No entanto, não podem ser peered redes virtuais que utilizam o modelo de implementação clássica umas às outras.
- Apesar da comunicação entre máquinas virtuais no dispõe redes virtuais não tem largura de banda adicional restrições, remate de largura de banda com base em tamanho da memória virtual ainda aplica-se.


![Básicas VNet efectuado](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Conectividade
Depois de duas redes virtuais são dispõe, uma máquina de virtual (função web/trabalho) na rede virtual diretamente pode ligar com outros máquinas virtuais na rede virtual dispõe. Estas duas redes tem completo de nível de IP conectividade.

A latência da rede para uma transição entre as duas máquinas virtuais em redes virtuais dispõe é o mesmo que para uma viagem round dentro de uma rede virtual local. O débito da rede baseia-se a largura de banda que é permitida para a máquina virtual proporcionada para o seu tamanho. Não existe nenhuma restrição adicional à largura de banda.

O tráfego entre as máquinas virtuais em redes virtuais dispõe é encaminhado diretamente através da infraestrutura do Azure back-end e não através de um gateway.

Máquinas virtuais numa rede virtual pode aceder os internos balanceamento de carga (ILB) os pontos finais na rede virtual dispõe. Grupos de segurança de rede (NSGs) podem ser aplicados em qualquer uma das redes virtual para bloquear o acesso a outras redes virtuais ou sub-redes se pretender.

Quando os utilizadores configurar efectuado, pode abrir ou fechar as regras NSG entre as redes virtuais. Se o utilizador escolhe abrir a ligação completa entre dispõe redes virtuais (que é a opção predefinida), em seguida, podem utilizar NSGs no sub-redes específicas ou máquinas virtuais para bloquear ou negar acesso específico.

Resolução de nomes DNS interna de Azure fornecido para máquinas virtuais não funciona através de redes virtuais dispõe. Máquinas virtuais têm nomes DNS internos que são resolvidos apenas a partir da rede virtual local. No entanto, os utilizadores podem configurar máquinas virtuais que estão a ser executada em redes virtuais dispõe como servidores DNS para uma rede virtual.

## <a name="service-chaining"></a>Interligação de serviço
Os utilizadores podem configurar tabelas de encaminhamento definida pelo utilizador que apontam para máquinas virtuais no redes virtuais dispõe, como "próximo salto", em endereço IP, conforme mostrado no diagrama neste artigo. Isto permite aos utilizadores alcançar a interligação de serviço, através da qual que podem direcionar tráfego de rede virtual para uma aplicação virtual que está a ser executada numa rede dispõe virtual através de tabelas encaminhar definidos pelo utilizador.

Os utilizadores eficazmente também podem criar ambientes de tipo de concentrador e raios onde o concentrador pode alojar componentes de infraestrutura como um aparelho virtual de rede. Todas as raios virtual redes, em seguida, podem peer com-la, bem como um subconjunto de tráfego para eletrodomésticos que estiver a executar na rede virtual concentrador. Resumindo, VNet efectuado permite que o endereço IP salto seguinte no 'definidas pelo utilizador tabela rota' para ser o endereço IP de uma máquina virtual na rede virtual dispõe.

## <a name="gateways-and-on-premises-connectivity"></a>Conectividade de gateways e no local
Cada rede virtual, independentemente de se-é dispõe com outra rede virtual, ainda pode ter as suas próprias gateway e utilizá-la para ligar a no local. Os utilizadores também podem configurar [VNet para VNet ligações](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) utilizando gateways, apesar das redes virtuais são dispõe.

Quando estiverem configuradas ambas as opções de interconectividade rede virtual, o tráfego entre as redes virtuais flua através da configuração peering (ou seja, através da estrutura Azure).

Quando redes virtuais são dispõe, os utilizadores podem configurar também o gateway na rede virtual dispõe, como um ponto de trânsito no local. Neste caso, a rede virtual que está a utilizar um gateway remoto não pode ter as suas próprias gateway. Uma rede virtual pode conter apenas um gateway. -Pode ser um gateway local ou um gateway remoto (na dispõe virtual rede), conforme apresentado na seguinte imagem.

Não é suportado trânsito gateway na relação peering entre redes virtuais utilizando o modelo de Gestor de recursos e os utilizando o modelo de implementação clássica. Ambas as redes virtuais na relação peering tem de utilizar o modelo de implementação do Gestor de recursos para trânsito um gateway para trabalhar.

Quando as redes virtuais que estão a partilhar uma única ligação do Azure ExpressRoute são dispõe, o tráfego entre eles vai através da relação peering (ou seja, através da rede Azure estrutura principal). Os utilizadores ainda podem utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Em alternativa, pode utilizar um gateway partilhado e configurar trânsito para conectividade no local.

![VNet efectuado trânsito](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Aprovisionamento
VNet efectuado é uma operação privilegiada. É uma função separada em VirtualNetworks espaço de nomes. Um utilizador pode ser fornecido direitos específicos para autorizar efectuado. Um utilizador que tenha acesso de leitura e escrita para a rede virtual herda estes direitos automaticamente.

Um utilizador que quer seja um administrador ou um utilizador com privilégios da capacidade de peering pode iniciar uma operação peering em VNet outra. Se existir um pedido de correspondente para efectuado no outro lado e outros requisitos são preenchidos, o efectuado será estabelecida.

Consulte os artigos na secção "Passos seguintes" para saber mais sobre como estabelecer VNet efectuado entre duas redes virtuais.

## <a name="limits"></a>Limites
Existem limites no número de peerings são permitidas para uma única rede virtual. Consulte [Azure limites de funcionamento em rede](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## <a name="pricing"></a>Preços
VNet efectuado serão gratuitamente durante o período de revisão. Após a disponibilização, irá existir um encargo nominal sobre o tráfego de entrada e saída que utiliza o efectuado. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Próximos passos
- [Configurar o efectuado entre redes virtuais](virtual-networks-create-vnetpeering-arm-portal.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre o [reencaminhamento de IP e rotas definidos pelo utilizador](virtual-networks-udr-overview.md).
