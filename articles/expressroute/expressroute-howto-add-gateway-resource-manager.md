<properties
   pageTitle="Adicionar um gateway VNet a uma rede virtual para ExpressRoute utilizando o Gestor de recursos e PowerShell | Microsoft Azure"
   description="Este artigo explica o adição de um gateway Vnet para um já criado VNet de Gestor de recursos para ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurar um gateway de rede virtual para ExpressRoute utilizando o Gestor de recursos e PowerShell


> [AZURE.SELECTOR]
- [PowerShell - Gestor de recursos](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-add-gateway-classic.md)


Este artigo irá guiá-lo através dos passos para adicionar, redimensionar e remover um gateway de rede virtual (VNet) para um VNet pré-existentes. Os passos para esta configuração são especificamente para VNets que foram criadas utilizando o **modelo de implementação do Gestor de recursos** e que serão ser utilizada numa configuração ExpressRoute. 

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Antes de começar

Certifique-se de que instalou os cmdlets do Azure PowerShell necessários para esta configuração (1.0.2 ou posterior). Se ainda não instalou os cmdlets, terá de fazê-lo antes de iniciar os passos de configuração. Para mais informações sobre como instalar o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>Próximos passos

Depois de ter criado o gateway VNet, pode ligar o seu VNet para um circuito ExpressRoute. Consulte o artigo [ligação uma rede Virtual para um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).
