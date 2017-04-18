<properties
   pageTitle="Melhores práticas de rede Azure segurança | Microsoft Azure"
   description="Este artigo fornece um conjunto de práticas recomendadas para utilizar de segurança de rede criada no Azure capacidades."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Melhores práticas de rede Azure segurança

Microsoft Azure permite-lhe ligar máquinas virtuais e equipamentos a outros dispositivos utilizarão, colocando-los em redes Virtual Azure. Uma rede Virtual Azure é uma construção de rede virtual que permite-lhe ligar cartões de interface de rede virtual a uma rede virtual para permitir baseadas em TCP/IP comunicações entre dispositivos de rede ativado. Azure máquinas virtuais ligado a uma rede Virtual Azure são possível ligar a dispositivos na mesma Azure Virtual rede, Azure Virtual redes diferentes, na Internet ou ímpares no seus próprio redes no local.

Neste artigo discutimos uma coleção de melhores práticas de rede Azure segurança. Estas práticas recomendadas são derivadas do nossa experiência com o Azure funcionamento em rede e as experiências de clientes como si mesmo.

Para cada prática recomendada, explicaremos:

- O que é a melhor prática
- Por que motivo pretende ativar essa prática recomendada
- O que poderá o resultado se falhar ativar a melhor prática
- Alternativas possíveis para a melhor prática
- Como pode saber ativar a melhor prática

Este artigo melhores práticas de segurança do Azure rede baseia um parecer consenso e as capacidades de plataformas Azure e conjuntos de funcionalidade, à medida que existam ao tempo que este artigo foi escrito. Opiniões tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Azure rede melhores práticas de segurança outros fabricantes referidas neste artigo incluem:

- Logicamente sub-redes de segmento
- Controlar o comportamento de encaminhamento
- Ativar túnel forçada
- Utilizar Virtual eletrodomésticos de rede
- Implemente DMZs para zonas de segurança
- Evitar a exposição à Internet com ligações WAN dedicadas
- Otimizar o tempo útil e desempenho
- Utilizar balanceamento de carga global
- Desativar o acesso RDP ao Azure máquinas virtuais
- Centro de segurança do Azure ativar
- Expandir o seu centro de dados para o Azure


## <a name="logically-segment-subnets"></a>Logicamente sub-redes de segmento

