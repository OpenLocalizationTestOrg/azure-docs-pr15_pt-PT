<properties 
    pageTitle="Descrição geral de arquitectura de rede de ambientes de aplicação de serviço" 
    description="Descrição geral da arquitectura de rede topologia ofApp ambientes de serviço." 
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

# <a name="network-architecture-overview-of-app-service-environments"></a>Descrição geral de arquitectura de rede de ambientes de aplicação de serviço

## <a name="introduction"></a>Introdução ##
Ambientes de serviço de aplicação sempre são criados dentro de uma sub-rede de uma [rede virtual] [ virtualnetwork] -aplicações em execução num ambiente do serviço de aplicação possam comunicar com os pontos finais privados localizados dentro da mesma topologia da rede virtual.  Uma vez que os clientes podem bloquear para baixo partes de infraestrutura de rede virtual, é importante compreender os tipos de rede comunicação fluxos monetários que ocorrem com um ambiente do serviço de aplicação.

## <a name="general-network-flow"></a>Fluxo de geral de rede ##
 
Quando um ambiente de serviço de aplicação (Auxiliar) utiliza um endereço IP virtual (VIP) público para as aplicações, todo o tráfego entrada chega a esse VIP público.  Inclui o tráfego HTTP e HTTPS para aplicações e outro tráfego para FTP, a funcionalidade de depuração remota e operações de gestão de Azure.  Para uma lista completa das portas específicas (necessárias e opcionais) que estão disponíveis no VIP público, consulte o artigo sobre como [controlar o tráfego de entrada] [ controllinginboundtraffic] para um ambiente do serviço de aplicação. 

Aplicação de serviço ambientes também suportam a execução aplicações que são vinculadas apenas para um endereço interno rede virtual, também conhecido como um endereço de (Balanceador de carga interno) ILB.  Num ILB activado tráfego Auxiliar, HTTP e HTTPS para aplicações, bem como chamadas de depuração remotas, chegam são enviadas no endereço ILB.  Para configurações ILB Auxiliar mais comuns, o tráfego FTP/FTPS também serão entregues no endereço ILB.  No entanto operações de gestão de Azure ainda serão fluxo para portas 454/455 no VIP público de um ILB activada Auxiliar.

O diagrama abaixo apresenta uma descrição geral de vários fluxos de entrada e saída da rede para um ambiente do serviço de aplicação onde as aplicações estão vinculadas para um endereço IP virtual público:

![Fluxos de rede geral][GeneralNetworkFlows]

Um ambiente do serviço de aplicação possam comunicar com uma variedade de pontos finais de cliente privado.  Por exemplo, aplicações em execução no ambiente de serviço de aplicação podem ligar a base de dados servidor (es) em execução em máquinas virtuais de IaaS na topologia da rede virtual mesmo.

>[AZURE.IMPORTANT] Consultar o diagrama de rede, os "outras calcular recursos" são implementados no sub-rede diferente do ambiente de serviço de aplicação. Implementar recursos na mesma sub-rede com o Auxiliar bloqueará conectividade de Auxiliar aos recursos (exceto específico dentro-Auxiliar encaminhamento). Implemente o para uma sub-rede diferente em vez disso (na VNET a mesma). O ambiente do serviço de aplicação, em seguida, poderá ligar. Não é configuração adicional necessária.

Ambientes de aplicação de serviço comunicam também com DB do Sql e armazenamento do Windows Azure recursos necessários para gerir e operativo um ambiente do serviço de aplicação.  Alguns dos recursos Sql e armazenamento que um ambiente do serviço de aplicação comunica com estão localizados na mesma região como o ambiente do serviço de aplicação, enquanto outras pessoas estão localizadas no regiões Azure remotos.  Como resultado, conectividade de saída para a Internet é sempre necessária para um ambiente do serviço de aplicação a funcionar corretamente. 

Uma vez que um ambiente do serviço de aplicação for implementado numa sub-rede, grupos de segurança de rede podem ser utilizados para controlar o tráfego de entrada para a sub-rede.  Para obter detalhes sobre como controlar o tráfego de entrada para um ambiente do serviço de aplicação, consulte o seguinte [artigo][controllinginboundtraffic].

Para obter detalhes sobre como permitir conectividade de Internet saída de um ambiente do serviço de aplicação, consulte o seguinte artigo sobre como trabalhar com [Express encaminhar][ExpressRoute].  A mesma abordagem descrita no artigo aplica-se ao trabalhar com a conectividade de Site para o Site e utilizar forçada túnel.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída ##
Quando um ambiente do serviço de aplicação faz com que as chamadas de saída, um endereço IP sempre está associado a chamadas de saída.  O endereço IP específico que é utilizado depende se o ponto final que está a ser denominado se encontra dentro de topologia da rede virtual ou fora da topologia da rede virtual.

