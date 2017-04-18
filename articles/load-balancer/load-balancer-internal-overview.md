
<properties
   pageTitle="Balanceador de carga interno descrição geral | Microsoft Azure"
   description="Descrição geral do Balanceador de carga interno e suas funcionalidades. Como funciona o um balanceador de carga para cenários Azure e possíveis configurar os pontos finais internos"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internal-load-balancer-overview"></a>Descrição geral do Balanceador de carga interno

Ao contrário, em seguida, Internet oposta Balanceador de carga, Balanceador de carga interna (ILB) encaminha o tráfego apenas aos recursos no interior do serviço de nuvem ou utilizar VPN para aceder à infraestrutura do Azure. A infraestrutura de restringe o acesso aos balanceamento de carga endereços IP virtuais (VIP) de um serviço na nuvem ou uma rede Virtual para que estes serão nunca diretamente expostos para um ponto final da Internet. Permite que interno aplicações de linha de negócio (LOB) para executar no Azure e ser acedidas a partir da nuvem ou a partir de recursos no local.

## <a name="why-you-may-need-an-internal-load-balancer"></a>Por que razão poderá ter um balanceador de carga interno

Azure interno carregar balanceamento (ILB) fornece balanceamento de carga entre máquinas virtuais que se encontram dentro de um serviço na nuvem ou uma rede virtual com um âmbito regional. Para obter informações sobre a configuração de redes virtuais com um âmbito regional e de utilização, consulte o artigo [Regionais redes virtuais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) no blogue do Azure. Redes virtuais existentes que tenham sido configuradas para um grupo de afinidade não é possível utilizar ILB.

ILB ativa os seguintes tipos de balanceamento de carga em:

- Dentro de um serviço na nuvem, a partir do máquinas virtuais para um conjunto de máquinas virtuais que se encontram dentro do serviço na nuvem mesmo (consulte figura 1).
- Dentro de uma rede virtual, a partir do máquinas virtuais na rede virtual para um conjunto de máquinas virtuais que se encontram dentro do serviço na nuvem mesmo a virtual da rede (consulte figura 2).
- Para uma rede virtual cruzada local, de computadores no local para um conjunto de máquinas virtuais que se encontram dentro do serviço na nuvem mesmo a virtual da rede (consulte figura 3).
- Acesso à Internet, várias camadas aplicações na qual as camadas de segurança não são expostos à Internet mas exigem início de balanceamento de carga para o tráfego da camada de acesso à Internet.
- Balanceamento de carga para aplicações LOB alojadas no Azure sem necessidade de software ou hardware do Balanceador de carga adicional. Incluindo servidores no local no conjunto de computadores cujo tráfego seja carga distribuído.

## <a name="internet-facing-multi-tier-applications"></a>Internet opostas aplicações com várias camadas


A camada web tem pontos finais de opostas Internet para os clientes da Internet e faz parte de um conjunto de balanceamento de carga. O Balanceador de carga distribuir tráfego recebido a partir dos clientes web para a porta TCP 443 (HTTPS) para os servidores de web.

Os servidores de base de dados estão atrás de um ponto final ILB que utilizar os servidores web para o armazenamento. Ponto final, qual o tráfego é balanceada pelos servidores de base de dados no conjunto de ILB de carga de balanceamento deste carga de serviço de base de dados.

A imagem seguinte mostra a Internet opostas várias camada aplicação dentro do serviço na nuvem mesmo.

Figura 1 - Internet opostas aplicação com várias camadas

![Interno balanceamento de carga serviço em nuvem único](./media/load-balancer-internal-overview/IC736321.png)

Outra utilização possível para uma aplicação de várias camada é quando o ILB implementada para um serviço em nuvem diferente daquele consumir o serviço para o ILB.

Serviços em nuvem utilizando a mesma rede virtual terá acesso para o ponto final ILB.

Figura 2 mostra web front-end servidores são num serviço de nuvem diferente a partir da base de dados back-end e utilizar o ponto final ILB dentro da mesma rede virtual.

![Interno balanceamento de carga entre serviços em nuvem](./media/load-balancer-internal-overview/IC744147.png)

Figura 2 - servidores front-end num serviço de nuvem diferente

## <a name="intranet-line-of-business-applications"></a>Linha de intranet de aplicações empresariais

Tráfego de clientes na rede no local transmite balanceamento de carga o conjunto de servidores LOB utilizando a ligação VPN à rede Azure.

O computador cliente terá acesso a um endereço IP do serviço de Azure VPN utilizando o ponto para o site VPN. Permite a utilização da aplicação de LOB alojada atrás do ponto final ILB.

![Balanceamento de carga interna utilizando o ponto para o site VPN](./media/load-balancer-internal-overview/IC744148.png)

Figura 3 - aplicações LOB alojadas atrás do ponto final LB

Outro cenário para LOB deve ter uma VPN do site para site à rede virtual onde o ponto final ILB está configurado. Isto permite que o tráfego de rede no local a ser encaminhado para o ponto final ILB.

![Balanceamento de carga interna utilizando VPN de sites](./media/load-balancer-internal-overview/IC744150.png)

Figura 4 - encaminhado para o ponto final ILB o tráfego de rede no local


## <a name="next-steps"></a>Próximos passos

[Azure suporte de Gestor de recursos para Balanceador de carga do Azure](load-balancer-arm.md)

[Começar a configurar uma opostas Balanceador de carga da Internet](load-balancer-get-started-internet-arm-ps.md)

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)

