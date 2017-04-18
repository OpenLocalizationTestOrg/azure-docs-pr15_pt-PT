<properties 
    pageTitle="Noções sobre sequência Analytics tarefa monitorização | Microsoft Azure" 
    description="Noções sobre a tarefa de análise da cadeia de monitorização" 
    keywords="monitor de consulta"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Compreender a análise da cadeia tarefa monitorização e como monitorizar as consultas

## <a name="introduction-the-monitor-page"></a>Introdução: A página do monitor

O portal de gestão do Azure e o Portal do Azure superfície métricas de chave de desempenho que podem ser utilizadas para monitorizar e o desempenho da consulta e de projeto de resolução de problemas. 

No portal de gestão do Azure, clique no separador **Monitor** de uma tarefa de análise da cadeia em execução para ver estes métricas. Existe um atraso de mais de 1 minuto nas métricas de desempenho a aparecer na página Monitor.  

  ![Tarefa de Dashboard de monitorização](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

No Portal do Azure, navegue para a tarefa de análise da cadeia estiver interessado em ver métricas para e ver a secção de **monitorização** .  

  ![Tarefa de monitorização do Portal Azure Dashboard](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

A primeira vez, que é criada uma tarefa de análise da cadeia numa região, terá de configurar diagnósticos para esse região. Para fazer isto, clique em qualquer ponto da secção de **monitorização** e o pá **Diagnósticos** irão aparecer. Aqui pode ativar a diagnósticos e especifique uma conta de armazenamento para monitorizar os dados.  

  ![Consulta de configurar o Portal Azure diagnósticos](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para a análise da cadeia


| Métrica | Definição |
|--------|-------------|
| % De utilização de Oliveira | A utilização de unidades a transmissão atribuído a uma tarefa a partir do separador escala da tarefa. Deverá este indicador atingir 80% ou acima, é elevada probabilidade que o processamento de eventos pode ser atrasado ou parado a progredir. |
| Eventos de entradas | Quantidade de dados é recebidos pela tarefa de análise da cadeia, número de eventos. Isto pode ser utilizado para validar que eventos estão a ser enviados para a origem de entrada. |
| Eventos de saída | Quantidade de dados que foi enviados pela tarefa de análise da cadeia para o destino de saída, em número de eventos. |
| Eventos de fora da ordem | Número de eventos recebidos fora ordem que foram largadas ou tendo em conta um carimbo ajustado, com base na política de ordenação do evento. Isto pode ser afectado pela configuração da definição da janela de tolerância fora de ordem. |
| Erros de conversão de dados | Número de erros de conversão de dados suportados por uma tarefa de análise da cadeia. |
| Erros de tempo de execução | Número de erros que ocorrem durante a execução de uma tarefa de análise da cadeia. |
| Eventos de entrada atrasados | Número de eventos recebidas mais tarde a partir da origem de que quer foi interrompida ou os respetivos carimbo de data/hora tiver sido ajustado, com base na configuração da política de ordenação de eventos da definição da janela de tolerância de chegada atrasada. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Personalizar monitorização no portal de gestão do Azure ##

Até 6 métricas podem ser apresentadas num gráfico.

Para alternar entre apresentar valores relativas (valor final apenas para cada métrica) e os valores de absoluto (eixo do Y apresentado), selecione relativas ou absoluto na parte superior do gráfico.

  ![Monitor de consulta relativa absoluta](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Métricas podem ser visualizadas no gráfico Monitor pela agregações de 1 hora, 12 horas, 7 dias ou 24 horas.

Para alterar o intervalo de tempo a gráfico apresenta métricas, selecione 1 hora, 24 horas ou dias 7 na parte superior do gráfico.

  ![Monitor de consulta escala temporal](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

Pode definir regras que podem notificá-lo por correio eletrónico caso a tarefa cruza um limite definido. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizar a monitorização no Portal do Azure ##

Pode ajustar o tipo de gráfico, métricas apresentados e intervalo nas definições de editar gráfico de tempo. Para obter detalhes, consulte o artigo [como personalizar monitorização](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Escala temporal do Azure consulta Portal Monitor](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>Estado da tarefa

O estado das tarefas de análise da cadeia pode ser visualizado no Portal clássica do Azure onde ver uma lista de tarefas. Pode ver a lista de tarefas ao clicar no ícone de análise da cadeia no Portal clássica do Azure.

| Estado | Definição |
|--------|------------|
| Criado | Uma tarefa foi criado, no entanto não foi iniciado. |
| Iniciar | Um utilizador clicou em iniciar a tarefa e, a tarefa está a ser iniciado |
| Em execução | A tarefa que é atribuída, processamento de entrada ou a aguardar entrada do processo. Se a tarefa de apresentar um Estado de funcionamento sem produzir resultados, é provável que a janela de tempo de processamento de dados é grande ou se a lógica de consulta é complicada. Qualquer outra razão poderá que atualmente não há quaisquer dados a ser enviados para a tarefa. |
| Parar a | Um utilizador clicou em Parar a tarefa e, a tarefa está a parar. |
| Parado | A tarefa foi interrompida. |
| Degradado | Este estado indica que uma tarefa de análise da cadeia está a encontrar erros breves (para ex. Entrada/saídos erros, processamento de erros, erros de conversão etc.). O trabalho ainda estiver em execução, no entanto, existem muitas erros gerados a ser. Esta tarefa necessita de atenção de cliente e o cliente pode ver os registos de operações para os erros. |
| Ocorreu uma falha | Isto indica que o trabalho falhou devido a erros e deixou de processamento. O cliente tem de analisar os registos de operações para poder depurar os erros. |
| Eliminar | Isto indica que a tarefa está a ser eliminada. |

## <a name="diagnosis"></a>Diagnóstico

No portal de gestão do Azure, o dashboard do trabalho fornece informações sobre onde precisa de procurar exporta de diagnóstico, ou seja, entradas, e/ou as operações de iniciar sessão. Pode clicar na ligação para aceder a localização apropriada para ver o diagnóstico.

  ![Erro de monitor de consulta](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

Clicar no recurso entrado ou saído fornece informações detalhadas de diagnóstico. Isto é atualizado com as informações de diagnóstico mais recentes enquanto a tarefa estiver em execução.

  ![Diagnósticos de consulta](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Tarefas de análise do Azure da cadeia de escala](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
