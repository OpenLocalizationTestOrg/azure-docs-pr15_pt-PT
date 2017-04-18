<properties
    pageTitle="Azure de aplicação de serviço para web, mobile e API apps | Microsoft Azure"
    description="Saiba como a aplicação de serviço de Azure ajuda-o a desenvolver, implementar e gerir web e as aplicações móveis."
    keywords="aplicação de serviço, azure aplicação de serviço, aplicação de serviço de custo, escala, dimensionável, implementação de aplicações, implementação de aplicações azure, paas, plataforma-como-a-service, Web site, web site, web, azure móvel"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>O que é a aplicação de serviço de Azure?

*Serviço de aplicação* é uma [plataforma-como-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) oferecer do Microsoft Azure. Crie web e aplicações móveis para qualquer plataforma ou dispositivo. Integrar as suas aplicações SaaS soluções, ligar-se com as aplicações no local e automatizar processos de negócio. Azure é executado as suas aplicações totalmente geridas máquinas virtuais (VMs), com a escolha de recursos VM partilhados ou VMs dedicadas.

Aplicação de serviço inclui a web e capacidades móveis que recomendamos anteriormente entregues separadamente como sites públicos do Azure e dos serviços do Azure Mobile. Também inclui novas capacidades para automatizar processos de negócio e nuvem APIs de alojamento. Como um serviço único integrado, o serviço de aplicação permite-lhe compor vários componentes de – sites públicos, extremidades anterior da aplicação móvel, RESTful APIs e processos de negócio – numa única solução.

O vídeo 4 minutos seguinte fornece uma breve explicação sobre como aplicação de serviço relacionado com o anteriores ofertas Azure e quais são as novidades no mesmo.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>Porquê utilizar a aplicação de serviço?

Aqui estão algumas das principais funcionalidades e capacidades de aplicação de serviço:

- **Vários idiomas e quadros** - aplicação de serviço tem suporte primeira classe para ASP.NET, Node.js, Java, PHP e Python. Também pode executar [o Windows PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) na aplicação de serviço VMs.

- **Otimização DevOps** - configurar [integração contínua e implementação](../app-service-web/app-service-continuous-deployment.md) com serviços de equipa do Visual Studio, GitHub ou BitBucket. Promova atualizações através do [teste e ambientes de teste](../app-service-web/web-sites-staged-publishing.md). Executar [A / B testes](../app-service-web/app-service-web-test-in-production-get-start.md). Gerir as suas aplicações na aplicação de serviço, utilizando o [Azure PowerShell](../powershell-install-configure.md) ou a [interface de comandos em diferentes plataformas (CLI)](../xplat-cli-install.md).

- **Dimensionar Global com elevada disponibilidade** - escala [para cima](../app-service-web/web-sites-scale.md) ou para [fora](../monitoring-and-diagnostics/insights-how-to-scale.md) automaticamente ou manualmente. Alojar as suas aplicações em qualquer lugar no infraestrutura de centro de dados global da Microsoft e o serviço de aplicação [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.

- **Ligações a dados no local e plataformas de SaaS** - selecione a partir de mais de 50 [conexões](../connectors/apis-list.md) para sistemas de empresa (como o SAP, Siebel e Oracle), os serviços de SaaS (como Salesforce e Office 365) e serviços de internet (como o Facebook e Twitter). Dados do Access no local utilizando [Ligações de híbrido](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes virtuais Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Segurança e conformidade** - aplicação de serviço é [ISO, SOC e PCI em conformidade](https://www.microsoft.com/TrustCenter/).

- **Modelos de aplicação** - escolha a partir de uma lista abrangente de modelos da aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem-lhe utilizar um Assistente para instalar o software de abrir origem popular como WordPress, Joomla e Drupal.

- **Integração do visual Studio** - ferramentas dedicadas no Visual Studio simplificar o trabalho criar, implementar e depuração.

## <a name="app-types-in-app-service"></a>Tipos de aplicação na aplicação de serviço

Aplicação oferece vários *tipos de aplicação*, cada um dos quais se destina a um tipo específico de carga de trabalho do anfitrião:

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - para aplicações web e Web sites de alojamento.

- [**Aplicações móveis**](../app-service-mobile/app-service-mobile-value-prop.md) Para alojar aplicação móvel trás extremidades.

- [**API aplicações**](../app-service-api/app-service-api-apps-why-best-platform.md) - para alojamento RESTful APIs.

- [**Aplicações de lógica**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - para automatizar processos de negócio e integrar sistemas e dados ao longo de nuvens sem escrever código.

O word *aplicação* aqui refere-se para os recursos alojamento dedicados para executar uma carga de trabalho. Escrita "web app" como exemplo, se encontra provavelmente habituado a pensar numa aplicação web como o cluster recursos e o código da aplicação que em conjunto entregar funcionalidade para um browser. Mas na aplicação de serviço de uma *aplicação web* é os recursos de cluster Azure fornece para alojar o seu código da aplicação. Se a aplicação é composta por de um web front-end e uma API RESTful back-end, poderia implementar ambos a uma aplicação web ou pode implementar o seu código front-end para uma aplicação web e o código de segurança para uma aplicação de API. A aplicação pode ser compostos por várias aplicações de serviço de aplicação de diferentes tipos.

## <a name="app-service-plans"></a>Planos do serviço de aplicação

[Planos do serviço de aplicação](azure-web-sites-web-hosting-plans-in-depth-overview.md) , especifique o tipo de recursos de cluster que as suas aplicações são executadas no. Se pretender cargas de tráfego simplificada, pode utilizar partilhadas VMs (**Free** e **partilhado** preços camadas). Para os carregamentos superiores, pode escolher a partir de vários tamanhos das VMs dedicados (camadas**básicas**, **padrão**e **Premium** ). Várias aplicações de serviço de aplicação, podem partilhar o mesmo plano e estes Dimensionar para as camadas comparar em conjunto no plano de.

Se precisar de mais escalabilidade e isolamento de rede, pode executar as suas aplicações num [Ambiente de serviço de aplicação](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Preços

Para obter informações sobre quanto os custos de aplicação de serviço, consulte o artigo [Preços de serviço de aplicação](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Testar a aplicação de serviço

[Criar uma aplicação web do exemplo, a aplicação móvel ou a aplicação de lógica](http://go.microsoft.com/fwlink/?LinkId=523751) e a reprodução com o mesmo para 1 hora, com sem cartão de crédito obrigatório, sem compromissos, sem transtornos.

Ou abra uma [conta Azure gratuita](https://azure.microsoft.com/pricing/free-trial/)e experimente uma das nossas tutoriais de introdução ao:

* [Tutorial: Criar uma aplicação web](../app-service-web/app-service-web-get-started.md)
* [Tutorial: Criar uma aplicação móvel](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Tutorial: Criar uma aplicação do API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Tutorial: Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
