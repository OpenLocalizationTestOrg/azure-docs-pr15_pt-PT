<properties
   pageTitle="Descrição geral de segurança de rede Azure | Microsoft Azure"
   description=" Este artigo torna mais fácil de compreender o que Microsoft Azure tem para oferecer na área de segurança da rede. Fornecemos explicações básicas para conceitos de segurança de rede principais e os requisitos e informações sobre o que Azure tem para oferecer em cada uma das seguintes áreas. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Descrição geral de segurança de rede Azure

Microsoft Azure inclui uma infraestrutura de rede robusta para suportar a aplicação e os requisitos de conectividade de serviço. A conectividade da rede é possível entre recursos localizados no Azure, no local e Azure alojados recursos e para e a partir da Internet e Azure.

O objetivo deste artigo é para que seja mais fácil de compreender o que Microsoft Azure tem para oferecer na área de segurança da rede. Aqui fornecemos explicações básicas para requisitos e conceitos de segurança de rede core. Também fornecemos-lhe informações sobre o que Azure tem para oferecer em cada uma das seguintes áreas. Existem várias ligações para outro conteúdo que permite-lhe obter uma compreensão mais aprofundada para as áreas na qual está interessado.

Este artigo Descrição geral de segurança do Azure rede irá focar-se no seguinte:

- Redes Azure
- Controlo de acesso de rede
- Seguro conectividade remota, o access e a publicação em local
- Disponibilidade
- Funcionalidade de registo
- Resolução de nomes
- Arquitetura de DMZ
- Centro de segurança do Azure

## <a name="azure-networking"></a>Redes Azure

Máquinas virtuais precisa de conectividade de rede. Para suportar esse requisito, Azure requer máquinas virtuais de estar ligado a uma rede Virtual Azure. Uma rede Virtual Azure é uma construção lógica incorporada na parte superior o ferro rede Azure física. Cada rede Virtual do Azure lógica está isolada de todas as outras Azure Virtual redes. Isto ajuda a garantir que o tráfego de rede no seu implementações não está acessível a outros clientes do Microsoft Azure.

Saiba mais:

