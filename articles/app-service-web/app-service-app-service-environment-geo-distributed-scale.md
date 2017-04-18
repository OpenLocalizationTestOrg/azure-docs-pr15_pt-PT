<properties 
    pageTitle="Geo distribuído escala com ambientes de aplicação de serviço" 
    description="Saiba como horizontalmente dimensionar aplicações utilizar distribuição geo com Gestor de tráfego e ambientes de serviço de aplicação." 
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
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Geo distribuído escala com ambientes de aplicação de serviço

## <a name="overview"></a>Descrição geral ##
Cenários de aplicações que requerem escala muito alta podem exceder a capacidade de recurso cluster disponível para uma única implementação de uma aplicação.  Aplicações de voto, eventos desportivos e eventos de entretenimento televisiva são todos os exemplos de cenários que requerem escala extremamente elevada. Requisitos de escala alta podem ser correspondidos por escalar horizontalmente para fora de aplicações, com vários implementações da aplicação efetuadas numa única região e através de regiões, para processar carga representarem requisitos.

Ambientes de aplicação de serviço são uma plataforma ideal para horizontal escala de saída.  Uma vez num aplicação serviço ambiente configuração tiver sido seleccionada que pode suportam uma taxa de pedido conhecidos, os programadores podem implementar adicionais ambientes de serviço de aplicação no moda "separador cookie" para alcançar uma capacidade de carga de pico pretendida.

Por exemplo, imaginemos uma aplicação em execução numa configuração de ambiente de serviço de aplicação foi testada para lidar com pedidos de 20K por segundo (RPS).  Se a capacidade de carga de pico pretendida for 100K RPS, em seguida, ambientes serviço aplicação cinco (5) podem ser criados e configurados para garantir que a aplicação pode processar a carga prevista máxima.

Uma vez que os clientes normalmente aceder a aplicações utilizar um domínio personalizado (ou relegado), os programadores necessitar de uma forma para distribuir pedidos de aplicação por todas as ocorrências de ambiente de serviço de aplicação.  É uma excelente forma para realizar esta tarefa resolver o domínio personalizado utilizando um [perfil do Gestor de tráfego Azure][AzureTrafficManagerProfile].  Perfil do Gestor de tráfego de pode ser configurado para aponte para todos os ambientes de serviço de aplicação individuais.  Gestor de tráfego automaticamente processará clientes distribuição em todos os ambientes de serviço de aplicação com base nas definições no perfil do Gestor de tráfego de balanceamento de carga.  Esta abordagem funciona independentemente se todos os ambientes de serviço de aplicação estão implementados numa região Azure única ou em todo o mundo implementados entre as regiões Azure múltiplos.

Além disso, uma vez que os clientes aceder aplicações através de domínio relegado, os clientes estão ignora do número de ambientes de serviço de aplicação a executar uma aplicação.  Como resultado os programadores podem forma rápida e fácil adicionar e remover, com base em carga de tráfego observados ambientes do serviço de aplicação.

O diagrama conceptual abaixo ilustra uma aplicação horizontalmente dimensionada saída em três ambientes de serviço de aplicação numa única região.

![Arquitetura conceptual][ConceptualArchitecture] 

O resto deste tópico explica os passos que envolvem a configurar o uma topologia distribuída para a aplicação de exemplo com vários ambientes de serviço de aplicação.

## <a name="planning-the-topology"></a>Planear a topologia ##
Antes de criação de saída ambiental uma aplicação distribuído, é útil para que algumas partes informações antecedência.

