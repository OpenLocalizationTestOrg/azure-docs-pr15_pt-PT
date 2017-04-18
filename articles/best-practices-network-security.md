<properties
   pageTitle="Segurança de serviços e de rede da nuvem Microsoft | Microsoft Azure"
   description="Obter informações sobre algumas das principais funcionalidades disponíveis no Azure para ajudar a criar seguro ambientes de rede"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Segurança Microsoft cloud services e de rede

Serviços em nuvem da Microsoft entregar hyperscale serviços e infraestrutura, capacidades empresarial e várias opções para a conectividade de híbrido. Os clientes podem optar por aceder a estes serviços através da Internet ou com o Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma do Microsoft Azure permite que os clientes para forma totalmente integrada expandir infraestrutura para a nuvem e criar arquitecturas multi-camada. Para além disso, terceiros pode ativar capacidades melhoradas através da oferta de serviços de segurança e equipamentos virtuais. Este documento técnico fornece uma descrição geral de segurança e arquitetura problemas que os clientes deverão tomar em consideração quando utilizar os serviços em nuvem da Microsoft acedidos através do ExpressRoute. Também abrange criar serviços mais seguros no Azure redes virtuais.

## <a name="fast-start"></a>Começar rapidamente
O gráfico de lógica seguinte pode encaminhá-lhe um exemplo das várias técnicas de segurança disponíveis com a plataforma Azure específico. Para uma referência rápida, localize o exemplo que melhor se adequa seu caso. Para obter explicações mais completas, continue a ler através de papel.
![Fluxograma de opções de segurança][0]

