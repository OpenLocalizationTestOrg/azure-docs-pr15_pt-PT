<properties
   pageTitle="Exemplo de DMZ – construir uma DMZ a proteger as redes com uma Firewall, UDR e NSG | Microsoft Azure"
   description="Criar uma DMZ com uma Firewall, definidas pelo utilizador (UDR) encaminhamento e grupos de segurança de rede (NSG)"
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
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3 – construir uma DMZ a proteger as redes com uma Firewall, UDR e NSG

[Regressar à página segurança limite melhor práticas][HOME]

Este exemplo criará uma DMZ com uma firewall, quatro servidores do windows, encaminhamento de definidos pelo utilizador, reencaminhamento IP e grupos de segurança de rede. Também irá orientá-através de cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. Também existe uma secção de cenário de tráfego para fornecer uma aprofundadas passo a passo como tráfego avança através de camadas de defesa a DMZ. Por fim, nas referências de secção é o código completo e instruções para criar este ambiente de teste e experimentar vários cenários. 

![DMZ bidirecional com NVA, NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente
Neste exemplo, não existe uma subscrição que contém o seguinte:

- Três serviços em nuvem: "SecSvc001", "FrontEnd001" e "BackEnd001"
- Uma rede Virtual "CorpNetwork", com três sub-redes: "SecNet", "FrontEnd" e "Back-end"
- Um rede virtual aparelho neste exemplo, uma firewall, ligado à sub-rede SecNet
- Um servidor do Windows que representa um servidor da aplicação web ("IIS01")
- Os servidores de duas janelas que representam novamente a aplicação terminam servidores ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor de DNS ("DNS01")

Na secção referências abaixo existe um script do PowerShell que irá criar a maior parte do ambiente descrito acima. Construir a VMs e redes virtuais, embora são efetuadas por script de exemplo, não se descritas detalhadamente neste documento.

Para criar o ambiente:

  1.    Guardar o ficheiro de xml de configuração de rede incluído na secção referências (atualizado com nomes, localização e IP endereços para fazer corresponder o cenário determinado)
  2.    Atualizar as variáveis de utilizador no script para corresponder ao ambiente que o script está a ser executado contra (subscrições, nomes de serviço, etc)
  3.    Executar o script PowerShell

**Nota**: A região representada no script do PowerShell tem de corresponder à região representada no ficheiro xml de configuração da rede.

Assim que o script executada com sucesso os seguintes passos de scripts pós podem ser tidos:

1.  Configurar regras de firewall, isto é abrangido na secção abaixo intitulada: Descrição da regra Firewall.
2.  Opcionalmente, na secção referências são duas scripts para configurar o servidor web e o servidor de aplicação com uma aplicação web simples para permitir a testar com esta configuração DMZ.

Assim que o script executada com sucesso a firewall regras terá de ser concluída, é abrangido na secção intitulada: as regras de Firewall.

## <a name="user-defined-routing-udr"></a>Encaminhamento (UDR) definidas pelo utilizador
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

O VNETLocal é sempre o prefix(es) endereço definido de VNet para essa rede específico (ie-lo será alterado do VNet para VNet dependendo de como é definida cada VNet específico). As restantes rotas de sistema são estáticas e predefinida como acima.

Como para os termos de prioridade, rotas são processadas através do método de correspondência de prefixo mais longa (LPM), assim, a rota mais específica na tabela teria se aplicam a um endereço de destino determinado.

Por conseguinte, o tráfego (por exemplo, para o servidor de DNS01, 10.0.2.4) destinado à rede local (10.0.0.0/16) deverá ser encaminhada através de VNet ao seu destino devido a rota de 10.0.0.0/16. Por outras palavras, para 10.0.2.4, a rota de 10.0.0.0/16 é a rota mais específica, apesar da 10.0.0.0/8 e 0.0.0.0/0 também podem aplicar, mas uma vez que são menor específico não afetam este tráfego. Assim, o tráfego para 10.0.2.4 seria tem um próximo salto a VNet local e simplesmente rota para o destino.

Se o tráfego foi destinado 10.1.1.1 por exemplo, a rota de 10.0.0.0/16 não seria aplicar, mas o 10.0.0.0/8 seria mais específica e este o tráfego seria largadas ("preto degraus"), uma vez que o próximo salto é nulo. 

Se o destino não se aplicam a qualquer uma dos prefixos nulo ou os prefixos VNETLocal, em seguida, -dar menos específica encaminhar, 0.0.0.0/0 e ser encaminhado para a Internet como próximo salto e, consequentemente, fora do limite de internet do Azure.

Se existirem duas prefixos idênticos na tabela de rotas, segue-se a ordem de preferência com base no atributo rotas "origem":

1.  "VirtualAppliance" = a rota de definidas um utilizador manualmente adicionado à tabela
2.  "VPNGateway" = uma rota dinâmicas (BGP quando utilizado com redes híbrido), adicionados por um protocolo de rede dinâmicos, estas rotas podem ser alteradas ao longo do tempo tal como o protocolo dinâmico automaticamente reflete alterações na rede dispõe
3.  "Predefinido" = as rotas do sistema, o local VNet e as entradas estáticas, conforme apresentado na tabela rota acima.

>[AZURE.NOTE] Agora pode utilizar utilizador definidos encaminhamento (UDR) com ExpressRoute e Gateways de VPN para forçar o tráfego de publicação em local de saída e entrada a ser encaminhado para uma aplicação virtual de rede (NVA).

#### <a name="creating-the-local-routes"></a>Criar as rotas locais

Neste exemplo, são necessárias duas tabelas de encaminhamento, uma para as sub-redes front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede determinada. Neste exemplo, cada tabela tem três rotas:

1. Tráfego de sub-rede local com sem seguinte salto definido para permitir o tráfego de sub-rede local para ignorar a firewall
2. Virtual tráfego de rede com um salto seguinte definida como a firewall, este parâmetro substitui a regra de predefinida que lhe permite tráfego VNet local encaminhar diretamente
3. Todos os restante o tráfego (0/0) com um salto seguinte definida como a firewall

Assim que são criadas as tabelas de encaminhamento estiverem ligados ao respetivos sub-redes. Para a sub-rede Frontend tabela encaminhamento, uma vez criado e vinculado à sub-rede deve o seguinte aspeto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Neste exemplo, os comandos seguintes são utilizados para criar a tabela de rotas, adicionar uma rota definidas pelo utilizador e, em seguida, vincular a tabela encaminhar para uma sub-rede (nota; quaisquer itens abaixo que começa com um cifrão (por exemplo: $BESubnet) são definidas pelo utilizador variáveis o script na secção referência deste documento):

1.  Primeiro tem de ser criada a tabela de encaminhamento base. Este fragmento de mostra a criação da tabela para a sub-rede back-end. Em script, uma tabela correspondente também é criada para a sub-rede front-end.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Depois de criada a tabela de rotas, vias definidas pelo utilizador específico podem ser adicionadas. Neste recortados, todo o tráfego (0.0.0.0/0) será encaminhado através do aparelho virtual (uma variável, $VMIP [0], é utilizada para transmitir o endereço de IP atribuídos quando o aparelho virtual foi criado anteriormente no script). Em script, uma regra de correspondente também é criada na tabela front-end.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. A entrada de rota acima irá substituir a "0.0.0.0/0" rota predefinida, mas a regra de 10.0.0.0/16 predefinida ainda existente que lhe permitirá tráfego dentro VNet para encaminhar diretamente para o destino e não para o aparelho Virtual de rede. Para corrigir este comportamento a regra de seguir deve ser adicionado.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. Neste momento é a opção para ser efetuadas. Com as duas rotas acima todo o tráfego irá encaminhar para a firewall para avaliação, o tráfego par dentro de uma única sub-rede. Isto poderão ser utilizado, no entanto, para permitir o tráfego dentro de uma sub-rede para encaminhar localmente sem participação da firewall um terceiro, pode ser adicionada a regra muito específica. Esta rota Unidos qualquer endereço destine para sub-rede local pode apenas aí encaminha diretamente (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Por fim, com a tabela encaminhamento criada e povoada com um rotas definidas pelo utilizador, a tabela tem agora de estar vinculada a uma sub-rede. Em script, a tabela de rota front-end também está vinculada ao sub-rede front-end. Eis o script encadernação para a sub-rede back-end.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP reencaminhamento de chamadas
Uma funcionalidade de acompanha para UDR, é reencaminhamento IP. Esta é uma definição de um aparelho Virtual que permite-lhe receber tráfego não especificamente endereçado ao aparelho e em seguida, reencaminhe que o tráfego para o seu destino ultimate.

Por exemplo, se o tráfego de AppVM01 faz um pedido para o servidor de DNS01, UDR seria encaminhar isto para a firewall. Com o reencaminhamento de IP ativado, o tráfego para o destino DNS01 (10.0.2.4) serão aceites por aparelho (10.0.0.4) e, em seguida, definir o reencaminhamento ao seu destino ultimate (10.0.2.4). Sem reencaminhamento IP ativado no Firewall, o tráfego não seria ser aceite pelo aparelho apesar da tabela rota tem a firewall como próximo salto. 

>[AZURE.IMPORTANT] É fundamental de se lembrar de ativar o reencaminhamento IP em conjunto com o encaminhamento de definidos pelo utilizador.

Configurar o reencaminhamento IP é um único comando e pode ser feito hora de criação da VM. Para o fluxo de neste exemplo, o fragmento de código é perto do final do script e agrupados com os comandos UDR:

1.  Ligar a instância VM que é o seu aparelho virtual, a firewall neste caso e ativar o reencaminhamento IP (nota; qualquer item vermelho que começa com um cifrão (por exemplo: $VMName[0]) é uma variável de definidas pelo utilizador a partir do script na secção referência deste documento. O zero entre parênteses Retos, [0,] representa a primeira VM na matriz de VMs, para o exemplo de script trabalhar sem modificação, a primeira VM (VM 0) tem de ser a firewall):

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e, em seguida, carregado com uma única regra. Este grupo é, em seguida, só vinculado a sub-redes front-end e back-end (não SecNet). Forma declarativa está a ser incorporada a seguinte regra:

1.  Qualquer tráfego (todas as portas) da Internet para o inteiro VNet (sub-todas as redes) é negado

Apesar de NSGs são utilizados neste exemplo, o objetivo principal é como uma camada de defesa contra uma configuração manual errada secundária. Queremos bloquear todo o tráfego de entrada da internet quer o front-end ou sub-redes back-end, o tráfego só deverão fluxo através da sub-rede SecNet a firewall (e, em seguida, se apropriado para o front-end ou back-end sub-redes). Além disso, com as regras UDR num local, qualquer tráfego que transforme-a das sub-redes front-end ou back-end seria ser direcionado saída para a firewall (graças ao UDR). A firewall seria ver isto como um fluxo de assimétrico e diminuiria o tráfego de saída. Assim, existem três camadas de segurança proteger as sub-redes front-end e back-end 1) sem pontos finais abertos no FrontEnd001 e BackEnd001 cloud services, 2) NSGs negação de tráfego da Internet, 3) o tráfego de assimétricos largar firewall.

