<properties 
   pageTitle="Guia de resolução de problemas de ExpressRoute - introdução ARP tabelas | Microsoft Azure"
   description="Esta página fornece instruções sobre como obter o ARP tabelas para um circuito ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="ganesr"/>

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Guia de resolução de problemas de ExpressRoute - tabelas de ARP introdução no modelo de implementação de Gestor de recursos

> [AZURE.SELECTOR]
[PowerShell - Gestor de recursos](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - clássico](expressroute-troubleshooting-arp-classic.md)

Este artigo explica os passos para saber as tabelas de ARP para sua circuito ExpressRoute. 

>[AZURE.IMPORTANT] Este documento destina-se para o ajudar a diagnosticar e corrigir problemas de simples. Não se destina a ser substituição para suporte da Microsoft. Tem de abrir um bilhetes de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , se não conseguir resolver o problema utilizando as orientações descrita abaixo.

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabelas resolução ARP (Protocol) e ARP de endereços
Resolução de protocolo ARP (Address) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é utilizado para mapear o endereço de Ethernet (endereço MAC) com um endereço ip.

A tabela ARP fornece um mapeamento do endereço ipv4 e endereço do MAC para um determinado efectuado. A tabela ARP para um efectuado de circuito ExpressRoute fornece as seguintes informações para cada interface (principal e secundário)

1. Mapeamento de endereço ip de interface de router no local para o endereço do MAC
2. Mapeamento de endereço ip do ExpressRoute router interface para o endereço do MAC
3. O mapeamento idade

ARP tabelas podem ajudar a validar a configuração de camada 2 e resolução de problemas básicos de camadas 2 problemas de conectividade. 

Tabela de exemplo ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A secção seguinte fornece informações sobre como pode ver as tabelas de ARP vistas por routers ExpressRoute limite. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para conhecer as tabelas ARP

Certifique-se de que tem o seguinte procedimento antes de mais do progresso

 - Um circuito ExpressRoute válida configurado com efectuado pelo menos uma. O circuito tem de ser configurado totalmente pelo fornecedor de conectividade. (Ou o seu fornecedor de conectividade) deve ter configurado pelo menos um da peerings (público privado, Azure Azure e Microsoft) neste circuito.
 - Intervalos de endereços IP utilizados para configurar o peerings (público privado, Azure Azure e Microsoft). Reveja os exemplos de atribuição de endereço ip na [página de requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para ficar a saber como os endereços ip são mapeados para interfaces no seu lado e no lado ExpressRoute. Pode obter informações sobre a configuração peering ao rever a [página de configuração de efectuado ExpressRoute](expressroute-howto-routing-arm.md).
 - Informações da sua equipa de rede / fornecedor de conectividade nos endereços de MAC das interfaces utilizado com estes endereços IP.
 - Tem de ter o módulo mais recente do PowerShell para Azure (versão mais recente ou 1.50).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obter o ARP tabelas para sua circuito ExpressRoute
Esta secção fornece instruções sobre como pode ver as tabelas de ARP por efectuado através do PowerShell. Ou ao seu fornecedor de conectividade tem de ter configurado efectuado antes de a progredir ainda mais. Cada circuito tem dois caminhos (principais e secundários). Pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para efectuado privado Azure
O cmdlet seguinte fornece o ARP tabelas para efectuado privado Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Exemplo de resultado é mostrado abaixo para uma das formas de

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para efectuado público Azure
O cmdlet seguinte fornece o ARP tabelas para efectuado público Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Exemplo de resultado é mostrado abaixo para uma das formas de

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para Microsoft efectuado
O cmdlet seguinte fornece o ARP tabelas para o Microsoft efectuado

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Exemplo de resultado é mostrado abaixo para uma das formas de

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como utilizar estas informações
A tabela ARP de um efectuado pode ser utilizada para determinar validar conectividade e de configuração da camada 2. Esta secção fornece uma descrição geral do qual serão o aspeto da tabelas ARP em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela de ARP quando um circuito está no estado de funcionamento (estado esperado)

 - A tabela ARP terá uma entrada para o lado no local com um endereço IP válido e o endereço de MAC e uma entrada semelhante para o lado da Microsoft. 
 - O último octeto do endereço ip no local será sempre um número ímpar.
 - O último octeto do endereço ip Microsoft será sempre um número par.
 - O mesmo endereço MAC aparecerá no lado Microsoft para todos os 3 peerings (principais / secundários). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP tabela ', quando no local / lado do fornecedor de conectividade tem problemas

 - Apenas uma entrada vai aparecer na tabela ARP. Isto vai mostrar o mapeamento entre o endereço de MAC e o endereço IP utilizado no lado Microsoft. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Abra um pedido de suporte com o seu fornecedor de conectividade para depurar esses problemas. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela de ARP ao lado de Microsoft tem problemas

 - Não verá uma tabela de ARP apresentada para um efectuado se existem problemas no lado Microsoft. 
 -  Abra um bilhetes de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que tem um problema com a conectividade de camada 2. 

## <a name="next-steps"></a>Próximos passos

 - Validar Layer 3 configurações para sua circuito ExpressRoute
     - Obter a rota de resumo para determinar o estado de sessões BGP 
     - Obter encaminhar tabela para determinar quais prefixos são anunciados ao longo ExpressRoute
 - Validar a transferência de dados ao rever bytes numa / saída
 - Abra um bilhetes de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , se ainda estiver a ter problemas.
