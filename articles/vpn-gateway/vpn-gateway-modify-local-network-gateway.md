<properties
   pageTitle="Modificar prefixos de endereço IP de gateway de rede local e IP do gateway | Microsoft Azure"
   description="Este artigo explica o alterar prefixos de endereços IP para o gateway de rede local"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as definições de gateway de rede local através do PowerShell

Por vezes, alteram as definições para o gateway de rede local AddressPrefix ou GatewayIPAddress. As instruções abaixo irão ajudá-lo modificar as definições de gateway de rede local. Também pode modificar estas definições no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
    
Terá de instalar a versão mais recente dos cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

## <a name="to-modify-ip-address-prefixes"></a>Para modificar prefixos de endereço IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Para modificar o endereço IP do gateway

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximos passos

Pode verificar a sua ligação de gateway. Consulte o artigo [verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).