Um ponto interessante relativamente ao grupo de segurança de rede neste exemplo é que contém apenas uma regra, apresentada abaixo, que é recusar o tráfego da internet para toda a virtual rede que inclui a sub-rede de segurança. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

No entanto, uma vez que o NSG apenas está vinculado ao sub-redes front-end e back-end, a regra não é processada sobre o tráfego de entrada para a sub-rede de segurança. Como resultado, apesar da regra NSG diz sem tráfego da Internet para qualquer endereço no VNet, porque o NSG nunca foi vinculado à sub-rede de segurança, o tráfego irá fluxo à sub-rede de segurança.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Regras da firewall
A firewall, regras de reencaminhamento de chamadas terão de ser criado. Uma vez que a firewall é o tráfego de bloqueio ou reencaminhamento de chamadas todos de entrada, saído e dentro VNet são necessárias muitas regras de firewall. Além disso, todo o tráfego entrada irá clicada o endereço IP público de serviço de segurança (no portas diferentes), para serem processados pela firewall. Prática recomendada é para os fluxos de lógicos antes de configurar as sub-redes de diagrama e as regras de firewall para evitar Reformule as mais tarde. A figura seguinte é uma vista lógica das regras de firewall para este exemplo:
 
![Vista lógica das regras de Firewall][2]

>[AZURE.NOTE] Com base na rede aparelho Virtual utilizada, as portas gestão podem variar. Neste exemplo, que uma Firewall de NextGen Barracuda é referenciada que utiliza as portas 22, 801 e 807. Consulte a documentação de fornecedor do aparelho para localizar as portas exatas utilizadas para a gestão do dispositivo que está a ser utilizado.

