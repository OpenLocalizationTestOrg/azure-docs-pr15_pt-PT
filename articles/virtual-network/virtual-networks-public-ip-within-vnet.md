<properties 
   pageTitle="Como utilizar endereços IP públicos numa rede virtual"
   description="Saiba como configurar uma rede virtual para utilizar endereços IP públicos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Espaço de endereços IP público numa rede Virtual (VNet)

Redes virtuais (VNets) podem conter públicos e privados espaços de endereços IP (RFC 1918 blocos de endereço). Quando adiciona um intervalo de endereço IP público, será tratado como parte do espaço de endereços VNet IP privado que só está acessível a partir de VNet, VNets interligados e a partir da sua localização no local.

A imagem abaixo mostra um VNet que inclua espaços de endereços IP públicos e privados.

![Endereço IP público Conceptual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Como posso adicionar um intervalo de endereço IP público?

Adicionar um intervalo de endereço IP público da mesma forma que seria necessário adicionar um intervalo de endereços IP privado; utilizando um ficheiro de *netcfg* ou ao adicionar a configuração no [portal do Azure](http://portal.azure.com). Pode adicionar um intervalo de endereço IP público quando criar o seu VNet ou pode voltar atrás e adicioná-la mais tarde. O exemplo abaixo apresenta públicos e privados espaços de endereços IP configurados no VNet mesmo.

![Endereço IP público no Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Existem algumas limitações?

Existem alguns intervalos de endereços IP que não são permitidos:

- 224.0.0.0/4 (Multicast)

- 255.255.255.255/32 (difusão)

- 127.0.0.0/8 (loopback)

- 169.254.0.0/16 (local de ligação)

- 168.63.129.16/32 (DNS interno)

## <a name="next-steps"></a>Próximos passos

[Como gerir servidores DNS utilizados por um VNet](virtual-networks-manage-dns-in-vnet.md)