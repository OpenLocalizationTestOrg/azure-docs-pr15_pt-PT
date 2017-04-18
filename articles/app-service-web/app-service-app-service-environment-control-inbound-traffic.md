<properties 
    pageTitle="Como controlar o tráfego para um serviço de aplicação de ambiente de entrada" 
    description="Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um ambiente do serviço de aplicação." 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego para um serviço de aplicação de ambiente de entrada

## <a name="overview"></a>Descrição geral ##
Pode ser criado um ambiente do serviço de aplicação no **quer** uma rede virtual do Gestor de recursos do Azure, **ou** uma implementação clássica modelar [rede virtual][virtualnetwork].  Uma nova rede virtual e nova sub-rede podem ser definidas no momento que é criado um ambiente do serviço de aplicação.  Em alternativa, um ambiente do serviço de aplicação podem ser criado numa rede virtual pré-existentes e sub-rede pré-existentes.  Com uma alteração recente efetuada no de 2016 de Junho, agora podem ser implementadas ASEs para redes virtuais que utilizar intervalos de endereços público ou espaços de endereços RFC1918 (isto é endereços privados).  Para obter mais detalhes sobre como criar um ambiente do serviço de aplicação, consulte [como criar um ambiente do serviço de aplicação][HowToCreateAnAppServiceEnvironment].

Um ambiente do serviço de aplicação sempre deve ser criado dentro de uma sub-rede, uma vez que um sub-rede fornece um limite de rede que pode ser utilizado para bloquear o tráfego de entrada atrás montante dispositivos e serviços assim que o tráfego de HTTP e HTTPS for aceite apenas a partir de endereços IP montante específicos.

Tráfego de rede de entrada e saída numa sub-rede é controlado utilizando um [grupo de segurança de rede][NetworkSecurityGroups]. Controlar o tráfego de entrada necessita de criar regras de segurança de rede num grupo de segurança de rede e, em seguida, atribuir a segurança de rede agrupar sub-rede que contém o ambiente do serviço de aplicação.

Depois de um grupo de segurança de rede é atribuído a uma sub-rede, o tráfego de entrada para as aplicações no ambiente de serviço de aplicação é permitidos/bloqueados com base em permitir e negar regras definidas no grupo de segurança de rede.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>Portas de rede utilizadas num ambiente de aplicação de serviço ##
Antes de bloquear o tráfego de rede entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede necessários e opcionais utilizadas por um ambiente do serviço de aplicação.  Fechar acidentalmente desativar o tráfego para algumas portas pode resultar na perda de funcionalidade num ambiente do serviço de aplicação.

Segue-se uma lista de portas utilizadas por um ambiente do serviço de aplicação. Todas as portas estão **TCP**, salvo indicação em contrário claramente:

