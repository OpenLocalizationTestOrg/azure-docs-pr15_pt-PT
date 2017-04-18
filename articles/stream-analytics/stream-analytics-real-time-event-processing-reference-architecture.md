<properties 
    pageTitle="Evento em tempo real com o processamento de evento de análise da cadeia de processamento | Microsoft Azure" 
    description="Saiba como um conjunto de serviços Azure pode interagir para activar o processamento de eventos em tempo real e analytics." 
    keywords="processamento em tempo real, processamento de eventos, arquitetura de referência"
    services="stream-analytics,event-hubs,storage,sql-database" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="stream-analytics" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: evento em tempo real de processamento com a análise da cadeia do Microsoft Azure

A arquitetura de referência para o evento em tempo real de processamento com a análise da cadeia de Azure destina-se para fornecer um plano genérico para implementar uma plataforma em tempo real como uma solução de processamento de sequência de serviço (PaaS) com o Microsoft Azure.

## <a name="summary"></a>Resumo

Normalmente, as soluções de análise tem sido baseadas no capacidades, tais como ETL (extrair, transformar, carga) e armazenamento de dados, onde os dados são armazenados antes de análise. Requisitos de alteração, incluindo mais dados rapidamente recebidos, são conduza neste modelo existente para o limite. A capacidade para analisar dados de mover sequências antes de armazenamento é uma solução e enquanto não é uma nova funcionalidade, a abordagem não tiver sido amplamente aprovada através de todos os segmentos de indústria. 

Microsoft Azure fornece um catálogo amplo de tecnologias de análise que são capazes de uma matriz de cenários de solução diferente e requisitos de suporte. Selecionar quais os serviços Azure para implementar o para uma solução de fim para fim pode ser um desafio tendo em conta boca do ofertas. Este papel foi concebido para descrever as capacidades e interoperação dos diferentes serviços Azure que suportam uma solução de transmissão de evento. Também explica algumas situações em que os clientes beneficiar este tipo de abordagem.

## <a name="contents"></a>Conteúdo

- Resumo Executivo
- Introdução à análise em tempo real
- Proposta de valor de dados em tempo real no Azure
- Cenários comuns de análise em tempo real
- Arquitetura e componentes
    - Origens de dados
    - Camadas de integração de dados
    - Camadas de análise em tempo real
    - Camada de armazenamento de dados
    - Apresentação / consumo de camadas
- Conclusão

**Autor:** Centro de informações do Carlos Feddersen, arquitecto solução, dados de excelência, Microsoft Corporation

**Publicado:** De Janeiro de 2015

**Revisão:** 1.0

**Transferir:** [Evento em tempo real de processamento com a análise da cadeia do Microsoft Azure](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 
