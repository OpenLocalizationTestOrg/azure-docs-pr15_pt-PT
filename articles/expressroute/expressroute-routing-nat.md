<properties
   pageTitle="Requisitos de encaminhamento para ExpressRoute | Microsoft Azure"
   description="Esta página fornece os requisitos específicos para configurar e gerir o encaminhamento para ExpressRoute circuitos."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>Requisitos de encaminhamento ExpressRoute  

Para se ligar aos serviços em nuvem Microsoft utilizando ExpressRoute, terá de configurar e gerir o encaminhamento de. Alguns fornecedores de conectividade oferecem a configurar e gerir o encaminhamento de um serviço gerido. Selecione com o seu fornecedor de conectividade para ver se oferecem este serviço. Se não, tem de aderir aos seguintes requisitos. 

Consulte o artigo [circuitos e encaminhamento de domínios](expressroute-circuit-peerings.md) para obter uma descrição das sessões encaminhamento que precisam de ser configurada para facilitar a conectividade.

>[AZURE.NOTE] Microsoft não suporta qualquer protocolos de redundância router (por exemplo, HSRP, VRRP) para as configurações de elevada disponibilidade. Vamos depender de um par de sessões BGP por efectuado para elevada disponibilidade redundante.

## <a name="ip-addresses-used-for-peerings"></a>Endereços IP utilizados para peerings

Tem de reservar alguns blocos de endereços IP para configurar o encaminhamento entre a rede e routers de limite (MSEEs) da Microsoft Enterprise. Esta secção fornece uma lista de requisitos e descreve as regras em relação à como estes endereços IP tem de ser adquiridos e utilizados.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Endereços IP utilizados para efectuado privado Azure

