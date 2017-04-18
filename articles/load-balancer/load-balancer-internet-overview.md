
<properties
   pageTitle="Internet opostas descrição geral do Balanceador de carga | Microsoft Azure "
   description="Descrição geral para Internet opostas Balanceador de carga e suas funcionalidades. Como um balanceador de carga funciona para Azure utilizando máquinas virtuais e serviços em nuvem."
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


# <a name="internet-facing-load-balancer-overview"></a>Opostas carga balanceador descrição geral da Internet

Balanceador de carga Azure mapas o público IP endereço e número de porta de tráfego de entrada para o privado IP endereço e número de porta da máquina virtual e vice versa para o tráfego de resposta de máquina virtual. Regras de balanceamento de carga permitem-lhe distribuir tipos específicos de tráfego de entre vários máquinas virtuais ou serviços. Por exemplo, pode difundir a carga de tráfego de pedido de web em várias servidores web ou funções da web.

Para um serviço na nuvem que contém as instâncias de funções da web ou funções de trabalho, pode definir um ponto final de público no ficheiro de definição (.csdef) do serviço.

O ficheiro _servicedefinition.csdef_ contém a configuração de ponto final e quando tiver várias instâncias de função para uma implementação de funções web ou de trabalho, o Balanceador de carga serão configuração para o mesmo. A forma de adicionar instâncias a sua implementação nuvem está a mudar a contagem de instância no ficheiro de configuração de serviço (.csfg).

A figura seguinte mostra um ponto final de balanceamento de carga para o tráfego de web encriptadas que é partilhado entre três máquinas virtuais para a porta TCP pública e privada de 443. Estes máquinas virtuais três são num conjunto de balanceamento de carga.

![exemplo do Balanceador de carga público](./media/load-balancer-internet-overview/IC727496.png))

Figura 1 - ponto final de balanceamento de carga para o tráfego de web encriptada

Quando os clientes da Internet enviam pedidos de página web para o endereço IP público do serviço na nuvem na porta TCP 443, Balanceador de carga o Azure distribui os pedidos de entre três máquinas virtuais do conjunto de balanceamento de carga. Para mais informações acerca dos algoritmos Balanceador de carga, consulte a [página de descrição geral do Balanceador de carga](load-balancer-overview.md#load-balancer-features).

Por predefinição, Balanceador de carga de Azure distribuir o tráfego de rede de forma uniforme entre várias instâncias de máquina virtual. Também pode configurar afinidade sessão, para obter mais informações, consulte [o modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [Balanceador de carga interna](load-balancer-internal-overview.md) compreender melhor a Balanceador de carga corresponde a um melhor ajuste para a sua implementação na nuvem.

Também pode [começar a criar um opostas Balanceador de carga da Internet](load-balancer-get-started-internet-arm-ps.md) e configurar qual o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede de Balanceador de carga específica.

Se a aplicação necessitar manter as ligações vivo para servidores atrás de um balanceador de carga, é possível compreender mais sobre [as definições de tempo limite TCP idle para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Irá ajudar a para saber mais sobre o comportamento de ligação idle quando estiver a utilizar o Azure Balanceador de carga.