- **Domínio personalizado para a aplicação:**  O que é o nome de domínio personalizado que os clientes irão utilizar para aceder à aplicação?  O nome de domínio personalizado para a aplicação de exemplo é *www.scalableasedemo.com*
- **Gestor de tráfego domínio:**  Um nome de domínio tem de ser escolhido ao criar um [perfil do Gestor de tráfego Azure][AzureTrafficManagerProfile].  Este nome irá ser combinado com o sufixo de *trafficmanager.net* para registar uma entrada de domínio é gerida pelo Gestor de tráfego.  Para a aplicação de exemplo, o nome escolhido é *demonstração dimensionáveis auxiliar*.  Como resultado o nome completo do domínio gerido pelo Gestor de tráfego é *demo.trafficmanager.net dimensionáveis auxiliar*.
- **Estratégia de dimensionamento ambiental a aplicação:**  Vai ser distribuído ambiental a aplicação em vários ambientes de serviço de aplicação numa única região?  Várias regiões?  Uma mistura-e-correspondência de ambas as abordagens?  A decisão deve ser baseada em expectativas dos onde o tráfego do cliente vai originar, bem como também podem dimensionar os restantes de uma aplicação suportar infraestrutura de back-end.  Por exemplo, com uma aplicação sem estado de 100%, uma aplicação pode ser previstos excederem consideravelmente dimensionada utilizando uma combinação de vários ambientes de serviço de aplicação por região Azure, multiplicado por ambientes de serviço de aplicação implementado entre as regiões Azure múltiplos.  Com 15 + públicos Azure regiões disponíveis para escolha, clientes verdadeiramente podem criar uma planta de todo o mundo hyper escala aplicação.  Para a aplicação de exemplo utilizada para este artigo, três ambientes de serviço de aplicação foram criados numa única região Azure (Sul Central E.u.a.).
- **Convenção de nomenclatura para os ambientes de serviço de aplicação:**  Cada ambiente do serviço de aplicação requer um nome exclusivo.  Para além de um ou dois ambientes de serviço de aplicação é conveniente ter uma convenção de nomenclatura para ajudar a identificar cada ambiente do serviço de aplicação.  Para a aplicação de exemplo foi utilizada uma convenção de nomenclatura simple.  Os nomes dos três ambientes de serviço de aplicação estão *fe1ase*, *fe2ase*e *fe3ase*.
- **Convenção de nomenclatura para as aplicações:**  Uma vez que serão implementadas várias instâncias da aplicação, é necessário um nome para cada instância da aplicação implementada.  Uma funcionalidade pequeno conhecidos mas muito conveniente da aplicação de serviço ambientes é que o mesmo nome de aplicação pode ser utilizado em vários ambientes de serviço de aplicação.  Uma vez que cada ambiente do serviço de aplicação tem um sufixo de domínio exclusivos, os programadores podem optar por utilizar novamente o mesmo nome de aplicação em cada ambiente.  Por exemplo um programador pode ter aplicações com o nome da seguinte forma: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  Embora para a aplicação de exemplo cada instância de aplicação também tem um nome exclusivo.  Os nomes de instância da aplicação utilizados são *webfrontend1*, *webfrontend2*e *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>Configurar o perfil do Gestor de tráfego de ##
Depois de várias instâncias de uma aplicação são implementadas em vários ambientes de serviço de aplicação, as instâncias da aplicação individuais podem ser registadas com o Gestor de tráfego.  Para a aplicação de exemplo um Gestor de tráfego de perfil de é necessária para *demo.trafficmanager.net dimensionáveis auxiliar* que podem encaminhar clientes para qualquer uma das seguintes instâncias de aplicação implementado:

- **webfrontend1.fe1ase.p.azurewebsites.net:**  Uma instância da aplicação de exemplo implementada no ambiente de serviço de aplicação primeiro.
- **webfrontend2.fe2ase.p.azurewebsites.net:**  Uma instância da aplicação de exemplo implementada no ambiente de serviço de aplicação segundo.
- **webfrontend3.fe3ase.p.azurewebsites.net:**  Uma instância da aplicação de exemplo implementada no ambiente de serviço de aplicação de terceiros.

A forma mais fácil para registar várias Azure aplicação pontos finais de serviço, todos os em execução no **mesmo** Azure região, é com o Powershell de [suporte do Gestor de tráfego de Gestor de recursos do Azure][ARMTrafficManager].  

O primeiro passo é criar um perfil de Gestor de tráfego Azure.  O código abaixo mostra como o perfil foi criado para a aplicação de exemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Repare como o parâmetro *RelativeDnsName* foi definido para *demonstração dimensionáveis auxiliar*.  Este é o modo como o nome de domínio *demo.trafficmanager.net dimensionáveis auxiliar* é criado e associados a um perfil do Gestor de tráfego.

O parâmetro *TrafficRoutingMethod* define o Gestor de tráfego irá utilizar para determinar como distribuir a carga de cliente por todos os pontos finais disponíveis de política de balanceamento de carga.  Neste exemplo a *ponderado* método foi escolhido.  Isto resultará em pedidos de cliente que está a ser distribuídos por todos os pontos finais de aplicação registada com base na espessura relativa associada a cada ponto final. 