### <a name="logical-rule-description"></a>Descrição da regra lógico
O diagrama lógico acima, a sub-rede de segurança não é apresentada uma vez que a firewall do é o único recurso nessa sub-rede e este diagrama é que mostra as regras de firewall e como são logicamente permitem ou negar fluxos de tráfego e não o caminho real encaminhado. Além disso, as portas externas seleccionadas para o tráfego RDP ocupem variavam portas (8014 – 8026) e foram selecionadas para um pouco alinhar com os dois últimos octetos do endereço IP local para legibilidade mais fácil (por exemplo, o endereço de servidor local 10.0.1.4 está associado à porta externa 8014), no entanto quaisquer portas superiores em conflito não podem ser utilizadas.

Neste exemplo, precisamos de 7 tipos de regras, estes tipos de regra são descritos da seguinte forma:

- Regras externas (para o tráfego de entrada):
  1.    Regra de gestão de firewall: Esta regra de redirecionamento de aplicação permite o tráfego passar para as portas de gestão do aparelho virtual rede.
  2.    RDP regras (para cada um windows server): estas quatro regras (um para cada servidor) irão permitir a gestão dos servidores individuais através do RDP. Também pode ser unida para uma regra consoante as funcionalidades do aparelho Virtual rede a ser utilizado.
  3.    Regras de tráfego da aplicação: Existem duas regras de tráfego de aplicação, o primeiro para o tráfego de web front-end e o segundo para o tráfego de back-end (ex servidor web para a camada de dados). A configuração destas regras irá dependem a arquitetura de rede (onde os seus servidores são colocados) e fluxos de tráfego (que direção, são utilizados os fluxos de tráfego e quais as portas).
      - A primeira regra irá permitir o tráfego de aplicação propriamente dita contactar o servidor de aplicação. Enquanto permitir que as outras regras de segurança, gestão, etc., as regras de aplicação são o que permitam que utilizadores externos ou serviços aceder as aplicações. Neste exemplo, existe um servidor web única na porta 80, assim uma regra de aplicação único firewall irá redirecioná-o tráfego de entrada para IP externo, para o endereço web servidores interno IP. A sessão de tráfego redirecionado seria ser NAT tinha para o servidor interno.
      - A segunda regra de tráfego de aplicação é a regra de back-end para permitir que o servidor Web a falar com o servidor de AppVM01 (mas não AppVM02) através da qualquer porta.
- Regras internas (para o tráfego de dentro VNet)
  4.    Saída a regra de Internet: esta regra irá permitir o tráfego a partir de qualquer rede para passar para as redes selecionadas. Esta regra é normalmente uma regra de predefinida já na firewall, mas em estado desativado. Esta regra deve ser ativada para este exemplo.
  5.    Regra DNS: Esta regra só permite DNS (porta 53) tráfego passar para o servidor de DNS. Para este ambiente a maior parte dos tráfego a partir de front-end para o back-end está bloqueado, esta regra permite especificamente DNS a partir de qualquer sub-rede local.
  6.    Regra de sub-rede para sub-rede: esta regra é permitir que qualquer servidor na sub-rede back-end para ligar a qualquer servidor em sub-rede front-end (mas não o inverso).
- Incluía regra (para o tráfego não cumpre qualquer um dos acima):
  7.    Negar todos os tráfego regra: Este deve estar sempre a regra final (em termos de prioridade baixa) e como tal se os fluxos de tráfego de falha corresponde a qualquer uma das regras precedentes que vão ser largada por esta regra. Esta é uma regra de predefinida e normalmente ativado, sem modificações geralmente são necessários.

>[AZURE.TIP] Na segunda regra de tráfego de aplicação, qualquer porta é permitida para fácil neste exemplo, num cenário de real a porta mais específica e intervalos de endereços deverão ser utilizados para reduzir a superfície de ataque desta regra.

<br />

>[AZURE.IMPORTANT] Assim que são criadas todas as regras acima, é importante rever a prioridade de cada regra para garantir que o tráfego vai ser permitido ou negado conforme pretender. Neste exemplo, as regras são por ordem de prioridade. É fácil estar bloqueado fora da firewall devido a regras mal ordenadas. No mínimo, certifique-se de que a gestão de para o próprio firewall é sempre a regra de prioridade mais alta absolutas.

### <a name="rule-prerequisites"></a>Pré-requisitos de regra
Um pré-requisito para a Máquina Virtual a executar a firewall são os pontos finais públicos. Para a firewall processar tráfego os pontos finais públicos adequados tem de estar abertos. Existem três tipos de tráfego neste exemplo; Tráfego de RDP 1) gestão o tráfego para o controlo a firewall e regras de firewall, 2) para controlar os servidores do windows e o tráfego de aplicações 3). Estas são as três colunas de tipos de tráfego no canto superior metade da vista lógica das regras de firewall acima.

>[AZURE.IMPORTANT] Uma chave takeway aqui é Lembre-se de que seja reencaminhado **todo** o tráfego através da firewall. Por isso, para ambiente de trabalho remoto no servidor IIS01, mesmo que é o serviço de nuvem Front-End e, no sub-rede Front-End, para aceder a este servidor iremos precisa para RDP da firewall em porta 8014 e, em seguida, permitir a firewall encaminhar o pedido RDP internamente para a porta de RDP IIS01. Botão de "Ligar" o portal Azure não funciona porque não existe nenhum caminho RDP direto para IIS01 (medida em pode ver o portal). Isto significa que todas as ligações da internet serão-se para o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx (referência o diagrama para o mapeamento de porta externa e IP internos e porta acima).

Um ponto final seja aberto um no momento da criação de VM ou publique compilação, tal como é feito o script de exemplo e mostrado abaixo neste fragmento de código (nota; qualquer item que começa com um cifrão (por exemplo: $VMName[$i]) é uma variável de definidas pelo utilizador a partir do script na secção referência deste documento. "$I" entre parênteses, [$i], representa o número de matriz de um VM específico numa matriz de VMs):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Apesar de não claramente apresentadas aqui devido a utilização de variáveis, mas os pontos finais são **apenas** aberto o serviço de nuvem de segurança. Isto é para assegurar que todo o tráfego entrada é processado (encaminhado, NAT tinha, ignorados) pela firewall.

