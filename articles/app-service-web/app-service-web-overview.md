<properties
    pageTitle="Descrição geral de aplicações Web | Microsoft Azure"
    description="Saiba como a aplicação de serviço de Azure ajuda-o a desenvolver e aplicações web do anfitrião"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Descrição geral de aplicações Web

*Aplicação de serviço Web Apps* é uma plataforma de cluster totalmente geridas que está optimizada para aplicações web e Web sites de alojamento. Esta oferta de [plataforma-como-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) do Microsoft Azure permite a concentrar-se no seu lógica empresarial enquanto Azure leva-o até cuidado infraestrutura para executar e dimensionar as suas aplicações.

O vídeo de 5-minute seguinte apresenta Azure aplicação de serviço Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>O que é uma aplicação web na aplicação de serviço?

Na aplicação de serviço, uma *aplicação web* é os recursos de cluster Azure fornece para uma Web site ou aplicação web de alojamento.  

Os recursos de cluster podem estar em partilhadas ou dedicadas máquinas de virtuais (VMs), consoante a camada preços que escolher. O código da aplicação executa uma VM gerida que está isolada de outros clientes.

O código pode estar em qualquer idioma ou framework que seja suportado pela [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md), como o ASP.NET, Node.js, Java, PHP ou Python. Também pode executar scripts que utilizam o [PowerShell e outras linguagens de script](web-sites-create-web-jobs.md#acceptablefiles) numa aplicação web.

Para obter exemplos dos cenários de aplicação típicas que pode utilizar aplicações para o Web, consulte o artigo [cenários de aplicação Web](https://azure.microsoft.com/documentation/scenarios/web-app/) e a secção de **cenários e recomendações** da [aplicação de serviço de Azure, comparação máquinas virtuais, ferro de serviço e serviços em nuvem](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Porquê utilizar Web Apps?

Aqui estão algumas das principais funcionalidades da aplicação de serviço que se aplicam a Web Apps:

- **Vários idiomas e quadros** - aplicação de serviço tem suporte primeira classe para ASP.NET, Node.js, Java, PHP e Python. Também pode executar [PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) na aplicação de serviço VMs.

- **Otimização DevOps** - configurar [integração contínua e implementação](../app-service-web/app-service-continuous-deployment.md) com serviços de equipa do Visual Studio, GitHub ou BitBucket. Promova atualizações através do [teste e ambientes de teste](../app-service-web/web-sites-staged-publishing.md). Executar [A / B testes](../app-service-web/app-service-web-test-in-production-get-start.md). Gerir as suas aplicações na aplicação de serviço, utilizando o [Azure PowerShell](../powershell-install-configure.md) ou a [interface de comandos em diferentes plataformas (CLI)](../xplat-cli-install.md).

- **Dimensionar Global com elevada disponibilidade** - escala [para cima](../app-service-web/web-sites-scale.md) ou para [fora](../monitoring-and-diagnostics/insights-how-to-scale.md) automaticamente ou manualmente. Alojar as suas aplicações em qualquer lugar no infraestrutura de centro de dados global da Microsoft e o serviço de aplicação [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.

- **Ligações a dados no local e plataformas de SaaS** - selecione a partir de mais de 50 [conexões](../connectors/apis-list.md) para sistemas de empresa (como o SAP, Siebel e Oracle), os serviços de SaaS (como Salesforce e Office 365) e serviços de internet (como o Facebook e Twitter). Dados do Access no local utilizando [Ligações de híbrido](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes virtuais Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Segurança e conformidade** - aplicação de serviço é [ISO, SOC e PCI em conformidade](https://www.microsoft.com/TrustCenter/).

- **Modelos de aplicação** - escolha a partir de uma lista abrangente de modelos da aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem-lhe utilizar um Assistente para instalar o software de abrir origem popular como WordPress, Joomla e Drupal.

- **Integração do visual Studio** - ferramentas dedicadas no Visual Studio simplificar o trabalho criar, implementar e depuração.

Além disso, uma aplicação web pode tirar partido das funcionalidades disponibilizadas pelos [API aplicações](../app-service-api/app-service-api-apps-why-best-platform.md) (tais como CORS suportam) e [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (tais como as notificações push). Para mais informações sobre tipos de aplicação na aplicação de serviço, consulte o artigo [Descrição geral do serviço de aplicação do Azure](../app-service/app-service-value-prop-what-is.md).

Para além de aplicações Web na aplicação de serviço, o Azure oferece outros serviços que podem ser utilizados para aplicações web e Web sites de alojamento. Para a maioria dos cenários, Web Apps é a melhor escolha.  Para microservice arquitetura, considere [Ferro de serviço](https://azure.microsoft.com/documentation/services/service-fabric)e se precisar de mais controlo sobre os VMs que executa o seu código no, considere [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para mais informações sobre como escolher entre estas Azure serviços, consulte o artigo [aplicação de serviço de Azure, máquinas virtuais, ferro de serviço e comparação de serviços em nuvem](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Introdução

Para começar a utilizar ao implementar o código de exemplo para uma nova aplicação web na aplicação de serviço, siga o tutorial [Implementar a aplicação web do primeira para Azure em 5 minutos](app-service-web-get-started.md) . Terá de uma conta Azure gratuita.

Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.
