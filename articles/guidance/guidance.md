
<properties
   pageTitle="Azure orientações | padrões & práticas | Microsoft Azure"
   description="Melhores práticas e seta de quatro pontas Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Orientações Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A equipa do Microsoft padrões & práticas faz parte de equipa Consultivo de cliente do Azure. Nosso objetivo é para ajudar os programadores, arquitectura e profissionais de TI ser efetuada com êxito a plataforma do Microsoft Azure. Vamos desenvolver orientações que mostra as práticas recomendadas para criar soluções de nuvem no Azure.

## <a name="checklists"></a>Listas de verificação

Estas listas são uma referência rápida para rever os fundamentais aspetos da disponibilidade e escalabilidade. 

- [Lista de verificação de disponibilidade][AvailabilityChecklist] 

    Um resumo das práticas recomendadas para assegurar disponibilidade.

- [Lista de verificação escalabilidade][ScalabilityChecklist]

    Um resumo das práticas recomendadas para estruturar e implementar os serviços de dimensionáveis e processamento de gestão de dados.

## <a name="best-practices-articles"></a>Melhores práticas artigos

Estes artigos fornecem uma aprofundada do conceitos importantes geralmente associados à nuvem computação. 

- [API estrutura][APIDesign] 

    Um debate dos problemas de design a ter em conta ao estruturar um web API.

- [API implementação][APIImplementation] 

    Um conjunto de práticas recomendadas para implementar e um web API de publicação.

- [Orientação de segurança API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    Um debate de autenticação e autorização preocupações (por exemplo, tipos de token, protocolos de autorização, fluxos de autorização e mitigação ameaça).

- [Orientações Autoscaling][AutoscalingGuidance] 

    Um resumo das considerações para dimensionamento soluções sem necessidade de intervenção manual.

- [Orientação de tarefas do fundo][BackgroundJobsGuidance] 

    Uma descrição das opções disponíveis e práticas recomendadas para implementar tarefas que devem ser executadas em segundo plano, separadamente a partir de qualquer primeiro plano ou operações interativas.

- [Conteúdo orientações de rede de entrega (CDN)][CDNGuidance] 

    Informações gerais e práticas recomendadas para utilizar a CDN para minimizar a carga suas aplicações e maximizar a disponibilidade e o desempenho.

- [Colocação em cache de orientação][CachingGuidance] 

    Um resumo de como utilizar a colocação em cache para melhorar o desempenho e escalabilidade de um sistema.

- [Orientação de divisão de dados][DataPartitioningGuidance]

    Estratégias que pode utilizar para dados da partição para melhorar a escalabilidade, reduzir contenção e otimizar o desempenho.

- [Monitorização e diagnósticos de orientação][MonitoringandDiagnosticsGuidance] 

    Orientações sobre como controlar como os utilizadores utilizam o sistema de rastreio de utilização de recursos e geralmente monitorizar o estado de funcionamento e o desempenho do seu sistema de.

- [Convenções de nomenclatura recomendadas][naming-conventions] 

    Convenções de nomenclatura recomendadas para recursos Azure.

- [Volte a tentar informações gerais][RetryGeneralGuidance] 

    Debate dos conceitos gerais para o processamento de erros de breves.

- [Volte a tentar orientações específico do serviço][RetryServiceSpecificGuidance]

    Um resumo das funcionalidades de repetir para muitos dos serviços Azure, incluindo informações para o ajudar a utilizar, adaptar ou expandir o dispositivo repetir desse serviço.

## <a name="scenario-guides"></a>Guias de cenário

- [Em execução Elasticsearch no Azure][elasticsearch] 
    
    Elasticsearch é um motor de busca altamente dimensionáveis de código fonte do aberto e a base de dados. É adequado para situações em que necessitam de análise rápida e deteção de informações contidas em grandes conjuntos de dados. Estas orientações analisa alguns aspetos a ter em conta ao estruturar um cluster de Elasticsearch chaves.

- [Gestão de identidades para aplicações multi-inquilino][identity-multitenant] 
    
    Multitenancy é uma arquitectura onde múltiplos inquilinos do partilham uma aplicação, mas são isolados uns dos outros. Estas orientações mostra-lhe como a gestão de identidades do utilizador numa aplicação multi-inquilino, utilizando o [Azure Active Directory] [ AzureAD] para processar iniciar sessão e de autenticação.
    
- [Desenvolver soluções de dados grande](https://msdn.microsoft.com/library/dn749874.aspx)

    Este guia explora a utilização de HDInsight cenários como iterativo informações detalhadas, como um armazém de dados, para processos ETL e integração nos sistemas de BI já existentes. Também inclui orientações sobre compreender os conceitos de dados grandes, planeamento e soluções de dados grande estruturar e implementar a estas soluções.
    
## <a name="patterns"></a>Padrões

- [Padrões de estrutura da nuvem: Arquitetura à orientações para aplicações na nuvem](https://msdn.microsoft.com/library/dn568099.aspx)

    Nuvem estrutura padrões é uma biblioteca de padrões de design e tópicos de orientação relacionados. -Articulates o benefício de aplicar padrões ao que mostra como cada porção pode adequam arquitecturas de aplicação na nuvem.
    
- [Otimizar o desempenho para aplicações na nuvem](https://github.com/mspnp/performance-optimization)

    Estas orientações são uma informações detalhadas de anti-padrões comuns que dificultar aplicações a partir do dimensionamento em caso de carga. Inclui amostras demonstrar anti-padrões oito e uma [introdução de análise de desempenho](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) e um guia para [avaliar o desempenho contra métricas chave](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Arquitecturas de referência

Os nossos arquitecturas de referência estiverem dispostas por cenário.
Cada arquitetura individual oferece práticas recomendadas e à passos e um componente executável que engloba as recomendações.

A biblioteca de atual do arquitecturas de referência está disponível em [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Orientações RDP

Estes tópicos descrevem como estruturar aplicações que são e são para falha num ambiente distribuído na nuvem.   

- [Descrição geral de RDP][ResiliencyOvervew]

     Como criar aplicações a plataforma Azure que podem recuperar de falhas e continuar a funcionar. Descreve uma abordagem estruturada para alcançar RDP, a partir de estrutura para implementação, implementação e operações.

- [Lista de verificação RDP][resiliency-checklist]

    Plano de uma lista de verificação das recomendações irá ajudá-lo para uma variedade de modos de falha que podem ocorrer.

- [Análise de modo a falha][resiliency-fma] 

    Análise de modo a falha (FMA) é um processo para criar RDP para um sistema, identificando pontos de falha possíveis. Como ponto de partida para o processo de FMA, este artigo contém um catálogo potenciais modos de falha e os respetivos atenuações. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

