<properties
   pageTitle="ExpressRoute localizações | Microsoft Azure"
   description="Este artigo fornece uma descrição detalhada das localizações onde são fornecidos serviços e como se ligar à regiões Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>Parceiros de ExpressRoute e efectuadas localizações

As tabelas neste artigo fornecem informações sobre fornecedores de conectividade ExpressRoute, cobertura geográfica ExpressRoute, serviços em nuvem Microsoft suportados através de ExpressRoute e ExpressRoute integradores (SIs).

## <a name="partners"></a>Fornecedores de conectividade de ExpressRoute

ExpressRoute é suportada em todas as regiões Azure e localizações. O mapa seguinte fornece uma lista das regiões Azure e ExpressRoute localizações. ExpressRoute localizações referem-se aos onde Microsoft elementos com vários fornecedores de serviços.

![Mapa de localização][0]

Terá acesso aos serviços Azure através de todas as regiões dentro de uma região geopolítica se estiver ligado a pelo menos uma localização de ExpressRoute dentro de uma região geopolítica. A tabela seguinte fornece um mapa das regiões Azure ExpressRoute localizações dentro de uma região geopolítica.

|**Região geopolítica**|**Regiões Azure**|**ExpressRoute localizações**|
|---|---|---|
|**América do Norte**|Leste dos EUA, EUA Ocidental, Leste dos EUA 2, Central (EUA), Sul Central (EUA), EUA Central da América do Norte, Canadá Central, Canadá leste|Aveiro, Chicago, Dallas, Las Vegas, Lamego, Nova Iorque, Seattle, Cc silício do vale do, Washington, Setúbal +, Localidade Quebec +, Évora|
|**América do Sul**|Sul do Brasil|São Paulo|
|**Europa**|Europa Norte, Ocidental Europa, Reino Unido oeste, Sul Reino Unido|Amsterdam, Dublin, Londres, Newport(Wales) +, Faro|
|**Países da Ásia**|Este asiático, Sudeste asiático|RAE de Hong Kong, Singapura|
|**Japão**|Japan oeste, Leste Japão|Osaka, Tóquio|
|**Austrália**|Austrália Sudeste, Leste Austrália|Melbourne, Sidney|
|**Índia**|Índia oeste, Índia Central, Índia Sul|Chennai, Mumbai|



A tabela abaixo fornece informações sobre regiões e limites do geopolíticos para nuvens nacionais.

|**Região geopolítica**|**Regiões Azure**|**ExpressRoute localizações**|
|---|---|---|---|
|**Nuvem de Governo dos EUA**|US administração pública Iowa, EUA administração pública Virginia|Chicago, Dallas, Nova Iorque, Washington Centro de dados|
|**China**|China norte, China leste|Pequim, Xangai|
|**Alemanha**|Alemanha Central, Alemanha leste|Berlim, Frankfurt|


Conectividade entre as regiões geopolíticas não é suportada no SKU ExpressRoute padrão. É necessário ativar o suplemento de premium ExpressRoute suportar a conectividade global. Conectividade ao ambientes de nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade se essa necessidade surgir.


## <a name="connectivity-provider-locations"></a>Localizações do fornecedor de conectividade

