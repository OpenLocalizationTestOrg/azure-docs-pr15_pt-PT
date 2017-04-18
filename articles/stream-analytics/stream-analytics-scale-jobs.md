<properties
    pageTitle="Dimensionar tarefas de análise da cadeia para aumentar o débito | Microsoft Azure"
    description="Saiba como dimensionar trabalhos de análise da cadeia ao configurar a partições entradas, a definição de consulta de sintonização e definir a tarefa de transmissão de unidades."
    keywords="dados streaming, transmissão de processamento de dados, o gráfico analytics"
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

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Dimensionar tarefas de análise do Azure da cadeia para aumentar o débito de processamento de dados da cadeia

Saiba como ajustar o trabalhos de análise e calcular *transmissão unidades* para análise da cadeia, como dimensionar trabalhos de análise da cadeia ao configurar a partições entradas, a definição de consulta de análise de sintonização e definir a tarefa de unidades de transmissão. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de uma tarefa de análise da cadeia?
Uma definição de tarefa de análise da cadeia inclui entradas, de uma consulta e de saída. Entradas do tipo são de onde a tarefa de lê a sequência de dados, a consulta é utilizada para transformar a sequência de entrada de dados e o resultado é onde a tarefa envia os resultados da tarefa.  

Uma tarefa requer pelo menos uma origem de entrada para a transmissão de dados. A origem de entrada de fluxo de dados pode ser armazenada num concentrador de eventos do Azure Service Bus ou em armazenamento de Blobs do Azure. Para mais informações, consulte o artigo [Introdução ao Azure da cadeia Analytics](stream-analytics-introduction.md) e [começar a utilizar a análise da cadeia de Azure](stream-analytics-get-started.md).

## <a name="configuring-streaming-units"></a>Configurar a transmissão de unidades
Unidades de transmissão (SUs) representa os recursos e a capacidade de computação necessária para executar a uma tarefa de análise da cadeia de Azure. SUs fornecem uma maneira que descreva o evento relativo processamento capacidade com base numa medida mistura de CPU, memória e leitura e escrita taxas. Cada unidade de transmissão corresponde aproximadamente 1MB/segundo de débito. 