Um cliente de gestão de terá de estar instalado num PC para gerir a firewall e criar as configurações conforme necessário. Veja o fornecedor de documentação da sua firewall (ou outros NVA) sobre como gerir o dispositivo. O resto desta secção e a secção seguinte, criação de regras de Firewall, irá descrevem a configuração do firewall propriamente dito, através do cliente de gestão de fornecedores (ou seja, não o Azure portal ou PowerShell).

Instruções para transferência do cliente e estabelecer ligação ao Barracuda utilizado neste exemplo podem ser encontradas aqui: [Barracuda GN Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Assim que tiver sessão iniciada para a firewall mas antes de criar regras de firewall, existem duas categorias de objeto pré-requisito que podem tornar a criação das regras; Objetos de serviço e de rede.

Neste exemplo, três objectos de rede com nome devem ser definido (um para a sub-rede de back-end, também um objeto de rede para o endereço IP do servidor de DNS e de sub-rede Frontend). Para criar uma rede com nome; começar a partir do dashboard de cliente de Barracuda GN Admin, navegue para o separador de configuração, na secção configuração operacional clique Ruleset, em seguida, clique em "Redes" no menu de objetos de Firewall, em seguida, clique em novo no menu Editar redes. O objeto de rede pode agora ser criado, adicionando o nome e o prefixo:

![Criar um objeto de rede de front-end][3]
 
Esta opção criará uma rede com nome para a sub-rede front-end, um objeto semelhante deve ser criado para sub-rede back-end. Agora as sub-redes podem ser referenciadas mais facilmente pelo nome nas regras de firewall.

Para o objeto de servidor DNS:

![Criar um objecto de servidor DNS][4]

Esta referência de endereço IP única será utilizada numa regra de DNS posteriormente no documento.

Os objetos de pré-requisito segundo são objetos de serviços. Estes serão representam as portas de ligação RDP para cada servidor. Uma vez que está vinculado o objeto de serviço RDP existente para a porta RDP predefinida, 3389, novos serviços podem ser criados para permitir o tráfego das portas externos (8014 8026). As novas portas também podem ser adicionadas ao serviço RDP existente, mas para facilidade de demonstração, pode ser criada uma regra individual para cada servidor. Para criar uma nova regra RDP para um servidor; começar a partir do dashboard de cliente de Barracuda GN Admin, navegue para o separador de configuração, na secção configuração operacional clique Ruleset, em seguida, clique em "Serviços" no menu de objetos de Firewall, desloque-se para baixo na lista de serviços e selecione o serviço de "RDP". Botão direito do rato e selecione copiar, em seguida, botão direito do rato e selecione colar. Agora existe um objeto de serviço de RDP Copy1 que podem ser editadas. Botão direito do rato RDP Copy1 e selecione editar, o objeto de serviço editar janela será apresentada até conforme mostrado aqui:

![Cópia da regra de RDP predefinida][5]

Os valores, em seguida, podem ser editados para representar o serviço RDP para um servidor específico. Para AppVM01 a regra de RDP predefinida acima deve ser alterada para refletir um novo nome de serviço, descrição e porta RDP externa utilizados no diagrama figura 8 (Nota: as portas são alteradas da predefinição de RDP de 3389 para a porta externa a ser utilizada para este servidor específico, no caso de AppVM01 a porta externa é 8025) o serviço modificado é mostrado abaixo :

![Regra de AppVM01][6]
 
Este processo deve ser repetido para criar serviços RDP para os servidores de restantes; AppVM02, DNS01 e IIS01. A criação de um destes serviços irá tornar a criação de regra mais simples e mais óbvias na secção seguinte.

>[AZURE.NOTE] Um serviço RDP para a Firewall não é necessária para duas razões; 1) primeiro a firewall VM é uma imagem de Linux com base no modo SSH seria utilizada no Porta 22 para a gestão de VM em vez de RDP e 2) porta 22 e duas outras portas gestão são permitidas na primeira regra gestão descrita abaixo para permitir a conectividade de gestão.

### <a name="firewall-rules-creation"></a>Criação de regras de firewall
Existem três tipos de regras de firewall utilizados neste exemplo, tenham ícones distintos:

A regra de aplicação redirecionar: ![Redirecionar o ícone da aplicação][7]

A regra de destino NAT: ![Destino NAT ícone][8]

A regra de fase: ![Passar ícone][9]

Podem encontrar mais informações sobre estas regras no web site da Barracuda.

Para criar as seguintes regras (ou verificar regras predefinido existentes), a partir do dashboard de cliente Barracuda GN Admin, navegue para o separador de configuração, na configuração operacionais secção, clique em Ruleset. Uma grelha denominada, "Principais regras" irá mostrar as regras de ativas e desativadas existentes neste firewall. No canto superior direito da grelha deste é uma pequena, verde "+" botão, clique em esta opção para criar uma nova regra (Nota: a firewall do pode ser "bloqueada" para as alterações, se vir um botão marcado como "Bloquear" e não conseguir criar ou editar regras, clique neste botão para "desbloquear" o conjunto de regras e permitir ações de edição). Se pretender editar uma regra existente, selecione nessa regra, botão direito do rato e selecione Editar regra.

Assim que as regras são criadas e/ou modificadas, devem ser enviados para a firewall e, em seguida, ativados, se isto não é feito as alterações de regra só terão efeito. O processo de push e ativação é descrito abaixo as descrições de regra de detalhes.

As especificidades de cada regra necessário para concluir este exemplo são descritas da seguinte forma:

- **Regra de gestão de firewall**: esta aplicação redirecionar regra permite que o tráfego passar para as portas de gestão do aparelho virtual rede, neste exemplo, uma Firewall de NextGen Barracuda. As portas de gestão são 801, 807 e, opcionalmente, 22. As portas internas e externas são os mesmos (ou seja, não tradução de porta). Esta regra, o ACCESS de gestão da configuração, é uma regra de predefinida e ativada por predefinição (na versão de Barracuda NextGen Firewall 6.1).

    ![Regra de gestão de firewall][10]

>[AZURE.TIP] O espaço de endereços de origem nesta regra for qualquer, se os intervalos de endereços IP gestão são conhecidos, reduzir este âmbito seria também para reduzir a superfície de ataque para as portas gestão.