Se o ponto final que está a ser denominado estiver **fora** da topologia de rede virtual, em seguida, o endereço de saída (também conhecidos pelo endereço NAT saído) que é utilizado é o público VIP a aplicação de ambiente de serviço.  Este endereço pode ser encontrado na interface de utilizador do portal para o ambiente do serviço de aplicação no pá de propriedades.
 
![Endereço IP de saída][OutboundIPAddress]

Também pode ser determinado este endereço para ASEs conter apenas um VIP público através da criação de uma aplicação no ambiente de serviço de aplicação e, em seguida, efetuar uma *nslookup* no endereço a aplicação. O endereço IP resultante é tanto o VIP público, bem como endereço NAT saído do ambiente de serviço de aplicação.

Se for o ponto final que está a ser denominado **dentro** da topologia de rede virtual, o endereço de saída da aplicação chamada será o endereço IP interno do recurso cluster individuais executar a aplicação.  No entanto não é um mapeamento persistente virtual rede internos de endereços IP de aplicações.  Aplicações podem deslocar-se ao longo de recursos de cluster diferente e o conjunto de cluster disponível recursos num ambiente do serviço de aplicação, podem alterar devido dimensionamento operações.

No entanto, uma vez que um ambiente do serviço de aplicação sempre localizado dentro de uma sub-rede, são garantir que o endereço IP interno de um recurso de cluster a executar uma aplicação sempre aparecerá no intervalo CIDR da sub-rede.  Como resultado, quando ACL extensivamente ou grupos de segurança de rede são utilizados para proteger o acesso a outros pontos finais dentro da rede virtual, o intervalo de sub-rede que contém o ambiente do serviço de aplicação tem de lhe ser concedido acesso.

O diagrama seguinte mostra estes conceitos mais detalhadamente:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

- Uma vez que o público VIP a aplicação de ambiente de serviço 192.23.1.2, que é o endereço IP de saída utilizado quando efetuar chamadas para os pontos finais de "Internet".
- O intervalo CIDR de sub-rede que contenham para o ambiente do serviço de aplicação é 10.0.1.0/26.  Outros pontos finais dentro de infraestrutura de rede virtual do mesmo Verão chamadas a partir das aplicações como provenientes de algures este intervalo de endereços.

## <a name="calls-between-app-service-environments"></a>Efetuar chamadas entre ambientes de aplicação de serviço ##
Um cenário mais complexo pode ocorrer se implementar vários ambientes de serviço de aplicação na mesma rede virtual e fazer chamadas de saída de um ambiente do serviço de aplicação para ambiente de outro serviço de aplicação.  Estes tipos de cruz chamadas também serão tratadas como chamadas de "Internet" ambiente de serviço de aplicação.

O diagrama seguinte mostra um exemplo de uma arquitetura de camadas com aplicações no ambiente de serviço de uma aplicação (por exemplo, "Porta frente" web apps) chamar aplicações num ambiente do serviço de aplicação segunda (por exemplo, internas back-end API aplicações não se destina a ser acessíveis a partir da Internet). 

![Efetuar chamadas entre ambientes de aplicação de serviço][CallsBetweenAppServiceEnvironments] 

No exemplo acima o ambiente de serviço de aplicação "Auxiliar um" tem um endereço IP de saída de 192.23.1.2.  Se uma aplicação a ser executado neste ambiente de serviço de aplicação efetuar uma chamada de saída para uma aplicação em execução num ambiente de serviço de aplicação segunda ("Auxiliar dois") localizada na mesma rede virtual, a chamada de saída será tratado como uma chamada de "Internet".  Como resultado o tráfego de rede entregue na segunda ambiente de serviço de aplicação mostrará como provenientes 192.23.1.2 (ou seja, não sub-rede intervalo de endereços a primeira aplicação de ambiente de serviço).

Apesar das chamadas entre diferentes ambientes de serviço de aplicação são tratadas como chamadas de "Internet", quando ambos os ambientes de serviço de aplicação estão localizados na mesma região Azure o tráfego de rede permanecerá na rede Azure regional e não será física fluxo através da Internet pública.  Como resultado pode utilizar um grupo de segurança de rede na sub-rede a segunda aplicação de ambiente de serviço para permitir apenas as chamadas de entrada a partir do ambiente de serviço de aplicação primeiro (cujo endereço IP de saída é 192.23.1.2), assegurando comunicação segura entre os ambientes de serviço de aplicação.

## <a name="additional-links-and-information"></a>Ligações adicionais e informações ##
Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Para obter detalhes sobre entrada portas utilizadas pelo ambientes de serviço de aplicação e utilizar grupos de segurança de rede para controlar o tráfego de entrada está disponível [aqui][controllinginboundtraffic].

Detalhes sobre a utilização de utilizador definidos rotas para conceder acesso à Internet a aplicação de serviço ambientes está disponível neste [artigo][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