Pode utilizar para configurar o peerings endereços IP privados ou endereços IP públicos. O intervalo de endereço utilizado para configurar rotas não deve sobrepor-se com os intervalos de endereços utilizados para criar redes virtuais no Azure. 

 - Tem de reserva um /29 sub-rede ou duas /30 sub-redes para interfaces de encaminhamento.
 - Podem ser sub-redes utilizados para o encaminhamento endereços IP privados ou endereços IP públicos.
 - As sub-redes tem não entram em conflito com o intervalo reservado pelo cliente para utilização na nuvem da Microsoft.
 - Se um /29 sub-rede é utilizada, irá ser dividida em dois /30 sub-redes. 
     - A primeira /30 sub-rede será utilizada para a ligação principal e o segundo/sub-rede 30 será utilizada para a ligação secundária.
     - Para cada um da /30 sub-redes, tem de utilizar o primeiro endereço IP da /30 sub-rede no seu router. A Microsoft utilizará o segundo endereço IP da /30 sub-rede para configurar uma sessão BGP.
     - Tem de configurar ambas as sessões BGP para os nossos [disponibilidade SLA](https://azure.microsoft.com/support/legal/sla/) seja válido.  

#### <a name="example-for-private-peering"></a>Exemplo de efectuado privada

Se optar por utilizar a.b.c.d/29 para configurar o efectuado, ser dividida em dois /30 sub-redes. No exemplo abaixo, vamos abordar como sub-rede a.b.c.d/29 é utilizada. 

a.b.c.d/29 serão dividir a.b.c.d/30 e a.b.c.d+4/30 e transmitidos para baixo para o Microsoft através de aprovisionamento APIs. Irá utilizar a.b.c.d+1 como o PI VRF para o PE principal e Microsoft irá consumir a.b.c.d+2 como o PI VRF para o MSEE principal. Irá utilizar a.b.c.d+5 como o PI VRF para o PE secundário e serão utilizadas pela Microsoft a.b.c.d+6 como o PI VRF para o MSEE secundário.

Considere a um caso onde selecionar 192.168.100.128/29 para configurar o efectuado privado. 192.168.100.128/29 inclui os endereços de 192.168.100.128 para 192.168.100.135, entre os quais:

- 192.168.100.128/30 serão atribuídas ao link1, com o fornecedor de utilizar 192.168.100.129 e Microsoft utilizando 192.168.100.130.
- 192.168.100.132/30 serão atribuídas ao link2, com o fornecedor de utilizar 192.168.100.133 e Microsoft utilizando 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Endereços IP utilizados para o público Azure e Microsoft efectuado

Tem de utilizar endereços IP públicos que é o proprietário para configurar as sessões BGP. Microsoft têm de confirmar que é o proprietário os endereços IP através de encaminhamento de registos de Internet e registos de encaminhamento de Internet. 

- Tem de utilizar um único/sub-rede 29 ou duas /30 sub-redes para configurar o BGP efectuado para cada efectuado por ExpressRoute circuito (se tiver mais do que um). 
- Se um /29 sub-rede é utilizada, irá ser dividida em dois /30 sub-redes. 
    - A primeira /30 sub-rede será utilizada para a ligação principal e o segundo/sub-rede 30 será utilizada para a ligação secundária.
    - Para cada um da /30 sub-redes, tem de utilizar o primeiro endereço IP da /30 sub-rede no seu router. A Microsoft utilizará o segundo endereço IP da /30 sub-rede para configurar uma sessão BGP.
    - Tem de configurar ambas as sessões BGP para os nossos [disponibilidade SLA](https://azure.microsoft.com/support/legal/sla/) seja válido.

## <a name="public-ip-address-requirement"></a>Requisito de endereço IP público 

### <a name="private-peering"></a>Efectuado privada 

Pode optar por utilizar os endereços IPv4 públicos ou privados para efectuado privado. Fornecemos isolamento de ponto a ponto do tráfego para poder sobrepostos de endereços com outros clientes não não possível em caso de efectuado privado. Não são anunciados estes endereços Internet. 

### <a name="public-peering"></a>Efectuado público

O caminho peering público Azure permite-lhe ligar à alojado no Azure sobre os respetivos endereços de IP públicos de todos os serviços. Estes incluem serviços listados nas [ExpessRoute FAQ](expressroute-faqs.md) e quaisquer serviços alojados pelo fabricantes independentes de software Microsoft Azure. Conectividade ao serviços do Microsoft Azure no efectuado público é sempre iniciada da sua rede sessão na rede da Microsoft. Tem de utilizar endereços IP público para o tráfego destinado a rede da Microsoft.

### <a name="microsoft-peering"></a>Microsoft efectuado

O caminho peering Microsoft permite-lhe ligar aos serviços em nuvem Microsoft que não são suportados através do Azure caminho peering público. A lista dos serviços inclui serviços do Office 365, como o Exchange Online, SharePoint Online, Skype para empresas e CRM Online. Microsoft suporta a conectividade de bidirecional no efectuado a Microsoft. Tráfego destinado a serviços em nuvem da Microsoft tem de utilizar válidos endereços IPv4 do públicos para o mesmo entra a rede do Microsoft.

Certifique-se de que o endereço IP e como número estão registados para si dos registos indicados abaixo.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Endereços IP públicos anunciados para a Microsoft sobre ExpressRoute não devem ser anunciados à Internet. Isto poderá quebrar conectividade com outros serviços da Microsoft. No entanto, poderão ser anunciados endereços IP público utilizados por servidores na sua rede que comunicam com os pontos finais do Office 365 no interior da Microsoft sobre ExpressRoute. 

## <a name="dynamic-route-exchange"></a>Exchange encaminhar dinâmicos

Encaminhamento exchange serão através de protocolo eBGP. Sessões EBGP são estabelecidas entre os MSEEs e nos routers. Autenticação de sessões BGP não é um requisito. Se necessário, pode ser configurado um hash MD5. Consulte o artigo o [Encaminhamento de configurar](expressroute-howto-routing-classic.md) e [circuito aprovisionamento fluxos de trabalho e os Estados dos circuitos](expressroute-workflows.md) para obter informações sobre como configurar sessões BGP.

## <a name="autonomous-system-numbers"></a>Números de sistema autónomos

COMO 12076 serão utilizadas pela Microsoft para Azure público, Azure privado e Microsoft efectuado. Vamos ter reservados ASNs a partir do 65515 65520 para utilização interna. 16 e 32 bits como números são suportadas.

Não existem requisitos à volta de simetria de transferência de dados. Formas de avançar e regressar podem percorrer pares router diferente. Rotas idênticas tem de ser anunciadas a partir de um dos lados através de vários pares de circuito que lhe pertencem. Métricas de rota não são necessários para ser idêntico.

## <a name="route-aggregation-and-prefix-limits"></a>Encaminhar os limites de agregação e o prefixo

Suportamos até 4000 prefixos anunciados-nos através do Azure efectuado privado. Isto pode ser aumentado até 10.000 prefixos se o suplemento de premium ExpressRoute está ativado. Vamos aceitar até 200 prefixos por sessão BGP para Azure público e Microsoft efectuado. 

A sessão BGP será ignorada se o número de prefixos excede o limite. Vamos aceitará rotas predefinidas na privado peering ligação apenas. Fornecedor de tem filtrar rota predefinida e endereços IP privados (RFC 1918) a partir do Azure público e caminhos de efectuado da Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Trânsito e encaminhamento de região de cruz

ExpressRoute não pode ser configurado como routers de trânsito. Terá de depender do seu fornecedor de conectividade para serviços de encaminhamento de trânsito.

## <a name="advertising-default-routes"></a>Rotas predefinidas a publicidade

Rotas predefinidas são permitidas apenas no Azure sessões privados efectuados. Neste caso, podemos irá encaminha todo o tráfego de redes virtual associado à sua rede. Publicidade rotas predefinidas para efectuado privado resultará no caminho da internet do Azure sejam bloqueado. Tem de confiar no seu limite da empresa para encaminhar o tráfego de e para a internet para serviços hospedados no Azure. 

 Para ativar a conectividade com outros serviços Azure e serviços de infraestrutura, tem de Certifique-se de uma das seguintes itens é no local:

 - Efectuado público Azure está ativado para encaminhar tráfego para os pontos finais públicos
 - Utilize o encaminhamento de definidos pelo utilizador para permitir a conectividade de internet para cada sub-rede que exige o ligação à Internet.
 
>[AZURE.NOTE] Publicidade rotas predefinidas irá interromper Windows e outros ativação de licença VM. Siga as instruções [aqui](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) para resolver este problema.

## <a name="support-for-bgp-communities-preview"></a>Suporte para Comunidades BGP (pré-visualização)


Esta secção fornece uma descrição geral de como Comunidades BGP serão utilizadas com ExpressRoute. Microsoft irá anunciar rotas público e caminhos de efectuado Microsoft com rotas etiquetadas com valores de Comunidade adequado. A justificação para fazê-lo e os detalhes nos valores da Comunidade são descritos abaixo. Microsoft, no entanto, não vai respeitar os valores de Comunidade tagged às rotas anunciadas para a Microsoft.

Se estiver a ligar à Microsoft através de ExpressRoute em qualquer uma localização peering dentro de uma região geopolítica, terá acesso a todos os serviços em nuvem da Microsoft em todas as regiões dentro do limite geopolítica. 

Por exemplo, se ligado para a Microsoft na Amsterdam através de ExpressRoute, terá acesso a todos os serviços de nuvem Microsoft alojado no Norte e Europa Ocidental. 

Referem-se para a página [efectuadas localizações e parceiros de ExpressRoute](expressroute-locations.md) para uma lista detalhada das regiões geopolíticas, associados regiões Azure e ExpressRoute correspondente efectuado localizações.

Pode comprar mais do que um ExpressRoute circuito por região geopolítica. Está a ter várias ligações oferece-lhe benefícios significativos sobre a disponibilidade de alta devido a redundância geo. Em casos onde tem vários ExpressRoute circuitos, vai receber o mesmo conjunto de prefixos anunciada Microsoft no efectuado público e Microsoft efectuado caminhos. Isto significa que terá de diferentes caminhos da sua rede para o Microsoft. Isto pode potencialmente causar decisões de encaminhamento sub ideais ser efetuadas na rede da sua. Como resultado, que se possa deparar experiências conectividade sub ideal para diferentes serviços. 

Microsoft irá tag prefixos anunciados através de efectuado público e Microsoft efectuado com valores de Comunidade BGP apropriados a indicar que a região estão alojados os prefixos no. Pode depender os valores de Comunidade a tomar decisões de encaminhamento adequadas para oferecer [Encaminhamento ideal para clientes](expressroute-optimize-routing.md).

| **Região geopolítica** | **Região do Microsoft Azure** | **Valor de Comunidade BGP** |
|---|---|---|
| **América do Norte** |    |  |
|    | Leste dos EUA | 12076:51004 |
|    | Leste dos EUA 2 | 12076:51005 |
|    | Ocidental dos e.u.a. | 12076:51006 |
|    | US Ocidental 2 | 12076:51026 |
|    | Oeste Central (EUA) | 12076:51027 |
|    | América do Norte Central (EUA) | 12076:51007 |
|    | Sul Central (EUA) | 12076:51008 |
|    | Central (EUA) | 12076:51009 |
|    | Canadá Central | 12076:51020 |
|    | Leste Canadá | 12076:51021 |
| **América do Sul** |  |  |
|    | Sul do Brasil | 12076:51014 |
| **Europa** |    |  |
|    | Europa Norte | 12076:51003 |
|    | Europa Ocidental | 12076:51002 |
| **Ásia-Pacífico** |    |   |
|    | Este asiático | 12076:51010 |
|    | Sudeste asiático | 12076:51011 |
| **Japão** |     |   |
|    | Japão leste | 12076:51012 |
|    | Japão oeste | 12076:51013 |
| **Austrália** |    |   | 
|    | Leste Austrália | 12076:51015 |
|    | Sudeste Austrália | 12076:51016 |
| **Índia** |    |   |
|    | Sul Índia | 12076:51019 |
|    | Oeste Índia | 12076:51018 |
|    | Índia Central | 12076:51017 |

Todas as rotas anunciadas da Microsoft serão marcadas com o valor de Comunidade adequado. 

>[AZURE.IMPORTANT] Prefixos globais serão marcados com um valor de Comunidade adequado e vai ser anunciados apenas quando ExpressRoute premium suplemento está ativado.


Para além de acima, Microsoft também irá marcar prefixos baseados no serviço que pertencem. Aplica-se apenas para o Microsoft efectuado. A tabela abaixo fornece um mapeamento de serviço para o valor de Comunidade BGP.

| **Serviço** | **Valor de Comunidade BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype para empresas** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Outros serviços do Office 365** | 12076:5100 |

>[AZURE.NOTE] Microsoft não respeita quaisquer valores de Comunidade BGP que definiu no rotas anunciadas para a Microsoft.

## <a name="next-steps"></a>Próximos passos

- Configure a sua ligação de ExpressRoute.

    - [Criar um circuito ExpressRoute para o modelo clássico de implementação](expressroute-howto-circuit-classic.md) ou [criar e modificar um circuito ExpressRoute utilizando o Gestor de recursos do Azure](expressroute-howto-circuit-arm.md)
    - [Configurar o encaminhamento para o modelo clássico de implementação](expressroute-howto-routing-classic.md) ou [configurar o encaminhamento para o modelo de implementação do Gestor de recursos](expressroute-howto-routing-arm.md)
    - [Ligação de um VNet clássica para um circuito ExpressRoute](expressroute-howto-linkvnet-classic.md) ou [ligação VNet um Gestor de recursos para um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)