- **Regras de RDP**: regras estes destino NAT irão permitir a gestão dos servidores individuais através do RDP.
Existem quatro campos Crítica necessários para criar esta regra:
  1.    Origem – para permitir que RDP a partir de qualquer lugar, a referência "Um" é utilizada no campo de origem.
  2.    Utilize o serviço – o objeto de serviço adequada criada anteriormente, neste caso, "AppVM01 RDP", as portas externas redirecionar para o endereço IP local servidores e à porta 3386 (a porta predefinida RDP). Esta regra específica é para acesso RDP AppVM01.
  3.    Destino – deverá *porta local no firewall*, o "DCHP 1 Local IP" ou eth0, se utilizar IPs estático. O número de ordinal (eth0, eth1, etc) podem ser diferente se o seu aparelho rede tem várias interfaces locais. Esta é a porta a firewall está a enviar saída do (podem ser iguais a porta de receção), o destino encaminhado real é no campo de lista de alvos.
  4.    Redirecionamento – esta secção indica o aparelho virtual onde finalmente redirecionar este tráfego. É o redirecionamento mais simples colocar a porta e IP (opcional) no campo de lista de alvos. Se não porta é utilizada a porta de destino no pedido de entrada será utilizada (ie sem conversão), se uma porta está designada a porta também estarão NAT juntamente com o PI abordem.

    ![Regra de RDP firewall][11]

    Um total de quatro regras de RDP terá de ser criada: 

  	|   Nome da regra    | Servidor  |   Serviço   |  Lista de alvos  |
  	|----------------|---------|-------------|---------------|
  	| RDP para IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP para DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP para AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP para AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] Limitar o âmbito de campos de origem e o serviço irá reduzir a superfície de ataque. O âmbito limitado mais que permitirá funcionalidade deve ser utilizado.

- **As regras de tráfego da aplicação**: Existem duas regras de tráfego de aplicação, o primeiro para o tráfego de web front-end e o segundo para o tráfego de back-end (ex servidor web para a camada de dados). Estas regras serão dependem a arquitetura de rede (onde os seus servidores são colocados) e fluxos de tráfego (que direção, são utilizados os fluxos de tráfego e quais as portas).

    A regra de front-end para o tráfego da web é explicado pela primeira vez:

    ![Regra de Web firewall][12]
 
    Esta regra de destino NAT permite que o tráfego de aplicação propriamente dita contactar o servidor de aplicação. Enquanto permitir que as outras regras de segurança, gestão, etc., as regras de aplicação são o que permitam que utilizadores externos ou serviços aceder as aplicações. Neste exemplo, existe um servidor web única na porta 80, pelo que a regra de aplicação único firewall irá redirecioná-o tráfego de entrada para IP externo, para o endereço web servidores interno IP.

    **Nota**: que não haja nenhuma porta atribuída no campo de lista de alvos, se, por conseguinte, a porta de entrada 80 (ou 443 para o serviço selecionado) será utilizada no redirecionamento do servidor web. Se o servidor web está a escutar uma porta diferente, por exemplo porta 8080, o campo da lista de alvos pode ser atualizado para 10.0.1.4:8080 para permitir o redirecionamento de porta também.

    A seguinte regra de tráfego de aplicação é a regra de back-end para permitir que o servidor Web a falar com o servidor de AppVM01 (mas não AppVM02) através de qualquer serviço:

    ![Regra de AppVM01 firewall][13]

    Esta regra fase permite qualquer servidor IIS na sub-rede Frontend para alcançar a AppVM01 (endereço IP 10.0.2.5) em qualquer porta, utilizando qualquer protocolo para aceder aos dados necessários pela aplicação web.

    Neste ecrã captura uma "\<explícitas destino\>" é utilizado no campo de destino para significam 10.0.2.5 como o destino. Isto pode ser quer explícito conforme mostrado ou um com o nome rede objeto (tal como foi feito nas pré-requisitos para o servidor DNS). Este é por excesso para o administrador da firewall Control que será utilizado método. Para adicionar 10.0.2.5 como um Explict Desitnation, faça duplo clique na primeira linha em branco em \<explícitas destino\> e introduza o endereço na janela que é apresentado.

    Com esta regra passar, sem NAT é necessária, uma vez que este é o tráfego interno, para que o método de ligação, pode ser definido como "Sem SNAT".

    **Nota**: rede de origem a esta regra for qualquer recurso na sub-rede front-end, se apenas haverá um ou um número específico conhecido de servidores web, um recurso de objeto de rede foi criado para ser mais específicas aos endereços IP dos exatos em vez de sub-rede Frontend completa.

>[AZURE.TIP] Esta regra utiliza o serviço de "Qualquer" para facilitar a aplicação de exemplo configurar e utilizar, isto também permitirá ICMPv4 (ping) numa única regra. No entanto, esta não é uma prática recomendada. As portas e protocolos ("serviços") devem ser reduzidos para mínimo possível que permite a operação de aplicação reduzir a superfície de ataque ao longo este limite.

<br />

>[AZURE.TIP] Apesar desta regra mostra uma referência de explícitas destino a ser utilizada, uma abordagem coerente deve ser utilizada em toda a configuração da firewall. Recomenda-se que o objeto de rede com nome ser utilizado para legibilidade mais fácil e compatibilidade. O explícitas destino só é utilizado aqui para mostrar um método de referência alternativo e não é recomendado geralmente (especialmente para configurações complexas).

- **Saída a regra de Internet**: passar esta regra irá permitir o tráfego da rede qualquer origem para passar para as redes de destino selecionadas. Esta regra é uma regra de predefinida normalmente já na firewall Barracuda NextGen, mas se encontra num estado desativado. Clicar com o botão direito nesta regra, pode aceder ao comando de ativar a regra. A regra é mostrada aqui foi modificada para adicionar as duas sub-redes locais que foram criadas como referências na secção pré-requisito deste documento para o atributo de origem desta regra.

    ![Regra de saída da firewall][14]

- **Regra de DNS**: passar esta regra só permite DNS (porta 53) tráfego passar para o servidor de DNS. Para este ambiente a maior parte dos tráfego a partir de front-end para o back-end está bloqueado, esta regra permite especificamente DNS.

    ![Regra de DNS firewall][15]

    **Nota**: neste ecrã está incluída captura o método de ligação. Uma vez que esta regra que interno IP para o tráfego de endereço IP interno, sem NATing é necessária, este o método de ligação é definido para "Sem SNAT" para esta regra fase.

