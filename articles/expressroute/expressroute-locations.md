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

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365 e CRM Online** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Suportada | Suportada | Melbourne, Sidney |
| **[Aryaka redes]( http://www.aryaka.com/)** | Suportada | Suportada | Amsterdam, silício do vale do, Singapura, Tóquio, Washington Centro de dados |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Suportada | Suportada | Amsterdam, Chicago, Dallas, Londres, silício do vale do, Singapura, Sidney, Washington Centro de dados |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Suportada | Suportada | Amsterdam, RAE de Hong Kong, Londres, silício do vale do, Singapura, Sidney, Tóquio, Washington Centro de dados |
|**CenturyLink** | Brevemente | Brevemente| Silicon Valley |
|**China Telecom Global** | Suportada | Não é suportado | RAE de Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Suportada | Brevemente | Dallas, Setúbal + Évora |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Suportada | Suportada | Amsterdam, Dublin, Londres, Tóquio |
| **Comcast** | Suportada | Suportada | Chicago, Silicon Valley, Washington Centro de dados |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Suportada | Suportada | Lamego | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Suportada | Suportada | Amsterdam, Aveiro, Chicago, Dallas, RAE de Hong Kong, Londres, Lamego, Melbourne, Nova Iorque, Osaka, Faro +, são Paulo, Seattle, silício do vale do, Singapura, Sidney, Tóquio, Évora, Washington Centro de dados |
| **euNetworks** |  Suportada | Suportada | Amsterdam |
| **GÉANT** | Suportada | Suportada | Amsterdam |
| **[Internet iniciativa Japão Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Suportada | Suportada | Osaka, Tóquio |
| **[InterCloud]( https://www.intercloud.com/)** | Suportada | Suportada | Centro de dados do Amsterdam, Londres, Singapura, Washington |
| **Ligar Internet Solutions - na nuvem** | Suportada | Suportada | Amsterdam, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Suportada | Suportada | Faro Amsterdam, Londres, |
| **Jisc** | Suportada | Suportada | Londres | 
| **[Comunicações de nível 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Suportada | Suportada | Amsterdam, Chicago, Dallas, Las Vegas +, Londres, Seattle, silício do vale do, Washington Centro de dados |
| **Megaport** | Suportada | Suportada | Dallas, RAE de Hong Kong, Las Vegas, Lamego, Melbourne, Nova Iorque, Seattle, Singapura, Sidney, Washington Centro de dados |
| **TITÂNIO** | Suportada | Suportada | Londres |
| **Dados de geração seguintes** | Brevemente | Brevemente | Newport(Wales) + |
| **NEXTDC** | Suportada | Suportada | Melbourne, Sidney |
| **Comunicações NTT** | Suportada | Suportada | Osaka Londres, Lamego, Tóquio |
| **[Cor de laranja]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Suportada | Suportada | Amsterdam, RAE de Hong Kong, Londres, silício do vale do, Singapura, Sidney, Washington Centro de dados |
| **Limitado PCCW Global** | Suportada | Suportada | RAE de Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Suportada | Suportada | Singapura |
| **Softbank** | Suportada | Suportada | Osaka, Tóquio | 
| **[Comunicações Tata](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Suportada | Suportada | Mumbai no Amsterdam, Chennai, RAE de Hong Kong, Londres, silício do vale do, Singapura, Washington Cc |
| **[Grupo TeleCity]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Suportada | Suportada | Amsterdam, Dublin, Londres |
| **Telefonica** | Suportada | Suportada | São Paulo |
| **Telenor** | Suportada | Suportada | Amsterdam, Londres |
| **[Telecom Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Suportada | Suportada | Melbourne, Sidney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Suportada | Suportada | Amsterdam, RAE de Hong Kong, Londres, silício do vale do, Singapura, Sidney, Tóquio, Washington Centro de dados |
| **Vodafone** | Suportada | Não é suportado | Londres | 
| **[Grupo Zayo]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Suportada | Suportada | Chicago, Lamego, Nova Iorque Silicon Valley, Évora, Washington Centro de dados |

 **+**indica brevemente

### <a name="national-cloud-environments"></a>Ambientes nacionais na nuvem

#### <a name="us-government-cloud"></a>Nuvem de Governo dos EUA

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Suportada | Suportada | Cc Chicago, Washington |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Suportada | Suportada | Chicago, Dallas, Nova Iorque, Washington Centro de dados |
| **[Comunicações de nível 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Suportada | Suportada | Chicago, Nova Iorque +, Washington Centro de dados |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Suportada | Suportada | Chicago, Dallas + Nova Iorque, Washington Centro de dados |

#### <a name="china"></a>China

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Suportada | Não é suportado | Pequim, Xangai|
Para saber mais, consulte o artigo [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Alemanha

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Suportada | Não é suportado | Berlim +, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Suportada | Não é suportado | Frankfurt|
| **abrigo "e"** | Suportada | Não é suportado | Berlim|
| **Interxion** | Suportada | Não é suportado | Frankfurt|

## <a name="nonpartners"></a>Conectividade através de fornecedores de serviço não listados

Se o seu fornecedor de conectividade não estiver listado em secções anteriores, ainda pode criar uma ligação.

- Selecione com o seu fornecedor de conectividade para ver se estão ligados a quaisquer do intercâmbio de informações na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizadas pelos fornecedores do exchange. Vários fornecedores de conectividade já estiver ligados a trocas de Ethernet.

    - [Nuvem de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Que o seu fornecedor de conectividade expandir a sua rede para uma localização peering à escolha.
    - Certifique-se de que seu fornecedor de conectividade expande a conectividade de uma forma altamente disponível para que não existem única pontos de falha.
- Ordem de um circuito ExpressRoute com o exchange o seu fornecedor de conectividade para ligar à Microsoft.
    - Siga os passos no artigo [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

|**Fornecedor de conectividade**|**Exchange**|**Localizações**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapura|
|**Comunicações Alasca**|Equinix|Lisboa|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|Nova Iorque, Washington Centro de dados|
|**[Comunicações XO](http://www.xo.com/)**|Equinix|Silicon Valley|


## <a name="expressroute-system-integrators"></a>ExpressRoute integradores

Activar a conectividade privada para corresponder às suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um das integradores indicadas na tabela seguinte para ajudá-lo com a ativação para ExpressRoute.

|**Integrador de sistema**|**Continente**|
|---|---|
|**[Inc Avanade.](http://www.avanade.com/)**| Países da Ásia, Europa, dos e.u.a. |
|**[Soluções de DotNet](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|-NOS|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Países da Ásia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | -NOS |
|**[Liderança de projeto](http://www.projectleadership.net/azure)** | -NOS |

## <a name="next-steps"></a>Próximos passos

- Para mais informações sobre ExpressRoute, consulte as [ExpressRoute FAQ](expressroute-faqs.md).
- Certifique-se de que todos os pré-requisitos forem cumpridos. Consulte o artigo [ExpressRoute pré-requisitos](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
