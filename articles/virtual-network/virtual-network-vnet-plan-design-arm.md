<properties
   pageTitle="Plano de rede Virtual Azure (VNet) e guia de desenho | Microsoft Azure"
   description="Saiba como planear e estruturar redes virtuais no Azure com base nos seus requisitos de isolamento, conectividade e a localização."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Planear e estruturar redes Virtual do Azure

Criar um VNet para experimentar com é fácil suficiente, mas provavelmente, irá implementar VNets vários ao longo do tempo para suportar as necessidades de produção da sua organização. Com algumas planeamento e estrutura, será capaz de implementar VNets e ligar-se os recursos que necessita de forma mais eficaz. Se não estiver familiarizado com VNets, é recomendado que [Saiba mais sobre VNets](virtual-networks-overview.md) e [como implementar](virtual-networks-create-vnet-arm-pportal.md) um antes de continuar. 

## <a name="plan"></a>Plano

Uma compreensão aprofundada de subscrições Azure, regiões e recursos de rede é fundamental para ter êxito. Pode utilizar a lista de considerações abaixo como ponto de partida. Assim que a compreender esses considerações, pode definir os requisitos para a estrutura da rede.

### <a name="considerations"></a>Considerações sobre

Antes do planeamento de atendimento questões abaixo, considere o seguinte:

- Tudo o que criar no Azure é composto por uma ou mais recursos. Uma máquina de virtual (VM) é um recurso, a interface de adaptador de rede (NIC) utilizada por uma VM for um recurso, o endereço IP público utilizado por uma NIC for um recurso, VNet NIC está ligada ao é um recurso.
- Criar recursos dentro de uma [região Azure](https://azure.microsoft.com/regions/#services) e subscrição. E recursos só podem estar ligados a um VNet que existe na região e subscrição estiverem na mesma. 
- Pode ligar VNets umas às outras ao utilizar um Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). Também pode ligar VNets ao longo das regiões e subscrições desta forma.
- Pode ligar VNets à sua rede no local, utilizando uma das [Opções de conectividade](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponíveis no Azure. 
- Diferentes recursos podem ser agrupados em conjunto em [grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), tornando mais fácil de gerir o recurso como uma unidade. Um grupo de recursos pode conter recursos a partir de várias regiões, desde que os recursos pertencem a mesma subscrição.

### <a name="define-requirements"></a>Definir requisitos

Utilize as perguntas abaixo como ponto de partida para a estrutura da rede Azure.  

1. Quais as localizações Azure irá utilizar para o anfitrião VNets?
2. É necessário fornecem comunicação entre estas localizações Azure?
3. Que precisa fornecer a comunicação entre o Azure VNet(s) e o seu datacenter(s) no local?
4. Quantos infraestrutura como VMs de serviço (IaaS), nuvem funções e services fazer de aplicações web que necessário para a sua solução?
5. É necessário isolar tráfego com base em grupos de VMs (ou seja, front-end web back-end da base de dados servidores e)?
6. Que precisa controlar o fluxo de tráfego utilizando eletrodomésticos virtuais?
7. Precisam os utilizadores diferentes conjuntos de permissões para os recursos Azure diferentes?

### <a name="understand-vnet-and-subnet-properties"></a>Compreender as propriedades VNet e de sub-rede

Recursos VNet e sub-redes ajudam a definir um limite de segurança das cargas de trabalho em execução no Azure. Um VNet é caracterizada por uma coleção de espaços de endereço, definido como blocos CIDR. 

>[AZURE.NOTE] Os administradores de rede estão familiarizados com notação CIDR. Se não estiver familiarizado com CIDR, [Saiba mais acerca do mesmo](http://whatismyipaddress.com/cidr).

VNets contêm as seguintes propriedades.

|Propriedade|Descrição|Restrições|
|---|---|---|
|**nome**|Nome VNet|Cadeia de carateres até 80. Pode conter letras, números, sublinhado, períodos ou hífenes. Tem de começar com uma letra ou número. Tem de terminar com uma letra, o número ou o caráter de sublinhado. Pode contém superior ou letras minúsculas.|  
|**localização**|Localização Azure (também conhecida como região).|Tem de ser uma das localizações Azure válidas.|
|**addressSpace**|Colecção de prefixos de endereços que constituem VNet em notação CIDR.|Tem de ser uma matriz de blocos de endereço CIDR válidos, incluindo os intervalos de endereços IP públicos.|
|**sub-redes**|Colecção de sub-redes que compõem o VNet|consulte a tabela de propriedades de sub-rede abaixo.||
|**dhcpOptions**|Objeto que contém uma única propriedade necessária com o nome **dnsServers**.||
|**dnsServers**|Matriz de servidores DNS utilizados pela VNet. Se não for especificado nenhum servidor, é utilizada a resolução do nome interno Azure.|Tem de ser uma matriz de até 10 servidores DNS, através do endereço IP.| 

Uma sub-rede é um recurso de subordinado de uma VNet e ajuda-o a definir segmentos de espaços de endereço dentro de um bloco CIDR utilizando prefixos de endereço IP. NIC pode ser adicionada a sub-redes e ligada VMs, fornecendo conectividade para vários das cargas de trabalho.

Sub-redes contêm as seguintes propriedades. 

|Propriedade|Descrição|Restrições|
|---|---|---|
|**nome**|Nome de sub-rede|Cadeia de carateres até 80. Pode conter letras, números, sublinhado, períodos ou hífenes. Tem de começar com uma letra ou número. Tem de terminar com uma letra, o número ou o caráter de sublinhado. Pode contém superior ou letras minúsculas.|
|**localização**|Localização Azure (também conhecida como região).|Tem de ser uma das localizações Azure válidas.|
|**addressPrefix**|Prefixo único endereço que compõem a sub-rede em notação CIDR|Tem de ser um único bloco CIDR que faz parte de um dos espaços de endereço a VNet.|
|**networkSecurityGroup**|NSG aplicado à sub-rede|consulte o artigo [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Tabela de rota aplicada à sub-rede|consulte o artigo [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Coleção de objetos de configuração de IP utilizados pelo NIC ligado à sub-rede|consulte [configuração de IP](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Resolução de nomes

Por predefinição, o seu VNet utiliza [resolução do nome do Azure fornecido pela.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) Para resolver nomes dentro de VNet e na Internet pública. No entanto, se o seu VNets se ligar à sua centros de dados no local, terá de fornecer [o seu servidor de DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) para resolver nomes entre as redes.  

### <a name="limits"></a>Limites

Reveja os limites de redes no artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) para se certificar de que a estrutura não entram em conflito com qualquer um dos limites. Alguns limites podem ser aumentados ao abrir um bilhetes de suporte.

### <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções RBCA)

Pode utilizar o [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) para controlar o nível de acesso a utilizadores diferentes poderão ter de diferentes recursos no Azure. Dessa forma pode segregar o trabalho efetuado pela sua equipa com base nas suas necessidades. 

Medida em redes virtuais estão preocupados, os utilizadores na função **Contribuinte de rede** têm controlo total sobre recursos de rede virtual do Gestor de recursos do Azure. Da mesma forma, os utilizadores na função **Contribuinte clássico de rede** têm controlo total sobre recursos de rede virtual clássica.

>[AZURE.NOTE] Também pode [criar o seus próprio funções](../active-directory/role-based-access-control-configure.md) para separar às suas necessidades administrativas.

## <a name="design"></a>Estrutura

Quando já souber as respostas a questões na secção [Planear](#Plan) , reveja o seguinte antes de definir o seu VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Número de subscrições e VNets

Considere criar várias VNets nos cenários seguintes:

- **VMs que necessitam de ser colocados em diferentes localizações Azure**. VNets no Azure são regionais. Não é possível abrangem localizações. Por conseguinte, precisa de VNet pelo menos uma para cada Azure localização que pretende anfitrião VMs no.
- **Das cargas de trabalho que precisam de ser completamente isolados uns dos outros**. Pode criar VNets separada, que ainda utilizar os mesmo espaços de endereços IP, para identificar diferentes das cargas de trabalho a partir de um do outro. 

Tenha em atenção que os limites de que ver acima estão por região, por subscrição. Isto significa que pode utilizar múltiplas subscrições para aumentar o limite de recursos que pode manter no Azure. Pode utilizar um site para o site VPN ou num circuito ExpressRoute, para ligar VNets diferentes subscrições.

### <a name="subscription-and-vnet-design-patterns"></a>Subscrição e padrões de design VNet

A tabela abaixo mostra alguns padrões de design comuns para utilizar a subscrições e VNets.

|Cenário|Diagrama|Profissionais|Desvantagens|
|---|---|---|---|
|Subscrição única, dois VNets por aplicação|![Subscrição única](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Apenas uma subscrição para gerir.|Número máximo de VNets por região Azure. Precisa de mais subscrições após esta. Reveja o artigo [limites de Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.|
|Uma subscrição por aplicação, dois VNets por aplicação|![Subscrição única](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Utiliza apenas dois VNets por subscrição.|Mais difícil gerir quando existem demasiadas aplicações.|
|Uma subscrição por unidade de negócio, dois VNets por aplicação.|![Subscrição única](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Equilibrar entre o número de subscrições e VNets.|Número máximo de VNets por unidade de negócio (subscrição). Reveja o artigo [limites de Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.|
|Uma subscrição por unidade de negócio, dois VNets por grupo de aplicações.|![Subscrição única](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Equilibrar entre o número de subscrições e VNets.|Aplicações tem de estar isoladas utilizando sub-redes e NSGs.|


### <a name="number-of-subnets"></a>Número de sub-redes

Deverá tomar em consideração várias sub-redes num VNet nos cenários seguintes:

- **Não suficiente endereços IP privados para todos os NIC numa sub-rede**. Se o seu espaço de endereços de sub-rede não contiver suficiente endereços IP para o número de NIC na sub-rede, tem de criar várias sub-redes. Tenha em atenção que Azure reservas 5 endereços IP privados de cada sub-rede que não podem ser utilizados: os endereços do primeiros e últimos do espaço de endereços (para o endereço de sub-rede e multicast) e 3 endereços para ser utilizado internamente (para fins DHCP e DNS). 
- **Segurança**. Pode utilizar sub-redes para separar os grupos de VMs um do outro das cargas de trabalho tem uma estrutura com várias camada e aplicar diferentes [grupos de segurança de rede (NSGs)](virtual-networks-nsg.md#subnets) para esses sub-redes.
- **Conectividade híbrido**. Pode utilizar os gateways VPN e circuitos ExpressRoute para [Ligar](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) o VNets entre si e para o seu center(s) de dados no local. Os gateways VPN e ExpressRoute circuitos requerem uma sub-rede dos seus próprios ser criada.
- **Eletrodomésticos Virtual**. Pode utilizar um aparelho virtual, como uma firewall, Aceleradores WAN ou VPN gateway uma VNet Azure. Ao fazê-lo, que necessita para [encaminhar tráfego](virtual-networks-udr-overview.md) para esses eletrodomésticos e isolá-los na sua própria sub-rede.

### <a name="subnet-and-nsg-design-patterns"></a>Sub-rede e padrões de design NSG

A tabela abaixo mostra alguns padrões de design comuns para utilizar sub-redes.

|Cenário|Diagrama|Profissionais|Desvantagens|
|---|---|---|---|
|Sub-rede única, NSGs por camada de aplicação, por aplicação|![Sub-rede única](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Apenas uma sub-rede para gerir.|Vários NSGs necessários isolar cada aplicação.|
|Uma sub-rede por aplicação, NSGs por camada de aplicação|![Sub-rede por aplicação](./media/virtual-network-vnet-plan-design-arm/figure6.png)|Menos NSGs para gerir.|Várias sub-redes para gerir.|
|Uma sub-rede por camada de aplicação, NSGs por aplicação.|![Sub-rede por camadas](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Equilibrar entre o número de sub-redes e NSGs.|Número máximo de NSGs por subscrição. Reveja o artigo [limites de Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.|
|Uma sub-rede por camada de aplicação, por aplicação, NSGs por sub-rede|![Sub-rede por camada por aplicação](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Possivelmente mais pequeno número de NSGs.|Várias sub-redes para gerir.|

## <a name="sample-design"></a>Estrutura de exemplo

Para ilustrar a aplicação das informações neste artigo, considere o seguinte cenário.

Trabalhar numa empresa que tenha 2 centros de dados na América do Norte e centros de dados de duas Europe. Que identificou aplicações opostas 6 do cliente diferente, mantidas por 2 diferentes unidades empresariais que pretende migrar para o Azure como um piloto. A arquitetura básica para as aplicações são os seguintes:

- App1, App2, App3 e App4 são aplicações web alojadas em servidores Linux executar Ubuntu. Cada aplicação se liga ao servidor de uma aplicação separada que aloja RESTful serviços nos servidores Linux. Os serviços RESTful ligar a uma base de dados do MySQL back-end.
- App5 e App6 são aplicações web alojadas em servidores do Windows com o Windows Server 2012 R2. Cada aplicação liga a uma base de dados do SQL Server back-end.
- Todas as aplicações atualmente estão alojadas dos centros de dados da empresa na América do Norte.
- Os centros de dados no local, utilize o espaço de endereços 10.0.0.0/8.

Tem de estruturar uma solução de rede virtual que cumpre os seguintes requisitos:

- Cada unidade de negócio não deve ser afetada por consumo de recursos de unidades de outras empresas.
- Deverá minimizar a quantidade de VNets e sub-redes para facilitar a gestão.
- Cada unidade de negócio deverá ter um teste/desenvolvimento única VNet utilizado para todas as aplicações.
- Cada aplicação está alojada no 2 centros de dados Azure diferentes por continente (América do Norte e Europa).
- Cada aplicação está completamente isolada uns dos outros.
- Cada aplicação pode ser acedida pelos clientes através da Internet através de HTTP.
- Cada aplicação pode ser acedida pelos utilizadores ligados aos centros de dados no local, utilizando um túnel encriptado.
- Ligação aos centros de dados no local deve utilizar dispositivos VPN existentes.
- Grupo de rede da empresa deve ter controlo total sobre a configuração de VNet.
- Os programadores de cada unidade de negócio só deverá conseguir implementar VMs sub-redes existentes.
- Todas as aplicações serão migradas como estão para Azure (elevação e turno).
- As bases de dados em cada localização devem criar uma réplica noutras localizações Azure uma vez por dia.
- Deve utilizar cada aplicação 5 servidores de web front-end, 2 servidores de aplicação (quando for necessário) e 2 servidores de base de dados.

### <a name="plan"></a>Plano

Deverá começar a estrutura de planeamento através de uma resposta a pergunta na secção [definir requisitos](#Define-requirements) , conforme apresentado abaixo.

1. Quais as localizações Azure irá utilizar para o anfitrião VNets?

    2 localizações na América do Norte e 2 localizações na Europa. Deve escolher as baseadas na localização física da sua centros de dados existente no local. Dessa forma a ligação a partir do seu localizações físicas ao Azure terá uma latência melhor.

2. É necessário fornecem comunicação entre estas localizações Azure?

    Sim. Uma vez que as bases de dados têm de ser replicadas para todas as localizações.

3. Que precisa fornecer dados center(s) a comunicação entre o Azure VNet(s) e o seu no local?

    Sim. Uma vez que os utilizadores ligarem aos centros de dados no local tem de conseguir aceder as aplicações através de um túnel encriptado.
 
4. Quantos IaaS VMs necessita para a sua solução?

    200 IaaS VMs. App1, App2 e App3 requerem 5 servidores web cada, 2 servidores de aplicações cada e 2 servidores de base de dados. Que é um total de 9 IaaS VMs por aplicação ou 36 IaaS VMs. App5 e App6 requerem servidores web 5 e 2 servidores de base de dados. Que é um total de 7 IaaS VMs por aplicação ou 14 IaaS VMs. Por conseguinte, precisa de 50 IaaS VMs para todas as aplicações em cada região Azure. Uma vez que precisamos de utilizar 4 regiões, vai ser 200 IaaS VMs.

    Também terá de fornecer os servidores DNS na cada VNet ou na sua centros de dados no local para resolver o nome entre o VMs IaaS do Azure e a sua rede no local. 

5. É necessário isolar tráfego com base em grupos de VMs (ou seja, front-end web back-end da base de dados servidores e)?

    Sim. Cada aplicação deve ser completamente isolada uns dos outros e também deve ser isolada cada camada de aplicação. 

6. Que precisa controlar o fluxo de tráfego utilizando eletrodomésticos virtuais?

    Não. Eletrodomésticos virtuais podem ser utilizados para fornecer maior controlo sobre o fluxo de tráfego, incluindo o registo de plano de dados mais detalhado. 

7. Precisam os utilizadores diferentes conjuntos de permissões para os recursos Azure diferentes?

    Sim. A equipa de rede precisa de controlo total sobre as definições de redes virtuais, enquanto os programadores só deverá conseguir implementar os respetivos VMs sub-redes pré-existentes. 

### <a name="design"></a>Estrutura

Deve seguir a estrutura especificando subscrições, VNets, sub-redes e NSGs. Abordaremos NSGs aqui, mas deve saber mais sobre [NSGs](virtual-networks-nsg.md) antes de concluir a sua estrutura.

**Número de subscrições e VNets**

Os seguintes requisitos estão relacionados com subscrições e VNets:

- Cada unidade de negócio não deve ser afetada por consumo de recursos de unidades de outras empresas.
- Deverá minimizar a quantidade de VNets e sub-redes.
- Cada unidade de negócio deverá ter um teste/desenvolvimento única VNet utilizado para todas as aplicações.
- Cada aplicação está alojada no 2 centros de dados Azure diferentes por continente (América do Norte e Europa).

Com base nessas exigências, tem uma subscrição para cada unidade de negócio. Desta forma, consumo de recursos a partir de uma unidade de negócio irá não contabilizados limites para outras unidades de negócio. E uma vez que pretender minimizar o número de VNets, deverá tomar em consideração utilizando o padrão de **uma subscrição por unidade de negócio, dois VNets por grupo de aplicações** , conforme visto abaixo.

![Subscrição única](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Também precisa de especificar o espaço de endereços para cada VNet. Uma vez que necessita centros de conectividade entre os dados no local e as regiões Azure, o espaço de endereços utilizado para Azure VNets não consegue entrar em conflito com a rede no local e o espaço de endereços utilizado por cada VNet não deve entrar em conflito com outro VNets existente. Pode utilizar os espaços de endereço na tabela abaixo para satisfazer estes requisitos.  

|**Subscrição**|**VNet**|**Região Azure**|**Espaço de endereços**|
|---|---|---|---|
|BU1|ProdBU1US1|Ocidental dos e.u.a.|172.16.0.0/16|
|BU1|ProdBU1US2|Leste dos EUA|172.17.0.0/16|
|BU1|ProdBU1EU1|Europa Norte|172.18.0.0/16|
|BU1|ProdBU1EU2|Europa Ocidental|172.19.0.0/16|
|BU1|TestDevBU1|Ocidental dos e.u.a.|172.20.0.0/16|
|BU2|TestDevBU2|Ocidental dos e.u.a.|172.21.0.0/16|
|BU2|ProdBU2US1|Ocidental dos e.u.a.|172.22.0.0/16|
|BU2|ProdBU2US2|Leste dos EUA|172.23.0.0/16|
|BU2|ProdBU2EU1|Europa Norte|172.24.0.0/16|
|BU2|ProdBU2EU2|Europa Ocidental|172.25.0.0/16|

**Número de sub-redes e NSGs**

Os seguintes requisitos estão relacionados com sub-redes e NSGs:

- Deverá minimizar a quantidade de VNets e sub-redes.
- Cada aplicação está completamente isolada uns dos outros.
- Cada aplicação pode ser acedida pelos clientes através da Internet através de HTTP.
- Cada aplicação pode ser acedida pelos utilizadores ligados aos centros de dados no local, utilizando um túnel encriptado.
- Ligação aos centros de dados no local deve utilizar dispositivos VPN existentes.
- As bases de dados em cada localização devem criar uma réplica noutras localizações Azure uma vez por dia.

Com base nessas exigências, poderia utilize uma sub-rede por camada de aplicação e utilize NSGs para filtrar o tráfego por aplicação. Desta forma, só tem 3 sub-redes em cada VNet (front-end, camada de aplicação e camada de dados) e um NSG por aplicação por sub-rede. Neste caso, deve considerar utilizando o padrão de estrutura de **uma sub-rede por camada de aplicação, NSGs por aplicação** . A figura seguinte mostra a utilização do padrão estrutura que representa o VNet **ProdBU1US1** .

![Uma sub-rede por camada, um NSG por aplicação por camadas](./media/virtual-network-vnet-plan-design-arm/figure11.png)

No entanto, também tem de criar uma sub-rede extra para a ligação VPN entre o VNets e os centros de dados no local. E precisa de especificar o espaço de endereços para cada sub-rede. A figura seguinte mostra uma solução de exemplo para **ProdBU1US1** VNet. Pretende criar uma réplica este cenário para cada VNet. Cada cor representa uma aplicação diferente.

![VNet de exemplo](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Controlo de acesso**

Os seguintes requisitos estão relacionados com o controlo de acesso:

- Grupo de rede da empresa deve ter controlo total sobre a configuração de VNet.
- Os programadores de cada unidade de negócio só deverá conseguir implementar VMs sub-redes existentes.

Com base nessas exigências, pode adicionar os utilizadores da equipa do funcionamento em rede para a função de **Rede contribuinte** incorporada no cada subscrição; e crie uma função personalizada para os programadores de aplicação cada subscrição que lhe dá uma-los direitos para adicionar VMs para sub-redes existentes.

## <a name="next-steps"></a>Próximos passos

- [Implementar uma rede virtual](virtual-networks-create-vnet-arm-template-click.md) baseadas num cenário.
- Compreender como [balanceamento de carga](../load-balancer/load-balancer-overview.md) VMs IaaS e [Gerir o encaminhamento de regiões Azure múltiplos](../traffic-manager/traffic-manager-overview.md).
- Saiba mais sobre [NSGs e como planear e estruturar](virtual-networks-nsg.md) uma solução NSG.
- Saiba mais sobre as suas [cruzada local e opções de conectividade de VNet](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