> [AZURE.SELECTOR]
[Localizações pelo fornecedor](expressroute-locations.md#connectivity-provider-locations)
[fornecedores pela localização](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Produção Azure
| **Localização**  | **Fornecedores de serviços** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka redes, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - ligar na nuvem, Interxion, Verizon de comunicações, laranja, Tata comunicações, TeleCity grupo Telenor, de nível 3 |
| **Aveiro** | Equinix |
| **Chennai** | Comunicações Tata |
| **Chicago** | AT & T NetBond, Comcast, Equinix, comunicações de nível 3, Zayo grupo |
| **Dallas** | AT & T NetBond, Cologix, Equinix, nível 3 comunicações, Megaport |
| **Dublin** | Grupo Colt, Telecity |
| **RAE de Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, laranja, PCCW Global limitado, Tata comunicações, Verizon |
| **Londres** | AT & T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - ligar na nuvem, Interxion, Jisc, nível 3 comunicações, Titânio, NTT comunicações, laranja, Tata comunicações, Telecity grupo Telenor, Verizon, Vodafone |
| **Las Vegas** | Nível 3 comunicações +, Megaport
| **Lamego** | CoreSite, Equinix, Megaport, NTT, Zayo grupo |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telecom Corporation |
| **Nova Iorque** | Equinix, Megaport, Zayo grupo |
| **Newport(Wales) +** | Seguinte geração dados + |
| **Setúbal** | Cologix + |
| **Mumbai** | Comunicações Tata |
| **Osaka** | Equinix, Internet iniciativa Japão Inc. - IIJ, NTT comunicações, Softbank |
| **Faro** | Interxion, Equinix + |
| **São Paulo** | Equinix, Telefonica |
| **Lisboa** | Equinix, comunicações de nível 3, Megaport |
| **Silicon Valley** | Redes Aryaka, AT & T NetBond, British Telecom, CenturyLink +, Comcast, Equinix, nível 3 comunicações, laranja, Tata comunicações, Verizon, Zayo grupo |
| **Singapura** | Redes Aryaka, AT & T NetBond, British Telecom, Equinix, InterCloud, Megaport, laranja, SingTel, Tata comunicações, Verizon |
| **Sidney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, laranja, Telecom Corporation, Verizon |
| **Tóquio** | Aryaka redes, British Telecom, Colt, Equinix, Internet iniciativa Japão Inc. - IIJ, NTT comunicações, Softbank, Verizon |
| **Évora** | Cologix, Equinix, Zayo grupo |
| **Centro de dados de Washington** | Redes Aryaka, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, nível 3 comunicações, Megaport, laranja, Tata comunicações, Verizon, Zayo grupo |

 **+**indica brevemente

### <a name="national-cloud-environments"></a>Ambientes nacionais na nuvem

#### <a name="us-government-cloud"></a>Nuvem de Governo dos EUA

| **Localização**  |**Fornecedores de serviços** |
|---------------|--------------------|
| **Chicago** | AT & T NetBond, Equinix, nível 3 comunicações, Verizon |
| **Dallas** |  Equinix, Verizon + |
| **Nova Iorque** | Equinix, nível 3 comunicações +, Verizon |
| **Centro de dados de Washington** | AT & T NetBond, Equinix, nível 3 comunicações, Verizon |

#### <a name="china"></a>China

| **Localização**  | **Fornecedores de serviços** |
|---------------|-----------------------|
| **Pequim** | China Telecom |
| **Xangai** |  China Telecom |
Para obter mais informações, consulte o artigo [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Alemanha

| **Localização**  | **Fornecedores de serviços** |
|---------------|-----------------------|
| **Berlim** | Colt + abrigo "e" |
| **Frankfurt** | Colt, Equinix, Interxion |

## <a name="nonpartners"></a>Conectividade através de fornecedores de serviço não listados

Se o seu fornecedor de conectividade não estiver listado em secções anteriores, ainda pode criar uma ligação.

- Selecione com o seu fornecedor de conectividade para ver se estão ligados a quaisquer do intercâmbio de informações na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizadas pelos fornecedores do exchange. Vários fornecedores de conectividade já estiver ligados a trocas de Ethernet.

    - [Nuvem de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Que o seu fornecedor de conectividade expandir a sua rede para uma localização peering à escolha.
    - Certifique-se de que seu fornecedor de conectividade expande a conectividade de uma forma altamente disponível para que não existem única pontos de falha.
- Ordem de um circuito ExpressRoute com o exchange o seu fornecedor de conectividade para ligar à Microsoft.
    - Siga os passos no artigo [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

|**Localização**|**Exchange**|**Fornecedores de conectividade**|
|-------------|------------|-------------------------|
| **Nova Iorque** | Equinix | Lightower |
| **Lisboa** | Equinix | Comunicações Alasca |
| **Silicon Valley** | Equinix | Comunicações XO |
| **Singapura** | Equinix | 1CLOUDSTAR |
| **Centro de dados de Washington** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute integradores

Activar a conectividade privada para corresponder às suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um das integradores indicadas na tabela seguinte para ajudá-lo com a ativação para ExpressRoute.

|**Continente**|**Integradores**|
|-------------|---------------------|
| **Países da Ásia** | Avanade Inc., OneAs1a|
| **Europa** | Avanade Inc., Dotnet soluções|
| **-NOS** | Avanade Inc, Equinix Professional Services, Perficient, liderança de projeto|

## <a name="next-steps"></a>Próximos passos

- Para mais informações sobre ExpressRoute, consulte as [ExpressRoute FAQ](expressroute-faqs.md).
- Certifique-se de que todos os pré-requisitos forem cumpridos. Consulte o artigo [ExpressRoute pré-requisitos](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
