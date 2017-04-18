<properties 
    pageTitle="Em segurança ligar a recursos de back-end a partir de um ambiente de aplicação de serviço" 
    description="Saiba mais sobre como ligar em segurança para recursos de back-end a partir de um ambiente do serviço de aplicação." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Em segurança ligar a recursos de back-end a partir de um ambiente de aplicação de serviço #

## <a name="overview"></a>Descrição geral ##
Uma vez que um ambiente do serviço de aplicação sempre é criado em **qualquer** uma rede virtual do Gestor de recursos do Azure, **ou** uma implementação clássica modelar [rede virtual][virtualnetwork], podem um fluxo de ligações de saída a partir de um ambiente do serviço de aplicação para outros recursos de back-end exclusivamente através da rede virtual.  Com uma alteração recente efetuada no de 2016 de Junho, também podem ser implementadas ASEs para redes virtuais que utilizam intervalos de endereços público ou espaços de endereços RFC1918 (isto é endereços privados).  

Por exemplo, poderá ser uma SQL Server em execução num cluster de máquinas virtuais com porta 1433 bloqueada.  O ponto final poderá ser ACLd para permitir apenas o acesso a partir de outros recursos na mesma rede virtual.  

Como outro exemplo, os pontos finais sensíveis a maiúsculas e pode ser executada no local e estar ligados ao Azure através de qualquer um dos [Site para o Site] [ SiteToSite] ou [Azure ExpressRoute] [ ExpressRoute] ligações.  Como resultado, apenas os recursos no virtuais redes ligadas para o Site para o Site ou ExpressRoute túneis poderão aceder a pontos finais de no local.

Para todas as estes cenários, aplicações em execução no ambiente de serviço de aplicação poderão ligar em segurança para os servidores de vários e recursos.  O tráfego de saída a partir das aplicações em execução num ambiente do serviço de aplicação para os pontos finais privados na mesma rede virtual (ou ligado à rede virtual mesmo), será apenas fluxo através da rede virtual.  Não será um fluxo de tráfego de saída para os pontos finais privados através da Internet pública.

Um truque aplica-se para o tráfego de saída de um ambiente do serviço de aplicação para os pontos finais dentro de uma rede virtual.  Ambientes de aplicação de serviço não é possível alcançar os pontos finais da máquinas virtuais localizados na **mesma** sub-rede como o ambiente do serviço de aplicação.  Isto normalmente não deve ser um problema desde que são implementadas ambientes de serviço de aplicação para uma sub-rede reservada para utilização em modo exclusivo por apenas o ambiente do serviço de aplicação.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e de conectividade de saída ##
Para um ambiente do serviço de aplicação funcionar corretamente, é necessário acesso de saída para vários pontos finais. Se uma lista completa dos pontos finais externos utilizados por um Auxiliar na secção "Obrigatório a conectividade da rede" do artigo [Configuração de rede para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Ambientes de aplicação de serviço requerer uma infraestrutura DNS válida configurada para a rede virtual.  Se por qualquer motivo, a configuração de DNS for alterada após a criação de um ambiente do serviço de aplicação, os programadores podem forçar um ambiente do serviço de aplicação para continuar a nova configuração de DNS.  Acionar gradual ambiente reiniciar o computador utilizando o ícone de "Reiniciar" localizado na parte superior da pá de gestão de ambiente de serviço de aplicação no portal do irão provocar o ambiente continuar a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet ser configuração antecedência antes de criar um ambiente do serviço de aplicação.  Se a configuração de DNS de uma rede virtual for alterada enquanto está a ser criado um ambiente do serviço de aplicação, que irá resultar no falhe de processo de criação de ambiente de serviço de aplicação.  Num vein semelhante, se existe um servidor de DNS personalizado no final de um gateway VPN e o servidor DNS está inacessível ou indisponível, o processo de criação de ambiente de serviço de aplicação também irá falhar.

## <a name="connecting-to-a-sql-server"></a>Ligar a um servidor SQL
Uma configuração do SQL Server comum tem um ponto final escutar porta 1433:

![Ponto final do SQL Server][SqlServerEndpoint]

Existem duas abordagens para restringir o tráfego para este ponto final:


- [Listas de controlo de acesso de rede] [ NetworkAccessControlLists] (ACL de rede)

- [Grupos de segurança de rede][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Restringir o acesso com uma rede ACL

Porta 1433 pode ser protegida utilizando uma lista de controlo de acesso de rede.  O exemplo abaixo listas brancas cliente endereços provenientes dentro de uma rede virtual e bloquear o acesso a todos os outros clientes.

![Exemplo de lista de controlo de acesso de rede][NetworkAccessControlListExample]

Poderão quaisquer aplicações em execução no ambiente de serviço de aplicação na mesma rede virtual como o SQL Server ligar a instância do SQL Server utilizando o endereço IP de **VNet interna** para a máquina virtual do SQL Server.  

A cadeia de ligação de exemplo abaixo referencia o SQL Server utilizando o endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Apesar da máquina virtual tem um público ponto final de também, tentativas de ligação utilizando o endereço IP público serão rejeitadas devido a ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringir o acesso com um grupo de segurança de rede
É uma abordagem alternativa para proteger o acesso com um grupo de segurança de rede.  Grupos de segurança de rede podem ser aplicados para máquinas virtuais individuais ou para uma sub-rede que contém máquinas virtuais.

Em primeiro lugar um grupo de segurança de rede precisa de ser criada:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso a apenas VNet tráfego interno é muito simple com um grupo de segurança de rede.  As regras de predefinidas num grupo de segurança de rede só permitem o acesso a partir de outros clientes de rede na mesma rede virtual.

Como resultado bloquear acesso ao SQL Server é tão simple como aplicar um grupo de segurança de rede com as regras de predefinido a dos máquinas virtuais a executar o SQL Server ou sub-rede que contém as máquinas virtuais.

O exemplo abaixo aplica-se um grupo de segurança de rede à sub-rede que contenham:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
O resultado final é um conjunto de regras de segurança que bloqueiam acesso externo, enquanto permitir o acesso interno VNet:

![Regras de segurança de rede predefinidas][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Introdução
Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Para iniciar a aplicação de serviço ambientes, consulte [Introdução à aplicação de ambiente de serviço][IntroToAppServiceEnvironment]

Para obter detalhes à volta de controlar o tráfego de entrada para o seu ambiente de serviço de aplicação, consulte o artigo [controlar o tráfego para um ambiente do serviço de aplicação][ControlInboundASE]

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
