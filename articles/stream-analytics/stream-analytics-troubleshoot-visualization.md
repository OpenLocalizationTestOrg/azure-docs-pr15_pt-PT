<properties
    pageTitle="Visualizar e resolver problemas de tarefas da cadeia Analytics | Microsoft Azure"
    description="Aprenda a visualizar um pipeline de tarefa de análise da cadeia para personalizada resolver o problema utilizando a funcionalidade de diagrama de diagnóstico."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualizar e tarefas de análise da cadeia de resolução de problemas

Análise da cadeia, tal como acontece com outras tecnologias baseado na nuvem, de resolução de problemas é, por vezes, necessário para analisar porque é que uma tarefa não produzir saída esperada (ou nenhuma saída para esse assunto). Com esta situação deve ter em conta, a análise da cadeia fornece a capacidade para visualizar uma tarefa de transmissão. Isto também é útil como uma ferramenta de modelação e tem a vantagem de lado para esses que exigem documentação do seu trabalho.

No painel de visualização as entradas estão visíveis bem como a consulta a ser executado e, em seguida, todas as saídas de configurado. Problemas de conectividade ou configuração podem tornar-se mais evidentes e também pode ser útil ver uma representação visual da sua configuração.

## <a name="using-the-diagnosis-diagram-tool"></a>Utilizar a ferramenta de diagrama de diagnóstico

Para aceder a este visualizer, basta clicar no botão "Diagrama de diagnóstico" a pá "as definições" do da tarefa a análise da cadeia.

![Stream-Analytics-Troubleshoot-visualization-Diagnosis-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada e saída são o estado de codificadas com para indicar atuais cores desse componente, conforme apresentado abaixo.

![Stream-Analytics-Troubleshoot-visualization-Input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Quando o utilizador pretende ver os passos da consulta intermédio para compreender os padrões de fluxo de dados no interior de uma tarefa, a ferramenta de visualização fornece uma vista da repartição da consulta para o respetivos passos componente e a sequência de fluxo. Clicar em cada passo da consulta irá mostrar a secção correspondente numa consulta edição painel tal como ilustrado. 

![Stream-Analytics-Troubleshoot-visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
