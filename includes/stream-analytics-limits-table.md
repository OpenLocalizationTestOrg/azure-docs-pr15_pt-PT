<properties 
   pageTitle="Tabela de limites de análise da cadeia"
   description="Descreve os limites de sistema e tamanhos recomendados para ligações e componentes de análise da cadeia."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificador do limite | Limite       | Comentários |
|----------------- | ------------|--------- |
| Número máximo de unidades transmissão por subscrição por região | 50 | Um pedido para aumentar a transmissão unidades para a sua subscrição para além das 50 pode ser efetuado ao contactar o [Suporte da Microsoft](https://support.microsoft.com/en-us). |
| Débito máximo de uma unidade de transmissão | 1MB / s * | Débito máximo por DOM depende do cenário. Débito real pode ser inferior e depende complexidade da consulta e a partições. Podem encontrar mais detalhes no artigo [trabalhos de escala Azure da cadeia Analytics para aumentar o débito](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Número máximo de entradas do tipo por tarefa | 60 | Existe um limite de disco rígido de 60 entradas do tipo por tarefa de análise da cadeia. |
| Número máximo de resultados por tarefa | 60 | Existe um limite de disco rígido de 60 saídas por tarefa de análise da cadeia. |
| Número máximo de funções por tarefa | 60 | Existe um limite de disco rígido de 60 funções por tarefa de análise da cadeia. |
| Número máximo de tarefas por região | 1500 | Cada subscrição poderá ter até 1500 tarefas à região geográfica errada. |