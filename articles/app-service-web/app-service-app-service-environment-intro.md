<properties 
    pageTitle="Introdução à aplicação de serviço ambiente" 
    description="Saiba mais sobre a funcionalidade de ambiente de serviço de aplicação que fornece unidades da escala associadas VNet, seguro dedicado para executar todas as aplicações." 
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

# <a name="introduction-to-app-service-environment"></a>Introdução à aplicação de serviço ambiente

## <a name="overview"></a>Descrição geral ##
Um ambiente do serviço de aplicação é um [Premium] [ PremiumTier] opção de plano de serviço de aplicação do Azure que fornece um ambiente totalmente isolado e dedicado para segura execução do aplicações de serviço de aplicação do Azure alta, incluindo [Aplicações Web]do serviço[WebApps], [Aplicações Mobile][MobileApps]e [API Apps][APIApps].  

Ambientes de aplicação de serviço são ideais para das cargas de trabalho de aplicação que exige o:

- Escala muito alta
- Isolamento e acesso seguro à rede

Os clientes podem criar vários ambientes de serviço de aplicação numa única região Azure e entre as regiões Azure múltiplos.  Isto torna ambientes de serviço de aplicação ideal para horizontalmente dimensionamento camadas de aplicação de estado menos apoio alta RPS das cargas de trabalho.

Ambientes de aplicação de serviço são isolados para executar apenas as aplicações de um único cliente e são sempre implementados para uma rede virtual.  Os clientes tenham extensivamente controlo sobre o tráfego de rede ambas as aplicações de entrada e saída e aplicações podem estabelecer ligações seguras alta velocidade através de redes virtuais para recursos da empresa no local.

Todos os artigos e como-para do sobre ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Para uma descrição geral de como os ambientes de serviço de aplicação ativar escala alta e proteger o acesso de rede, consulte o [Vôo abrangente picado AzureCon] [ AzureConDeepDive] na aplicação de serviço ambientes!

Para uma aprofundada no horizontalmente dimensionamento utilizando vários ambientes de serviço de aplicação consulte o artigo sobre como configurar um [ambiental distribuído geo aplicação][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança apresentada na vôo picado abrangente a AzureCon foi configurada, consulte o artigo sobre como implementar um [colocados arquitetura de segurança](app-service-app-service-environment-layered-security.md) com ambientes de serviço de aplicação.

Aplicações em execução no ambientes de serviço de aplicação, podem ter o seu acesso controlado pela dispositivos montante como firewalls de aplicação web (WAF).  O artigo sobre [como configurar um WAF para ambientes de serviço de aplicação](app-service-app-service-environment-web-application-firewall.md) abrange este cenário. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Cluster dedicado recursos ##
Todos os recursos de cluster num ambiente do serviço de aplicação estão dedicados exclusivamente a uma subscrição única e um ambiente do serviço de aplicação podem ser configurado com até cinquenta (50) cluster recursos para utilização exclusiva por uma única aplicação.

Um ambiente do serviço de aplicação é composto por um agrupamento de recursos cluster front-end, bem como agrupamentos de recursos de cluster de trabalho de um a três. 

O conjunto de front-end contém recursos de cluster responsável por cessação SSL como balanceamento de carga bem automática de pedidos de aplicação dentro de um ambiente do serviço de aplicação. 

Cada conjunto de trabalho contém recursos de cluster alocados à [Aplicação de serviço planos][AppServicePlan], que contêm um ou mais aplicações de serviço de aplicação do Azure sucessivamente.  Dado que podem existir até três agrupamentos de trabalho diferente num ambiente do serviço de aplicação, tem a flexibilidade de poder escolher recursos de cluster diferente para cada conjunto de trabalho.  

Por exemplo, esta opção permite-lhe criar um conjunto de trabalho com menos poderosos cluster recursos para a aplicação de serviço planos destinados a aplicações de desenvolvimento ou de teste.  Um conjunto de trabalho segundo (ou mesmo terceiro) foi utilize mais eficazes cluster recursos destinados a aplicação de serviço planos executar aplicações de produção.

Para obter mais detalhes sobre a quantidade de recursos de cluster disponíveis para conjuntos de dados front-end e trabalhador, consulte [como configurar um ambiente do serviço de aplicação][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre a disponíveis calcular tamanhos de recurso suportados um ambiente do serviço de aplicação, consulte os [Preços de serviço de aplicação] [ AppServicePricing] da página e rever as opções disponíveis para ambientes de serviço de aplicação no Premium preços de camadas.

## <a name="virtual-network-support"></a>Suporte de rede virtual ##
Pode ser criado um ambiente do serviço de aplicação no **quer** uma rede virtual do Gestor de recursos do Azure, **ou** uma implementação clássica modelar rede virtual ([mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]).  Uma vez que um ambiente do serviço de aplicação sempre existe na rede virtual e com mais precisão dentro de uma sub-rede de uma rede virtual, pode tirar partido as funcionalidades de segurança de redes virtuais para controlar ambas as comunicações de entrada e saída da rede.  

Um ambiente do serviço de aplicação pode ser qualquer um dos Internet opostas com um endereço IP público ou internos opostas com apenas um endereço de Azure Balanceador de carga interna (ILB).

Pode utilizar [grupos de segurança de rede] [ NetworkSecurityGroups] para restringir as comunicações de rede de entrada para a sub-rede onde reside um ambiente do serviço de aplicação.  Esta opção permite-lhe executar as aplicações atrasado montante dispositivos e serviços como o firewalls de aplicação web e os fornecedores de SaaS de rede.

Aplicações frequentemente também precisa de aceder a recursos da empresa, tais como bases de dados internas e os serviços web.  Uma abordagem comum é tornar estes pontos finais disponíveis apenas para o tráfego de rede interna a fluir dentro de uma rede virtual Azure.  Depois de um ambiente do serviço de aplicação é aderido à mesma rede virtual como os serviços internos, aplicações em execução no ambiente podem aceder aos mesmos, incluindo os pontos finais acessíveis através do [Site para o Site] [ SiteToSite] e [Azure ExpressRoute] [ ExpressRoute] ligações.

Para obter mais detalhes sobre como os ambientes de serviço de aplicação funciona com redes virtuais e redes no local consultar os seguintes artigos no [Arquitetura de rede][NetworkArchitectureOverview], [Controlar o tráfego de entrada][ControllingInboundTraffic]e [Em segurança estabelecer ligação ao back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para iniciar a aplicação de serviço ambientes, consulte o artigo [Como para criar uma aplicação de ambiente de serviço][HowToCreateAnAppServiceEnvironment]

Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

Para uma descrição geral da arquitectura de rede do ambiente de serviço de aplicação, consulte o artigo [Descrição geral de arquitectura de rede] [ NetworkArchitectureOverview] artigo.

Para obter detalhes sobre como utilizar um ambiente do serviço de aplicação com ExpressRoute, consulte o seguinte artigo [encaminhar Express]em ambientes de serviço de aplicação[NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