- **Regra de sub-rede para sub-rede**: passar esta regra é uma regra de predefinido que tenha sido ativada e modificada para permitir que qualquer servidor na sub-rede back-end para ligar a qualquer servidor na sub-rede front-end. Esta regra é tráfego todos os interno, por isso o método de ligação, pode ser definido para sem SNAT.

    ![Regra de VNet dentro da firewall][16]

    **Nota**: A caixa de verificação bidirecional não der (nem está selecionada na maior parte das regras), este é significativo para esta regra em que faz esta regra "uma direccional", uma ligação pode ser iniciada a partir da sub-rede back-end para a rede de front-end, mas não o inverso. Se essa caixa de verificação foi selecionada, esta regra seria activar o tráfego de bidirecional, qual a partir do nosso diagrama lógico não pretendido.

- **Regra de tráfego de todos os negar**: Isto deve estar sempre a regra final (em termos de prioridade baixa) e, como tal se falha para corresponder a nenhum dos fluxos de tráfego de anterior regras vão ser largada por esta regra. Esta é uma regra de predefinida e normalmente ativado, sem modificações geralmente são necessários. 

    ![Firewall negar regra][17]

>[AZURE.IMPORTANT] Assim que são criadas todas as regras acima, é importante rever a prioridade de cada regra para garantir que o tráfego vai ser permitido ou negado conforme pretender. Neste exemplo, as regras são pela ordem que devem ser apresentados na grelha de principais de regras no cliente de gestão do Barracuda de reencaminhamento de chamadas.

## <a name="rule-activation"></a>Ativação de regra
Com o ruleset modificada para a especificação do diagrama lógica, o ruleset deve ser carregado para a firewall e, em seguida, ativado.

![Ativação de regra de firewall][18]
 
No canto superior direito do cliente de gestão do são um cluster dos botões. Clique no botão de "Enviar alterações" para enviar as regras modificadas para a firewall e, em seguida, clique no botão "Ativar".
 
Com a ativação de ruleset a firewall esta compilação de ambiente de exemplo está concluída.

## <a name="traffic-scenarios"></a>Cenários de tráfego
>[AZURE.IMPORTANT] Uma chave takeway é Lembre-se de que seja reencaminhado **todo** o tráfego através da firewall. Por isso, para ambiente de trabalho remoto no servidor IIS01, mesmo que é o serviço de nuvem Front-End e, no sub-rede Front-End, para aceder a este servidor iremos precisa para RDP da firewall em porta 8014 e, em seguida, permitir a firewall encaminhar o pedido RDP internamente para a porta de RDP IIS01. Botão de "Ligar" o portal Azure não funciona porque não existe nenhum caminho RDP direto para IIS01 (medida em pode ver o portal). Isto significa que todas as ligações da internet serão-se para o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx.

Para estes cenários, as seguintes regras de firewall devem estar no local:

1.  Gestão da firewall
2.  RDP para IIS01
3.  RDP para DNS01
4.  RDP para AppVM01
5.  RDP para AppVM02
6.  Tráfego da aplicação na Web
7.  Aplicação o tráfego para o AppVM01
8.  Saída à Internet
9.  Frontend para DNS01
10. Tráfego de dentro-sub-rede (back-end para front-end apenas)
11. Negar tudo

A firewall real ruleset provavelmente terá muitas outras regras para além de estas, as regras de qualquer firewall determinado também terá números de prioridade diferentes que os listados aqui. Esta lista e números associados são fornecer relevância entre basta estas onze regras e a prioridade relativa a-los. Por outras palavras; na firewall real, o "RDP para IIS01" podem ser regra número 5, mas desde que é abaixo a regra "Gestão da Firewall" e acima a regra "RDP para DNS01" seria alinhar com a intenção desta lista. A lista também irá ajudar a cenários permitindo uma questão de brevidade; abaixo Por exemplo, "FW regra 9 (DNS)". Também para uma questão de brevidade, as regras RDP quatro será constituem chamado, "as regras RDP" quando o cenário de tráfego está relacionado com o RDP.

Também resgatar que grupos de segurança de rede estão no local para o tráfego de internet entrada sub-redes front-end e back-end.

#### <a name="allowed-internet-to-web-server"></a>(Permitidos) Internet para o servidor Web
1.  Página HTTP de pedidos de utilizador do Internet a partir do SecSvc001.CloudApp.Net (Internet serviço de nuvem opostas)
2.  Tráfego através de ponto final abrir na porta 80 a interface de firewall no 10.0.0.4:80 fases do serviço de nuvem
3.  Sem NSG atribuído a sub-rede de segurança, por isso, regras NSG sistema permitem o tráfego para firewall
4.  O tráfego de acertos endereço IP interno da firewall (10.0.1.4)
5.  Firewall começa processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    Regras FW 2-5 (RDP regras) não aplicar, mover para a regra seguinte
  3.    FW regra 6 (aplicação: Web) aplicar, o tráfego é permitido, NAT de firewall para 10.0.1.4 (IIS01)
6.  A sub-rede Frontend começa processamento de regra de entrada:
  1.    Não se aplica NSG regra 1 (bloco Internet) (este tráfego foi NAT teria pela firewall, assim, o endereço de origem está agora a firewall que se encontra na sub-rede de segurança e visto por sub-rede Frontend NSG para ser tráfego "local" e, pelo que é permitido), deslocar-se para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
7.  IIS01 está a escutar tráfego da web, receber este pedido e inicia o processamento do pedido
8.  Tentativas de IIS01 inicia uma sessão de FTP para AppVM01 sub-rede back-end
9.  A rota UDR Frontend sub-rede faz com que a firewall do salto
10. Sem regras de saída numa sub-rede front-end, o tráfego é permitido
11. Firewall começa processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    Regra FW 2-5 (RDP regras) não aplicar, mover para a regra seguinte
  3.    FW regra 6 (aplicação: Web) não aplicar, mover para a regra seguinte
  4.    FW regra 7 (aplicação: back-end) aplicar, o tráfego é permitido, firewall encaminha o tráfego para o 10.0.2.5 (AppVM01)
12. A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não aplicar, mover para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
13.  AppVM01 recebe o pedido e inicia a sessão e responde
14. A rota UDR back-end sub-rede faz com que a firewall do salto
15. Uma vez que não existem regras NSG saídas na sub-rede back-end que a resposta é permitida
16. Uma vez que esta está a devolver tráfego de uma sessão de estabelecida a firewall transmite a resposta para o servidor web (IIS01)
17. Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não aplicar, mover para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
18. O servidor do IIS recebe a resposta, conclui a transação com AppVM01 e, em seguida, conclui construir a resposta de HTTP, esta resposta HTTP é enviada para o autor do pedido
19. Uma vez que não existem regras NSG saídas na sub-rede front-end que a resposta é permitida
20. A resposta de HTTP acertos a firewall e porque esta é a resposta a uma sessão NAT estabelecida for aceite pela firewall
21. A firewall redireciona, em seguida, voltar a resposta do utilizador Internet
22. Uma vez que existem não saída regras NSG ou saltos UDR na sub-rede front-end a resposta é permitida e o utilizador Internet recebe a página web solicitou.

