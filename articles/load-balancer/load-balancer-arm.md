<properties
   pageTitle="Azure suporte de Gestor de recursos para Balanceador de carga | Microsoft Azure "
   description="Utilizar o powershell para Balanceador de carga com o Gestor de recursos do Azure. Utilizar modelos para Balanceador de carga"
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


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Utilizar o Azure de Gestor de recursos de suporte com Balanceador de carga Azure

Gestor de recursos do Azure é a arquitetura de gestão preferido nos serviços do Azure. Azure Balanceador de carga pode ser gerido utilizando APIs com base no Gestor de recursos do Azure e ferramentas.

## <a name="concepts"></a>Conceitos

Com o Gestor de recursos, Balanceador de carga Azure contém os seguintes recursos de subordinados:

- Configuração de IP Front-end – um balanceador de carga pode incluir um ou mais front-end endereços IP, caso contrário, conhecidos como um IPs virtual (VIP). Estes endereços IP servem penetração para o tráfego.

- Conjunto de back-end endereços – estes são os endereços IP associados com a máquina virtual rede Interface cartão (NIC) para o qual vai ser distribuída carga.

- Regras de balanceamento de carga – uma propriedade regra de mapas um determinado front-end IP e a combinação de porta a um conjunto de endereços IP de back-end e a combinação de porta. Um balanceador de carga única pode ter vários balanceamento de carga regras. Cada regra é uma combinação de um front-end IP e portas e back-end IP e porta associada ao VMs.

- Sondas – sondas permitem-lhe controlar o estado de funcionamento de instâncias VM. Se uma sonda de estado de funcionamento falhar, a instância VM serão tidos terminar rotação automaticamente.

- Regras de entrada NAT – regras NAT que define o tráfego de entrada a fluir através de parte da frente terminam IP e distribuído ao IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de guia de introdução

Gestor de recursos do Azure permite-lhe aprovisionar as suas aplicações utilizando um modelo de declarativa. Num único modelo, pode implementar os vários serviços juntamente com as respectivas dependências. Utilize o mesmo modelo para repetidamente implementar a aplicação durante a todas as fases do ciclo de vida de aplicação.

Modelos podem incluir as definições para máquinas virtuais, redes virtuais, conjuntos de disponibilidade, Interfaces de rede (NIC), contas de armazenamento, balanceadores de carga, grupos de segurança de rede e IPs público. Com modelos pode criar tudo o que precisa para uma aplicação complexa. O ficheiro de modelo pode ser verificado no sistema de gestão de conteúdo de controlo de versão e colaboração.

[Saiba mais acerca dos modelos](http://go.microsoft.com/fwlink/?LinkId=544798)

[Obter mais informações sobre recursos de rede](../virtual-network/resource-groups-networking.md)

Modelos de guia de introdução utilizando Balanceador de carga Azure podem encontrar num [repositório de GitHub](https://github.com/Azure/azure-quickstart-templates) hospedagem de um conjunto de modelos da Comunidade gerado.

Exemplos de modelos:

- [2 VMs numa Balanceador de carga e as regras de balanceamento de carga](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 VMs num VNET com um balanceador de carga interno e Balanceador de carga de regras](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 VMs num Balanceador de carga e configurar regras NAT a LB](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurar o Azure Balanceador de carga com uma PowerShell ou um clip

Introdução ao cmdlets, ferramentas de linha de comandos e REST APIs Gestor de recursos do Azure

- [Cmdlets de funcionamento em rede do Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) podem ser utilizados para criar um balanceador de carga.
- [Como criar um balanceador de carga utilizando o Gestor de recursos do Azure](load-balancer-get-started-ilb-arm-ps.md)
- [Utilizar o clip Azure com gestão de recursos Azure](../xplat-cli-azure-resource-manager.md)
- [APIs do resto do Balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>Próximos passos

Também pode [começar a criar um opostas Balanceador de carga da Internet](load-balancer-get-started-internet-arm-ps.md) e configurar qual o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede de Balanceador de carga específica.

Saiba como gerir [definições de tempo limite do TCP idle para um balanceador de carga](load-balancer-tcp-idle-timeout.md). O que é importante quando necessita de aplicação manter as ligações vivo para servidores atrás de um balanceador de carga.
