<properties 
   pageTitle="Informações sobre os gateways de rede virtual ExpressRoute | Microsoft Azure"
   description="Saiba mais sobre os gateways de rede virtual para ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>Sobre os gateways de rede virtual para ExpressRoute


Um gateway de rede virtual é utilizado para enviar tráfego de rede entre o Azure redes virtuais e no local localizações. Quando configurar uma ligação de ExpressRoute, tem de criar e configurar um gateway de rede virtual e uma ligação de gateway rede virtual.

Quando cria um gateway de rede virtual, que especificar várias definições. As definições de necessária indica se o gateway será utilizado para o tráfego de ExpressRoute ou VPN do Site para o Site. No modelo de implementação do Gestor de recursos, a definição é '-GatewayType'.

Quando o tráfego de rede é enviado numa ligação privada dedicada, pode utilizar o tipo de gateway 'ExpressRoute'. Isto é também conhecido como um gateway ExpressRoute. Quando o tráfego de rede é enviado encriptado através da Internet pública, pode utilizar o tipo de gateway 'Vpn'. Isto é referido como um gateway VPN. Site para Site ponto para o Site e ligações VNet para VNet todas as utilizam um gateway VPN. 

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter uma rede virtual gateway que utiliza - GatewayType Vpn e que utiliza - GatewayType ExpressRoute. Este artigo foca-se no gateway ExpressRoute rede virtual.

## <a name="gwsku"></a>SKUs do gateway

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se pretende actualizar o gateway para um gateway mais eficazes SKU, na maioria dos casos pode utilizar o cmdlet do PowerShell 'Redimensionamento AzureRmVirtualNetworkGateway'. Isto irá funcionar para actualizações para padrão e reversa SKUs. No entanto, para atualizar para o SKU UltraPerformance, terá de recriar o gateway.

###  <a name="aggthroughput"></a>Estimado débito agregar pelo gateway SKU


A tabela seguinte mostra os tipos de gateway e o débito agregação estimado. Esta tabela aplica-se para o Gestor de recursos e de modelos de implementação clássica.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>Cmdlets REST APIs e PowerShell

Para recursos de suporte adicionais e requisitos de sintaxe específico ao utilizar REST APIs e os cmdlets do PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

|**Clássico** | **Gestor de recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Próximos passos

Consulte o artigo [Descrição geral de ExpressRoute](expressroute-introduction.md) para obter mais informações sobre as configurações de ligação disponíveis. 







 
