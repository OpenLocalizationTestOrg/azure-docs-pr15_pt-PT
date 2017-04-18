<properties
    pageTitle="O que fazer em caso de uma interrupção de serviço Azure que afetam redes virtuais Azure | Microsoft Azure"
    description="Saiba o que fazer em caso de uma interrupção de serviço Azure que afetam redes virtuais Azure."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Rede virtual – continuidade do negócio

##<a name="overview"></a>Descrição geral

Uma rede Virtual (VNet) é uma representação lógica da sua rede na nuvem. Permite-lhe definir o seu próprio espaço de endereços IP privado e segmentar a rede em sub-redes. VNets funciona como um limite de confiança para alojar o seu recursos cluster como máquinas virtuais do Azure e serviços em nuvem (funções web/trabalho). Um VNet permite direta comunicações de IP privada entre os recursos hospedados no mesmo. Também pode ser associada uma rede Virtual a uma rede no local através de uma das opções híbrido como um VPN Gateway ou ExpressRoute.
 
É criada uma VNet no âmbito de uma região. Pode criar VNets com o mesmo espaço de endereços em dois diferentes regiões (ou seja, Oriente-nos e oeste-nos mas não consigo ligá-las umas às outras diretamente). 

##<a name="business-continuity"></a>Continuidade de negócios

Podem existir várias maneiras diferentes que a aplicação foi interrompida. Uma região determinada pode ser cortada completamente devido a uma falhas natural ou uma falhas parcial devido a uma falha de vários dispositivos/serviços. O impacto no serviço VNet é diferente em cada uma das seguintes situações.

**P: o que deve fazer em caso de uma falha de toda a região? Isto é, se uma região é completamente corte devido a uma falhas natural? O que acontece às redes virtuais alojado na região?**

R: a rede Virtual e recursos na região afectado permanece inacessível durante o período de tempo a interrupção do serviço.

![Diagrama de rede Virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: o que posso para criar a mesma rede Virtual numa região diferente?**

R: virtual rede (VNet) é bastante lightweight recurso. Pode invocar Azure APIs para criar um VNet com o mesmo espaço de endereços numa região diferente. Para criar o ambiente do mesmo que foi presente na região afetado, tem de efetuar chamadas de API para implementar novamente o seu serviços em nuvem (funções web/trabalho) e máquinas virtuais que tinha. Também terá giratório para cima um Gateway VPN e ligar a sua rede no local se tinha conectividade no local (como uma versão híbrida).

As instruções para criar um VNet forem encontradas [aqui](./virtual-networks-create-vnet-arm-pportal.md). 

**P: uma réplica de um VNet numa região determinado pode ser recriada na região outra antecedência?**

R: Sim, pode criar dois VNets com o mesmo espaço de endereços IP privado e recursos nas duas regiões diferentes antecedência. Se um cliente estava a alojar internet opostas serviços na VNet, que poderiam definiram configurar o Gestor de tráfego para geo-encaminhar tráfego para o região que está ativo. No entanto, um cliente não consegue ligar duas VNets com o mesmo espaço de endereços à sua rede no local, como seria fazer com que o encaminhamento de problemas. No momento de um falhas e perda de um VNet numa região, um cliente pode ligar a outras VNet na região disponível com correspondência de espaço de endereços à sua rede no local.

As instruções para criar um VNet forem encontradas [aqui](./virtual-networks-create-vnet-arm-pportal.md).
