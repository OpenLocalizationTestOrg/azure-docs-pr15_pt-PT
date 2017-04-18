<properties 
   pageTitle="Contadores de desempenho do Windows e Linux no registo de análise | Microsoft Azure"
   description="Contadores de desempenho são recolhidos pela análise de registo para analisar o desempenho no Windows e Linux agentes.  Este artigo descreve como configurar a recolha de contadores de desempenho para ambas as janelas e Linux agentes, detalhes de estes são armazenados no repositório de OMS e sobre como analisá-los no portal do OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Windows e Linux origens de dados de desempenho no registo de análise 

Contadores de desempenho no Windows e Linux fornecem visão o desempenho de componentes de hardware, sistemas operativos e aplicações.  Análise de registo pode recolher contadores de desempenho em intervalos frequentes para uma análise perto em Tempo Real (NRT) para além de elaboração de relatórios e agregar dados de desempenho para uma análise mais longa de termos.

![Contadores de desempenho](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurar contadores de desempenho

Configure contadores de desempenho no [menu de dados em definições de análise de registo](log-analytics-data-sources.md#configuring-data-sources).

Quando configurar o Windows ou Linux desempenho contadores para uma nova área de trabalho OMS pela primeira vez, é-lhe dada a opção para criar rapidamente várias contadores comuns.  Estas estão listadas com uma caixa de verificação ao lado de cada.  Certifique-se de que quaisquer contadores que pretende criar inicialmente são verificadas e, em seguida, clique em **Adicionar contadores de desempenho seleccionados**.

![Configurar contadores de desempenho do Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para recolher.

1. Escreva o nome do contador na caixa de texto na Formatar *objecto (instância) \counter*.  Quando começar a escrever, são apresentadas com uma lista correspondente de contadores comuns.  Ou pode selecionar um contador a partir da lista ou tipo de uma das suas próprias.  Também pode devolver todas as ocorrências de um determinado contador ao especificar *object\counter*. 
2. Clique em **+** ou prima **Enter** para adicionar o contador à lista.
3. Quando adiciona um contador, utiliza a predefinição de 10 segundos para o **Intervalo de amostra**.  Pode alterar para um valor superior de 1800 segundos (30 minutos) se quiser reduzir os requisitos de armazenamento os recolha de dados de desempenho.
4. Quando tiver terminado de adicionar contadores, clique no botão **Guardar** na parte superior do ecrã para guardar a configuração.

![Configurar contadores de desempenho Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho Linux para recolher.

1. Por predefinição, todas as alterações à configuração automaticamente são enviadas para todos os agentes.  Para Linux agentes, um ficheiro de configuração é enviado para a recolha Fluentd.  Se pretender modificar este ficheiro manualmente em cada agente Linux, em seguida, desmarque a caixa *Aplicar abaixo configuração para minha máquinas Linux*.
2. Escreva o nome do contador na caixa de texto na Formatar *objecto (instância) \counter*.  Quando começar a escrever, são apresentadas com uma lista correspondente de contadores comuns.  Ou pode selecionar um contador a partir da lista ou tipo de uma das suas próprias.  
2. Clique em **+** ou prima **Enter** para adicionar o contador à lista de outros contadores para o objeto.
3. Todos os contadores de um objeto utilizam o mesmo **Intervalo de amostra**.  A predefinição é 10 segundos.  Pode alterar para um valor superior de 1800 segundos (30 minutos) se quiser reduzir os requisitos de armazenamento os recolha de dados de desempenho.
4. Quando tiver terminado de adicionar contadores, clique no botão **Guardar** na parte superior do ecrã para guardar a configuração.

## <a name="data-collection"></a>Recolha de dados

Análise de registo recolhe todos os contadores de desempenho especificado no respetivo intervalo da amostra especificado no agentes de todos os que têm que contador instalado.  Os dados não são agregados, e os dados não processados estão disponíveis em todas as vistas de pesquisa de registo para a duração especificada pela sua subscrição OMS.


## <a name="performance-record-properties"></a>Propriedades do registo de desempenho

Registos de desempenho tenham um tipo de **Desempenho** e tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Computador         | Computador que o evento foi recolhido a partir do. |
| CounterName      | Nome do contador de desempenho |
| CaminhoContador      | Caminho completo da contador no formulário de \\ \\ \<computador >\\objecto (instância)\\contador. |
| Contravalor     | Valor numérico do contador.  |
| InstanceName     | Nome da instância do evento.  Esvaziar se nenhuma instância. |
| Nome de objecto       | Nome do objeto desempenho |
| SourceSystem  | Tipo de agente dos que dados foram recolhidos a partir de. <br> Ligar OpsManager – agente do Windows, quer directo ou SCOM <br> Linux – todos os agentes de Linux  <br> AzureStorage – diagnósticos do Azure |
| TimeGenerated       | Data e hora, que os dados foram recolhidos. |


## <a name="sizing-estimates"></a>Estimativas de redimensionamento

 Uma estimativa aproximada para a coleção de um determinado contador em intervalos de 10 segundo é de cerca de 1 MB por dia por instância.  Pode calcular os requisitos de armazenamento de um determinado contador com a seguinte fórmula.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Registo de pesquisas com os registos de desempenho

A tabela seguinte fornece exemplos diferentes de pesquisas de registo que obter registos de desempenho.

| Consulta | Descrição |
|:--|:--|
| Tipo = desempenho | Todos os dados de desempenho |
| Tipo = desempenho computador = "Meu computador" | Todos os dados de desempenho a partir de um computador em particular |
| Tipo = desempenho CounterName = "Comprimento de fila de disco atual" | Todos os dados de desempenho para um determinado contador |
| Tipo = desempenho (nome de objecto = processador) CounterName = "Hora de processador %" InstanceName = total & #124; medir Avg(Average) como AVGCPU pelo computador | Utilização da CPU média através de todos os computadores |
| Tipo = desempenho (CounterName = "Hora de processador %") & #124;  medir max(Max) pelo computador | Utilização da CPU máximo em todos os computadores |
| Tipo = desempenho nome de objecto = disco lógico CounterName = computador "Atual no disco comprimento da fila" = "MyComputerName" & #124; medir Avg(Average) por InstanceName | Comprimento médio da fila no disco atual em todas as ocorrências de um determinado computador |
| Tipo = desempenho CounterName = "DiskTransfers/seg" & #124; medir percentile95(Average) pelo computador | 95 percentil de transferência de disco/seg através de todos os computadores |
| Tipo = desempenho CounterName = InstanceName "Hora de processador %" = total"& #124; medir avg(CounterValue) pelo intervalo de computador 1 hora | Média de hora a hora de utilização da CPU através de todos os computadores |
| Tipo = desempenho computador = "Meu computador" CounterName = % * InstanceName = total & #124; medir percentile70(CounterValue) pelo intervalo CounterName 1 hora | Percentil 70 horários de cada contador de percentagem % para um determinado computador |
| Tipo = desempenho CounterName = InstanceName "Hora de processador %" = total"(computador ="Meu computador") & #124; medir min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) pelo intervalo de computador 1 hora | Preço por hora média, mínima, máxima e 75 percentil utilização da CPU para um computador específico |

## <a name="viewing-performance-data"></a>Ver dados de desempenho

Quando executa uma pesquisa de registo de dados de desempenho, a vista do **registo** é apresentada por predefinição.  Para ver os dados no formulário de gráfico, clique em **métricas**.  Para uma vista detalhada estruturador, clique na **+** junto a um contador.  

![Vista de métricas fechada](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Se o intervalo de tempo que selecionou é 6 horas ou menos, o gráfico é atualizado em poucos segundos.  Dados dinâmicos são apresentados no lado direito do gráfico no azul claro.

![Vista de métricas expandida com dados dinâmicos](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Agregar dados de desempenho numa pesquisa de registo, consulte o artigo [agregação métrica a pedido e de visualização no OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções.  
- Exporte dados recolhidos ao [Power BI](log-analytics-powerbi.md) para análise e visualizações adicionais.