[Exemplo 1: Crie uma rede de perímetro (também conhecido como DMZ, zona e sub-rede filtrada) para o ajudar a proteger as aplicações com grupos de segurança de rede (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Exemplo 2: Crie uma rede de perímetro para ajudar a proteger as aplicações com uma firewall e NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemplo 3: Crie uma rede de perímetro para o ajudar a proteger as redes com uma firewall, encaminhar definidos pelo utilizador (UDR) e NSG.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Exemplo 4: Adicione uma ligação de híbrido com uma rede privada virtual (VPN) do site para o site, virtual aparelho.](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemplo 5: Adicione uma ligação de híbrido com um gateway site para o site, Azure VPN.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Exemplo 6: Adicione uma ligação de híbrido com ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Exemplos para adicionar ligações entre redes virtuais, elevada disponibilidade e a interligação de serviço serão adicionados a este documento sobre os próximos meses.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Proteção de conformidade e infraestrutura da Microsoft
Microsoft ter tomado posição liderança iniciativas de conformidade uma ferramenta necessária ao clientes empresariais de suporte. Seguem-se alguns as certificações de conformidade para Azure: ![distintivos de conformidade Azure][1]

Para obter mais detalhes, consulte as informações de conformidade no do [Centro de fidedignidade do Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

A Microsoft tem uma abordagem abrangente para proteger a infraestrutura da nuvem necessária para executar hyperscale serviços globais. Infraestrutura da nuvem Microsoft inclui hardware, software, redes e administrativos e funcionários de operações, para além dos centros de dados físicos.

![Funcionalidades de segurança Azure][2]

Esta abordagem fornece uma Fundação mais segura para os clientes implementar os seus serviços na nuvem da Microsoft. O passo seguinte é para clientes estruturar e criar uma arquitetura de segurança para proteger estes serviços.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquitecturas de segurança tradicionais e redes de perímetro
Apesar de Microsoft fortemente correspondam no proteger a infraestrutura da nuvem, clientes tem também de proteger os respetivos serviços em nuvem e grupos de recursos. Uma abordagem de várias camadas à segurança fornece a melhor defesa. Uma zona de segurança de rede de perímetro protege recursos de rede interna a partir de uma rede ou não fidedigna. Uma rede de perímetro refere-se para a suaves ou partes da rede que sentar entre da Internet e a empresa protegida infraestrutura de TI.

Em redes de empresa normal, a infraestrutura de core fortemente é adicionada na perímetro, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e pontos de imposição de política. Os dispositivos podem incluir o seguinte: firewalls, distribuído negação de serviço (DDoS) prevenção, intrusos detecção ou proteção sistemas (IDS/IPS) e dispositivos VPN. Imposição de política pode assumir a forma de políticas de firewall, listas de controlo de acesso (ACL) ou encaminhamento específico. A primeira linha de defesa na rede, diretamente aceitar recebido tráfego da Internet, é uma combinação destes mecanismos bloquear ataques e o tráfego nocivo permitindo legítimos pedidos ainda mais sessão na rede. Este tráfego encaminha diretamente para recursos na rede de perímetro. Esse recurso poderá, em seguida, "falar com" recursos mais aprofundados na rede, em trânsito o limite da seguinte para validação primeiro. A camada mais exterior é denominada rede de perímetro porque este parte da rede está divulgada na Internet, normalmente com alguma forma de proteção em ambos os lados. A figura seguinte mostra um exemplo de rede de perímetro uma única sub-rede numa rede da empresa, com dois limites de segurança.

![Uma rede de perímetro numa rede da empresa][3]

Existem muitos arquitecturas utilizadas para implementar uma rede de perímetro, a partir de um balanceador de carga simples à frente de um farm de web a uma rede de perímetro sub-rede vários com mecanismos variadas em cada limite para bloquear o tráfego e a proteger as camadas mais aprofundadas da rede da empresa. Como é criada rede de perímetro depende as necessidades específicas da organização e a sua tolerância risco geral.

Como clientes mover as cargas de trabalho para o públicos nuvens, é fundamental para suportar funcionalidades semelhantes arquitetura de rede de perímetro no Azure para cumprir os requisitos de segurança e conformidade. Este documento fornece diretrizes sobre como os clientes podem ser criados num ambiente de rede seguro no Azure. Foca-se na rede de perímetro, mas também inclui um debate abrangente de muitos aspetos de segurança de rede. As seguintes questões informam este debate:

- Como pode ser criada numa rede de perímetro no Azure?
- Quais são algumas das funcionalidades Azure disponíveis para criar rede de perímetro?
- Como podem ser protegidas back-end cargas de trabalho?
- Como são controladas comunicações na Internet para as cargas de trabalho no Azure?
- Como podem ser protegidas as redes no local a partir de implementações no Azure?
- Quando devem ser usadas funcionalidades de segurança Azure nativo versus eletrodomésticos de terceiros ou serviços?

O diagrama seguinte mostra várias camadas de segurança que Azure fornece aos clientes. Estes camadas são nativo da plataforma Azure propriamente dito e funcionalidades do cliente:

![Arquitetura de segurança Azure][4]

Entrada a partir da Internet, DDoS Azure ajuda a protege contra ataques em grande escala contra Azure. A camada seguinte é definido pelo cliente públicos pontos finais, que são utilizados para determinar qual o tráfego pode passar através do serviço de nuvem para a rede virtual. Azure nativa virtual isolamento de rede garante isolamento total de todas as outras redes, e que o tráfego flua apenas através de caminhos de utilizador configurado e métodos. Estes caminhos e métodos estão a camada seguinte, onde NSGs, UDR e eletrodomésticos virtual da rede podem ser utilizados para criar limites de segurança para proteger a implementação de aplicações na rede protegida.

A secção seguinte fornece uma descrição geral do Azure redes virtuais. Estes redes virtuais são criados por clientes e são o que os respetivos implementadas das cargas de trabalho estão ligadas à. Redes virtuais são a base de todas as funcionalidades de segurança de rede necessário para estabelecer uma rede de perímetro para proteger implementações do cliente no Azure.

## <a name="overview-of-azure-virtual-networks"></a>Descrição geral das redes virtuais Azure
Antes do tráfego da Internet pode aceder às redes virtuais Azure, existem duas camadas de segurança inerentes para a plataforma do Azure:

1.  **Proteção de DDoS**: proteção DDoS é uma camada da rede física Azure que protege a plataforma Azure propriamente dito ataques em grande escala baseados na Internet. Estes ataques utilizam vários nós "bot" uma tentativa sobrecarregar um serviço de Internet. Azure tem uma protecção DDoS robusta malha depende da conectividade de Internet de entrada todos os. Esta camada de proteção DDoS não tem utilizador configuráveis atributos e não está acessível ao cliente. Protege Azure como uma plataforma ataques em grande escala, mas não irá diretamente a proteger a aplicação de cliente individuais. Camadas adicionais de resistência podem ser configuradas pelo cliente contra um ataque localizado. Por exemplo, se cliente A foi ataques com um ataque DDoS em grande escala num ponto final público, Azure bloqueia ligações ao respetivo serviço. Cliente A poderá falhar sobre a outra rede virtual ou ponto final não envolvidos com ataque para restaurar o serviço do serviço. De referir que, apesar de cliente A poderá ser afectado nesse ponto final, outros serviços de fora desse ponto final seriam afectados. Além disso, outros serviços e os clientes seriam consulte o artigo sem impacto a partir desse ataque.
2.  **Pontos finais de serviço**: os pontos finais permitem nuvem grupos services ou o recurso de ter o público endereços Internet IP e portas exposto. O ponto final irá utilizar o endereço de tradução NAT (rede) para encaminhar o tráfego para o endereço interno e a porta da rede virtual Azure. Este é o caminho principal para o tráfego externo passar para a rede virtual. Os pontos finais de serviço são configuráveis pelo utilizador para determinar qual o tráfego é transmitido no e como e onde-é convertido para a rede virtual.

Após o tráfego atinge a rede virtual, existem muitas funcionalidades que entram na play. Azure redes virtuais são foundation para clientes anexar as cargas de trabalho e onde se aplica a segurança de nível de rede básica. É uma rede privada (uma sobreposição da rede virtual) no Azure para clientes com as seguintes funcionalidades e características:
 
- **Isolamento de tráfego**: uma rede virtual é o limite de isolamento de tráfego a plataforma Azure. Máquinas virtuais (VMs) numa rede virtual não é possível comunicar diretamente para VMs numa rede virtual diferente, mesmo se ambas as redes virtuais são criadas pelo mesmo cliente. Esta é uma propriedade crítica, que garante VMs de cliente e comunicação permanece privada dentro de uma rede virtual.
- **Topologia multitier**: redes virtuais permitir que os clientes definir a topologia multitier atribuindo sub-redes e designar espaços de endereço em separado para diferentes elementos ou "camadas" da suas das cargas de trabalho. Estes agrupamentos lógicos topologias permitir que os clientes definir a política de acesso diferentes baseada nos tipos de carga de trabalho e também controlam fluxos de tráfego entre as camadas.
- **Conectividade de publicação em local**: clientes podem estabelecer ligação de publicação em local entre uma rede virtual e vários sites no local ou noutras redes virtuais no Azure. Para fazer isto, clientes podem utilizar Azure VPN Gateways ou eletrodomésticos virtual rede de terceiros. Azure suporta site para o site (S2S) VPNs utilizando protocolos IPsec/IKE padrão e ExpressRoute privada conectividade.
- **NSG** permite que os clientes criar regras (ACL) ao nível desejado de granularidade: interfaces, VMs individuais ou sub-redes virtuais de rede. Os clientes podem controlar o acesso, permitindo ou negar comunicação entre as cargas de trabalho dentro de uma rede virtual, de sistemas redes do cliente através de conectividade de publicação em local, ou direcionar comunicação na Internet.
- **UDR** e **Reencaminhamento IP** permitir que os clientes definir os caminhos de comunicação entre diferentes camadas dentro de uma rede virtual. Os clientes podem implementar uma firewall, IDS/IPS e outros eletrodomésticos virtuais e encaminhar o tráfego de rede através destas aplicações de segurança para imposição de política de limite de segurança, auditoria e controlo.
- **Eletrodomésticos virtuais rede** no Azure Marketplace: eletrodomésticos de segurança como firewalls, balanceadores de carga e IDS/IPS estão disponíveis no Azure Marketplace e a Galeria de imagem VM. Os clientes podem implementar estes eletrodomésticos para os respetivos redes virtuais e, mais especificamente, na suas limites de segurança (incluindo as sub-redes de rede de perímetro) para concluir um ambiente de rede segura de várias camadas.

Com estas funcionalidades e capacidades, um exemplo de como uma arquitetura de rede de perímetro pode ser construídos com base em Azure é o seguinte procedimento:

![Uma rede de perímetro numa rede virtual Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Características de rede do perímetro e requisitos
Rede de perímetro foi concebida para ser o front-end da rede, diretamente uma interface comunicação da Internet. Os pacotes a receber devem fluir através de eletrodomésticos segurança, como a firewall, IDS e IPS, antes de atingir os servidores de back-end. Pacotes de Internet vinculadas a partir das cargas de trabalho podem também fluir através de eletrodomésticos de segurança na rede de perímetro para imposição de política, inspeção e efeitos de auditoria, antes de sair da rede. Para além disso, a rede de perímetro pode alojar gateways VPN cruzada local entre redes virtual do cliente e redes de no local.

### <a name="perimeter-network-characteristics"></a>Características de rede de perímetro
Referenciar na figura anterior, algumas das características da rede de perímetro boa são da seguinte forma:

- Acesso à Internet:
    - Os perímetro de rede sub-rede propriamente dito é acesso à Internet, diretamente comunicar com Internet.
    - IPs público, VIP e/ou pontos finais de serviço passam o tráfego da Internet para a rede front-end e dispositivos.
    - Tráfego da Internet transmite através de dispositivos de segurança antes de outros recursos da rede front-end.
    - Se estiver ativada segurança de saída, o tráfego passa através de dispositivos de segurança, como o passo final, antes de passar à Internet.
- Rede protegida:
    - Não existe nenhuma direto caminho da Internet para a infraestrutura de core.
    - Canais para a infraestrutura de core tem percorrer através de dispositivos de segurança como NSGs, firewalls ou dispositivos VPN.
    - Outros dispositivos não tem una da Internet e de infraestrutura do core.
    - Dispositivos de segurança sobre o acesso à Internet e de rede protegida opostas limites da rede de perímetro (por exemplo, os dois firewall ícones apresentados na figura anterior) realmente poderão ser um única aparelho virtual com regras diferenciadas ou interfaces para cada limite. (Ou seja, um dispositivo, logicamente separado, processamento a carregar para ambos os limites da rede de perímetro.)
- Outros comuns práticas e restrições:
    - Das cargas de trabalho não tem de armazenar informações críticas empresas.
    - O Access e atualizações para implementações e configurações de rede de perímetro estão limitadas a apenas os administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de perímetro da rede
Para permitir que estas características, siga estas diretrizes sobre os requisitos de rede virtual para implementar uma rede com êxito perímetro:

- **Arquitetura de sub-rede:** Especifique a rede virtual assim que uma sub-rede completa é dedicada como rede de perímetro, são separada de outros sub-redes na mesma rede virtual. Este procedimento garante o tráfego entre rede de perímetro e outros fluxos de camadas sub-rede interna ou privada através de uma firewall ou IDS/IPS aparelho virtual sobre os limites de sub-rede com rotas definidos pelo utilizador.
- **NSG:** Os perímetro de rede sub-rede propriamente dito deve estar aberta para permitir a comunicação com a Internet, mas que não significa clientes devem ser ignorar NSGs. Siga comuns práticas de segurança para minimizar as superfícies de rede expostas à Internet. Bloquear os intervalos de endereços remoto tem permitidos para aceder as implementações ou os protocolos de aplicação específica e portas que estejam abertas. Poderão existir circunstâncias, no entanto, em que esta não é sempre possível. Por exemplo, se clientes tiverem um site externo no Azure, a rede de perímetro deverá permitir os pedidos de web recebidos de qualquer endereço IP público, mas só deve abrir as portas de aplicação web: TCP: 80 e TCP:443.
- **Tabela encaminhamento:** Os perímetro de rede sub-rede propriamente dito deverá conseguir comunicar diretamente à Internet, mas não deverá permitir direta as comunicações entre as redes de fim ou no local anterior sem ter de aceder através de um aparelho firewall ou segurança.
- **a configuração do aparelho segurança:** Para encaminhar e inspecionar pacotes entre rede de perímetro e o resto das redes protegidas, eletrodomésticos a segurança como firewall, IDS e IPS dispositivos podem ser alojados em multi. Podem ter NIC separada para a rede de perímetro e as sub-redes back-end. NIC na rede de perímetro comunica diretamente para e da Internet, com as NSGs correspondentes e a tabela de encaminhamento de rede de perímetro. NIC estabelecer ligação ao sub-redes back-end têm mais restritos NSGs e tabelas de encaminhamento das sub-redes back-end correspondentes.
- **Funcionalidade do aparelho de segurança:** As aplicações de segurança implementadas na rede de perímetro normalmente efetuar as seguintes funcionalidades:
    - Firewall: Impor regras de firewall ou políticas de controlo de acesso para os pedidos recebidos.
    - Deteção e prevenção de ameaças: detetar e mitigação ataques maliciosos da Internet.
    - Auditoria e registo: manutenção de registos detalhados para auditoria e análise.
    - Inverter proxy: redirecionar os pedidos recebidos para os servidores de back-end correspondentes. Isto envolve mapeamento de e traduzir os endereços de destino nos dispositivos front-end, firewalls normalmente, para os endereços de servidor back-end.
    - Reencaminhar proxy: fornecer NAT e a execução de auditoria para a comunicação iniciada a partir de dentro da rede virtual à Internet.
    - Router: Reencaminhamento de chamadas recebido e de sub-rede cruzada o tráfego de dentro da rede virtual.
    - Dispositivo VPN: serve como os gateways VPN cruzada local de conectividade VPN cruzada local entre redes do cliente no local e redes virtuais Azure.
    - Servidor de VPN: aceitar os clientes VPN estabelecer ligação ao Azure redes virtuais.

>[AZURE.TIP] Manter os seguintes grupos de duas separados: indivíduos autorizados a aceder a engrenagem de segurança de rede do perímetro e as pessoas autorizadas como administradores da aplicação de programação, implementação ou operações. Manter estes grupos separadas permite uma separação de tarefas e impede que uma única pessoa ignorar segurança de aplicações e controlos de segurança de rede.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Perguntas para ser-lhe pedido durante a criação dos limites da rede
Nesta secção, a menos que mencionado especificamente, o termo "redes" refere-se a redes virtuais privadas Azure criadas por um administrador de subscrição. O termo não faça referência para as redes físicas subjacentes dentro Azure.

Além disso, redes virtuais Azure com frequência são utilizados para expandir redes tradicional no local. É possível incorporar site para o site ou soluções de rede híbrido ExpressRoute com arquitecturas de rede de perímetro. Esta é uma consideração importante na criação de limites de segurança de rede.

As seguintes três questões são críticas para responder quando estiver a compilar uma rede com uma rede de perímetro e vários limites de segurança.

#### <a name="1-how-many-boundaries-are-needed"></a>1) limites do quantas são necessários?
É o primeiro ponto de decisão decida quantas limites de segurança forem necessários num determinado cenário:

- Volta de uma única: uma rede perímetro front-end, entre a rede virtual e na Internet.
- Dois limites: um no lado da Internet da rede de perímetro e outro entre a sub-rede de rede do perímetro e as sub-redes back-end nas redes virtuais Azure.
- Limites de três: um no lado da Internet da rede de perímetro, uma entre a rede de perímetro e sub-redes back-end e uma entre as sub-redes back-end e à rede no local.
- Limites de N: um número variável. Consoante os requisitos de segurança, existe realmente qualquer número de limites de segurança que podem ser aplicadas numa rede determinada.

O número e tipo de limites de variam com base no tolerância de risco de uma empresa e o cenário específico a ser implementada conforme necessário. Isto é frequentemente uma decisão conjunta efetuada por vários grupos dentro de uma organização, incluindo frequentemente um risco e equipa de conformidade, uma rede e a equipa de plataforma e uma equipa de desenvolvimento de aplicações. Pessoas com conhecimentos de segurança, os dados em causa e as tecnologias a ser utilizadas deverão ter uma diga esta decisão para garantir a posição de segurança apropriado para cada execução.

>[AZURE.TIP] Utilize o menor número de limites que satisfaz os requisitos de segurança para uma determinada situação. Com mais limites mais difícil operações e resolução de problemas podem ser, assim como o overhead gestão envolvido com a gestão de várias políticas de limite ao longo do tempo. No entanto, limites insuficientes aumentar o risco. Localizar o saldo é crítica.

![Rede de híbrido com três limites de segurança][6]

Figura anterior mostra uma vista de alto nível de uma rede do limite de três segurança. Os limites são entre do perímetro rede e Internet, os Azure front-end e back-end privados sub-redes e a sub-rede back-end Azure e no local da rede da empresa.

#### <a name="2-where-are-the-boundaries-located"></a>2) onde estão localizados os limites?
Assim que o número de limites de é decidido, onde implementá-los é o ponto de decisão seguinte. Normalmente, existem três opções:
- Utilizar um serviço de intermédio baseados na Internet (por exemplo, um baseado na nuvem Web aplicação firewall, que não é abordado neste documento)
- Utilizar funcionalidades nativas e/ou eletrodomésticos virtual rede no Azure
- Utilizar dispositivos físicos na rede no local

Em redes puramente Azure, as opções são funcionalidades Azure nativas (por exemplo, balanceadores de carga Azure) ou rede virtual eletrodomésticos a partir do ecossistema parceiro avançada do Azure (por exemplo, ponto de verificação firewalls).

Se for necessário um limite entre o Azure e uma rede no local, podem residem os dispositivos de segurança em ambos os lados da ligação (ou ambos os lados). Assim uma decisão tem de ser efetuada na localização para colocar engrenagem de segurança.

Na figura anterior, a rede de Internet para perímetro e os limites da frente para back-end estão totalmente contidos dentro do Azure em tem de ser funcionalidades Azure nativas ou rede eletrodomésticos virtuais. Dispositivos de segurança sobre o limite entre o Azure (back-end sub-rede) e a rede da empresa poderão ser do Azure lado ou no lado no local ou mesmo uma combinação de dispositivos em ambos os lados. Pode ser significativas vantagens e desvantagens para qualquer uma das opções que devem ser consideradas seriamente.

Por exemplo, utilizar engrenagem de segurança física existente no lado de rede no local tem a vantagem que não é necessária nenhuma engrenagem nova. Apenas necessita reconfiguração. A desvantagem, no entanto, é que todo o tráfego tem regresse a partir do Azure à rede no local para ser visto por da engrenagem de segurança. Assim o tráfego Azure para Azure poderia implicam latência significativa e afetam o desempenho da aplicação e utilizador uma experiência, se tiver sido novamente forçada à rede no local para imposição de política de segurança.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) como são implementados os limites?
Cada limite de segurança muito provavelmente terá requisitos diferentes capacidade (por exemplo, IDS e as regras de firewall no lado da Internet da rede de perímetro, mas apenas ACL entre a rede de perímetro e back-end sub-rede). Decidir quais os dispositivos para utilizar depende os requisitos de cenário e segurança. Na secção seguinte, exemplos 1, 2 e 3 discutir algumas opções que podem ser utilizadas. Rever as funcionalidades de rede nativa Azure e os dispositivos disponíveis no Azure a partir do ecossistema parceiro mostra as opções de número infindável disponíveis para resolver praticamente qualquer cenário.

Noutro ponto de decisão de implementação de chave é como ligar à rede no local com o Azure. Deve utilizar o Azure gateway virtual ou um aparelho virtual da rede? Estas opções são explicadas detalhadamente maior na secção seguinte (exemplos 4, 5 e 6).

Para além disso, o tráfego entre redes virtuais dentro Azure pode ser necessárias. Estes cenários serão adicionados a uma data posterior.

Quando já souber as respostas às perguntas anterior, a secção [Início rápido](#fast-start) pode ajudar a identificar que exemplos são mais adequados para um determinado cenário.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemplos: Construir limites de segurança com redes virtuais Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemplo 1: Criar uma rede de perímetro para ajudar a proteger as aplicações com NSGs
[Regressar ao início rápido](#fast-start) | [detalhado construir instruções para este exemplo][Example1]

![Rede de perímetro com NSG de entrada][7]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, não existe uma subscrição que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede virtual, o "CorpNetwork", com duas sub-redes: "FrontEnd" e "Back-end"
- Um grupo de segurança de rede que foi aplicada aos ambas as sub-redes
- Um servidor do Windows que representa um servidor da aplicação web ("IIS01")
- Dois servidores do Windows que representam os servidores de back-end de aplicação ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor de DNS ("DNS01")

Para obter scripts e um modelo de Gestor de recursos do Azure, consulte as [instruções detalhadas compilação][Example1].

#### <a name="nsg-description"></a>Descrição NSG
Neste exemplo, um grupo NSG é criado e, em seguida, carregado com seis regras.

>[AZURE.TIP] Regra geral, deverá criar suas regras do "Permitir" específicas em primeiro lugar, seguidas pelas regras de "Negar" mais genéricas. A prioridade determinada determina quais são as regras são avaliadas primeiro. Depois de tráfego é encontrado para aplicar a uma regra específica, são avaliadas sem novas regras. Podem aplicar regras NSG a direção recebidas (da perspetiva de sub-rede).

Forma declarativa, as seguintes regras foram a ser criadas para o tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido.
2.  Tráfego RDP (porta 3389) da Internet a qualquer máquina Virtual é permitido.
3.  HTTP o tráfego (porta 80) da Internet para o servidor web (IIS01) é permitido.
4.  Qualquer tráfego (todas as portas) a partir do IIS01 para AppVM1 é permitido.
5.  Negado qualquer tráfego (todas as portas) da Internet para toda a virtual rede (ambos os sub-redes).
6.  Negado qualquer tráfego (todas as portas) a partir da sub-rede front-end à sub-rede back-end.

Com estas regras vinculado ao cada sub-rede, se um pedido de HTTP foi entrado da Internet para o servidor web, ambas as regras 3 (permitir) e 5 (negar) seria aplicável. Mas porque regra 3 tem uma prioridade mais alta, apenas quiser aplicar e regra de 5 não seria entrar em reproduzir. Assim, o pedido de HTTP seria permitido para o servidor web. Se esse mesmo tráfego foi tentar contactar o servidor de DNS01, regra 5 (negar) seria o primeiro para aplicar e o tráfego não seria permitido a passar para o servidor. Regra de 6 (negar) bloqueia a sub-rede front-end a partir de falar à sub-rede back-end (exceto o tráfego permitido em regras de 1 e 4). Este procedimento protege a rede de back-end, caso um intruso comprometer a aplicação web no front-end. O intruso seria tem acesso à rede "protegida" back-end (apenas para recursos que apresentado quando se clica no servidor AppVM01) limitado.

Existe uma regra de saída predefinido que permite o tráfego de saída à Internet. Neste exemplo, vamos está permitir o tráfego de saída e não modificar todas as regras de saída. Para bloquear o tráfego em ambos os sentidos, definidos pelo utilizador encaminhamento é necessário (consulte exemplo 3).

#### <a name="conclusion"></a>Conclusão
Esta é uma forma relativamente simple e simples de isolar a sub-rede back-end a partir de tráfego de entrada. Para mais informações, consulte as [instruções detalhadas compilação][Example1]. Estas instruções incluem:

- Como construir esta rede de perímetro com scripts de PowerShell.
- Como construir esta rede de perímetro com um modelo de Gestor de recursos do Azure.
- Descrições detalhadas dos cada comando NSG.
- Cenários de fluxo de tráfego detalhada, que mostra como o tráfego é permitido ou negado em cada camada.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2: Criar uma rede de perímetro para ajudar a proteger as aplicações com uma firewall e NSGs
[Regressar ao início rápido](#fast-start) | [detalhado construir instruções para este exemplo][Example2]

![Rede de perímetro com NVA e NSG de entrada][8]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, não existe uma subscrição que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede virtual, o "CorpNetwork", com duas sub-redes: "FrontEnd" e "Back-end"
- Um grupo de segurança de rede que foi aplicada aos ambas as sub-redes
- Um aparelho virtual de rede, neste caso, uma firewall, ligado à sub-rede front-end
- Um servidor do Windows que representa um servidor da aplicação web ("IIS01")
- Dois servidores do Windows que representam os servidores de back-end de aplicação ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor de DNS ("DNS01")

Para obter scripts e um modelo de Gestor de recursos do Azure, consulte as [instruções detalhadas compilação][Example2].

#### <a name="nsg-description"></a>Descrição NSG
Neste exemplo, um grupo NSG é criado e, em seguida, carregado com seis regras.

>[AZURE.TIP] Regra geral, deverá criar suas regras do "Permitir" específicas em primeiro lugar, seguidas pelas regras de "Negar" mais genéricas. A prioridade determinada determina quais são as regras são avaliadas primeiro. Depois de tráfego é encontrado para aplicar a uma regra específica, são avaliadas sem novas regras. Podem aplicar regras NSG a direção recebidas (da perspetiva de sub-rede).

Forma declarativa, as seguintes regras foram a ser criadas para o tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido.
2.  Tráfego RDP (porta 3389) da Internet a qualquer máquina Virtual é permitido.
3.  Qualquer tráfego da Internet (todas as portas) para o aparelho virtual de rede (firewall) é permitido.
4.  Qualquer tráfego (todas as portas) a partir do IIS01 para AppVM1 é permitido.
5.  Negado qualquer tráfego (todas as portas) da Internet para toda a virtual rede (ambos os sub-redes).
6.  Negado qualquer tráfego (todas as portas) a partir da sub-rede front-end à sub-rede back-end.

Com estas regras vinculado ao cada sub-rede, se um pedido de HTTP foi entrado da Internet para o firewall, ambas as regras 3 (permitir) e 5 (negar) seria aplicável. Mas porque regra 3 tem uma prioridade mais alta, apenas quiser aplicar e regra de 5 não seria entrar em reproduzir. Assim, o pedido de HTTP seria permitido à firewall. Se esse mesmo tráfego estava a tentar contactar o servidor de IIS01, apesar de ser na sub-rede front-end, regra 5 (negar) seria aplicável, e o tráfego não seria permitido para passar para o servidor. Regra de 6 (negar) bloqueia a sub-rede front-end a partir de falar à sub-rede back-end (exceto o tráfego permitido em regras de 1 e 4). Este procedimento protege a rede de back-end, caso um intruso comprometer a aplicação web no front-end. O intruso seria tem acesso à rede "protegida" back-end (apenas para recursos que apresentado quando se clica no servidor AppVM01) limitado.

Existe uma regra de saída predefinido que permite o tráfego de saída à Internet. Neste exemplo, vamos está permitir o tráfego de saída e não modificar todas as regras de saída. Para bloquear o tráfego em ambos os sentidos, definidos pelo utilizador encaminhamento é necessário (consulte exemplo 3).

#### <a name="firewall-rule-description"></a>Descrição da regra firewall
A firewall, deverá ser criado regras de reencaminhamento de chamadas. Uma vez que este exemplo apenas encaminha o tráfego da Internet no vinculadas a firewall e, em seguida, para o servidor web, reencaminhamento de chamadas apenas uma tradução de rede endereço (NAT) é necessária, regra.

A regra de reencaminhamento de chamadas aceita qualquer endereço de origem de entrada que acertos a firewall está a tentar entrar HTTP (portas 80 e 443 para HTTPS). Que tem enviados terminar interface local a firewall e redirecionado para o servidor web com o endereço IP do 10.0.1.5.

#### <a name="conclusion"></a>Conclusão
Esta é uma forma relativamente linear de proteger a sua aplicação com uma firewall e isolar sub-rede back-end a partir de tráfego de entrada. Para mais informações, consulte as [instruções detalhadas compilação][Example2]. Estas instruções incluem:

- Como construir esta rede de perímetro com scripts de PowerShell.
- Como construir neste exemplo com um modelo de Gestor de recursos do Azure.
- Descrições detalhadas dos cada regra NSG de comando e de firewall.
- Cenários de fluxo de tráfego detalhada, que mostra como o tráfego é permitido ou negado em cada camada.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3: Criar uma rede de perímetro para o ajudar a proteger as redes com uma firewall, UDR e NSG
[Regressar ao início rápido](#fast-start) | [detalhado construir instruções para este exemplo][Example3]

![Rede de perímetro bidirecional com NVA, NSG e UDR][9]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, não existe uma subscrição que contém o seguinte:

- Três serviços em nuvem: "SecSvc001", "FrontEnd001" e "BackEnd001"
- Uma rede virtual, o "CorpNetwork", com três sub-redes: "SecNet", "FrontEnd" e "Back-end"
- Um aparelho virtual de rede, neste caso, uma firewall, ligado à sub-rede SecNet
- Um servidor do Windows que representa um servidor da aplicação web ("IIS01")
- Dois servidores do Windows que representam os servidores de back-end de aplicação ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor de DNS ("DNS01")

Para obter scripts e um modelo de Gestor de recursos do Azure, consulte as [instruções detalhadas compilação][Example3].

#### <a name="udr-description"></a>Descrição UDR
Por predefinição, as seguintes rotas de sistema são definidas como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

O VNETLocal é sempre o prefix(es) definido endereço da rede virtual para essa rede específico (ou seja,-alterações da rede virtual a rede virtual, dependendo de como é definida cada rede virtual específico). As restantes rotas de sistema são estáticas e predefinida como indicado na tabela.

Neste exemplo, duas tabelas de encaminhamento são criados, uma de cada tipo para as sub-redes front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede determinada. Neste exemplo, cada tabela tem três rotas que direcionar todo o tráfego (0.0.0.0/0) através da firewall (do próximo salto = endereço IP do aparelho Virtual):

1. Tráfego de sub-rede local com sem seguinte salto definido para permitir o tráfego de sub-rede local para ignorar a firewall.
2. Virtual tráfego de rede com um salto seguinte definida como a firewall. Este parâmetro substitui a regra de predefinida que permite que o tráfego de rede virtual local encaminhar diretamente.
3. Todos os restante tráfego (0/0) com um salto seguinte definida como a firewall.

>[AZURE.TIP] Não está a ter a entrada de sub-rede local na UDR irá interromper as comunicações de sub-rede local. 
> - No nosso exemplo, 10.0.1.0/24 apontando para VNETLocal é fundamental como sair de pacote caso contrário, o servidor Web (10.0.1.4) destinado para outro local servidor (por exemplo) 10.0.1.25 irá falhar à medida que serão enviadas sobre para NVA, que irá enviá-la para a sub-rede, e a sub-rede será novamente enviá-la para o NVA e assim sucessivamente.
> - Hipóteses de um ciclo de encaminhamento são normalmente mais elevadas nessa eletrodomésticos multi-nic que estão ligados diretamente para cada sub-rede estão a comunicar com, que é frequentemente tradicional, no local, eletrodomésticos. 

Assim que são criadas as tabelas de encaminhamento, se encontrem vinculados à suas sub-redes. A tabela encaminhamento sub-rede front-end, uma vez criado e vinculado ao sub-rede, teria o seguinte aspeto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] Agora pode ser aplicada UDR à sub-rede do gateway no qual o circuito ExpressRoute está ligado.
>
> Exemplos de como ativar a rede de perímetro com ExpressRoute ou redes de site para o site são apresentados nos exemplos 3 e 4.


#### <a name="ip-forwarding-description"></a>Descrição do IP de reencaminhamento
Reencaminhamento IP é uma funcionalidade de acompanha para UDR. Esta é uma definição de um aparelho virtual que permite-lhe receber tráfego não especificamente endereçado ao aparelho e, em seguida, reencaminhe que o tráfego para o seu destino ultimate.

Por exemplo, se o tráfego de AppVM01 faz um pedido para o servidor de DNS01, UDR seria encaminha-isto para a firewall. Com o reencaminhamento de IP ativado, o tráfego para o destino DNS01 (10.0.2.4) é aceites por aparelho (10.0.0.4) e, em seguida, reencaminhado ao seu destino ultimate (10.0.2.4). Sem reencaminhamento IP ativado no firewall, o tráfego não seria ser aceite pelo aparelho apesar da tabela rota tem a firewall como próximo salto. Para utilizar uma aplicação virtual, é fundamental não se esqueça de ativar o reencaminhamento IP em conjunto com UDR.

#### <a name="nsg-description"></a>Descrição NSG
Neste exemplo, um grupo NSG é criado e, em seguida, carregado com uma única regra. Este grupo é, em seguida, só vinculado a sub-redes front-end e back-end (não SecNet). Forma declarativa está a ser incorporada a seguinte regra:

- Negado qualquer tráfego (todas as portas) da Internet para toda a virtual rede (sub-todas as redes).

Apesar de NSGs são utilizados neste exemplo, o seu objetivo principal é como uma camada secundária de defesa contra uma configuração manual errada. O objetivo é bloquear todo o tráfego entrada da Internet para as sub-redes front-end ou back-end. Só deverá fluxo de tráfego através da sub-rede SecNet a firewall (e, em seguida, se apropriado, para as sub-redes front-end ou back-end). Além disso, com as regras UDR num local, qualquer tráfego que transforme-a das sub-redes front-end ou back-end iria ser direcionado saída para a firewall (graças ao UDR). A firewall seria ver isto como um fluxo de assimétrico e diminuiria o tráfego de saída. Assim, existem três camadas de segurança proteger as sub-redes:
- Sem pontos finais abertos no FrontEnd001 e BackEnd001 cloud services.
- NSGs negação de tráfego da Internet.
- O firewall largar assimétricos tráfego.

Um ponto interessante relativamente ao NSG neste exemplo é que contém apenas uma regra que é recusar o tráfego da Internet para toda a virtual rede, incluindo a sub-rede de segurança. No entanto, uma vez que o NSG apenas está vinculado ao sub-redes front-end e back-end, a regra não é processada sobre o tráfego de entrada para a sub-rede de segurança. Como resultado, o tráfego irá fluxo à sub-rede de segurança.

#### <a name="firewall-rules"></a>Regras da firewall
A firewall, deverá ser criado regras de reencaminhamento de chamadas. Uma vez que a firewall a bloquear ou reencaminhamento de chamadas todos de entrada, saído e internas virtual tráfego de rede, são necessários muitas regras de firewall. Além disso, todo o tráfego entrada irá clicada o endereço IP público de serviço de segurança (no portas diferentes), para serem processados pela firewall. Prática recomendada é para os fluxos de lógicos antes de configurar as sub-redes de diagrama e as regras da firewall, para evitar Reformule as mais tarde. A figura seguinte é uma vista lógica das regras de firewall para este exemplo:
 
![Vista lógica das regras de firewall][10]

>[AZURE.NOTE] Com base na rede aparelho Virtual utilizada, as portas gestão podem variar. Neste exemplo, uma Firewall de NextGen Barracuda é referenciada, que utiliza a portas 22, 801 e 807. Consulte a documentação de fornecedor do aparelho para localizar as portas exatas utilizadas para a gestão do dispositivo que está a ser utilizado.

#### <a name="firewall-rules-description"></a>Descrição de regras de firewall
No diagrama lógico anterior, a sub-rede de segurança não é apresentada. Isto acontece porque a firewall é o único recurso nessa sub-rede e este diagrama é que mostra as regras de firewall e como são logicamente permitem ou negar fluxos de tráfego não encaminhado caminho real. Além disso, as portas externas seleccionadas para o tráfego RDP ocupem variavam portas (8014 – 8026) e foram selecionadas para um pouco alinhar com os dois últimos octetos do endereço IP local para legibilidade mais fácil (por exemplo, o endereço de servidor local 10.0.1.4 está associado a porta externa 8014). Qualquer superiores não conflituosa portas, no entanto, podem ser utilizadas.

Neste exemplo, precisamos de sete tipos de regras:

- Regras externas (para o tráfego de entrada):
  1.    Regra de gestão de firewall: esta aplicação redirecionar regra permite que o tráfego passar para as portas de gestão do aparelho virtual rede.
  2.    Regras RDP (para cada um Windows server): estas quatro regras (um para cada servidor) permitem a gestão dos servidores individuais via RDP. Também pode ser unida para uma regra, dependendo das capacidades do aparelho virtual rede a ser utilizado.
  3.    As regras de tráfego da aplicação: Existem duas estas regras, o primeiro para o tráfego de web front-end e o segundo para o tráfego de back-end (por exemplo, servidor web para a camada de dados). A configuração destas regras depende da arquitectura de rede (onde os seus servidores são colocados) e fluxos de tráfego (que direção, são utilizados os fluxos de tráfego e quais as portas).
      - A primeira regra permite que o tráfego de aplicação propriamente dita contactar o servidor de aplicação. Enquanto permitir que as outras regras de segurança e gestão, as regras de tráfego de aplicação são o que permitam que utilizadores externos ou serviços aceder as aplicações. Neste exemplo, existe um servidor web única na porta 80. Assim uma regra de aplicação único firewall redireciona o tráfego de entrada para IP externo, para o endereço web servidores interno IP. A sessão de tráfego redirecionado seria ser convertida através do NAT para o servidor interno.
      - A segunda regra é a regra de back-end para permitir que o servidor web a falar com o servidor de AppVM01 (mas não AppVM02) através da qualquer porta.
- Regras internas (para o tráfego de rede internas virtual)
  4.    Saída a regra de Internet: esta regra permite que o tráfego a partir de qualquer rede para passar para as redes selecionadas. Esta regra é normalmente uma regra de predefinida já na firewall, mas em estado desativado. Esta regra deve ser ativada para este exemplo.
  5.    Regra DNS: esta regra só permite DNS (porta 53) tráfego passar para o servidor de DNS. Para este ambiente está bloqueado a maior parte dos tráfego de front-end para o back-end. Esta regra especificamente permite que o DNS a partir de qualquer sub-rede local.
  6.    Sub-rede a regra sub-rede: esta regra é permitir que qualquer servidor na sub-rede back-end para ligar a qualquer servidor em sub-rede front-end (mas não o inverso).
- Incluía regra (para o tráfego não cumpre qualquer uma das anterior):
  7.    Negar todos os regra de tráfego: esta deve estar sempre a regra final (em termos de prioridade baixa) e, como tal se um fluxo de tráfego falhar corresponder a nenhum dos últimos regras vão ser largada por esta regra. Esta é uma regra de predefinida e normalmente ativado. Sem modificações geralmente são necessários.

>[AZURE.TIP] Na segunda regra de tráfego de aplicação, a para simplificar a neste exemplo, é permitida qualquer porta. Num cenário de real, deverão ser utilizados para reduzir a superfície de ataque desta regra a porta mais específica e intervalos de endereços.

Assim que todas as regras anterior são criadas, é importante rever a prioridade de cada regra para garantir que o tráfego vai ser permitido ou negado conforme pretender. Neste exemplo, as regras são por ordem de prioridade.

#### <a name="conclusion"></a>Conclusão
Esta é uma forma mais complexa mas mais completa de proteger e isolar a rede que exemplos anteriores. (Exemplo 2 protege apenas a aplicação e exemplo 1 apenas isola sub-redes). Esta estrutura permite para monitorizar o tráfego em ambos os sentidos e protege não apenas o servidor de entrada de aplicação, mas impõe política de segurança de rede para todos os servidores esta rede. Além disso, dependendo do aparelho utilizado, auditoria de tráfego completo e detecção podem ser obtidas. Para mais informações, consulte as [instruções detalhadas compilação][Example3]. Estas instruções incluem:

- Como construir esta rede de perímetro de exemplo com scripts de PowerShell.
- Como construir neste exemplo com um modelo de Gestor de recursos do Azure.
- Descrições de cada UDR, NSG detalhadas comando e regra de firewall.
- Cenários de fluxo de tráfego detalhada, que mostra como o tráfego é permitido ou negado em cada camada.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Exemplo 4: Adicionar uma ligação de híbrido com uma rede privada virtual (VPN) do site para o site, virtual aparelho
[Regressar ao início rápido](#fast-start) | Instruções de compilação disponíveis de detalhadas mais cedo

![Rede de perímetro com NVA ligados rede híbrido][11]

#### <a name="environment-description"></a>Descrição do ambiente
Redes híbrido utilizando um aparelho virtual de rede (NVA) podem ser adicionada a qualquer um dos tipos de rede perímetro descritos nos exemplos 1, 2 ou 3.

Como apresentado na figura anterior, uma ligação VPN através da Internet (de site para site) é utilizada para ligar uma rede no local para uma rede virtual Azure através de um NVA.

>[AZURE.NOTE] Se utilizar ExpressRoute com a opção Azure público efectuado ativada, deve ser criada uma rota estática. Isto deve encaminhar ao endereço IP de VPN NVA saída de Internet para a sua empresa e não através de ExpressRoute WAN. NAT obrigatório a opção ExpressRoute Azure público efectuado pode interromper a sessão de VPN.

Assim que a ligação VPN estiver no local, o NVA torna-se o concentrador central para todas as redes e sub-redes. As regras de reencaminhamento de chamadas firewall determinam quais fluxos de tráfego são permitidos, são convertidas via NAT, redireccionadas ou desaparecem (mesmo para fluxos de tráfego entre a rede no local e o Azure).

Fluxos de tráfego devem ser cuidadosamente, que podem ser otimizados ou degradado por este padrão de estrutura, consoante o específico caso de utilização.

Utilizar o ambiente integrado exemplo 3 e, em seguida, adicionar uma ligação de rede do site para o site VPN híbrido, produz a estrutura seguinte:

![Rede de perímetro com NVA ligado através de um site para o site VPN][12]

O router no local ou outro dispositivo de rede é compatível com o seu NVA para VPN, seria o cliente VPN. Este dispositivo físico seria responsável por iniciar e manter a ligação VPN com o seu NVA.

Logicamente para NVA, a rede aspeto separado quatro "zonas de segurança" com as regras no NVA a ser primário director da tráfego de entre estas zonas:

![Rede lógica perspetiva NVA][13]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede do site para o site VPN híbrido a uma rede virtual Azure pode alargar a rede no local para o Azure de uma forma segura. Ao utilizar uma ligação VPN, o tráfego da sua está encriptado e encaminha através da Internet. NVA neste exemplo fornece uma localização central para impor e gerir a política de segurança. Para mais informações, consulte as instruções de compilação detalhadas (provado). Estas instruções incluem:

- Como construir esta rede de perímetro de exemplo com scripts de PowerShell.
- Como construir neste exemplo com um modelo de Gestor de recursos do Azure.
- Cenários de fluxo de tráfego detalhada, que mostra como os fluxos de tráfego através deste estrutura.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Exemplo 5: Adicionar uma ligação de híbrido com um gateway site para o site, Azure VPN
[Regressar ao início rápido](#fast-start) | Instruções de compilação disponíveis de detalhadas mais cedo

![Rede de perímetro com a rede do gateway híbrido ligada][14]

#### <a name="environment-description"></a>Descrição do ambiente
Pode ser adicionada à rede híbrido utilizando um gateway Azure VPN para qualquer tipo de rede de perímetro descrito nos exemplos 1 ou 2.

Como apresentado na figura anterior, uma ligação VPN através da Internet (de site para site) é utilizada para ligar uma rede no local para uma rede virtual Azure através de um gateway Azure VPN.

>[AZURE.NOTE] Se utilizar ExpressRoute com a opção Azure público efectuado ativada, deve ser criada uma rota estática. Isto deve encaminhar ao endereço IP de VPN NVA saída de Internet para a sua empresa e não através de ExpressRoute WAN. NAT obrigatório a opção ExpressRoute Azure público efectuado pode interromper a sessão de VPN.

A figura seguinte mostra as duas extremidades de rede nesta opção. No limite primeiro, a NVA e NSGs controlar fluxos de tráfego para redes internas Azure e entre Azure e na Internet. A segunda margem é o gateway de Azure VPN, que é uma extremidade de rede completamente separadas e isolada entre no local e Azure.

Fluxos de tráfego devem ser cuidadosamente, que podem ser otimizados ou degradado por este padrão de estrutura, consoante o específico caso de utilização.

Utilizar o ambiente integrado exemplo 1 e, em seguida, adicionar uma ligação de rede do site para o site VPN híbrido, produz a estrutura seguinte:

![Rede de perímetro com gateway ligado através de uma ligação de ExpressRoute][15]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede do site para o site VPN híbrido a uma rede virtual Azure pode alargar a rede no local para o Azure de uma forma segura. Utilizar o gateway Azure VPN nativo, o tráfego da sua é IPSec encriptado e encaminha através da Internet. Além disso, a utilização do gateway Azure VPN pode fornecer uma opção de custo inferior (sem adicionais licenciamento custo tal como acontece com terceiros NVAs). Este é mais económico exemplo 1, onde não NVA é utilizado. Para mais informações, consulte as instruções de compilação detalhadas (provado). Estas instruções incluem:

- Como construir esta rede de perímetro de exemplo com scripts de PowerShell.
- Como construir neste exemplo com um modelo de Gestor de recursos do Azure.
- Cenários de fluxo de tráfego detalhada, que mostra como os fluxos de tráfego através deste estrutura.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemplo 6: Adicionar uma ligação de híbrido com ExpressRoute
[Regressar ao início rápido](#fast-start) | Instruções de compilação disponíveis de detalhadas mais cedo

![Rede de perímetro com a rede do gateway híbrido ligada][16]

#### <a name="environment-description"></a>Descrição do ambiente
Pode ser adicionada à rede de híbrido utilizando uma ligação de peering privada ExpressRoute para qualquer tipo de rede de perímetro descrito nos exemplos 1 ou 2.

Como apresentado na figura anterior, ExpressRoute privada efectuado fornece uma ligação direta entre à rede no local e a rede virtual Azure. Tráfego transits apenas a rede de fornecedor de serviço e à rede do Microsoft Azure, nunca tocar na Internet.

>[AZURE.NOTE] Existem algumas restrições ao utilizar UDR com ExpressRoute, devido a complexidade da encaminhamento dinâmico utilizados no Azure virtual gateway. Estas são da seguinte forma:
>
>- UDR não deve ser aplicada à sub-rede do gateway no qual o ExpressRoute ligado Azure virtual gateway está ligado.
>- ExpressRoute ligado Azure virtual gateway não pode ser que o dispositivo de salto seguinte para outras UDR vinculado sub-redes.
>
>
<br />

>[AZURE.TIP] Utilizar ExpressRoute mantém o tráfego de rede da empresa fora da Internet para maior segurança e aumentar significativamente o desempenho. Também permite para os acordos do nível serviço a partir do seu fornecedor de ExpressRoute. O Gateway Azure pode passar até 2 Gb por s com ExpressRoute, Considerando que com VPNs de site para o site, o débito máximo Azure Gateway é 200 Mb/s.

Conforme visto no diagrama seguinte, com esta opção ambiente tem agora duas suaves de rede. A NVA e NSG controlam fluxos de tráfego para redes internas Azure e entre o Azure e Internet, enquanto o gateway é um limite de rede completamente separadas e isolada entre no local e Azure.

Fluxos de tráfego devem ser cuidadosamente, que podem ser otimizados ou degradado por este padrão de estrutura, consoante o específico caso de utilização.

Utilizar o ambiente integrado exemplo 1 e, em seguida, adicionar uma ligação de rede do ExpressRoute híbrido, produz a estrutura seguinte:

![Rede de perímetro com gateway ligado através de uma ligação de ExpressRoute][17]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede Peering ExpressRoute privado pode de expandir à rede no local para o Azure latência segura, inferior, superior efetuar forma. Além disso, a utilização do Gateway Azure nativo, tal como no exemplo, fornece uma opção de custo inferior (às licenciamento tal como acontece com terceiros NVAs não adicionais). Para mais informações, consulte as instruções de compilação detalhadas (provado). Estas instruções incluem:

- Como construir esta rede de perímetro de exemplo com scripts de PowerShell.
- Como construir neste exemplo com um modelo de Gestor de recursos do Azure.
- Cenários de fluxo de tráfego detalhada, que mostra como os fluxos de tráfego através deste estrutura.

## <a name="references"></a>Referências
### <a name="helpful-websites-and-documentation"></a>Web sites úteis e documentação
- Azure do Access com o Gestor de recursos Azure:
- Aceder ao Azure com PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentação de rede virtual: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentação do grupo de segurança de rede: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentação encaminhamento definida pelo utilizador: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure gateways virtuais: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- Site para o Site VPNs: [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- Documentação ExpressRoute (não se esqueça de dar saída as secções "Introdução ao" e "Como para"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Fluxograma de opções de segurança"
[1]: ./media/best-practices-network-security/compliancebadges.png "Descrições de conformidade Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Funcionalidades de segurança Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Uma DMZ numa rede da empresa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitetura de segurança Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Uma DMZ numa rede Virtual Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rede de híbrido com três limites de segurança"
[7]: ./media/best-practices-network-security/example1design.png "DMZ com NSG de entrada"
[8]: ./media/best-practices-network-security/example2design.png "DMZ com NVA e NSG de entrada"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vista lógica das regras de Firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ com NVA ligado rede híbrido"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ com NVA ligado através de um Site para o Site VPN"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rede lógica NVA perspetiva"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ com Azure Gateway ligado rede de implementações do Site para o Site"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ com Azure Gateway utilizando VPN do Site para o Site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ com Azure Gateway ligado rede ExpressRoute híbrido"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ com Azure Gateway utilizando uma ligação de ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
