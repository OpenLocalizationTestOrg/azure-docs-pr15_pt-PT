<properties 
   pageTitle="Ligação híbrido com a aplicação de camadas 2 | Microsoft Azure"
   description="Saiba como implementar eletrodomésticos virtuais e UDR para criar um ambiente de aplicação de várias camadas no Azure"
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
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Cenário de aparelho virtual

Um cenário comum entre o cliente Azure maior é a necessidade de fornecer uma aplicação de duas camadas exposta à Internet, enquanto permitir o acesso para a camada volta a partir de um centro de dados no local. Este documento irá guiá-lo através de um cenário com rotas definidos pelo utilizador (UDR), um Gateway VPN e eletrodomésticos virtual de rede para implementar um ambiente de duas camadas que cumpre os seguintes requisitos:

- Aplicação Web tem de ser acessível a partir da Internet pública apenas.
- Servidor Web que aloja a aplicação tem de conseguir aceder a um servidor da aplicação de back-end.
- Todo o tráfego da Internet para a aplicação web tem de percorrer um aparelho virtual de firewall. Este aparelho virtual será utilizado para o tráfego de Internet apenas.
- Todo o tráfego de aceder ao servidor de aplicações tem de percorrer um aparelho virtual de firewall. Este aparelho virtual será utilizado para aceder ao servidor de fim de back-end e acesso provenientes de rede no local através de um Gateway VPN.
- Os administradores tem de conseguir gerir os eletrodomésticos virtual firewall dos respetivos computadores no local, utilizando um terceiro da firewall aparelho virtual utilizado exclusivamente para fins de gestão.

Este é um cenário de DMZ padrão com uma DMZ e uma rede protegida. Essa cenário pode ser construídos com base no Azure utilizando NSGs, eletrodomésticos virtual firewall ou uma combinação de ambos. A tabela abaixo mostra algumas das vantagens e desvantagens entre NSGs e eletrodomésticos virtual firewall.

||Profissionais|Desvantagens|
|---|---|---|
|NSG|Sem custos. <br/>Integrados Azure RBAC. <br/>Regras podem ser criadas em modelos de processador.|Pode variar complexidade nos ambientes maiores. |
|Firewall|Controlo total sobre o plano de dados. <br/>Gestão central através de consola de firewall.|Custo do aparelho de firewall. <br/>Não integrado com o Azure RBAC.|

A solução abaixo utiliza eletrodomésticos virtual firewall para implementar um cenário de rede protegida/DMZ.

## <a name="considerations"></a>Considerações sobre

Pode implementar o ambiente acima explicado no Azure utilizando diferentes funcionalidades disponíveis, hoje, da seguinte forma.

- **Rede Virtual (VNet)**. Um VNet Azure funciona de forma semelhante a uma rede no local e pode ser segmentada numa ou mais sub-redes para fornecer isolamento de tráfego e separação de preocupações.
- **Aparelho Virtual**. Os parceiros várias fornecer virtuais eletrodomésticos no mercado Azure que podem ser utilizados para as três firewalls descritas acima. 
- **Rotas (UDR) definidas pelo utilizador**. Encaminhar tabelas podem conter UDRs utilizados pelo Azure funcionamento em rede para controlar o fluxo de pacotes dentro de uma VNet. Estas tabelas rota podem ser aplicadas a sub-redes. Uma das funcionalidades mais recentes no Azure é a capacidade de aplicar uma tabela de encaminhar a GatewaySubnet, fornecer a capacidade de reencaminhar todo o tráfego entram VNet o Azure a partir de uma ligação de híbrido para uma aplicação virtual.
- **Reencaminhamento IP**. Por predefinição, o motor de funcionamento em rede Azure reencaminhar os pacotes para cartões de interface de rede virtual (NIC) apenas se o endereço IP de destino do pacote corresponde ao endereço NIC IP. Por isso, se um UDR define que um pacote deve ser enviado para um dado aparelho virtual, o motor de funcionamento em rede Azure diminuiria desse pacote. Para assegurar que o pacote é entregue a uma VM (neste caso, uma aplicação virtual) que não seja o destino real para o pacote, pode ter de ativar o reencaminhamento IP para o aparelho virtual.
- **Grupos de segurança de rede (NSGs)**. O exemplo abaixo não efetuar a utilização das NSGs, mas pode utilizar NSGs aplicados ao sub-redes e/ou do NIC esta solução para filtrar ainda mais o tráfego e terminar esses sub-redes e NIC.


