
<properties
   pageTitle="Elasticsearch no Azure orientações | Microsoft Azure"
   description="Elasticsearch no Azure orientações."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch no Azure orientações 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch é um motor de busca altamente dimensionáveis de código fonte do aberto e a base de dados. É útil para situações que necessitam de análise rápida e deteção de informações contidas em grandes conjuntos de dados. Estas orientações analisa alguns aspetos a ter em conta ao estruturar um cluster de Elasticsearch, com um foco nas formas para otimizar e testar a configuração da chaves.

> [AZURE.NOTE]Estas orientações assume alguma familiaridade básica com Elasticsearch. Para obter informações mais detalhadas, visite o [Web site de Elasticsearch](https://www.elastic.co/products/elasticsearch). 

- **[Executar Elasticsearch no Azure][]** fornece uma breve introdução à estrutura geral do Elasticsearch e descreve como implementar um cluster de Elasticsearch utilizando o Azure. 
- **[Desempenho do ingestão dados Tuning para Elasticsearch no Azure][]** descreve de implementação de um cluster de Elasticsearch e fornece uma análise aprofundada de várias opções de configuração a ter em consideração quando se espera que uma taxa alta de ingestão de dados.
- **[Optimizar a agregação de dados e o desempenho da consulta para Elasticsearch no Azure][]** fornece uma análise aprofundada das opções a considerar quando decidir como otimizar o seu sistema de pesquisa de consulta e desempenho.
- **[Configurar a recuperação e recuperação no Elasticsearch no Azure][]** descreve algumas funcionalidades de um cluster de Elasticsearch que o podem ajudar a minimizar as hipóteses de perda de dados e as horas de recuperação expandida dados importantes.
- **[Criar um ambiente de teste de desempenho para Elasticsearch no Azure][]** descreve como configurar um ambiente para testar a ingestão de dados de desempenho e das cargas de trabalho de consulta num Elasticsearch cluster. 
- **[Implementar uma JMeter testar o plano para Elasticsearch][]** resume testes de desempenho em execução implementados utilizando os planos de teste de JMeter juntamente com o código de Java incorporada como um teste JUnit para desempenhar tarefas como carregar dados para o cluster Elasticsearch.
- **[Implementar uma demonstração de JMeter JUnit para testar o desempenho do Elasticsearch][]** descreve como criar e utilizar uma demonstração de JUnit que pode gerar e carregue os dados a um cluster de Elasticsearch. Este procedimento fornece uma abordagem altamente flexível para carregar testes que pode gerar grandes quantidades de dados de teste sem dependendo ficheiros de dados externos. 
- **[Executar os testes de RDP Elasticsearch automatizados][]** resume como executar os testes de RDP utilizados nesta série. 
- **[Executar os testes de desempenho Elasticsearch automatizados][]** resume como executar os testes de desempenho utilizados nesta série.


[Em execução Elasticsearch no Azure]: guidance-elasticsearch-running-on-azure.md
[Otimização do desempenho do ingestão dados para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Criar um testes de desempenho ambiente para Elasticsearch no Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementar um plano de teste de JMeter para Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Implementar uma demonstração de JMeter JUnit para testar o desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Otimização do desempenho de consulta para Elasticsearch no Azure e agregação de dados]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configurar a recuperação e recuperação no Elasticsearch no Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Executar os testes de RDP Elasticsearch automatizado]: guidance-elasticsearch-running-automated-resilience-tests.md
[Executar os testes de desempenho Elasticsearch automatizado]: guidance-elasticsearch-running-automated-performance-tests.md