- 454: **obrigatório porta** utilizado pelo infraestrutura Azure para gerir e manutenção de ambientes de serviço de aplicação através de SSL.  Não bloquear o tráfego para esta porta.  Esta porta sempre está vinculada à VIP público de um Auxiliar.
- 455: **obrigatório porta** utilizado pelo infraestrutura Azure para gerir e manutenção de ambientes de serviço de aplicação através de SSL.  Não bloquear o tráfego para esta porta.  Esta porta sempre está vinculada à VIP público de um Auxiliar.
- 80: predefinido porta para o tráfego de HTTP entrada às aplicações em execução no serviço de aplicação planos num ambiente do serviço de aplicação.  Num Auxiliar com capacidade de ILB, esta porta está vinculada para o endereço ILB da Auxiliar.
- 443: predefinido porta para o tráfego de SSL entrada às aplicações em execução no serviço de aplicação planos num ambiente do serviço de aplicação.  Num Auxiliar com capacidade de ILB, esta porta está vinculada para o endereço ILB da Auxiliar.
- 21: canal de controlo para FTP.  Esta porta pode ser bloqueada com segurança se FTP não está a ser utilizado.  Num Auxiliar com capacidade de ILB, pode estar vinculada esta porta para o endereço ILB para um Auxiliar.
- 990: canal de controlo para FTPS.  Esta porta pode ser bloqueada com segurança se FTPS não está a ser utilizado.  Num Auxiliar com capacidade de ILB, pode estar vinculada esta porta para o endereço ILB para um Auxiliar.
- 10001 10020: canais de dados para FTP.  Tal como acontece com o canal de controlo, estas portas podem ser com segurança bloqueadas se FTP não está a ser utilizado.  Num Auxiliar com capacidade de ILB, esta porta pode estar vinculada ao endereço ILB a Auxiliar.
- 4016: utilizado para depuração remota com Visual Studio 2012.  Esta porta pode ser bloqueada com segurança se a funcionalidade não está a ser utilizada.  Num Auxiliar com capacidade de ILB, esta porta está vinculada para o endereço ILB da Auxiliar.
- 4018: utilizado para depuração remoto com o Visual Studio 2013.  Esta porta pode ser bloqueada com segurança se a funcionalidade não está a ser utilizada.  Num Auxiliar com capacidade de ILB, esta porta está vinculada para o endereço ILB da Auxiliar.
- 4020: utilizado para depuração remota com Visual Studio 2015.  Esta porta pode ser bloqueada com segurança se a funcionalidade não está a ser utilizada.  Num Auxiliar com capacidade de ILB, esta porta está vinculada ao endereço ILB da Auxiliar.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e de conectividade de saída ##
Para um ambiente do serviço de aplicação funcionar corretamente, é necessário acesso de saída para vários pontos finais. Se uma lista completa dos pontos finais externos utilizados por um Auxiliar na secção "Obrigatório a conectividade da rede" do artigo [Configuração de rede para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Aplicação de serviço ambientes requerem uma infraestrutura DNS válida configurada para a rede virtual.  Se por qualquer motivo, a configuração de DNS for alterada após a criação de um ambiente do serviço de aplicação, os programadores podem forçar um ambiente do serviço de aplicação para continuar a nova configuração de DNS.  Acionar gradual ambiente reiniciar o computador utilizando o ícone "Reiniciar" localizada na parte superior da pá de gestão de ambiente de serviço de aplicação no [portal do Azure] [ NewPortal] irão provocar o ambiente continuar a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet ser configuração antecedência antes de criar um ambiente do serviço de aplicação.  Se a configuração de DNS de uma rede virtual for alterada enquanto está a ser criado um ambiente do serviço de aplicação, que irá resultar no falhe de processo de criação de ambiente de serviço de aplicação.  Num vein semelhante, se existe um servidor de DNS personalizado no final de um gateway VPN e o servidor DNS está inacessível ou indisponível, o processo de criação de ambiente de serviço de aplicação também irá falhar.

## <a name="creating-a-network-security-group"></a>Criar um grupo de segurança de rede ##
Para detalhes completos sobre como o trabalho de grupos de segurança de rede, consulte as seguintes [informações][NetworkSecurityGroups].  Realça os detalhes abaixo tocar dos grupos de segurança de rede, com um foco sobre a configuração e aplicar um grupo de segurança de rede para uma sub-rede que contém um ambiente do serviço de aplicação.

**Nota:** Grupos de segurança de rede podem ser configurados graficamente utilizando o [Portal do Azure](https://portal.azure.com) ou através do Azure PowerShell.

Grupos de segurança de rede são criados pela primeira vez como uma entidade autónomo associada a uma subscrição. Uma vez que grupos de segurança de rede são criados numa região Azure, certifique-se de que o grupo de segurança de rede é criado na mesma região como o ambiente do serviço de aplicação.

A seguinte demonstra a criação de um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Quando um grupo de segurança de rede estiver criado, um ou mais regras de segurança de rede são adicionadas à mesma.  Uma vez que o conjunto de regras podem ser alteradas ao longo do tempo, recomenda-se para espaçar saída o esquema de numeração utilizado para as prioridades da regra para que seja mais fácil a inserção regras adicionais ao longo do tempo.

O exemplo abaixo apresenta uma regra que concede explicitamente o acesso às portas gestão necessário por infraestrutura do Azure para gerir e mantêm um ambiente do serviço de aplicação.  Note que todo o tráfego de gestão flua através de SSL e certificados de cliente, está protegido para que apesar das portas são abertas que sejam inacessível por qualquer entidade que não seja a infraestrutura de gestão Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Quando a bloquear o acesso a porta 80 e 443 para um ambiente do serviço de aplicação atrás montante dispositivos ou serviços "Ocultar", terá de conhecer o endereço IP montante.  Por exemplo, se estiver a utilizar uma firewall de aplicação web (WAF), o WAF terá as suas próprias endereço (ou endereços IP) que utiliza quando o tráfego para um ambiente do serviço de aplicação descendentes proxy.  Terá de utilizar este endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada a partir de um endereço IP montante específico explicitamente é permitido.  O endereço *1.2.3.4* é utilizada como um marcador de posição para o endereço IP de um montante WAF.  Altere o valor para corresponder ao endereço utilizado pelo seu dispositivo montante ou serviço.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Se suporte FTP for pretendido, as seguintes regras podem ser utilizadas como um modelo para conceder acesso a porta de controlo FTP e dados portas de canal de.  Uma vez que FTP é um protocolo com estado, não poderá conseguir encaminhar o tráfego FTP através de um dispositivo de firewall ou proxy HTTP/HTTPS tradicional.  Neste caso terá de definir o *SourceAddressPrefix* para um valor diferente - por exemplo o intervalo de endereços IP do programador ou máquinas implementação no qual FTP clientes estão a ser executado. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** o intervalo de porta de canal de dados podem ser alteradas durante o período de pré-visualização.)

Se for utilizado depuração remota com o Visual Studio, as seguintes regras demonstram como conceder acesso.  Existe uma regra separada para cada versão suportada do Visual Studio, uma vez que cada versão utiliza uma porta diferente para a depuração remota.  Tal como acontece com acesso FTP, o tráfego depuração remoto poderá não o fluxo corretamente através de um WAF tradicional ou dispositivo de proxy.  O *SourceAddressPrefix* em vez disso, pode ser definido para o intervalo de endereço IP de máquinas programador a executar o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuir um grupo de segurança de rede para uma sub-rede ##
Um grupo de segurança de rede tem uma regra de segurança predefinidos que recusa o acesso a todo o tráfego externo.  O resultado de combinar as regras de segurança de rede descritas acima e a regra de segurança predefinidos bloquear o tráfego de entrada, é que apenas o tráfego do endereço de origem intervalos associados a uma ação de *Permitir* poderão a enviar tráfego para aplicações em execução num ambiente do serviço de aplicação.

Depois de um grupo de segurança de rede é povoado com regras de segurança, necessita de ser atribuída à sub-rede que contém o ambiente do serviço de aplicação.  O comando de atribuição referencia tanto o nome da rede virtual onde reside o ambiente do serviço de aplicação, bem como o nome da sub-rede onde foi criado o ambiente do serviço de aplicação.  

O exemplo abaixo mostra um grupo de segurança de rede a ser atribuído a um sub-rede e de rede virtual:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Assim que a atribuição de grupo de segurança de rede é concluída com êxito (a atribuição é um operações de execução longa e pode demorar alguns minutos a concluir), só o tráfego de entrada *Permitir* regras de correspondência com êxito será atinja aplicações no ambiente de serviço de aplicação.

Para integridade o exemplo seguinte mostra como remover e, por conseguinte, dis-associar o grupo de segurança de rede da sub-rede:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para explícita de tipos de SSL de IP ##
Se uma aplicação está configurada com um SSL de IP explícita de tipos de endereço (aplicável ao ASEs conter apenas um VIP público), em vez de utilizar o endereço IP predefinido a aplicação de ambiente de serviço, HTTP e HTTPS tráfego flui para a sub-rede através de um conjunto diferente de portas diferente de portas 80 e 443.

O par individual de portas utilizadas por cada endereço IP SSL pode ser encontrado na interface de utilizador do portal de pá UX de detalhes do ambiente de serviço de aplicação.  Selecione "todas as definições"--> "Endereços IP".  O pá "Endereços IP" mostra uma tabela de todos os endereços de IP SSL explicitamente configuradas para o ambiente de serviço de aplicação, juntamente com o par de porta especial que é utilizado para encaminhar o tráfego HTTP e HTTPS associado a cada endereço IP SSL.  É este par de porta que precisa de ser utilizada para os parâmetros de DestinationPortRange ao configurar regras de um grupo de segurança de rede.

Quando uma aplicação num Auxiliar está configurada para utilizar IP SSL, clientes externos não irão visualizar e não necessita de se preocupar o mapeamento de par porta especial.  O tráfego para as aplicações irá fluxo normalmente para o endereço IP SSL configurado.  A tradução ao par porta especial acontece automaticamente internamente durante a ramificação final da encaminhar o tráfego para a sub-rede que contém o Auxiliar. 

## <a name="getting-started"></a>Introdução

Para iniciar a aplicação de serviço ambientes, consulte [Introdução à aplicação de ambiente de serviço][IntroToAppServiceEnvironment]

Todos os artigos e como-para para o ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para a aplicação de serviço ambientes](../app-service/app-service-app-service-environments-readme.md).

Para obter detalhes à volta de forma segura estabelecer ligação ao recurso back-end a partir de um ambiente do serviço de aplicação de aplicações, consulte o artigo [ligar-se em segurança para recursos de back-end a partir de um ambiente do serviço de aplicação][SecurelyConnecttoBackend]

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