![Conectividade do IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo, não existe uma subscrição que contém o seguinte:

- 2 grupos de recursos, não apresentados no diagrama. 
    - **ONPREMRG**. Contém todos os recursos necessários para simular uma rede no local.
    - **AZURERG**. Contém todos os recursos necessários para o ambiente de rede virtual Azure. 
- Um VNet com o nome **onpremvnet** utilizados para imitar um centro de dados no local segmentado, conforme indicado abaixo.
    - **onpremsn1**. Sub-rede que contém uma máquina de virtual (VM) a executar o Ubuntu para imitar um servidor no local.
    - **onpremsn2**. Sub-rede que contém uma VM executar Ubuntu para imitar um computador local utilizado por um administrador.
- Existe aparelho virtual uma do firewall, chamado **OPFW** na **onpremvnet** utilizada para manter um túnel para **azurevnet**.
- Um VNet denominada **azurevnet** segmentado, conforme indicado abaixo.
    - **azsn1**. Sub-rede firewall externa exclusivamente utilizada para a firewall externa. Todo o tráfego de Internet irá valore nesta sub-rede. Esta sub-rede contém apenas uma NIC ligada à firewall externa.
    - **azsn2**. Sub-rede front-end alojamento uma VM em execução como um servidor web que irão ser acedido a partir da Internet.
    - **azsn3**. Back-end sub-rede alojamento uma VM executar um servidor de aplicação de back-end que irão ser acedido pelo servidor web front-end.
    - **azsn4**. Sub-rede gestão utilizado exclusivamente para fornecer acesso de gestão para todos os eletrodomésticos virtual de firewall. Esta sub-rede contém apenas uma NIC para cada aparelho virtual de firewall utilizada na solução.
    - **GatewaySubnet**. Sub-rede de ligação do Azure híbrido necessário para o ExpressRoute e Gateway de VPN para fornecer conectividade entre o Azure VNets e noutras redes. 
- Existem 3 eletrodomésticos virtual firewall na rede **azurevnet** . 
    - **AZF1**. Firewall externa divulgada na Internet pública utilizando um recurso de endereço IP público no Azure. É necessário para se certificar de que tem um modelo a partir de mercado ou diretamente a partir do seu fornecedor do aparelho, esse disposições um aparelho virtual de 3-NIC.
    - **AZF2**. Firewall interno utilizado para controlar o tráfego entre **azsn2** e **azsn3**. Também é um aparelho virtual 3 NIC.
    - **AZF3**. Firewall de gestão acessível para os administradores a partir do Centro de dados no local e está ligado a uma sub-rede gestão utilizada para gerir todas as eletrodomésticos de firewall. Pode encontrar modelos de aparelho virtual de 2-NIC no mercado ou pedir um diretamente a partir do seu fornecedor do aparelho.

## <a name="user-defined-routing-udr"></a>Encaminhamento (UDR) definidas pelo utilizador

Cada sub-rede no Azure pode ser associada a uma tabela UDR utilizada para definir como tráfego iniciados por nessa sub-rede é encaminhado. Se não UDRs são definidos, o Azure utiliza rotas predefinidas para permitir o tráfego flua de uma sub-rede para outra. Para compreender melhor UDRs, visite [o que são rotas de definidos pelo utilizador e reencaminhamento IP](./virtual-networks-udr-overview.md#ip-forwarding).

Para assegurar a comunicação é feita através de aparelho firewall à direita, com base no último requisito acima, tem de criar a seguinte tabela rota que contém UDRs **azurevnet**.

### <a name="azgwudr"></a>azgwudr

Neste cenário, apenas o tráfego fluir de no local para Azure será utilizado para gerir as firewalls ligando a **AZF3**e que o tráfego tem aceda através da firewall interna, **AZF2**. Por conseguinte, apenas uma via é necessária **GatewaySubnet** conforme apresentado abaixo.

|Destino|Próximo salto|EXPLICAÇÃO|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Permite que o tráfego no local alcançar firewall gestão **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destino|Próximo salto|EXPLICAÇÃO|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Permite que o tráfego da sub-rede back-end que aloja o servidor de aplicação através de **AZF2**|
|0.0.0.0/0|10.0.2.10|Permite que todos os outros o tráfego para ser encaminhada através de **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destino|Próximo salto|EXPLICAÇÃO|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Permite que o tráfego **azsn2** para fluxo do servidor de aplicação para o servidorweb através de **AZF2**|

Também precisa de criar tabelas de encaminhar para as sub-redes no **onpremvnet** para imitar o Centro de dados no local.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destino|Próximo salto|EXPLICAÇÃO|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Permite que o tráfego **onpremsn2** através de **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destino|Próximo salto|EXPLICAÇÃO|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Permite que o tráfego da sub-rede com cópia de segurança no Azure através de **OPFW**|
|192.168.1.0/24|192.168.2.4|Permite que o tráfego **onpremsn1** através de **OPFW**|

## <a name="ip-forwarding"></a>IP reencaminhamento de chamadas 

UDR e reencaminhamento IP são funcionalidades que pode utilizar em conjunto para permitir que eletrodomésticos virtuais ser utilizado para controlar o fluxo de tráfego numa VNet Azure.  Um aparelho virtual é mais do que uma VM que é executada uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT nada.

Este aparelho virtual VM têm de receber o tráfego de entrada que não é resolvido a própria. Para permitir que uma VM receber tráfego endereçado a outros destinos, tem de ativar IP de reencaminhamento para a VM. Este é um Azure definição, não uma definição num sistema operativo convidado. O seu aparelho virtual ainda precisa para executar alguns tipo de aplicação para processar o tráfego recebido e encaminhá-lo corretamente.

Para saber mais sobre o reencaminhamento IP, visite [o que são percursos do definidos pelo utilizador e reencaminhamento IP](./virtual-networks-udr-overview.md#ip-forwarding).

Por exemplo, imagine que tem a seguinte configuração numa vnet Azure:

- Sub-rede **onpremsn1** contém uma VM denominada **onpremvm1**.
- Sub-rede **onpremsn2** contém uma VM denominada **onpremvm2**.
- Um aparelho virtual denominado **OPFW** está ligado à **onpremsn1** e **onpremsn2**.
- Uma rota definidas pelo utilizador ligada a **onpremsn1** Especifica que todo o tráfego para **onpremsn2** deve ser enviado para **OPFW**.

Neste momento, se **onpremvm1** tentar estabelecer uma ligação com **onpremvm2**, será utilizada a UDR e tráfego será enviado para **OPFW** como próximo salto. Tenha em atenção o destino de pacote real não está a ser alterado, ainda indica **onpremvm2** é o destino. 

Sem reencaminhamento IP ativado para **OPFW**, a lógica de funcionamento em rede virtual Azure irá largue os pacotes, uma vez que-la apenas permite que os pacotes sejam enviadas para uma VM se o endereço IP a VM é o destino para o pacote.

Com o reencaminhamento de IP, a lógica de rede virtual Azure irá reencaminhar os pacotes para OPFW, sem alterar o endereço de destino original. **OPFW** tem processam os pacotes e determinar o que fazer com os mesmos.

Para o cenário acima para trabalhar, tem de ativar IP de reencaminhamento na NIC para **OPFW**, **AZF1**, **AZF2**e **AZF3** que são utilizadas para encaminhamento (NIC todos exceto os ligadas à sub-rede gestão). 

## <a name="firewall-rules"></a>Regras da firewall

Conforme descrito acima, reencaminhamento de IP, só assegura pacotes são enviadas para os eletrodomésticos virtuais. O seu aparelho ainda tem de decidir o que fazer com esses pacotes. Cenário que se segue, terá de criar as seguintes regras no seu eletrodomésticos:

### <a name="opfw"></a>OPFW

OPFW representa um dispositivo no local que contém as seguintes regras:

- **Encaminhar**: todo o tráfego para 10.0.0.0/16 (**azurevnet**) deve ser enviado através de túnel **ONPREMAZURE**.
- **Política**: permitir todo o tráfego bidireccional entre **port2** e **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 representa um aparelho virtual Azure que contém as seguintes regras:

- **Política**: permitir todo o tráfego bidireccional entre **port1** e **port2**.

### <a name="azf2"></a>AZF2

AZF2 representa um aparelho virtual Azure que contém as seguintes regras:

- **Encaminhar**: todo o tráfego para 10.0.0.0/16 (**onpremvnet**) deve ser enviado para o endereço IP (ou seja, 10.0.0.1) Azure gateway através do **port1**.
- **Política**: permitir todo o tráfego bidireccional entre **port1** e **port2**.

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSGs)

Neste cenário, NSGs não estão a ser utilizados. No entanto, pode aplicar NSGs para cada sub-rede para restringir o tráfego recebido e envio. Por exemplo, pode aplicar as seguintes regras NSG à sub-rede FW externos.

**Receção**

- Permita que todo o tráfego TCP da Internet porta 80 em qualquer VM na sub-rede.
- Negar todos os outros tráfego da Internet.

**Saída**
- Negar todo o tráfego à Internet.

## <a name="high-level-steps"></a>Alto passos nível

Para implementar neste cenário, siga os passos de alto níveis abaixo.

1.  Início de sessão à sua subscrição do Azure.
2.  Se pretender implementar um VNet para imitar à rede no local, Aprovisione os recursos que fazem parte de **ONPREMRG**.
3.  Aprovisione os recursos que fazem parte de **AZURERG**.
4.  Aprovisione o túnel a partir do **onpremvnet** para **azurevnet**.
5.  Depois de configurações para todos os recursos, inicie sessão no **onpremvm2** e executar o ping 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.
