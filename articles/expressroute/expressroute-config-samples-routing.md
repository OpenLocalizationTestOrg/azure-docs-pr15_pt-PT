<properties
   pageTitle="Exemplos de configuração do ExpressRoute cliente router | Microsoft Azure"
   description="Esta página disponibiliza amostras de configuração do router para routers Cisco e zimbro."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Exemplos de configuração do router para configurar e gerir encaminhamento

Esta página disponibiliza interface e encaminhamento amostras de configuração para Cisco IOS-XE e zimbro MX routers série. Estes têm como objetivo amostras para obter orientações apenas e não pode ser utilizados tal como está. Pode trabalhar com o seu fornecedor para obter configurações adequadas para a sua rede. 

>[AZURE.IMPORTANT] Exemplos nesta página destinam-se para ser puramente para obter orientações. Tem de trabalhar com a equipa de vendas / técnico do seu fornecedor e a equipa de rede para obter configurações adequadas para corresponder às suas necessidades. A Microsoft não suporta problemas relacionados com configurações listadas nesta página. Terá de contactar o fornecedor do seu dispositivo para problemas de suporte.

Exemplos de configuração de router abaixo aplicam a todos os peerings. Reveja [ExpressRoute peerings](expressroute-circuit-peerings.md) e [requisitos de encaminhamento de ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre o encaminhamento.

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE com base routers

Os exemplos nesta secção aplicam-se para qualquer router com a família SO IOS XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. a configurar interfaces e interfaces subpastas

Necessitar de uma interface sub por efectuado em cada router que ligar à Microsoft. Uma interface sub pode ser identificada com um ID de VLAN ou um par empilhado de IDs de VLAN e um endereço IP.

#### <a name="dot1q-interface-definition"></a>Definição de interface Dot1Q

Este exemplo fornece a definição de sub-interface para uma sub-interface com um ID de VLAN único. O ID de VLAN é exclusivo por efectuado. O último octeto IPv4 do endereço de correio será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>Definição de interface QinQ

Este exemplo fornece a definição de interface subpastas de uma sub-interface com um ID de VLAN duas. O ID de VLAN externa (s-tag), se for utilizado se mantém o mesmo ao longo de todo os peerings. O ID de VLAN interna (c tag) é exclusivo por efectuado. O último octeto IPv4 do endereço de correio será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. a configurar eBGP sessões

Tem de configurar uma sessão BGP com a Microsoft para cada efectuado. O exemplo abaixo permite-lhe configurar uma sessão BGP com a Microsoft. Se o endereço de IPv4 que utilizou para a interface de sub foi a.b.c. d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço de IPv4 o vizinho BGP será sempre um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurar prefixos para anunciada sobre a sessão BGP

Pode configurar o seu router para anunciar prefixos selecionados para a Microsoft. Pode fazê-lo a utilizar o exemplo abaixo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. conjunto mapas do encaminhar

Pode utilizar mapas encaminhar e prefixo listas a filtro prefixos propagados na sua rede. Pode utilizar o exemplo abaixo para efetuar a tarefa. Certifique-se de que já configurou o prefixo apropriado listas.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Routers de série zimbro MX 

Os exemplos nesta secção aplicam-se para qualquer routers de série zimbro MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. a configurar interfaces e interfaces subpastas

#### <a name="dot1q-interface-definition"></a>Definição de interface Dot1Q

Este exemplo fornece a definição de sub-interface para uma sub-interface com um ID de VLAN único. O ID de VLAN é exclusivo por efectuado. O último octeto IPv4 do endereço de correio será sempre um número ímpar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>Definição de interface QinQ

Este exemplo fornece a definição de interface subpastas de uma sub-interface com um ID de VLAN duas. O ID de VLAN externa (s-tag), se for utilizado se mantém o mesmo ao longo de todo os peerings. O ID de VLAN interna (c tag) é exclusivo por efectuado. O último octeto IPv4 do endereço de correio será sempre um número ímpar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. a configurar eBGP sessões

Tem de configurar uma sessão BGP com a Microsoft para cada efectuado. O exemplo abaixo permite-lhe configurar uma sessão BGP com a Microsoft. Se o endereço de IPv4 que utilizou para a interface de sub foi a.b.c. d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço de IPv4 o vizinho BGP será sempre um número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurar prefixos para anunciada sobre a sessão BGP

Pode configurar o seu router para anunciar prefixos selecionados para a Microsoft. Pode fazê-lo a utilizar o exemplo abaixo.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. conjunto mapas do encaminhar

Pode utilizar mapas encaminhar e prefixo listas a filtro prefixos propagados na sua rede. Pode utilizar o exemplo abaixo para efetuar a tarefa. Certifique-se de que já configurou o prefixo apropriado listas.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Próximos passos

Consulte as [ExpressRoute FAQ](expressroute-faqs.md) para obter mais detalhes.
