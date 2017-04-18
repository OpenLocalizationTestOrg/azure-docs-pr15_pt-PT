<properties 
    pageTitle="Playbook de solução de análise de telemetria veículo | Microsoft Azure" 
    description="Utilizar as funcionalidades de análise de Cortana para obter informações em tempo real e aspeto do Office no estado de funcionamento do veículo e condução hábitos." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Playbook de solução de análise de telemetria veículo

Este **menu** contém ligações para os capítulos neste playbook. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Descrição geral
Computadores Super tem passado terminar o laboratório e espera no nossa garagem! Estes automóveis vanguardas contêm uma vasta gama de sensores, que lhe dá uma-los a capacidade de registar e monitorizar milhões de eventos cada segundo. Esperar que que por 2020, a maior parte destes carros irá ter sido ligado à internet. Imagine tocar para este património de dados para fornecer a melhor no Centro de proteção de classe, fiabilidade e experiência condução! Microsoft disponibilizou isto dream realidade com Cortana Intelligence.

Cortana Intelligence da Microsoft é um totalmente gerida big data e o conjunto de aplicações de análise avançadas que lhe permite transformar dados em ação inteligente. Pretendemos introduzi-lo a Cortana informações da empresa veículo telemetria Analytics solução modelo. Esta solução demonstra como distribuição de carro, fabricantes de automóveis e empresas de seguros que podem utilizar as funcionalidades de análise de Cortana para ganhar em tempo real e aspeto do Office informações sobre o estado de funcionamento do veículo e condução hábitos. 

A solução é implementada como um [padrão de arquitectura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) que mostra o total potenciais da plataforma Cortana Intelligence para em tempo real e processamento batch. Solução: 

- Fornece um simulator telemática veículo
- tira partido de evento concentradores para ingesting milhões de eventos de telemetria veículo simulada para Azure 
- utiliza a análise da cadeia para obter informações em tempo real no estado de funcionamento do veículo
-  persiste os dados para o armazenamento a longo prazo para análise de lote mais rica. 
- tira partido de máquina de aprendizagem para deteção anomalia em tempo real e batch processamento para obter informações de aspeto do Office.
- tira partido de HDInsight para transformar dados em escala e fábrica de dados para processar orchestration, agendamento, gestão de recursos e monitorização do pipeline de processamento batch 
- Fornece um dashboard formatado para dados em tempo real e visualizações de análise de aspeto do Office utilizando o Power BI-esta solução

## <a name="architecture"></a>Arquitetura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figura 1 – arquitetura de solução da análise de telemetria de veículo*

Esta solução inclui os seguintes **componentes de análise da Cortana** e demonstra sua integração de ponto a ponto


- **Concentradores de evento** para ingesting milhões de eventos de telemetria veículo para Azure.
- **Análise da cadeia** para obterem informações em tempo real no estado de funcionamento do veículo e persistir esses dados para o armazenamento a longo prazo para análise de lote mais rica.
- **Formação de máquina** de detecção anomalia em tempo real e processamento batch para obter informações de aspeto do Office.
- **HDInsight** é utilizadas para transformar dados à escala
- **Dados fábrica** trata orchestration, agendamento, gestão de recursos e monitorização do pipeline de processamento batch.
- **Power BI** dá-esta solução um dashboard formatado para dados em tempo real e visualizações de análise de aspeto do Office.

Esta solução acede duas de diferentes **origens de dados**: 

- **Simulado sinais de veículo e diagnósticos de**: uma simulator de telemática veículo emite sinais que correspondem ao estado de veículo e o padrão de condução num determinado ponto de informações de diagnóstico e hora. 
- **Catálogo de veículo**: um conjunto de dados de referência, que contém uma VIN para o mapeamento do modelo.
