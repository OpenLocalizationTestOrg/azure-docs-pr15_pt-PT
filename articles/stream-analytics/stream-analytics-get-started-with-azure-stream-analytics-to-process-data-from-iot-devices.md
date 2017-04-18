<properties
    pageTitle="Introdução ao Azure da cadeia Analytics para processar dados a partir de dispositivos IoT. | Microsoft Azure"
    description="IoT sensor etiquetas e dados sequências com a análise da cadeia e processamento de dados em tempo real"
    keywords="solução IOT, começar a trabalhar com iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introdução ao Azure da cadeia Analytics para processar dados a partir de dispositivos IoT

Neste tutorial, irá aprender a criar uma sequência de processamento lógica para recolher dados a partir de dispositivos de Internet coisas (IoT). Vamos irá utilizar um caso de utilização de Internet coisas (IoT) reais, para demonstrar como construir a sua solução rapidamente e económico.

## <a name="prerequisites"></a>Pré-requisitos

-   [Subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Consulta e dados de ficheiros de exemplo transferíveis do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Cenário

Contoso, que é uma empresa no espaço automatização industrial, tem completamente automática o processo de fabrico. Máquina neste fábrica tem sensores que estão capazes de emitir sequências de dados em tempo real. Neste cenário, um Gestor de piso de produção pretende ter informações em tempo real a partir de dados a sensor Procure padrões e efetuar ações nos mesmos. Utilizamos a sequência de análise de consulta idioma (SAQL) sobre os dados de sensor para encontrar interessantes padrões da sequência de entrada de dados.

Aqui dados está a ser gerados a partir de um dispositivo de etiqueta de sensor Texas instrumentos.

![Etiqueta de sensor Texas instrumentos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

A carga útil dos dados está no formato JSON e aspeto o seguinte procedimento:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

Num cenário de reais, é possível que tenha centenas destes sensores gerar eventos como uma sequência. Idealmente, um gateway seria executar código para transmitir estes eventos para [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/) ou [Azure IoT concentradores](https://azure.microsoft.com/services/iot-hub/). A tarefa de análise da cadeia seria ingerir esta última estes eventos de evento concentradores e executar consultas de análise em tempo real contra as sequências de. Em seguida, pode enviar os resultados para uma das [suportadas saídas](stream-analytics-define-outputs.md).

Para fácil utilização, este guia de introdução ao obter fornece um ficheiro de dados de exemplo, que foi capturado a partir de dispositivos de etiqueta de real sensor. Pode executar consultas nos dados de exemplo e ver resultados. Tutoriais subsequentes, vai aprender a ligar-se a tarefa para introduções e exporta e implementá-los ao serviço do Azure.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa de análise da cadeia

1. No [portal do Azure](http://portal.azure.com), clique no sinal de adição e, em seguida, escreva **A análise da cadeia** na janela de texto para a direita. Em seguida, selecione **a análise de fluxo de trabalho** na lista de resultados.

    ![Criar uma nova tarefa de análise da cadeia](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Introduza um nome exclusivo do trabalho e verifique se que a subscrição está correto para a tarefa. Em seguida, criar um novo grupo de recursos ou selecione existente na sua subscrição.

3. Em seguida, selecione uma localização para a tarefa. Para velocidade de processamento e redução do custo nos dados, é recomendável transferência ao selecionar a mesma localização como o grupo de recursos e a conta de armazenamento pretendido.

    ![Criar uma nova tarefa de análise da cadeia detalhes](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] Deve criar esta conta de armazenamento apenas uma vez por região. Este armazenamento irão ser partilhado por todas as tarefas de análise da cadeia que são criadas dessa região.

4. Selecione a caixa para colocar o seu trabalho no seu dashboard e, em seguida, clique em **Criar**.

    ![criação de trabalho em curso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Deverá visualizar uma 'implementação iniciada...' estão apresentados na parte superior direita da janela do browser. Mais cedo irá alterar para uma janela completa conforme apresentado abaixo.

    ![criação de trabalho em curso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Criar uma consulta de análise de sequência do Azure

Depois da tarefa é criada tem tempo para abri-lo e construir uma consulta. Pode aceder facilmente a tarefa ao clicar no mosaico do mesmo.

![Mosaico da tarefa](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

No painel de **Topologia de tarefa** clique na caixa de **consulta** para aceder ao Editor de consultas. Editor de **consultas** permite-lhe introduzir uma consulta T SQL que efetua a transformação sobre os dados do evento recebidas.

![Caixa de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Arquivar os seus dados não processados

A forma mais simples de consulta é uma consulta pass-through que arquiva todos os dados de entrada para a respectiva saída designada. Transferir o ficheiro de dados de exemplo a partir do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) para uma localização no seu computador. 

1. Cole a consulta do ficheiro PassThrough.txt. 

    ![Sequência de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Clique nas reticências junto a introdução de dados e selecione a caixa de **carregar dados de exemplo a partir de ficheiro** .

    ![Sequência de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. Um painel é aberto no lado direito como um resultado, em selecione o ficheiro de dados HelloWorldASA InputStream.json a partir da sua localização transferida e clique em **OK** na parte inferior do painel de.

    ![Sequência de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. Em seguida, clique em da engrenagem **Testar** no canto superior esquerdo da área da janela e processar a consulta de teste contra o conjunto de dados de exemplo. Será apresentada uma janela de resultados por baixo da sua consulta, como o processamento está concluído.

    ![Resultados dos testes](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar os dados com base numa condição

Vamos experimentar filtrar os resultados com base numa condição. Gostaríamos Mostrar resultados para apenas os eventos que vir de "sensorA." A consulta é no ficheiro Filtering.txt.

![Filtrar uma sequência de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Tenha em atenção que a consulta entre maiúsculas e minúsculas se compara um valor de cadeia. Clique em da engrenagem **teste** novamente para executar a consulta. A consulta deve devolver 389 linhas fora 1860 eventos.

![Segundo resultados de saída de teste de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: Alerta para acionar um fluxo de trabalho de empresas

Vamos fazer com os nossos consulta mais detalhadas. Para cada tipo de sensor, queremos monitorizar a temperatura média por 30 segundos janela e apresentar os resultados apenas se estiver a temperatura média acima dos 100 valores graus. Iremos escrever a seguinte consulta e, em seguida, clique em **Testar** para ver os resultados. A consulta é no ficheiro ThresholdAlerting.txt.

![consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora deverá ver os resultados que contenham apenas 245 linhas e os nomes dos sensores onde a média temperada for maior que 100. Esta consulta agrupa a sequência de eventos por **dspl**, que é o nome de sensor, através de uma **Janela Tumbling** de 30 segundos. Consultas temporais tem de indicar como queremos tempo ao progresso. Ao utilizar a cláusula **Por data/hora** , podemos tiver especificado a coluna **OUTPUTTIME** para associar vezes todos os cálculos temporais. Para obter informações detalhadas, leia os artigos MSDN sobre [funções de sistema baseado em janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx)e [Gestão do tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) .

### <a name="query-detect-absence-of-events"></a>Consulta: Detetar ausência de eventos

Como podemos escrever uma consulta para localizar uma falta de eventos de entrada? Vamos encontrar a última vez que um sensor enviou dados e, em seguida, não enviou eventos para os minutos seguinte. A consulta é no ficheiro AbsenseOfEvent.txt.

![Detetar ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aqui utilizamos uma associação **Externa esquerda** para o mesmo fluxo de dados (associação automática). Para uma associação **interna** , um resultado é devolvido apenas quando uma correspondência for encontrada.  Para uma associação **Externa à esquerda** , se um evento a partir do lado esquerdo da associação é não correspondente, é devolvida uma linha com nulo para todas as colunas do lado direito. Esta técnica é muito útil para localizar uma ausência de eventos. Consulte a nossa documentação do MSDN para obter mais informações sobre como [PARTICIPAR](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusão

É o objetivo deste tutorial demonstrar como escrever consultas de linguagem de consulta de análise da cadeia diferentes e ver resultados no browser. No entanto, isto é dar os primeiros passos. O que pode fazer muito mais com a análise da cadeia. Análise da cadeia suporta uma variedade de entradas do tipo e exporta e pode ainda utilizar funções Azure máquina formação para o tornar uma ferramenta robusta para analisar sequências de dados. Pode começar a explorar mais informações sobre a análise da cadeia utilizando a nossa [formação mapa](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Para mais informações sobre como escrever consultas, leia o artigo sobre [padrões comuns de consulta](./stream-analytics-stream-analytics-query-patterns.md).