- [Descrição geral de rede virtual](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controlo de acesso de rede
Controlo de acesso de rede é a legislação sobre de limitar a conectividade de e dispositivos específicos ou sub-redes dentro de uma rede Virtual Azure. O objetivo do controlo de acesso de rede é para se certificar de que os seus máquinas virtuais e serviços acessíveis para apenas os utilizadores e dispositivos ao qual pretende-las acessíveis. Aceder a controlos é baseada em permitir ou negar decisões para ligações para e do seu serviço ou máquina virtual.

Azure suporta vários tipos de controlo de acesso de rede. Estes incluem:

- Controlo de camada de rede
- Encaminhar o controlo e forçada túnel
- Aplicações de segurança de rede virtual

### <a name="network-layer-control"></a>Controlo de camada de rede
Qualquer implementação segura requer alguns medida do controlo de acesso de rede. O objetivo do controlo de acesso de rede é para se certificar de que seu máquinas virtuais e os serviços de rede que são executados no máquinas virtuais só podem comunicar com outros dispositivos utilizarão que precisam de comunicar com e todas as restantes tentativas de ligação estão bloqueadas.

Se precisar de controlo de acesso ao nível de rede base (com base em endereço IP e os protocolos TCP ou UDP), em seguida, pode utilizar grupos de segurança de rede. Um grupo de segurança de rede (NSG) é uma firewall de filtragem de pacotes básicas e permite-lhe controlar o acesso com base numa [cadeia de identificação 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs não fornecem inspeção de camada de aplicação ou autenticados aceder a controlos.

Saiba mais:

- [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Controlo de encaminhar e túnel forçada
A capacidade para controlar o comportamento de encaminhamento redes Virtual Azure é uma funcionalidade de controlo de segurança e acesso da rede crítica. Se o encaminhamento estiver configurado incorretamente, aplicações e serviços alojados no seu computador virtual podem ligar a dispositivos que não poderá ligar-se, incluindo dispositivos propriedade e operado pela intrusos potenciais.

Redes Azure suporta a capacidade de personalizar o comportamento de encaminhamento de tráfego de rede em redes Virtual Azure. Permite-lhe alterar as entradas da tabela encaminhamento predefinido na sua rede Virtual Azure. Controlo do comportamento de encaminhamento de ajuda a certificar-se de que todo o tráfego de um determinado dispositivo ou grupo de dispositivos introduz ou deixa a sua rede Virtual Azure através de uma localização específica.

Por exemplo, poderá ter um aparelho de segurança de rede virtual na sua rede Virtual Azure. Pretende Certifique-se de que todo o tráfego de rede Virtual Azure e para atravessa esse aparelho segurança virtual. Pode fazê-lo através da configuração de [Rotas de definidos pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) no Azure.

[Forçada túnel](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não são permitidos para iniciar uma ligação para dispositivos na Internet. Tenha em atenção que este é diferente do aceitar ligações de entrada e, em seguida, responder aos mesmos. Servidores front-end web precisa de responder a pedido de anfitriões da Internet e, por isso, origem de Internet tráfego é permitido de entrada para estes servidores da web e os servidores web são permitidos para responder.

O que não pretende permitir que é um servidor web front-end para iniciar um pedido de saída. Esses pedidos podem representar um risco de segurança, uma vez que estas ligações podem ser utilizadas para transferir o software maligno. Mesmo se assim o pretender nestes servidores front-end para iniciar pedidos de saída à Internet, poderá pretender forçá-los para percorrer os proxies de web no local para que pode tirar partido do URL filtragem e registo.

Em vez disso, que seria que pretende utilizar o túnel forçada para evitar esta situação. Quando ativa túnel forçada, todas as ligações à Internet são forçadas através do seu gateway no local. Pode configurar túnel forçada tirando partido da rotas de definidos pelo utilizador.

Saiba mais:

- [O que são rotas de definidos pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Aplicações de segurança de rede virtual
Enquanto grupos de segurança de rede, rotas de definidos pelo utilizador e túnel forçada fornecem-lhe um nível de segurança nas camadas de transporte e de rede do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), poderão existir momentos em que pretende para activar a segurança em níveis superiores a rede.

Por exemplo, os seus requisitos de segurança podem incluir:

- Autenticação e autorização antes de permissão do acesso à aplicação
- Deteção de intrusos e resposta intrusos
- Controlo de camada de aplicação para protocolos de alto nível
- Filtragem do URL
- Antivírus de nível de rede e antimalware
- Proteção de anti-bot
- Controlo de acesso de aplicação
- Proteção de DDoS adicional (acima de DDoS proteção fornecidos o ferro Azure propriamente dito)

Pode aceder a estas funcionalidades de segurança melhorado rede utilizando uma solução de parceiro Azure. Pode encontrar o parceiro Azure mais recente soluções de segurança de rede visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurar "segurança" e "segurança da rede".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade cruzada local
Programa de configuração, configuração e gestão das suas necessidades de recursos Azure de ser executadas remotamente. Além disso, poderá querer implementar soluções [híbrido IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que tenham componentes no local e na nuvem pública Azure. Estes cenários necessitam de acesso remoto seguro.

Redes Azure suporta os seguinte cenários de acesso remoto seguro:

- Ligar estações de trabalho individuais a uma rede Virtual do Azure
- Ligar a sua rede no local para uma rede Virtual Azure com uma VPN
- Ligar a sua rede no local para uma rede Virtual Azure com uma ligação WAN dedicada
- Ligar o Azure redes virtuais umas às outras

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Ligar estações de trabalho individuais a uma rede Virtual Azure
Poderão existir momentos quando pretende ativar pessoal os programadores ou operações individual gerir máquinas virtuais e serviços no Azure. Por exemplo, precisa de aceder a uma máquina virtual numa rede Virtual Azure e a política de segurança não permitir acesso remoto RDP ou SSH para máquinas virtuais individuais. Neste caso, pode utilizar uma ligação VPN ponto para local.

A ligação VPN do ponto-para-site utiliza o protocolo de [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) para permitem-lhe configurar uma ligação privada e segura entre o utilizador e a rede Virtual Azure. Uma vez é estabelecida a ligação VPN, o utilizador será possível RDP ou SSH através da ligação VPN para qualquer máquina virtual da rede Virtual Azure (partindo do princípio que o utilizador pode autenticar e está autorizado).

Saiba mais:

- [Configurar uma ligação ponto-para-Site a uma rede Virtual através do PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Ligar à rede no local a uma rede Virtual Azure com uma VPN
Pretende ligar a toda a rede da empresa, ou partes do mesmo, a uma rede Virtual Azure. Este é comuns no híbrido IT cenários onde empresas [alargar o Centro de dados no local para o Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos, empresas irão alojar partes de um serviço no Azure e peças no local, tal como quando uma solução inclui servidores front-end web no Azure e bases de dados back-end no local. Estes tipos de ligações de "publicação em local" também tornam a gestão do Azure localizado recursos mais seguro e ativar cenários como expandindo controladores de domínio do Active Directory para Azure.

Uma forma para realizar esta tarefa é utilizar um [site para o site VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre um site para o site VPN e está numa VPN ponto-para-site é que está numa VPN ponto-para-site liga-se um único dispositivo a uma rede Virtual Azure, enquanto está numa VPN do site para o site liga uma rede inteira (tal como a sua rede no local) a uma rede Virtual Azure. VPNs de site para o site a uma rede Virtual Azure utilizam o modo de túnel IPsec altamente seguro protocolo de VPN.

Saiba mais:

- [Criar um Gestor de recursos VNet com uma ligação VPN de site para o site utilizando o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Planeamento e estrutura para o VPN gateway](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Ligar a sua rede no local para uma rede Virtual Azure com uma ligação WAN dedicada
Ponto de sites e site para o site ligações de VPN são eficazes para ativar a conectividade de publicação em local. No entanto, algumas organizações tenha em consideração que tem as seguintes desvantagens:

- Ligações VPN mover dados através da Internet – este expõe estas ligações para potenciais problemas de segurança envolvidos com mover dados através de uma rede pública. Além disso, não não garantir fiabilidade e disponibilidade para ligações à Internet.
- Ligações de VPN para redes virtuais Azure podem ser consideradas restringido de largura de banda para algumas aplicações e efeitos, como estes max saída à volta de 200Mbps.

Organizações que necessitam do mais alto nível de segurança e disponibilidade para as respectivas ligações de publicação em local normalmente utilizam ligações WAN dedicadas para ligar a sites remotos. Azure fornece a capacidade de utilizar uma ligação WAN dedicada que pode utilizar para ligar à rede no local para uma rede Virtual Azure. Isto é activado através do Azure ExpressRoute.

Saiba mais:

- [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Ligar o Azure redes virtuais umas às outras
É possível para utilizar muitas Azure redes virtuais nas suas implementações. Existem muitas razões porque é que poderá efetuar esta ação. Um dos motivos poderá ser simplificar a gestão; outro poderá por motivos de segurança. Independentemente da motivação ou justificação para colocar em recursos em redes Virtual do Azure diferentes, poderão existir momentos quando quiser recursos sobre cada uma das redes para se ligar a um do outro.

Uma das opções seria serviços no Azure rede Virtual para ligar aos serviços noutra rede Virtual do Azure por "ciclo novamente" através da Internet. A ligação seria iniciar numa rede Virtual Azure, aceda através da Internet e, em seguida, regresse ao destino da rede Virtual Azure. Esta opção expõe a ligação para as questões de segurança inerentes para qualquer comunicação baseados na Internet.

Poderá ser uma melhor opção Criar ao Virtual uma Azure Virtual de rede para Azure rede VPN de site para o site. Ao Virtual este Azure rede para Azure Virtual rede site para o site VPN utiliza o mesmo protocolo de [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) como a ligação VPN do site para o site de publicação em local mencionada acima.

A vantagem de utilizar ao Virtual uma Azure Virtual de rede para Azure rede VPN de site para o site é que é estabelecida a ligação VPN sobre ferro rede Azure; Não é estabelecida através da Internet. Isto fornece-lhe uma camada extra de segurança em comparação comparada as VPNs de site para o site que se ligam através da Internet.

Saiba mais:

- [Configurar uma ligação de VNet para VNet através do Gestor de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilidade
Disponibilidade é um componente de chave de qualquer programa de segurança. Se os seus utilizadores e sistemas de conseguir aceder ao que precisam de aceder através da rede, o serviço pode ser considerado comprometida. Azure tem tecnologias de rede que suportam os seguintes mecanismos de alta disponibilidade:

- Balanceamento de carga baseado em HTTP
- Nível de balanceamento de carga
- Balanceamento de carga global

Balanceamento de carga é um mecanismo concebido para distribuir uniformemente ligações entre vários dispositivos. Os objetivos de balanceamento de carga são:

- Aumentar a disponibilidade – quando carregar saldo ligações através de vários dispositivos, um ou mais dos dispositivos podem ficar indisponíveis e os serviços em execução nos restantes dispositivos onlinehttps podem continuar a servir o conteúdo de serviço
- Aumentar o desempenho – quando carrega saldo ligações através de vários dispositivos, um único dispositivo não terá de efetuar o processador de acertos. Em vez disso, os pedidos de processamento e da memória para que serve o conteúdo for distribuída através de vários dispositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP
As organizações que execute serviços baseados na web com frequência pretendem ter um balanceador de carga baseado em HTTP à frente desses serviços web para o ajudar a segurar adequados níveis de desempenho e disponibilidade de alta. Contrariamente ao balanceadores de carga baseadas em rede tradicional, as decisões efetuadas por baseado em HTTP de balanceamento de carga balanceadores de carga são baseados em características do protocolo HTTP, não nos protocolos de camada de transporte e de rede.

Para fornecer-lhe baseado em HTTP balanceamento de carga para os seus serviços baseados na web, Azure fornece-lhe o Gateway de aplicação do Azure. O Gateway de aplicação do Azure suporta:

- Baseado em HTTP balanceamento de carga – decisões de balanceamento de carga são efetuadas com base no característica especiais para o protocolo HTTP
- Afinidade sessão baseada em cookie – esta capacidade assegura que ligações estabelecidas para um dos servidores atrás esse Balanceador de carga permanece intacta entre o cliente e servidor. Isto assegura estabilidade das transações.
- SSL passagem para da – quando uma ligação de cliente é estabelecida com Balanceador de carga, que está encriptado sessão entre o cliente e o Balanceador de carga utilizando o HTTPS (SSL /) protocolo. No entanto, para melhorar o desempenho, tem a opção para que a ligação entre o Balanceador de carga e o servidor web atrás da utilização do Balanceador de carga o protocolo HTTP (não encriptado). Isto é designado "SSL descarregar" porque os servidores de web atrás Balanceador de carga não experimentam o overhead processador envolvido com encriptação e, consequentemente, deverá conseguir pedidos de serviço mais rapidamente.
- Baseada em URL encaminhamento de conteúdo – esta funcionalidade torna possíveis para Balanceador de carga tomar decisões sobre onde reencaminhar ligações com base no URL de destino. Isto dá flexibilidade muitas mais que soluções que tornam carregar balanceamento decisões com base em endereços IP.

Saiba mais:

- [Descrição geral de Gateway de aplicação](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Nível de balanceamento de carga
Contrariamente ao balanceamento de carga baseado em HTTP, balanceamento de carga nível torna decisões de balanceamento de carga com base em IP porta e o endereço (números TCP ou UDP).
Pode obter os benefícios de nível de balanceamento de carga no Azure utilizando o Balanceador de carga Azure. Algumas características principais do Balanceador de carga Azure incluem:

- Balanceamento de carga nível com base em números de porta e o endereço IP
- Suporte para qualquer protocolo de camada de aplicação
- Carregar saldos a máquinas virtuais Azure e nuvem serviços instâncias de função
- Pode ser utilizada para acesso à Internet (Balanceamento de carga externa) e que não sejam Internet opostas aplicações (Balanceamento de carga interno) e máquinas virtuais
- Ponto final monitorização, que é utilizada para determinar se qualquer um dos serviços atrás Balanceador de carga ter ficam indisponíveis

Saiba mais:

- [Internet opostas Balanceador de carga de entre vários máquinas virtuais ou serviços](../load-balancer/load-balancer-internet-overview.md)
- [Descrição geral do Balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Balanceamento de carga global
Algumas organizações, pretenderá o nível mais alto de disponibilidade possíveis. Uma forma de atingir este objetivo é das aplicações do anfitrião no globalmente distribuídos centros de dados. Quando uma aplicação está alojada no centros de dados localizados em todo o mundo, é possíveis para uma região geopolítica completa ficar indisponíveis e ainda tem a aplicação para cima e a ser executado.

Para além das vantagens de disponibilidade que obter por alojamento aplicações no globalmente distribuídos centros de dados, também pode obter vantagens de desempenho. Estas vantagens de desempenho podem ser obtidas utilizando um mecanismo que direciona os pedidos de serviço para o Centro de dados que se aproxime do dispositivo que está a fazer o pedido.

Balanceamento de carga global pode fornecer-lhe as duas situações seguintes benefícios. No Azure, pode obter os benefícios da global balanceamento de carga utilizando o Gestor de tráfego Azure.

Saiba mais:

- [O que é o Gestor de tráfego?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Funcionalidade de registo
Registo de um nível de rede é uma função chave para qualquer cenário de segurança de rede. No Azure, pode registar informações obtidas para grupos de segurança de rede obter informações de registo de nível de rede. Com o registo de NSG, obtém informações a partir de:

- Registos de auditoria – estes registos são utilizados para ver todas as operações submetidas para as suas subscrições Azure. Estes registos estiverem ativados por predefinição e podem ser utilizados no portal do Azure.
- Registos dos eventos – estes registos fornecem informações sobre regras que NSG foram aplicadas.
- Contador registos – estes registos permitem-lhe saber quantas vezes cada regra NSG foi aplicada aos negar ou permitir o tráfego.

Também pode utilizar o [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma ferramenta de visualização de dados poderosa, para ver e analisar estes registos.

Saiba mais:

- [Registo a análise de grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Resolução de nomes
Resolução do nome é uma função crítica para todos os serviços que aloja no Azure. A partir de uma perspetiva de segurança, compromisso da função de resolução do nome pode levar a um intruso redireccionar pedidos dos seus sites para o site de um intruso. Resolução do nome seguro é um requisito para todos os seus serviços em nuvem alojado.

Existem dois tipos de resolução do nome que é necessário para o endereço:

- Resolução do nome interno – resolução do nome interno é utilizada pelos serviços no Azure Virtual redes, redes no local ou ambas. Utilizado para a resolução do nome interno de nomes não são acessíveis através da Internet. Para a melhor segurança, é importante que o esquema de resolução do nome interno não está acessível a utilizadores externos.
- Resolução de nomes externos – resolução do nome externo é utilizada por pessoas e dispositivos fora do seu no local e redes virtuais Azure. Estes são os nomes que são visíveis à Internet em são utilizados para direcionar a ligação para os seus serviços baseado na nuvem.

Para a resolução do nome interno, tem duas opções:

- Um servidor de DNS de rede Virtual Azure – quando cria uma nova Azure Virtual rede, é criado um servidor DNS por si. Este servidor DNS pode resolver os nomes dos máquinas localizados numa rede de Virtual Azure. Este servidor de DNS não é configurável e é gerido pelo Gestor de ferro Azure, tornando assim uma solução de resolução do nome seguro.
- Trazer o seu servidor DNS – tem a opção da colocação de um servidor de DNS do seu próprio escolher na sua rede Virtual Azure. Este servidor DNS pode ser que um Active Directory integrada do servidor de DNS ou a uma solução de servidor DNS dedicada fornecida por um parceiro Azure, que pode obter a partir do Azure Marketplace.

Saiba mais:

- [Descrição geral de rede virtual](../virtual-network/virtual-networks-overview.md)
- [Gerir servidores DNS utilizados por uma rede Virtual (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

Para a resolução DNS externa, tem duas opções:

- Alojar o seu próprio externo DNS server no local
- Alojar o seu servidor externo de DNS com um fornecedor de serviços

Muitas das grandes organizações irão aloja os seus próprios DNS servidores no local. Ele pode fazer isto vez que têm a rede conhecimentos e presença global para fazê-lo.

Na maioria dos casos, é aconselhável aloja os seus serviços de resolução do nome DNS com um fornecedor de serviços. Estes fornecedores de serviços têm o conhecimentos de rede e presença global para garantir a disponibilidade muito alta para os seus serviços de resolução do nome. Disponibilidade é essencial para serviços de DNS porque se seus serviços de resolução do nome falharem, ninguém poderão atingir o seu opostas serviços de Internet.

Azure fornece-lhe altamente disponíveis e performant externa DNS solução sob a forma de Azure DNS. Esta solução de resolução do nome externo tira partido infraestrutura do Azure DNS em todo o mundo. Permite-lhe alojar o seu domínio no Azure utilizando as ferramentas de credenciais, API, mesmo e faturação como dos outros serviços Azure. Como parte do Azure, também herda os controlos de segurança forte incorporados na plataforma.

Saiba mais:

- [Descrição geral de Azure DNS](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Arquitetura de DMZ
Muitas organizações enterprise utilizam DMZs para segmentar das suas redes para criar uma zona de intervalo de tempo entre a Internet e dos seus serviços. Parte da DMZ da rede é considerada uma zona de segurança de baixa e recursos sem valor alto são colocados nesse segmento de rede. Normalmente, irá ver dispositivos de segurança de rede que têm uma interface de rede do segmento DMZ e outra rede interface ligado a uma rede que tenha máquinas virtuais e serviços que aceitam ligações de entrada da Internet.

Existem um número de variações de estrutura de DMZ e decisão para implementar uma DMZ, e, em seguida, o qual o tipo de DMZ para utilizar caso decida utilizar uma, é baseada nos seus requisitos de segurança de rede.

Saiba mais:

- [Serviços em nuvem da Microsoft e a segurança de rede](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Centro de segurança do Azure
Centro de segurança ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

Centro de segurança do Azure ajuda-o a otimizar e monitorizar a segurança de rede por:

- Fornecer recomendações de segurança de rede
- Monitorizar o estado da configuração de segurança de rede
- Alertá-lo para a rede baseada ameaças ambos os níveis de ponto final e de rede

Saiba mais:

- [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md)