[Redes virtuais Azure](https://azure.microsoft.com/documentation/services/virtual-network/) são semelhantes a uma rede local na sua rede no local. A ideia atrás de uma rede Virtual Azure é que cria uma única IP endereço baseados no espaço rede privada no qual pode colocar todas as sua [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/). São os espaços de endereço IP privados, disponíveis no classe A (10.0.0.0/8), classe B (172.16.0.0/12) e classe C intervalos (192.168.0.0/16).

Semelhante ao que fizer no local, irá querer segmentar o maior espaço de endereços em sub-redes. Pode utilizar [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) com base sub-rede princípios para criar o seu sub-redes.

Encaminhamento entre sub-redes acontecerá automaticamente e não necessita configurar manualmente as tabelas de encaminhamento. No entanto, a predefinição é que não existem sem aceder a controlos de rede entre os sub-redes que criar na rede Virtual Azure. Para poder criar controlos de acesso de rede entre sub-redes, terá de colocar algo entre as sub-redes.

Uma das coisas que pode utilizar para efetuar esta tarefa é um [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs são dispositivos de inspeção de pacotes com estado simples que utilizam a 5-cadeia de identificação (o IP de origem, porta de origem, IP de destino, porta de destino e protocolo camada 4) abordagem para criar a permitir/negar as regras de tráfego de rede. Pode permitir ou negar o tráfego de e para único endereço IP e a partir de vários endereços IP ou mesmo para de e para sub-redes inteiras.

Utilizar NSGs para controlo de acesso de rede entre sub-redes permite-lhe colocar recursos que pertencem à mesma zona de segurança ou função na suas própria sub-redes. Por exemplo, pense de uma aplicação de 3 camadas simple que tenha uma camada web, uma camada de lógica de aplicação e uma camada de base de dados. Coloque máquinas virtuais que pertence, para cada um dos seguintes camadas para os seus próprios sub-redes. Em seguida, utilizar NSGs para controlar o tráfego entre as sub-redes:

- Web Camada virtual máquinas só podem iniciar ligações para as máquinas de lógica de aplicação e só pode aceitar ligações a partir da Internet
- Aplicação lógica virtual máquinas só pode iniciar ligações com camadas de base de dados e só pode aceitar ligações a partir da camada web
- Base de dados Camada virtual máquinas não é possível iniciar uma ligação com algo fora da sua própria sub-rede e só pode aceitar ligações a partir da camada de lógica de aplicação

Para saber mais sobre grupos de segurança de rede e como utilizá-los para logicamente segmentar redes Virtual Azure, leia o artigo [o que é um grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Controlar o comportamento de encaminhamento

Quando introduz uma máquina virtual numa rede Virtual Azure, notará que a máquina virtual pode ligar-se para outro computador virtual na mesma rede Virtual Azure, mesmo se as outras máquinas virtuais encontram em sub-redes diferentes. O motivo por que motivo é possível é que existe uma coleção de rotas de sistema que são ativadas por predefinição que permitem este tipo de comunicação. Estes rotas predefinidas permitem máquinas virtuais da mesma rede Virtual Azure para iniciar ligações entre si e com a Internet (comunicações de saída apenas na Internet).

Enquanto as rotas de sistema predefinidas são úteis para muitos cenários de implementação, existem em que pretende para personalizar a configuração de encaminhamento para sua implementações. Estas personalizações serão permitem-lhe configurar o endereço de salto seguinte para alcançar destinos específicos.

Recomendamos que configure rotas de definidos pelo utilizador quando implementar um aparelho de segurança de rede virtual, que irá falarmos sobre na prática recomendada posterior.

> [AZURE.NOTE] Percursos do definidos pelo utilizador não são obrigatórios e as rotas de sistema predefinido funcionarão na maioria dos casos.

Pode obter mais informações sobre rotas de definidos pelo utilizador e como configurá-los ao ler o artigo [o que são rotas de definidos pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Ativar túnel forçada

Para compreender melhor túnel forçada, é útil para compreender é que "Dividir túnel".
O exemplo mais comuns de divisão túnel é visualizado com ligações VPN. Imagine que estabelecer uma ligação VPN do hotel à rede empresarial. Esta ligação permite-lhe ligar aos recursos na rede da sua empresa e todas as comunicações aos recursos na rede da sua empresa aceda através do túnel VPN.

O que acontece quando pretender ligar a recursos na Internet? Quando dividido túnel está ativado, essas ligações aceda diretamente à Internet e não através do túnel VPN. Alguns especialistas de segurança, considere esta opção para ser um potencial risco e, consequentemente, recomendamos que dividido túnel desativado e todas as ligações, aqueles destinado para a Internet e aqueles destinados a recursos da empresa, aceda através do túnel VPN. A vantagem de este procedimento é que as ligações à Internet, em seguida, são forçadas através de dispositivos de segurança rede de empresa, que seria as maiúsculas/minúsculas se o cliente VPN ligado à Internet fora do túnel VPN.

Agora vamos trazer neste novamente para máquinas virtuais numa rede Virtual Azure. Rotas predefinido para uma rede Virtual Azure permitem máquinas virtuais para iniciar o tráfego para a Internet. Isto também pode representar um risco de segurança, tal como estas ligações de saída podem aumentar a superfície de ataque de uma máquina virtual e ser utilizadas por intrusos.
Por esta razão, recomendamos que permitem túnel forçada no seu máquinas virtuais sempre que tenha conectividade de publicação em local entre a sua rede Virtual do Azure e a sua rede no local. Irá falarmos sobre cruzada conectividade local mais tarde no Azure rede melhor práticas documento.

Se não possui uma ligação de local cruzada, certifique-se de que tira partido dos grupos de segurança de rede (debatido anteriormente) ou Azure virtual eletrodomésticos de segurança (next analisada) para impedir que as ligações de saída para a Internet a partir do seu máquinas virtuais do Azure de rede.

Para saber mais sobre forçada túnel e como ativá-la, leia o artigo [Configurar forçada túnel a utilizar o PowerShell e Gestor de recursos do Azure](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utilizar eletrodomésticos de rede virtual

Enquanto o utilizador definidos encaminhamento e de grupos de segurança de rede podem fornecer uma determinada medida de segurança de rede nas camadas de transporte e de rede do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), existem são vai ser situações em que irá pretende ou ter de ativar segurança níveis elevados da pilha. Nestes casos, recomendamos que implemente eletrodomésticos de segurança de rede virtual fornecidos pela parceiros Azure.

Aplicações de segurança de rede Azure podem entregar significativamente melhorados níveis de segurança sobre o que é fornecido pela controlos de nível de rede. Algumas das capacidades de segurança de rede fornecidas pelo eletrodomésticos de segurança de rede virtual incluem:

- Firewalling
- Intrusos deteção/intrusos prevenção
- Gestão de vulnerabilidade
- Controlo da aplicação
- Deteção de anomalia baseadas em rede
- Filtragem na Web
- Antivírus
- Proteção de Botnet

Se necessitar de um nível superior ao nível de segurança de rede pode obter com controlos de nível de acesso de rede, em seguida, recomendamos que investigar e implementar eletrodomésticos de segurança de rede virtual Azure.

Para saber mais sobre o que eletrodomésticos de segurança de rede virtual Azure estão disponíveis e sobre as suas capacidades, visite o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurar "segurança" e "segurança da rede".

##<a name="deploy-dmzs-for-security-zoning"></a>Implemente DMZs para zonas de segurança
Uma DMZ ou "perímetro rede" é um segmento de rede física ou lógica que foi concebido para fornecer uma camada adicional de segurança entre seus ativos e na Internet. É a intenção da DMZ colocar os dispositivos de controlo de acesso de rede especializada no limite da rede DMZ para que apenas o tráfego pretendido é permitido passado o dispositivo de segurança de rede e na sua rede Virtual Azure.

DMZs são úteis porque pode concentrar-se a gestão de controlo de acesso de rede, monitorização, registo e criar relatórios sobre os dispositivos na extremidade da sua rede Virtual Azure. Aqui faria normalmente ativar DDoS prevenção, sistemas de prevenção de detecção/intrusos intrusos (IDS/IPS), regras de firewall e políticas, filtragem na web, antimalware de rede e muito mais. Os dispositivos de segurança de rede sentar entre a Internet e a sua rede Virtual do Azure e tem uma interface em ambas as redes.

Apesar de esta é a estrutura básica de uma DMZ, existem diversos designs DMZ diferentes, tais como dois a dois, hospedados brochura, hospedados multi e outros.

Recomendamos que para todas as implementações alta segurança que considere implementar uma DMZ para melhorar o nível de segurança de rede para os recursos do Azure.

Para saber mais sobre DMZs e como implementá-los no Azure, leia o artigo [serviços em nuvem da Microsoft e a segurança de rede](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar a exposição à Internet com ligações WAN dedicadas
Muitas organizações tem escolhido a rota de TI híbrido. No híbrido IT, alguns dos elementos de informações da empresa estão no Azure, enquanto outras pessoas permanecem no local. Em muitos casos alguns componentes de um serviço irão estar em execução no Azure enquanto outros componentes permanecem no local.

No híbrido cenário IT, normalmente há algum tipo de conectividade de publicação em local. Esta publicação em local permite a conectividade da empresa ligar os respetivos redes no local à redes virtuais Azure. Existem duas soluções de conectividade de publicação em instalações disponíveis:

- VPN do site para o site
- ExpressRoute

[Site para o site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) representa uma ligação privada virtual entre a sua rede no local e uma rede Virtual Azure. Esta ligação ocorre através da Internet e permite-lhe informações de "túnel" dentro de uma ligação encriptada entre o Azure e de rede. VPN do site para o site é uma tecnologia segura, maduro que tenha sido implementada por empresas de todos os tamanhos, há décadas. Encriptação de túnel é executada a utilizar o [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Enquanto VPN do site para o site é uma tecnologia fidedigna, fiável e estabelecida, o tráfego interior do túnel percorrer na Internet. Além disso, largura de banda relativamente está restringida a um máximo de sobre 200Mbps.

Se necessitar de um nível de segurança ou desempenho excepcional para as ligações de publicação em local, recomendamos que utilize Azure ExpressRoute para a conectividade de publicação em local. ExpressRoute é uma WAN dedicada de ligação entre a sua localização no local ou um fornecedor de alojamento do Exchange. Dado que se trata de uma ligação de telco, os seus dados não viajam através da Internet e, consequentemente, não são expostos à riscos potenciais inerentes comunicações de Internet.

Para saber mais sobre como funciona o Azure ExpressRoute e como implementar, leia o artigo [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Otimizar o tempo útil e desempenho
Confidencialidade, integridade e disponibilidade (CIA) incluem Tríade do modelo de segurança mais influência de hoje. Confidencialidade é sobre a encriptação e privacidade, integridade é crucial garantir que que dados não são alterados por pessoal não autorizado e disponibilidade é crucial garantir que de indivíduos autorizados conseguem aceder à informação que estejam autorizados a aceder. Falha em qualquer uma das seguintes áreas representa uma potencial violação em segurança.

Disponibilidade pode ser considerada como sendo sobre o tempo útil e o desempenho. Se for um serviço para baixo, não podem ser acedidas informações. Se o desempenho é por isso, baixa como para tornar os dados inutilizável, em seguida, podemos pode ter em consideração os dados sejam inacessível. Por conseguinte, a partir de uma perspetiva de segurança, é necessário fazer o podemos para se certificar de que os nossos serviços tem tempo de utilização ideal e um desempenho.
Um método popular e eficaz utilizado para melhorar a disponibilidade e desempenho é utilizar balanceamento de carga. Balanceamento de carga é um método de distribuição de tráfego de rede por servidores que fazem parte de um serviço. Por exemplo, se tiver servidores front-end web como parte do seu serviço, pode utilizar balanceamento de carga para distribuir o tráfego através de vários servidores front-end web.

Esta distribuição de tráfego aumenta disponibilidade uma vez que se um dos servidores web ficar disponível, o Balanceador de carga irá interromper o envio de tráfego nesse servidor e redirecionar o tráfego para servidores que ainda estão online. Também balanceamento de carga ajuda desempenho, porque o processador, a rede e a memória gerais para servir pedidos são distribuído em todos os a carga de balanceamento de servidores.

Recomendamos que empregam balanceamento de carga, sempre que possível e, conforme adequado para os seus serviços. Vamos irá endereço adequação nas secções seguintes.
Ao nível da rede Virtual Azure, Azure fornece que com três principal carregado balanceamento opções:

- Balanceamento de carga baseado em HTTP
- Balanceamento de carga externa
- Balanceamento de carga interna

## <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP
Balanceamento de carga baseado em HTTP baseia decisões sobre que servidor para enviar ligações com características do protocolo HTTP. Azure tem um balanceador de carga HTTP direcionada pelo nome do Gateway de aplicação.

Recomendamos que-nos Gateway de aplicação do Azure quando:

- Aplicações que necessitam de pedidos de sessão do utilizador/cliente mesmo para chegar a mesma máquina virtual back-end. Exemplos de isto seriam ser aplicações carrinho de compras e itens servidores de correio da web.
- Aplicações que pretende libertar os farms web a partir de sobrecarga de taxas de cessação SSL tirando partido da funcionalidade [SSL descarregar](https://f5.com/glossary/ssl-offloading) Gateway de aplicação.
- Aplicações, como uma rede de entrega de conteúdos, que necessitam de balanceamento de vários pedidos HTTP na mesma ligação TCP de execução longa para ser encaminhado ou carregar para diferentes servidores de back-end.

Para saber mais sobre como funciona a Gateway de aplicação do Azure e como pode utilizá-lo no seu híbridas, leia o artigo [Descrição geral do Gateway aplicações](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Balanceamento de carga externa
Balanceamento de carga externa ocorre quando ligações de entrada da Internet estão carga distribuída entre os seus servidores localizados numa rede Virtual Azure. O Balanceador de carga externos Azure pode fornecer-lhe esta capacidade e recomendamos que utilize-lo quando não exigir as sessões autocolantes ou descarregar SSL.

Contrariamente ao balanceamento de carga baseado em HTTP, o Balanceador de carga externos utiliza informações nas camadas de transporte e de rede do modelo de rede OSI para tomar decisões no que servidor para carregar o saldo ligação.

Recomendamos que utilize o balanceamento de carga externos sempre que tiver [aplicações sem estado](http://whatis.techtarget.com/definition/stateless-app) aceitar pedidos recebidos da Internet.

Para saber mais sobre como funciona o Balanceador de carga externos do Azure e utilize a forma como pode implementá-lo, leia o artigo [Introdução criar um balanceador de carga opostas de Internet no Gestor de recursos através do PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Balanceamento de carga interna
Balanceamento de carga interna é semelhante a balanceamento de carga externa e utiliza o mesmo mecanismo para carregar o saldo ligações para os servidores atrás das mesmas. A única diferença é que o Balanceador de carga neste caso está a aceitar ligações a partir de máquinas virtuais que não estão na Internet. Na maioria dos casos, as ligações que são aceites para balanceamento de carga são iniciadas por dispositivos numa rede Virtual Azure.

Recomendamos que utilize interna balanceamento de carga para cenários que irão beneficiem das vantagens desta capacidade, tal como quando necessita de carregar saldo ligações para os servidores de SQL ou web interno.

Para saber mais sobre como funciona a Azure interno balanceamento de carga e como pode implementar, leia o artigo [Introdução criar um interno Balanceador de carga através do PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Utilizar balanceamento de carga global
Efetuar computação nuvem público-possíveis implementar globalmente distribuído aplicações que tenham componentes localizados na centros de dados todo o mundo. Isto é possível no Microsoft Azure devido a presença de um centro de dados global do Azure. Contrariamente de balanceamento de carga tecnologias mencionadas anteriormente, balanceamento de carga global torna possíveis disponibilizar serviços mesmo quando centros de dados inteiros poderão ficar indisponíveis.

Pode obter este tipo de global balanceamento de carga no Azure tirando partido do [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Gestor de tráfego torna é possível carregar o saldo ligações para os seus serviços com base na localização do utilizador.

Por exemplo, se o utilizador está a fazer um pedido de no seu serviço a partir da União Europeia, é direccionada a ligação para os seus serviços de localizada no Centro de dados da UE. Esta parte do tráfego gestor global carregar balanceamento ajuda a melhorar o desempenho porque ligar-se ao centro de dados mais próximo é mais rápido que estabelecer ligação ao centros de dados que estão longe.

No lado disponibilidade, balanceamento de carga global torna-se de que o seu serviço está disponível mesmo se um centro de dados inteiro deverá ficar disponível.

Por exemplo, se um centro de dados do Azure deve ficar indisponível devido a motivos ambientais ou devido ao corrente (por exemplo, falhas de rede regional), ligações para o serviço seriam reencaminhadas para o mais próximo do Centro de dados online. Este balanceamento de carga global é feito tirando partido das políticas DNS que pode criar no Gestor de tráfego.

Recomendamos que utilize o Gestor de tráfego para qualquer solução nuvem desenvolver que possui um âmbito distribuído ao longo de várias regiões e requer o nível mais alto de tempo de utilização possível.

Para saber mais sobre o Gestor de tráfego do Azure e como implementá-lo, leia o artigo [o que é o Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Desativar o acesso RDP/SSH ao Azure máquinas virtuais
É possível alcançar máquinas virtuais do Azure utilizando o [Protocolo de ambiente de trabalho remoto](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e os protocolos de [Shell seguro](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Estes protocolos tornam possíveis gerir máquinas virtuais a partir de localizações remotas e são padrão no Centro de dados computação.

Potencial problema de segurança com utilizando estes protocolos através da Internet é que os intrusos podem utilizar várias técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para aceder a máquinas virtuais do Azure. Assim que os intrusos obtêm acesso, pode utilizar o seu máquina virtual como um ponto de início para comprometer a outros computadores na sua rede Virtual Azure ou até mesmo ataque dispositivos utilizarão fora do Azure.

Por esta razão, recomendamos que desativar acesso direto RDP e SSH para sua máquinas virtuais do Azure da Internet. Depois de acesso RDP e SSH directo da Internet estiver desativado, tem outras opções que pode utilizar para aceder a estes máquinas virtuais para a gestão remota:

- Site de ponto VPN
- VPN do site para o site
- ExpressRoute

[Ponto-para-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) é outro termo para uma ligação de cliente/servidor VPN de acesso remoto. Está numa VPN ponto-para-site permite um único utilizador ligar a uma rede Virtual Azure através da Internet. Depois da ligação de ponto-para-site é estabelecida, o utilizador poderão utilizar RDP ou SSH para ligar a qualquer máquinas virtuais localizadas na rede Virtual Azure que o utilizador ligado através de VPN ponto-para-site. Este comando assume que o utilizador está autorizado para alcançar essas máquinas virtuais.

Site de ponto VPN é mais seguro que direta ligações RDP ou SSH porque o utilizador tem que autenticar duas vezes antes de ligar a uma máquina virtual. Em primeiro lugar, o utilizador tem de autenticar (e ser autorizada) para estabelecer a ligação VPN do ponto-para-site; em segundo lugar, o utilizador tem de autenticar (e ser autorizada) para estabelecer a sessão RDP ou SSH.

Um [site para o site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) liga-se uma rede inteira a outra rede através da Internet. Pode utilizar um site para o site VPN para se ligar à rede no local para uma rede Virtual Azure. Se implementar uma VPN do site para o site, os utilizadores na sua rede no local será possível ligar a máquinas virtuais na sua rede Virtual Azure utilizando o protocolo RDP ou SSH através da ligação VPN do site para o site e não requer para permitir o acesso direto RDP ou SSH através da Internet.

Também pode utilizar uma ligação WAN dedicada para fornecer a funcionalidade semelhante da VPN do site para o site. As principais diferenças são 1. a ligação WAN dedicada não percorrer na Internet e 2. as ligações WAN dedicadas são normalmente mais estável e performant. Azure fornece-lhe uma solução de ligação WAN dedicada a forma de [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Centro de segurança do Azure ativar
Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

Centro de segurança do Azure ajuda-o a otimizar e monitorizar a segurança de rede por:

- Fornecer recomendações de segurança de rede
- Monitorizar o estado da configuração de segurança de rede
- Alertá-lo para a rede baseada ameaças ambos os níveis de ponto final e de rede

Recomendamos vivamente que permitem o Centro de segurança do Azure para todas as implementações Azure.

Para saber mais sobre o Centro de segurança do Azure e como ativá-lo para as implementações, leia o artigo [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Em segurança alargar o seu centro de dados para o Azure
Muitos enterprise IT organizações procuram para expandir para a nuvem em vez de em crescimento os respetivos centros de dados no local. Esta expansão representa uma extensão de infraestrutura de TI existente para a nuvem pública. Tirando partido da publicação em local opções de conectividade é possível tratar redes Virtual Azure como sub-rede apenas outra na sua infraestrutura de rede no local.

No entanto, existe muitas planeamento e estrutura problemas que precisam de ser resolvidos pela primeira vez. Isto é particularmente importante na área de segurança da rede. Uma das formas recomendadas para compreender como abordar de um projeto como esse é ver um exemplo.

Microsoft criou o [Diagrama de arquitectura de referência do Centro de dados extensão](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) e materiais de suporte para o ajudar a compreender o que uma extensão de centro de dados teria o seguinte aspeto. Este procedimento fornece um exemplo de implementação de referência que pode utilizar para planear e estruturar uma extensão de centro de dados de empresa segura na nuvem. Recomendamos que reveja este documento para obter uma ideia dos componentes principais de uma solução seguro.

Para saber mais sobre como segura alargar o seu centro de dados para o Azure, veja o vídeo [Expandir o seu centro de dados ao Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
