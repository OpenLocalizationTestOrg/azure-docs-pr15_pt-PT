<properties 
    pageTitle="Detalhes de configuração de rede para trabalhar com encaminhar Express" 
    description="Detalhes de configuração de rede para executar o ambientes de serviço de aplicação num redes virtuais ligada a um circuito ExpressRoute." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Detalhes de configuração de rede para ambientes de aplicação de serviço com ExpressRoute 

## <a name="overview"></a>Descrição geral ##
Os clientes podem ligar um [Azure ExpressRoute] [ ExpressRoute] circuito à sua infraestrutura de rede virtual, assim expandir a sua rede no local para Azure.  Pode ser criado um ambiente do serviço de aplicação numa sub-rede desta [rede virtual] [ virtualnetwork] infraestrutura.  Aplicações em execução no ambiente de serviço de aplicação, em seguida, podem estabelecer ligações seguras para recursos de back-end acessíveis apenas através da ligação de ExpressRoute.  

Pode ser criado um ambiente do serviço de aplicação no **quer** uma rede virtual do Gestor de recursos do Azure, **ou** uma implementação clássica modelar rede virtual.  Com uma alteração recente efetuada no de 2016 de Junho, agora também podem ser implementadas ASEs para redes virtuais que utilizam intervalos de endereços público ou espaços de endereços RFC1918 (isto é endereços privados). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Conectividade da rede necessários ##
Existem requisitos de conectividade de rede para ambientes de serviço de aplicação que não poderá ser correspondidos inicialmente numa rede virtual ligada a uma ExpressRoute.  Aplicação de serviço ambientes exigem todas as ações seguintes para funcionar corretamente:


