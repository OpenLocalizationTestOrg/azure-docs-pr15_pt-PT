<properties
   pageTitle="Guia de resolução de problemas de ExpressRoute: introdução ARP tabelas | Microsoft Azure"
   description="Esta página disponibiliza instruções para obter o ARP tabelas para um circuito ExpressRoute."
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

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>Guia de resolução de problemas de ExpressRoute: introdução ARP tabelas no modelo de implementação clássico

> [AZURE.SELECTOR]
[PowerShell - Gestor de recursos](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - clássico](expressroute-troubleshooting-arp-classic.md)

Este artigo explica os passos de obtenção as tabelas de resolução ARP (Address Protocol) para o seu circuito Azure ExpressRoute.

>[AZURE.IMPORTANT] Este documento destina-se para o ajudar a diagnosticar e corrigir problemas de simples. Não se destina a ser substituição para suporte da Microsoft. Se não é possível resolver o problema utilizando as seguintes orientações, abra um pedido de suporte com a [Ajuda do Microsoft Azure + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabelas resolução ARP (Protocol) e ARP de endereços
ARP é um protocolo de camada 2 que está definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é utilizado para mapear um endereço de Ethernet (endereço MAC) para um endereço IP.

Uma tabela de ARP fornece um mapeamento do endereço IPv4 e endereço do MAC para um determinado efectuado. A tabela ARP para um efectuado de circuito ExpressRoute fornece as seguintes informações para cada interface (principal e secundário):

1. Mapeamento de um endereço IP de interface router no local para um endereço MAC
2. Mapeamento de um endereço IP de interface router ExpressRoute para um endereço MAC
3. A idade do mapeamento

As tabelas ARP podem ajudar com validar camada 2 configuração e com a resolução de problemas de conectividade de camada 2 básicos.

Segue-se um exemplo de uma tabela de ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A secção seguinte fornece informações sobre como ver as tabelas de ARP que são visualizadas pelos routers de limite da ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Pré-requisitos para utilizar tabelas ARP

Certifique-se de que tem o seguinte procedimento antes de continuar:

 - Circuito ExpressRoute válido que esteja configurado com efectuado pelo menos uma. O circuito tem de ser configurado totalmente pelo fornecedor de conectividade. (Ou o seu fornecedor de conectividade) tem de configurar pelo menos um da peerings (Azure privado, Azure público em ou Microsoft) neste circuito.

 - Intervalos de endereços IP utilizados para configurar o peerings (público privado, Azure Azure e Microsoft). Reveja os exemplos de atribuição de endereço IP na [página de requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para ficar a saber como os endereços IP são mapeados para interfaces no seu aise e no lado ExpressRoute. Pode obter informações sobre a configuração peering ao rever a [página de configuração de efectuado ExpressRoute](expressroute-howto-routing-classic.md).

 - Informações a partir do seu fornecedor de equipa ou conectividade de rede sobre os endereços de MAC das interfaces que são utilizados com estes endereços IP.

 - O módulo Windows PowerShell mais recente para Azure (versão 1.50 ou posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelas ARP para sua circuito ExpressRoute
Esta secção fornece instruções sobre como ver as tabelas de ARP para cada tipo de efectuado utilizando o PowerShell. Antes de continuar, que ou fornecedor de conectividade tem de configurar o efectuado. Cada circuito tem dois caminhos (principais e secundários). Pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para efectuado privado Azure
O cmdlet seguinte fornece o ARP tabelas para efectuado privado Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Segue-se a saída de exemplo para uma das formas de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para efectuado público Azure:
O cmdlet seguinte fornece o ARP tabelas para efectuado público Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Segue-se a saída de exemplo para uma das formas de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Segue-se a saída de exemplo para uma das formas de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para Microsoft efectuado
O cmdlet seguinte fornece o ARP tabelas para o Microsoft efectuado:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Exemplo de resultado é mostrado abaixo para uma das formas de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como utilizar estas informações
A tabela ARP de um efectuado pode ser utilizada para validar conectividade e de configuração da camada 2. Esta secção fornece uma descrição geral de como tabelas ARP Procurar em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela de ARP quando um circuito está um Estado operacional (esperado)

 - A tabela ARP tem uma entrada para o lado no local com um endereço IP e MAC válido e uma entrada semelhante para o lado da Microsoft.
 - O último octeto do endereço IP no local é sempre um número ímpar.
 - O último octeto do endereço IP do Microsoft é sempre um número par.
 - O mesmo endereço MAC aparece do lado do Microsoft para todos os três peerings (principal/secundário).


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela de ARP quando tem no local ou ao lado do fornecedor de conectividade tem problemas

 Aparece apenas uma entrada na tabela ARP. Mostra o mapeamento entre o endereço MAC e o endereço IP que é utilizado no lado Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Se tiver um problema da seguinte forma, abra um pedido de suporte com o seu fornecedor de conectividade para resolver a situação.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela de ARP ao lado do Microsoft tem problemas

 - Não verá uma tabela de ARP apresentada para um efectuado se existem problemas no lado Microsoft.
 -  Abra um pedido de suporte com a [Ajuda do Microsoft Azure + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que tem um problema com a conectividade de camada 2.

## <a name="next-steps"></a>Próximos passos

 - Valide Layer 3 configurações para sua circuito ExpressRoute:
     - Obter uma rota de resumo para determinar o estado de sessões BGP.
     - Obter uma tabela de encaminhar para determinar quais prefixos são anunciados ao longo ExpressRoute.
 - Valide a transferência de dados ao rever e reduzir bytes.
 - Abra um pedido de suporte com a [Ajuda do Microsoft Azure + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda estiver a ter problemas.