Escolher quantos SUs são necessários para uma determinada tarefa depende da configuração da partição de para as entradas e a consulta definidos para a tarefa. Pode selecionar até ao seu quota no transmissão unidades para uma tarefa utilizando o Portal clássico Azure. Cada subscrição Azure por predefinição tem uma quota de até 50 unidades transmissão para todas as tarefas de análise numa região específico. Para aumentar a transmissão unidades para as suas subscrições, contacte o [Suporte da Microsoft](http://support.microsoft.com).

O número de unidades transmissão que pode utilizam uma tarefa depende a configuração de partição para as entradas e a consulta definidos para a tarefa. Tenha também em atenção que deve ser utilizado um valor válido para as unidades da cadeia. Os valores válidos iniciar em 1, 3, 6 e, em seguida, para cima em incrementos de 6, conforme apresentado abaixo.

![Análise de sequência Azure transmitir em fluxo escala de unidades][img.stream.analytics.streaming.units.scale]

Este artigo mostrar-lhe como calcular e ajustar a consulta para aumentar o débito para trabalhos de análise.

## <a name="embarrassingly-parallel-job"></a>Tarefa embarrassingly paralela
A tarefa embarrassingly paralela é o cenário mais dimensionável que temos no Azure a análise da cadeia. Liga uma partição de entrada para uma instância da consulta-se para uma partição dos resultados. Atingir este paralelismo necessita de algumas coisas:

1.  Se a sua lógica de consulta é dependente a mesma chave a ser processada na mesma instância de consulta, em seguida, tem de garantir que os eventos Ir para a mesma partição de introdução de dados. No caso de evento concentradores, isto significa que os dados do evento tem de ter **PartitionKey** definir ou pode utilizar com partições remetentes. Para Blob, isto significa que os eventos são enviados para a mesma pasta partição. Se a sua lógica de consulta não requer a mesma chave ser processadas na mesma instância de consulta, em seguida, pode ignorar este requisito. Um exemplo de isto seria uma consulta selecionar/projeto/filtro simples.  
2.  Assim que os dados são apresentados como-tem de estar no lado entrado, é necessário para se certificar de que a sua consulta tiver partições. Isto requer que utilizar **Partição por** em todos os passos. Vários passos são permitidos mas todas elas tem de ser divididas pela mesma chave. Outra ação atenção é que, neste momento, a partições chave tem de ser definido como **PartitionId** para ter uma tarefa totalmente paralela.  
3.  Só evento concentradores e Blob atualmente com partições saída. Para os resultados de evento concentradores, tem de configurar o campo de **PartitionKey** a estar **PartitionId**. Para Blob, não tem de fazer nada.  
4.  Outra coisa tenha em atenção o número de partições entradas tem de ser igual o número de partições de saída. Saída blob atualmente não suporta a partições, mas isto há uma vez que herdam o partições esquema da consulta montante. Exemplos de valores de partição que permitiriam a uma tarefa totalmente paralela:  
    1.  8 evento concentradores entrada partições e 8 evento concentradores saída partições
    2.  8 concentradores de evento entrada a partições e a saída de BLOBs  
    3.  8 partições blob de entrada e saída de BLOBs  
    4.  8 partições de entrada blob e 8 evento concentradores partições de saída  

Eis alguns cenários de exemplo que são embarrassingly paralelos.

### <a name="simple-query"></a>Consulta simples
Introdução – concentradores evento com 8 partições saída – concentrador de evento com 8 partições

**Consulta:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Esta consulta é um filtro simple e como tal, não precisamos de se preocupar a partições a entrada de enviarmos a concentradores do evento. Vai notar que a consulta tem **Partição por** de **PartitionId**, de modo que recomendamos cumprir requisito #2 dos acima. No resultado, precisamos de configurar a saída de evento concentradores na tarefa para que o campo de **PartitionKey** definido como **PartitionId**. Uma última verificação, a partições entradas = = a partições de saída. Esta topologia é embarrassingly paralela.

### <a name="query-with-grouping-key"></a>Consulta com chave de agrupamento
Introdução – concentradores evento com 8 partições saída – Blob

**Consulta:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tem uma chave de agrupamento e como tal, a mesma chave tem de ser processadas na mesma instância de consulta. Isto significa que precisamos de enviar o nossos eventos para eventos concentradores de uma forma com partições. Que tecla podemos cuidado sobre? **PartitionId** é um conceito de lógica de tarefa, a chave real que podemos interessam é **TollBoothId**. Isto significa que recomendamos deve configurar o **PartitionKey** os dados de evento de enviarmos a concentradores de evento para ser o **TollBoothId** do evento. A consulta tem **Partição por** de **PartitionId**, pelo que existem boas. No resultado, uma vez que é Blob, podemos não precisa de se preocupar configurar **PartitionKey**. Para o requisito #4, novamente, este é Blob, para que não precisamos de se preocupá-lo. Esta topologia é embarrassingly paralela.

### <a name="multi-step-query-with-grouping-key"></a>Multi passo consulta com chave de agrupamento ###
Introdução – concentrador de evento com 8 partições saída – concentrador de evento com a 8 partições

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tem uma chave de agrupamento e como tal, a mesma chave tem de ser processadas na mesma instância de consulta. Vamos pode utilizar a mesma estratégia como a consulta anterior. A consulta tem vários passos. A cada passo tem **Partição por** de **PartitionId**? Sim, para que estamos a boas. Para o resultado, precisamos de definir o **PartitionKey** para **PartitionId** como outros fabricantes referidos acima e também é possível ver que tem o mesmo número de partições como a entrada. Esta topologia é embarrassingly paralela.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Cenários de exemplo que não estão embarrassingly, paralelas

### <a name="mismatched-partition-count"></a>Contagem de partições sem correspondência ###
Introdução – concentradores evento com 8 partições saída – concentrador de evento com a 32 partições

Não interessa o que a consulta é neste caso, uma vez que a entrada partição contar! = contagem de partições de saída.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>Não utilizar concentradores de evento ou Blobs como uma saída
Introdução – concentradores evento com 8 partições saída – obter

Obter saída atualmente não suporta a partições.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de passo multi com diferentes partição por valores
Introdução – concentrador de evento com 8 partições saída – concentrador de evento com a 8 partições

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Como pode ver, o segundo passo utiliza **TollBoothId** como a partições chave. Isto não é o mesmo que o primeiro passo e, consequentemente, irá necessitar-nos efetuar uma aleatoriamente. 

Estes são alguns exemplos e counterexamples de tarefas de análise da cadeia que poderão alcançar uma topologia de embarrassingly, paralela e com o mesmo a possibilidade de escala máxima. Para tarefas que não se ajustam a uma destes perfis, serão feitas futuras atualizações que explicam detalhadamente como período Dimensionar algumas situações outras canónico análise da cadeia.

Para fazer agora a utilização das informações gerais abaixo:

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular o número máximo transmissão unidades de uma tarefa
O número total de transmissão unidades que podem ser utilizadas por uma tarefa de análise da cadeia depende o número de passos da consulta definidos para a tarefa e o número de partições para cada passo.

### <a name="steps-in-a-query"></a>Passos numa consulta
Uma consulta pode ter um ou vários passos. Cada passo é uma subconsulta definida utilizando a palavra-chave **com** . A consulta apenas que for externo a palavra-chave **com** também é contabilizada como um passo, por exemplo, a instrução **SELECT** na seguinte consulta:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

A consulta anterior tem dois passos.

> [AZURE.NOTE] Esta consulta de exemplo vai ser explicada posteriormente neste artigo.

### <a name="partition-a-step"></a>Criar a partições um passo

Criar a partições um passo requer as seguintes condições:

- Tem de ser dividida a origem de entrada. Para obter mais informações, consulte o [Guia de programação do evento concentradores](../event-hubs/event-hubs-programming-guide.md).
- A instrução **SELECT** da consulta tem de leitura a partir de uma origem de entrada com partições.
- A consulta dentro do passo tem de ter a palavra-chave **Partição por**

Quando uma consulta é criado a partições, os eventos entrados será partição separada transformados e agregado no grupos e eventos de saídas são gerados para cada um dos grupos. Se um agregado combinado é conveniente, terá de criar um segundo passo não divididos para agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular o campo Unidades Máx transmissão unidades para uma tarefa

Todos os passos que não sejam divididos em conjunto podem Dimensionar até seis unidades transmissão para uma tarefa de análise da cadeia. Para adicionar adicionais de transmissão de unidades, um passo deve ser dividido. Cada partição pode ter unidades de transmissão de seis.

<table border="1">
<tr><th>Consulta de uma tarefa</th><th>Máximo de unidades para a tarefa de transmissão</th></td>

<tr><td>
<ul>
<li>A consulta contém um passo.</li>
<li>O passo não tiver a partições.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>A sequência de dados de entrada é dividida por 3.</li>
<li>A consulta contém um passo.</li>
<li>O passo é dividido.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>A consulta contém dois passos.</li>
<li>Nenhum dos passos é a partições.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>A entrada de fluxo de dados é dividida por 3.</li>
<li>A consulta contém dois passos. O passo entrado é a partições e não o segundo passo.</li>
<li>A instrução SELECT lê a partir da entrada com partições.</li>
</ul>
</td>
<td>24 (18 para obter os passos com partições) + 6 para obter os passos que não sejam divididos</td></tr>
</table>

### <a name="examples-of-scale"></a>Exemplos de escala
A seguinte consulta calcula o número de carros passam por uma estação de serviço pago com três tollbooths dentro de uma janela de três minutos. Esta consulta pode ser dimensionada até seis unidades transmissão.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para utilizar mais unidades de transmissão para a consulta, ambos os dados transmitir em fluxo entrada e tem de ser dividida a consulta. Dado que partição de fluxo de dados está definida como 3, a seguinte consulta modificada pode ser dimensionada até 18 unidades em sequência:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando uma consulta é criado a partições, os eventos entrados são processados e agregados no grupos partição separada. Eventos de saída também são gerados para cada um dos grupos. Criação de partições pode causar algumas resultados inesperados quando o campo **Agrupar por** não está na chave partição a entrada de fluxo de dados. Por exemplo, o campo **TollBoothId** na consulta exemplo anterior não está a chave de Input1 partição. Os dados a partir do cabina #1 podem propagar-se em múltiplas partições.

Cada uma das partições Input1 será processada separadamente pelo análise da cadeia e vários registos da contagem carro-pass-through para a mesmo cabina na mesma janela tumbling serão criados. Se a chave de entrada partição não pode ser alterada, este problema pode ser corrigido ao adicionar um passo não partição adicional, por exemplo:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta pode ser dimensionada para 24 transmissão unidades.

>[AZURE.NOTE] Se irão aderir sequências de dois, certifique-se de que as sequências são divididas pela chave partição da coluna que fizer associações e tiver o mesmo número de partições em ambos os fluxos de.


## <a name="configure-stream-analytics-job-partition"></a>Configurar a análise da cadeia tarefa partição

**Para ajustar uma unidade de transmissão de um projecto**

1. Inicie sessão no [portal de gestão](https://manage.windowsazure.com).
2. No painel esquerdo, clique em **Análise da cadeia** .
3. Clique na tarefa de análise da cadeia que quer dimensionar.
4. Clique em **escala** , na parte superior da página.

![Análise de sequência Azure transmitir em fluxo escala de unidades][img.stream.analytics.streaming.units.scale]

No Portal do Azure, as definições de escala podem ser acedidas em definições:

![Configuração de trabalho do Azure Portal da cadeia Analytics][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Monitorizar o desempenho de tarefa

Utilizar o portal de gestão, pode controlar o débito de uma tarefa no segundo/eventos:

![Tarefas de monitor de análise da cadeia Azure][img.stream.analytics.monitor.job]

Calcule o esperado débito a carga de trabalho no eventos/segundo. Se o débito for menor que o esperado, sintonizar partição de entrada, otimizar a consulta e adicionar unidades transmissão adicionais para o seu trabalho.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Débito de análise da cadeia em escala - cenário framboesa Pi


Para compreender como tarefas de análise da cadeia Dimensionar num cenário típico em termos de processamento de débito em múltiplas unidades Streaming, eis uma experiência que envia dados sensor (clientes) concentrador de evento, processa-lo e envia estatísticas de alerta ou como um resultado para outro concentrador de evento.

O cliente enviar dados sensor síntese a concentradores do evento no formato JSON para a análise da cadeia e o resultado de dados também está no formato JSON.  Eis como os dados de exemplo seria gosto –  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Consulta: "enviar um alerta quando a luz estiver desligada"  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Medir débito: Débito neste contexto é a quantidade de dados de entrada processadas pelos análise da cadeia num valor fixo de tempo (10 minutos). Para obter melhor processamento débito para os dados de entrada, ambos os dados transmitir em fluxo entrada e tem de ser dividida a consulta. Também é incluída **COUNT()**na consulta medir processados quantos eventos de entrada. Para assegurar que a tarefa não é simplesmente a aguardar para eventos introdução breve, cada partição do concentrador de evento entrada foi pré-carregados com dados de entrada suficientes (cerca de 300MB).  

Seguem-se os resultados com aumentar o número de unidades de transmissão e partição correspondente contagens de palavras no evento concentradores.  

<table border="1">
<tr><th>Partições entradas</th><th>Partições de saída</th><th>Transmissão de unidades</th><th>Constante débito
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

![IMG.stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