-  Conectividade de rede de saída para pontos finais de armazenamento do Windows Azure em todo o mundo em ambas as portas 80 e 443.  Isto inclui os pontos finais localizados na mesma região, como o ambiente de serviço de aplicação, bem como os pontos finais de armazenamento localizado na **outras** regiões Azure.  Azure armazenamento os pontos finais resolver em seguintes domínios de DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.  
-  Conectividade de rede de saída para o serviço de ficheiros do Azure na porta 445.
-  Conectividade de rede de saída para os pontos finais de Sql DB localizado na mesma região como o ambiente do serviço de aplicação.  Resolver os pontos finais de SQL DB no domínio que se segue: *database.windows.net*.  Isto requer que abrir o acesso às portas 1433, 11000 11999 e 14000 14999.  Para obter mais detalhes, consulte [Este artigo sobre a utilização de porta V12 de base de dados do Sql](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Conectividade de rede de saída para os pontos de finais de plano de gestão Azure (ASM e processador pontos finais).  Isto inclui conectividade de saída para *management.core.windows.net* e *management.azure.com*. 
-  Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*.  Isto é necessário para suportar a funcionalidade SSL.
-  A configuração de DNS para a rede virtual tem de ser capaz de resolver todos os pontos finais e domínios mencionados os pontos anteriores.  Se estes pontos finais não podem ser resolvidos, irão falhar tentativas de criação de ambiente de serviço de aplicação e ambientes de serviço de aplicação existente serão marcados como danificado.
-  Acesso de saída na porta 53 é necessário para a comunicação com os servidores DNS.
-  Se existir um servidor de DNS personalizado no final de um gateway VPN, o servidor DNS tem de ser acessível a partir da sub-rede que contém o ambiente do serviço de aplicação. 
-  O caminho de rede de saída não é possível viajam através de internos proxies empresariais nem pode ser forçar túnel para no local.  Fazê-lo altera o endereço NAT eficaz saída de tráfego de rede do ambiente de serviço de aplicação.  Alterar o endereço NAT saído de um ambiente de serviço de aplicação tráfego de rede irão causar falhas de conectividade a muitas dos pontos finais listados em cima.  Isto resulta em tentativas falhadas de criação de ambiente de serviço de aplicação, bem como anteriormente Saudável ambientes de serviço de aplicação que está a ser marcada como danificado.  
-  Acesso de rede portas necessárias de entrada para a aplicação de serviço ambientes deverá ser permitido conforme descrito neste [artigo][requiredports].

Os requisitos de DNS podem ser correspondidos por garantir uma infraestrutura DNS válida é configurada e manter-se para a rede virtual.  Se por qualquer motivo, a configuração de DNS for alterada após a criação de um ambiente do serviço de aplicação, os programadores podem forçar um ambiente do serviço de aplicação para continuar a nova configuração de DNS.  Acionar gradual ambiente reiniciar o computador utilizando o ícone "Reiniciar" localizada na parte superior da pá de gestão de ambiente de serviço de aplicação no [portal do Azure] [ NewPortal] irão provocar o ambiente continuar a nova configuração de DNS.

Os requisitos de acesso de rede de entrada poderá ser correspondidos por configurar um [grupo de segurança de rede] [ NetworkSecurityGroups] sub-rede o ambiente de serviço de aplicação para permitir o acesso necessário, tal como descrito neste [artigo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Ativar a conectividade da rede de saída para um ambiente de aplicação de serviço##
Por predefinição, um circuito ExpressRoute recentemente criado anuncia uma rota predefinida que permite a conectividade saída de Internet.  Com esta configuração será possível ligar a outros pontos finais Azure um ambiente do serviço de aplicação.

No entanto, uma configuração de cliente comum é definir os seus próprios rota de predefinida (0.0.0.0/0) força o tráfego de saída de Internet em vez disso fluxo no local.  Este fluxo de tráfego quebras Invariavelmente ambientes de serviço de aplicação, porque o tráfego de saída é quer bloqueado no local ou NAT utilizaria para um conjunto de endereços que já não funcionam com os pontos finais Azure vários reconhecido.

A solução é definir um (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém o ambiente do serviço de aplicação.  Um UDR define rotas de sub-rede específicas que serão respeitadas em vez da rota predefinida.

Se possível, recomenda-se para utilizar a configuração seguinte:

- A configuração de ExpressRoute anuncia 0.0.0.0/0 e por predefinição forçar estabelece um túnel todo o tráfego de saída no local.
- UDR aplicado à sub-rede que contém o ambiente do serviço de aplicação define 0.0.0.0/0 com um tipo de salto seguinte de Internet (um exemplo de isto é mais para baixo neste artigo).

O efeito combinado destes passos é que o nível de sub-rede UDR terá precedência sobre ExpressRoute forçada túnel, garantindo assim o acesso à Internet saído do ambiente de serviço de aplicação.

> [AZURE.IMPORTANT] Rotas definidas numa UDR **deve** ser específicas precedência sobre qualquer rotas anunciadas pela configuração ExpressRoute.  O exemplo abaixo utiliza o intervalo de endereços 0.0.0.0/0 abrangentes e como tal pode potencialmente acidentalmente substituído por anúncios de rota utilizando intervalos de endereços mais específicos.
>
>Ambientes de serviço de aplicação não são suportadas com configurações ExpressRoute esse **anunciar cruzada rotas a partir do caminho peering público para o caminho peering privado**.  ExpressRoute configurações que tenham efectuado público configurado, irá receber anúncios de rota da Microsoft para um conjunto maior de intervalos de endereços IP do Microsoft Azure.  Se estes intervalos de endereços são anunciada cruzada no caminho peering privado, o resultado final é que todos os pacotes de rede de saída de sub-rede o ambiente de serviço de aplicação será túnel forçar à infraestrutura de rede de um cliente no local.  Este fluxo de rede não é suportado atualmente com ambientes de serviço de aplicação.  Uma solução para este problema é para parar de publicidade cruzada rotas, a partir do caminho peering público para o caminho peering privada.

Obter informações gerais sobre vias definidas pelo utilizador estão disponíveis nesta [Descrição geral][UDROverview].  

Detalhes sobre como criar e configurar rotas definidas pelo utilizador está disponível neste [O guia][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Exemplo de configuração do UDR para um ambiente do serviço de aplicação ##

**Pré-requisitos**

1. Instalar o Powershell do Azure a partir da [página de transferências do Azure] [ AzureDownloads] (datada Junho 2015 ou posterior).  Em "Ferramentas de linha de comandos" existe uma ligação de "Instalar" em "Do Windows Powershell" que irá instalar os cmdlets do Powershell mais recentes.

2. É recomendado que uma única sub-rede é criada para utilização exclusiva por um ambiente do serviço de aplicação.  Este procedimento garante que UDRs aplicados à sub-rede só serão aberto o tráfego de saída para o ambiente do serviço de aplicação.
3. **Importante**: não implemente o ambiente do serviço de aplicação até **após** são seguidos os seguintes passos de configuração.  Isto garante que está disponível a conectividade da rede saída antes de tentar implementar um ambiente do serviço de aplicação.

**Passo 1: Criar uma tabela de rota com nome**

O fragmento de seguinte cria uma tabela de rota denominada "DirectInternetRouteTable" na região Costa-nos Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Passo 2: Criar uma ou mais rotas na tabela rota**

Terá de adicionar uma ou mais rotas à tabela encaminhar para poder ativar o acesso à Internet.  

É a abordagem recomendada para configurar o acesso à Internet de saída definir uma rota para 0.0.0.0/0 conforme apresentado abaixo.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Lembre-se de que 0.0.0.0/0 for um intervalo de endereço abrangentes e como tal será substituído pelo mais específicos intervalos de endereços anunciados pela ExpressRoute.  Para voltar iteramos a recomendação anterior, um UDR com uma rota 0.0.0.0/0 deve ser utilizada em conjunto com uma configuração de ExressRoute apenas anuncia 0.0.0.0/0 também. 

Como alternativa, pode transferir uma lista completa e atualizada CIDR intervalos utilizado por Azure.  O ficheiro Xml que contém todos os intervalos de endereços IP do Azure está disponível a partir do [Centro de transferências da Microsoft][DownloadCenterAddressRanges].  

Note apesar que estes intervalos alterar ao longo do tempo, por este motivo periódicas atualizações manuais às rotas definidas pelo utilizador para manter as sincronizadas.  Além disso, uma vez que existe um limite superior predefinido de 100 rotas numa única UDR, terá de "resumir" os intervalos de endereços IP do Azure para se ajustar dentro do limite 100 encaminhar, tendo em conta que UDR definido rotas necessitam de ser mais específico do que as rotas anunciada pelo seu ExpressRoute.  


**Passo 3: Associar a tabela rota à sub-rede que contém o ambiente do serviço de aplicação**

O último passo de configuração é associar a tabela rota à sub-rede onde será implementado no ambiente de serviço de aplicação.  O seguinte comando associa "DirectInternetRouteTable" para o "ASESubnet" que acaba por ser irá conter um ambiente do serviço de aplicação.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Passo 4: Final passos**

Assim que a tabela rota está vinculada à sub-rede, recomenda-se para o primeiro teste e confirme o efeito pretendido.  Por exemplo, implementar uma máquina virtual para a sub-rede e confirme que:


- Tráfego de saída para os pontos finais Azure e que não sejam Azure mencionado anteriormente neste artigo é **não** a fluir para baixo o circuito ExpressRoute.  É muito importante verificar este comportamento, uma vez que se for o tráfego de saída da sub-rede continua a ser forçada túnel no local, ambiente de serviço de aplicação criação sempre irá falhar. 
- Pesquisas DNS para os pontos finais mencionado anteriormente são todas corretamente resolver. 

Assim que são confirmados os passos acima, terá de eliminar a máquina virtual porque a sub-rede tem de estar "vazio" ao tempo que é criado o ambiente do serviço de aplicação.
 
Em seguida, avance com a criação de um ambiente do serviço de aplicação!

## <a name="getting-started"></a>Introdução
Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Para iniciar a aplicação de serviço ambientes, consulte [Introdução à aplicação de ambiente de serviço][IntroToAppServiceEnvironment]

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
