<properties 
    pageTitle="Arquitetura de camadas de segurança com ambientes de aplicação de serviço" 
    description="Implementar uma arquitetura de camadas de segurança com ambientes de serviço de aplicação." 
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
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementar uma arquitetura de camadas de segurança com ambientes de aplicação de serviço

## <a name="overview"></a>Descrição geral ##
 
Uma vez que os ambientes de serviço de aplicação fornecer um ambiente do runtime isoladas implementado para uma rede virtual, os programadores podem criar uma arquitetura de segurança camadas fornecendo diferentes níveis de acesso de rede para cada camada físico da aplicação.

Um vontade comuns é ocultar API back-end cujo acesso geral de Internet e permitir apenas APIs para designado por aplicações web montante.  [Grupos de segurança (NSGs) de rede] [ NetworkSecurityGroups] podem ser utilizados em sub-redes que contém os ambientes de serviço de aplicação para restringir o acesso do público a API aplicações.

O diagrama abaixo mostra uma arquitetura de exemplo com uma aplicação de WebAPI com base implementada num ambiente do serviço de aplicação.  Três instâncias de aplicação web em separado, implementadas em três aplicação serviço ambientes separada, efectuam chamadas de back-end para a aplicação WebAPI mesmo.

![Arquitetura conceptual][ConceptualArchitecture] 

Nos sinais de adição verdes indicar que o grupo de segurança de rede na sub-rede que contêm "apiase" permite que as chamadas de entrada a partir das aplicações web montante, como bem chamadas a partir da própria.  No entanto o mesmo grupo de segurança de rede explicitamente impede o acesso ao geral tráfego da Internet. 

O resto deste tópico explica os passos necessários para configurar o grupo de segurança de rede na sub-rede que contêm "apiase".

## <a name="determining-the-network-behavior"></a>Para determinar o comportamento de rede ##
Para saber quais as regras de segurança de rede são necessários, tem de determinar quais os clientes de rede são permitidos para alcançar o ambiente do serviço de aplicação que contém a aplicação de API e os clientes que serão bloqueados.

Desde a [grupos de segurança de rede (NSGs)] [ NetworkSecurityGroups] são aplicados ao sub-redes e ambientes de serviço de aplicação estão implementados em sub-redes, as regras contidas uma NSG aplicarem a **todas as** aplicações em execução no ambiente de serviço de aplicação.  Utilizar a arquitetura de exemplo para neste artigo, assim que um grupo de segurança de rede é aplicado à sub-rede que contêm "apiase", todas as aplicações em execução no "apiase" ambiente de serviço de aplicação serão protegidas pelo mesmo conjunto de regras de segurança. 

- **Determinar o endereço IP de saída do montante aos autores das chamadas:**  O que é o endereço ou endereços IP dos montante os autores das chamadas?  Estes endereços terá de ser permitidas explicitamente acesso a NSG.  Uma vez que as chamadas entre ambientes de serviço de aplicação são consideradas chamadas "Internet", isto significa que o endereço IP de saída atribuído a cada um dos três ambientes de serviço de aplicação montante precisa de ser permissão de acesso no NSG para a sub-rede "apiase".   Para obter mais detalhes sobre como determinar o endereço IP de saída para aplicações em execução num ambiente do serviço de aplicação, consulte a [Arquitetura de rede] [ NetworkArchitecture] artigo Descrição geral.
- **A aplicação de API back-end necessitarão de chamar a próprio?**  Um ponto, por vezes esquecido e ténue é o cenário onde a aplicação de back-end tem de chamar a próprio.  Se necessitar de uma aplicação de API back-end no ambiente de serviço de aplicação chamar a próprio, isto também é tratado como uma chamada "Internet".  A arquitetura de exemplo é necessária permissão do acesso a partir do endereço IP de saída da "apiase" ambiente de serviço de aplicação, assim.

## <a name="setting-up-the-network-security-group"></a>Configurar o grupo de segurança de rede ##
Assim que o conjunto de saída endereços IP forem conhecidas, o passo seguinte é construir um grupo de segurança de rede.  Grupos de segurança de rede podem ser criados para o Gestor de ambos os recursos baseados redes virtuais, bem como redes virtuais clássicas.  Os exemplos abaixo mostram criar e configurar uma NSG numa rede virtual clássica através do Powershell.

Para a arquitetura de exemplo, os ambientes estão localizados no Sul Central-nos, para que esse região. é criada uma NSG vazia:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Primeiro uma explícita de permitir regra é adicionada para infraestrutura do Azure gestão conforme indicado no artigo sobre [o tráfego de entrada] [ InboundTraffic] para ambientes de serviço de aplicação.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
Em seguida, são adicionadas duas regras para permitir que as chamadas HTTP e HTTPS a partir do primeiro montante aplicação serviço ambiente ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Lavar e repita para o segundo e terceiro montante aplicação ambientes de serviço ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por fim, conceda acesso ao endereço IP do saída aplicação da API back-end ambiente de serviço para que pode ligar novamente para si próprio.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Outras regras de segurança de rede sem tem de ser configurado, uma vez que cada NSG tem um conjunto de regras de predefinidas bloquear o acesso de entrada da Internet por predefinição.

A lista completa de regras no grupo de segurança de rede são apresentadas abaixo.  Tenha em atenção como a última regra, está realçada, bloqueia entrado acesso a partir de todos os autores das chamadas além dos qual foi explicitamente concedido acesso.

![Configuração de NSG][NSGConfiguration] 

O passo final é aplicar a NSG à sub-rede que contém "apiase" ambiente de serviço de aplicação.  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Com o NSG aplicado à sub-rede, apenas os três montante aplicação serviço ambientes e o ambiente do serviço de aplicação que contém a API back-end, são permitidos para telefonar para o ambiente de "apiase".


## <a name="additional-links-and-information"></a>Ligações adicionais e informações ##
Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Informações sobre os [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md). 

Noções sobre [endereços IP saídos] [ NetworkArchitecture] e ambientes de serviço de aplicação.

[Portas de rede] [ InboundTraffic] utilizado pelo ambientes de serviço de aplicação.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