Com o perfil criado, cada instância de aplicação é adicionada ao perfil como um ponto final de Azure nativo.  O código abaixo obtém uma referência para cada front-end web app e, em seguida, adiciona cada aplicação como um ponto final de Gestor de tráfego através do parâmetro *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Tenha em conta como se existe uma chamada para *Adicionar AzureTrafficManagerEndpointConfig* para cada instância de aplicação individuais.  O parâmetro *TargetResourceId* cada comando Powershell faz referência a uma das três instâncias de aplicação implementada.  Perfil do Gestor de tráfego de irá distribuir a carga por todos os pontos três finais registados no perfil.

Todos os pontos três finais utilizam o mesmo valor (10) para o parâmetro de *espessura* .  Isto resulta no Gestor de tráfego de pedidos de propagação de cliente através de todas as instâncias da aplicação três relativamente uniformemente. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontar para domínio personalizado a aplicação no Gestor de tráfego de domínio ##
O passo final necessário é aponte o domínio da aplicação no Gestor de tráfego domínio personalizado.  Para a aplicação de exemplo Isto significa que apontam para *www.scalableasedemo.com* *demo.trafficmanager.net dimensionáveis auxiliar*.  Este passo tem de ser concluído com a entidade que gere o domínio personalizado.  

Utilizar ferramentas de gestão de domínio a entidade de registo, um CNAME registos precisa de ser criada que aponta o domínio personalizado no Gestor de tráfego domínio.  A imagem abaixo mostra um exemplo de o aspeto esta configuração CNAME:

![CNAME para o domínio personalizado][CNAMEforCustomDomain] 

Apesar de não abrangidas neste tópico, lembre-se de que cada instância individual app necessita que o domínio personalizado registado com o mesmo assim.  Caso contrário, se um pedido de torna-o para uma instância da aplicação e a aplicação não tem o domínio personalizado registado com a aplicação, o pedido irá falhar.  

Neste exemplo, o domínio personalizado é *www.scalableasedemo.com*e cada instância da aplicação tem o domínio personalizado associado.

![Domínio personalizado][CustomDomain] 

Para um recapitular de registar um domínio personalizado com aplicações de serviço de aplicação do Azure, consulte o seguinte artigo sobre como [Registar domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentar a topologia distribuído ##
O resultado final da configuração do Gestor de tráfego e DNS é que os pedidos de *www.scalableasedemo.com* fluxo de através de sequência que se segue:

1. Num browser ou dispositivo fará uma pesquisa de DNS para *www.scalableasedemo.com*
2. A entrada CNAME na entidade de registo de domínio faz com que a pesquisa de DNS ser redirecionado para o Gestor de tráfego Azure.
3. Uma pesquisa de DNS é tornada para *demo.trafficmanager.net dimensionáveis auxiliar* relativamente a um dos servidores DNS do Gestor de tráfego de Azure.
4. Com base no balanceamento de política de (o parâmetro *TrafficRoutingMethod* utilizado anteriormente quando criar o perfil do Gestor de tráfego), o Gestor de tráfego irá Selecione uma dos pontos finais configurados e devolver o FQDN desse ponto final para o browser ou dispositivo.
5.  Uma vez que o FQDN do ponto final é o Url de uma instância de aplicação em execução no ambiente de serviço de aplicação, browser ou dispositivo irá pedir um servidor DNS da Microsoft Azure para resolver o FQDN para um endereço IP. 
6. Browser ou dispositivo irá enviar o pedido HTTP/S para o endereço IP.  
7. O pedido serão entregues das instâncias da aplicação executado dos ambientes de serviço de aplicação.

A imagem de consola abaixo mostra uma pesquisa de DNS do domínio personalizado a aplicação de exemplo com êxito resolver para uma instância de aplicação em execução dos três ambientes de serviço de aplicação de exemplo (neste caso, o segundo dos três ambientes de serviço de aplicação):

![Pesquisa de DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Ligações adicionais e informações ##
Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Documentação sobre o Powershell de [suporte do Gestor de tráfego de Gestor de recursos do Azure][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
