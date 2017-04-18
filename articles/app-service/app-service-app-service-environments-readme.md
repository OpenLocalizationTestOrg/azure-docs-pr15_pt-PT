<properties 
    pageTitle="Ambiente de aplicação de serviço | Microsoft Azure" 
    description="O que é um ambiente do Azure aplicação do serviço? Uma introdução à aplicação de ambiente de serviço." 
    keywords="ambiente de serviço de aplicação Azure, rede virtual e seguro redes"
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

# <a name="app-service-environment-documentation"></a>Documentação do ambiente de serviço de aplicação

Um ambiente do serviço de aplicação é um [Premium] [ PremiumTier] opção de plano de serviço de aplicação do Azure que fornece um ambiente totalmente isolado e dedicado para segura execução do aplicações de serviço de aplicação do Azure alta, incluindo [Aplicações Web]do serviço[WebApps], [Aplicações Mobile][MobileApps]e [API Apps][APIApps].  

Ambientes de aplicação de serviço são ideais para das cargas de trabalho de aplicação que exige o:

- Escala muito alta
- Isolamento e acesso seguro à rede

Os clientes podem criar vários ambientes de serviço de aplicação numa única região Azure e entre as regiões Azure múltiplos.  Isto torna ambientes de serviço de aplicação ideal para horizontalmente dimensionamento camadas de aplicação de estado menos apoio alta RPS das cargas de trabalho.

Ambientes de aplicação de serviço são isolados para executar apenas as aplicações de um único cliente e são sempre implementados para uma rede virtual.  Os clientes tenham extensivamente controlo sobre o tráfego de rede ambas as aplicações de entrada e saída utilizar [grupos de segurança de rede][NetworkSecurityGroups].  Aplicações também podem estabelecer ligações seguras alta velocidade através de redes virtuais para recursos da empresa no local.

Aplicações frequentemente precisa de aceder a recursos da empresa, tais como bases de dados internas e os serviços web.  Aplicações em execução no ambientes de serviço de aplicação podem aceder aos recursos acessíveis através do [Site para o Site] [ SiteToSite] VPN e [Azure ExpressRoute] [ ExpressRoute] ligações.

* [O que é um ambiente do serviço de aplicação?](../app-service-web/app-service-app-service-environment-intro.md)
* [Criar um ambiente de aplicação de serviço](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Criar aplicações num ambiente de aplicação de serviço](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Criar e utilizar um balanceador de carga interno com ambientes de aplicação de serviço](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurar um ambiente de aplicação de serviço](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Dimensionamento aplicações num ambiente de aplicação de serviço](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Segurança e de rede arquitetura](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Como do

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Vídeos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
