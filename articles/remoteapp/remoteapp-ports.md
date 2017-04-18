
<properties
    pageTitle="Lista de portas e URLs à lista branca para Azure RemoteApp implementado na rede virtual cliente | Microsoft Azure"
    description="Saiba quais as portas e URLs terá de configurar para a comunicação através de Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Lista de portas e URLs para permitir o acesso do Azure RemoteApp implementado no cliente Rede Virtual 

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

A seguinte se aplica ao Azure RemoteApp uma coleção de nuvem ou híbrido se estiver a implementá-la numa rede virtual (VNET). Para mais informações sobre redes virtuais, leia [Virtual descrição geral da rede](../virtual-network/virtual-networks-overview.md). Se tiver criado um grupo de segurança de rede (NSG) restringir o tráfego para os recursos da rede virtual que escolheu para RemoteApp do Azure, certifique-se da que seguinte está acessíveis e permitidos através das políticas de segurança da rede virtual. Para mais informações sobre os grupos de segurança de rede, leia [o que é um grupo de segurança da rede? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp sub-rede precisa de aceder a estes pontos finais e URLs: 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.windowsazure.com 
*    https://*RemoteApp.windowsazure.com  
*    https://*.Core.Windows.NET  
*    Saída: TCP: 443, TCP: 10101 10175 
*    Opcional – UDP: 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp clientes precisam de aceder a estes pontos finais e URLs: 

Os clientes posso dizer os ambientes de trabalho, etc. que as pessoas utilizar para ligar a aplicações implementadas na coleção de RemoteApp do Azure de dispositivos.

-  https://telemetry.RemoteApp.windowsazure.com  
-  https://*.RemoteApp.windowsazure.com (são as portas UDP opcionais para este endereço) 
-  https://login.Windows.NET  
-  https://login.microsoftonline.com  
-  https://www.RemoteApp.windowsazure.com 
-  https://*.Core.Windows.NET  
-  Saída: TCP: 443  
-  Opcional - UDP: 3391 