#### <a name="allowed-internet-rdp-to-backend"></a>(Permitidos) Internet RDP para back-end
1.  Administrador do servidor na internet os pedidos de sessão RDP para AppVM01 através do SecSvc001.CloudApp.Net:8025, onde 8025 é o número de porta de utilizador atribuído para a regra de firewall "RDP para AppVM01"
2.  O serviço de nuvem transmite tráfego através do ponto final abrir na porta 8025 a firewall interface 10.0.0.4:8025
3.  Sem NSG atribuído a sub-rede de segurança, por isso, regras NSG sistema permitem o tráfego para firewall
4.  Firewall começa processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    FW regra 2 (RDP IIS) não aplicar, mover para a regra seguinte
  3.    FW regra 3 (RDP DNS01) não aplicar, mover para a regra seguinte
  4.    Aplicar FW regra 4 (RDP AppVM01), o tráfego é permitido, NAT de firewall para 10.0.2.5:3386 (porta RDP AppVM01)
5.  A sub-rede back-end começa processamento de regra de entrada:
  1.    Não se aplica NSG regra 1 (bloco Internet) (este tráfego foi NAT teria pela firewall, assim, o endereço de origem está agora a firewall que se encontra na sub-rede de segurança e visto por sub-rede back-end NSG para ser tráfego "local" e, pelo que é permitido), deslocar-se para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
6.  AppVM01 está a escutar tráfego RDP e responde
7.  Com regras de NSG sem saída, aplicam regras predefinidas e tráfego de retorno é permitido
8.  UDR encaminha o tráfego de saída para a firewall como próximo salto
9.  Uma vez que esta está a devolver tráfego de uma sessão de estabelecida a firewall transmite a resposta para o utilizador de internet
10. Sessão RDP é activado
11. AppVM01 pede palavra-passe de nome de utilizador

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitidos) Pesquisa de servidor DNS da Web no servidor DNS
1.  Servidor, IIS01, necessidades em termos de feed de dados na www.data.gov, mas as necessidades para resolver o endereço da Web.
2.  A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede back-end) como o servidor DNS principal, IIS01 envia o pedido DNS para DNS01
3.  UDR encaminha o tráfego de saída para a firewall como próximo salto
4.  Sem regras de NSG saída estiverem vinculadas à sub-rede front-end, o tráfego é permitido
5.  Firewall começa processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    Regra FW 2-5 (RDP regras) não aplicar, mover para a regra seguinte
  3.    FW regras 6 e 7 (aplicação regras) não aplicar, mover para a regra seguinte
  4.    FW regra 8 (para Internet) não aplicar, mover para a regra seguinte
  5.    Aplicar FW regra 9 (DNS), o tráfego é permitido, firewall encaminha o tráfego para o 10.0.2.4 (DNS01)
6.  A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não aplicar, mover para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
7.  Servidor DNS recebe o pedido
8.  Servidor de DNS não tem o endereço em cache e pede um servidor DNS de raiz na internet
9.  UDR encaminha o tráfego de saída para a firewall como próximo salto
10. Sem regras de NSG saída sub-rede back-end, o tráfego é permitido
11. Firewall começa processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    Regra FW 2-5 (RDP regras) não aplicar, mover para a regra seguinte
  3.    FW regras 6 e 7 (aplicação regras) não aplicar, mover para a regra seguinte
  4.     Aplicar FW regra 8 (para Internet), o tráfego é permitido, sessão está SNAT saída ao servidor DNS de raiz na Internet
12. Servidor DNS da Internet responde, uma vez que esta sessão foi iniciada a partir da firewall, a resposta for aceite pela firewall
13. Como este é uma sessão de estabelecimento, a firewall reencaminha a resposta para o servidor de início, DNS01
14. A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não aplicar, mover para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
15. O servidor DNS recebe e coloca a resposta e, em seguida, responde ao pedido inicial novamente para IIS01
16. A rota UDR back-end sub-rede faz com que a firewall do salto
17. Sem regras de NSG saída existirem na sub-rede back-end, o tráfego é permitido
18. Esta é uma sessão de estabelecimento na firewall, a resposta é reencaminhada pela firewall novamente para o servidor do IIS
19. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não existe nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede front-end, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema predefinida permitir o tráfego entre sub-redes permitirá este tráfego para que o tráfego é permitido
20. IIS01 recebe a resposta de DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Permitidos) Servidor de back-end para front-end
1.  Um administrador tem sessão iniciado no AppVM02 através do RDP os pedidos de um ficheiro diretamente a partir do servidor de IIS01 através do Explorador do windows
2.  A rota UDR back-end sub-rede faz com que a firewall do salto
3.  Uma vez que não existem regras NSG saídas na sub-rede back-end que a resposta é permitida
4.  Firewall começa processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    Regra FW 2-5 (RDP regras) não aplicar, mover para a regra seguinte
  3.    FW regras 6 e 7 (aplicação regras) não aplicar, mover para a regra seguinte
  4.    FW regra 8 (para Internet) não aplicar, mover para a regra seguinte
  5.    FW regra 9 (DNS) não aplicar, mover para a regra seguinte
  6.    Aplicar FW regra 10 (sub-rede interna), o tráfego é permitido, firewall transmite tráfego para 10.0.1.4 (IIS01)
5.  Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não aplicar, mover para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
6.  Partindo do princípio de autenticação adequada e autorização, IIS01 aceita o pedido e responde
7.  A rota UDR Frontend sub-rede faz com que a firewall do salto
8.  Uma vez que não existem regras NSG saídas na sub-rede front-end que a resposta é permitida
9.  Como este é uma sessão existente na firewall esta resposta é permitida e a firewall devolve a resposta AppVM02
10. Back-end sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não aplicar, mover para a regra seguinte
  2.    Regras de NSG predefinidas permitir o tráfego de sub-rede para sub-rede, o tráfego é permitido, parar processamento de regra NSG
11. AppVM02 recebe a resposta

#### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta para o servidor Web
1.  Utilizador da Internet tenta aceder ao servidor web, IIS01, através do serviço de FrontEnd001.CloudApp.Net
2.  Uma vez que não existem pontos finais aberta para o tráfego de HTTP, esta não seria passar através do serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por alguma razão, o NSG (bloco Internet) na sub-rede Frontend seria bloquear esse tráfego
4.  Por fim, a rota UDR sub-rede Frontend iria enviar qualquer tráfego de saída de IIS01 para a firewall como próximo salto e a firewall seria ver isto como tráfego assimétrico e largue a resposta de saída, pelo que existem, pelo menos, as três camadas independentes de defesa da internet e IIS01 através do seu serviço em nuvem a impedir o acesso não autorizado/inapropriado.

#### <a name="denied-internet-to-backend-server"></a>(Negado) Internet para o servidor back-end
1.  Utilizador da Internet tenta aceder a um ficheiro no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2.  Uma vez que não existem pontos finais aberto para partilha de ficheiros, esta não seria passar o serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por alguma razão, o NSG (bloco Internet) seria bloquear esse tráfego
4.  Por fim, a rota UDR iria enviar qualquer tráfego de saída de AppVM01 para a firewall como próximo salto, e a firewall seria ver isto como tráfego assimétrico e largue a resposta de saída, pelo que existem, pelo menos, as três camadas independentes de defesa da internet e AppVM01 através do seu serviço em nuvem a impedir o acesso não autorizado/inapropriado.

#### <a name="denied-frontend-server-to-backend-server"></a>(Negado) Servidor de front-end para back-end
1.  Partem do pressuposto IIS01 foi comprometida e está em execução de código malicioso tentar analisar os servidores de sub-rede back-end.
2.  A rota UDR Frontend sub-rede seria enviar qualquer tráfego de saída do IIS01 para a firewall como próximo salto. Não se trata algo que pode ser alterado pela VM comprometida.
3.  A firewall seria processar o tráfego, se o pedido foi para AppVM01 ou para o servidor DNS para pesquisas de DNS tráfego potencialmente pode ser permitido pela firewall (devido ao FW regras 7 e 9). Todos os outros tráfego seria bloqueado por FW regra 11 (negar todos).
4.  Se ameaça detecção avançada foi ativada na firewall (que não é abrangido neste documento, consulte a documentação do fornecedor para o seu aparelho rede específica ameaça funcionalidades avançadas), poderá ser impedido par tráfego que seria permitido pelas regras de reencaminhamento de chamadas básicas de outros fabricantes referidas neste documento se o tráfego contidas assinaturas conhecidas ou padrões sinalizar uma regra de ameaça avançadas.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS da Internet no servidor DNS
1.  Tenta de utilizador da Internet pesquisar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net 
2.  Uma vez que não existem pontos finais aberto para o tráfego de DNS, esta não seria passar através do serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por algum motivo, a regra NSG (bloco Internet) na sub-rede Frontend seria bloquear esse tráfego
4.  Por fim, a rota UDR de sub-rede back-end iria enviar qualquer tráfego de saída de DNS01 para a firewall como próximo salto e, a firewall seria ver isto como tráfego assimétrico e largue a resposta de saída, pelo que existem, pelo menos, as três camadas independentes de defesa da internet e DNS01 através do seu serviço em nuvem a impedir o acesso não autorizado/inapropriado.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Internet para o access SQL através da Firewall
1.  Utilizador da Internet os pedidos de dados SQL do SecSvc001.CloudApp.Net (Internet serviço de nuvem opostas)
2.  Uma vez que não existem pontos finais aberto para SQL, esta não seria passar o serviço de nuvem e não seria chegar a firewall
3.  Se os pontos finais SQL estavam abertos por algum motivo, a firewall seria começar processamento de regra:
  1.    FW regra 1 (FW gestão) não aplicar, mover para a regra seguinte
  2.    Regras FW 2-5 (RDP regras) não aplicar, mover para a regra seguinte
  3.    FW regra 6 e 7 (aplicação regras) não aplicar, mover para a regra seguinte
  4.    FW regra 8 (para Internet) não aplicar, mover para a regra seguinte
  5.    FW regra 9 (DNS) não aplicar, mover para a regra seguinte
  6.    FW regra 10 (dentro sub-rede) não aplicar, mover para a regra seguinte
  7.    Aplicar regra FW 11 (negar todos), o tráfego é processamento de regra bloqueados, parar


## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Guarde o Script completo num ficheiro de script PowerShell. Guarde a configuração de rede para um ficheiro com o nome "NetworkConf2.xml".
Modificar as variáveis definidas pelo utilizador, conforme necessário. Executar o script, em seguida, siga as instruções do programa de configuração de regra de Firewall acima.

#### <a name="full-script"></a>Script completo
Este script irá, com base nas variáveis de definidas pelo utilizador:

1.  Ligar a uma subscrição do Azure
2.  Criar uma nova conta de armazenamento
3.  Criar um novo VNet e três sub-redes tal como foi definido no ficheiro de configuração de rede
4.  Construir cinco máquinas virtuais; 1 firewall e o servidor de 4 windows VMs
5.  Configure UDR incluindo:
  1.    Criar duas novas tabelas rota
  2.    Adicionar rotas às tabelas
  3.    Vincular tabelas para sub-redes adequados
6.  Activar o reencaminhamento de IP na NVA
7.  Configure NSG incluindo:
  1.    Criar um NSG
  2.    Adicionar uma regra
  3.    Encadernação a NSG para as sub-redes adequadas

Este script do PowerShell localmente no deve ser executado que um internet ligado PC ou de servidor.

>[AZURE.IMPORTANT] Quando este script é executado, poderão existir avisos ou outras mensagens informativas que pop no PowerShell. Mensagens de erro apenas vermelho são motivos para preocupações.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>Ficheiro de configuração de rede
Guardar este ficheiro xml com localização actualizada e adicionar a ligação a este ficheiro para a variável de $NetworkConfigFile no script acima.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Scripts de exemplo de aplicações
Se pretender instalar uma aplicação de exemplo para esta e outros exemplos DMZ, um foi fornecido a seguinte ligação: [Exemplo de Script de aplicação][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vista lógica das regras de Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Criar um objeto de rede de front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Criar um objecto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Cópia da regra de RDP predefinida"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regra de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ícone da aplicação redirecionamento"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ícone NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Pass ícone"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regra de gestão de firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regra de RDP firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regra de Web firewall"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regra de AppVM01 firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regra de saída da firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regra de DNS firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regra de VNet dentro da firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Firewall negar regra"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
