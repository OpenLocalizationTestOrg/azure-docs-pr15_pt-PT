<properties
    pageTitle="Análise da cadeia: Deteção de fraude em tempo real | Microsoft Azure"
    description="Saiba como criar uma solução de deteção de fraude em tempo real com a análise da cadeia. Utilize um concentrador de evento para o processamento de evento em tempo real."
    keywords="Deteção de anomalia, deteção de fraude, deteção de anomalia em tempo real"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Começar a utilizar a análise da cadeia de Azure: deteção de fraude em tempo real

Saiba como criar uma solução de fim para fim para deteção fraude em tempo real com a análise da cadeia de Azure. Trazer eventos para Azure evento concentradores, escrever a análise da cadeia de consultas para agregação ou alertar e enviar os resultados para um receptor de saída para obter informações sobre os dados com o processamento em tempo real. Deteção de anomalia em tempo real de telecomunicações é explicada, mas a técnica de exemplo é igualmente adequada para outros tipos de deteção de fraude como o cartão de crédito ou identidade de cenários de roubo.

Análise da cadeia é um serviço totalmente gerido que fornece o processamento de baixa latência, altamente disponível, dimensionáveis, complexa eventos através de transmissão de dados na nuvem. Para mais informações, consulte o artigo [Introdução ao Azure da cadeia Analytics](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Deteção de fraude de telecomunicações e SIM em tempo real

Numa empresa de telecomunicações tem um grande volume de dados para chamadas a receber. A empresa precisa seguintes a partir dos seus dados:

* Reduzir os dados para um montante fácil e obter informações sobre a utilização de cliente ao longo do tempo e em regiões geográficas.
* Deteta fraude SIM (várias chamadas a partir da mesma identidade à volta ao mesmo tempo, mas em locais diferentes geograficamente) em tempo real para que a empresa pode responder facilmente por notificando os clientes ou encerrar serviço.

Cenários de Internet coisas (IoT) canónicos tem um ton de telemetria ou dados provenientes de sensores. Os clientes pretende agregar os dados ou receber alertas sobre anomalias em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

- Transferir [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) a partir do Centro de transferências da Microsoft
- Opcional: O código de origem do gerador evento a partir do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Criar grupo de entrada e de consumidor do Azure evento concentradores

A aplicação de exemplo irá gerar eventos e transmiti-los para uma instância de evento concentradores para processamento em tempo real. Serviço Bus evento concentradores são o método preferencial ingestão de evento para análise da cadeia. Pode obter mais informações sobre o evento concentradores na [documentação do Azure Service Bus](/documentation/services/service-bus/).

Para criar um concentrador de evento:

1.  No [portal do Azure](https://manage.windowsazure.com/), clique em **Novo** > **Serviços de aplicação** > **Serviço BUS** > **CONCENTRADOR de evento** > **Criação rápida**. Fornece um nome, a região e o espaço de nomes novo ou existente para criar um novo concentrador de evento.  
2.  Como prática recomendada, deverá ler cada tarefa de análise da cadeia de um grupo de consumidor do concentrador de único evento. Vamos irá guiá-lo durante o processo de criação de um grupo de consumidor mais tarde. [Saiba mais sobre os grupos do consumidor](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um grupo do consumidor, aceda ao centro do evento recentemente criado, clique no separador **Consumidor grupos** , clique em **Criar** na parte inferior da página e, em seguida, forneça um nome para o grupo do consumidor.
3.  Para conceder acesso ao centro do evento, precisamos de criar uma política de acesso partilhado. Clique no separador **Configurar** do seu centro de evento.
4.  Em **Políticas de acesso PARTILHADOS**, crie uma nova política que possua permissões de **GERIR** .

    ![Partilhado onde pode criar uma política com permissões de gerir as políticas de acesso.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Clique em **Guardar** na parte inferior da página.
6.  Aceda a **Dashboard**, clique em **Informações de ligação** na parte inferior da página e, em seguida, copiar e guardar as informações de ligação.

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar a aplicação de gerador de evento

Fornecemos uma aplicação de cliente que irá gerar exemplo recebidas metadados de chamada e enviá-lo a concentradores do evento. Utilize os seguintes passos para configurar esta aplicação.  

1.  Transferir o [ficheiro TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)e deszipe-lo para um diretório.

    > [AZURE.NOTE] Windows pode impedir que o ficheiro transferido zip. Com o botão direito no ficheiro e selecione **Propriedades**. Se vir a mensagem "este ficheiro é proveniente de outro computador e poderá estar bloqueado para ajudar a proteger neste computador", selecione a caixa de **Desbloquear** e, em seguida, clique em aplicar no ficheiro zip.

2.  Substitua valores de Microsoft.ServiceBus.ConnectionString e EventHubName do telcodatagen.exe.config com o seu nome e a cadeia de ligação do Centro de evento.

    A cadeia de ligação que copiou do portal do Azure coloca o nome da ligação no final. Certifique-se de que remover "; EntityPath =<value>"a partir do" Adicionar chave = "campo.

3.  Inicie a aplicação. Utilização de é da seguinte forma:

    telcodatagen.exe [#NumCDRsPerHour] [SIM cartão fraude probabilidade] [#DurationHours]

O exemplo seguinte irá gerar 1.000 eventos com uma probabilidade de 20 por cento de fraude ao longo de duas horas.

    telcodatagen.exe 1000 .2 2

Irá ver registos a ser enviados para o seu centro de evento. Alguns campos de chave que recomendamos irá estar a utilizar nesta aplicação de deteção de fraude em tempo real são definidos aqui:

| Registo | Definição |
| ------------- | ------------- |
| CallrecTime | Hora de início de carimbo de data/hora para a chamada. |
| SwitchNum | Mudar de telefone utilizado para ligar a chamada. |
| CallingNum | Número de telefone do autor da chamada. |
| CallingIMSI | Identidade do assinante móvel internacional (da IMSI).  Identificador exclusivo do autor da chamada. |
| CalledNum | Número de telefone do destinatário da chamada. |
| CalledIMSI | Identidade do assinante móvel internacional (da IMSI).  Identificador exclusivo do destinatário da chamada. |


## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa de análise da cadeia
Agora que temos uma sequência de eventos de telecomunicações, podemos pode configurar uma tarefa de análise da cadeia para analisar estas eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionar uma tarefa de análise da cadeia

1.  No portal do Azure, clique em **Novo** > **Serviços de dados** > **A análise da cadeia** > **Criação rápida**.
2.  Especifique os seguintes valores e, em seguida, clique em **Criar tarefa de análise de sequência**:

    * **Nome da tarefa**: introduza um nome de tarefa.

    * **Região**: selecione a região em que pretende executar a tarefa. Considere a colocação a tarefa e centro do evento na mesma região para garantir melhor desempenho e para garantir que que será não paga transferir dados entre regiões.

    * **Conta de armazenamento**: selecione a conta de armazenamento Azure que pretende utilizar para armazenar dados de monitorização para todas as tarefas de análise da cadeia que executar esta região. Tem a opção escolher uma conta de armazenamento existente ou criar um novo.

3.  Clique em **Análise de sequência** no painel da esquerda para listar as tarefas de análise da cadeia.

    ![Ícone de serviço de análise da cadeia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    A nova tarefa será apresentada com o estado **criado**. Repare que o botão **Iniciar** na parte inferior da página está desativado. Tem de configurar a entrada de tarefa, saída e consulta antes de poder iniciar a tarefa.

### <a name="specify-job-input"></a>Especifique a entrada de tarefa
1.  Na sua tarefa de análise da cadeia, clique em **entradas** na parte superior da página e, em seguida, clique em **Adicionar entrada**. Caixa de diálogo que é aberta irá guiá-lo através de vários passos para configurar o seu teclado.
2.  Clique em **fluxo de dados**e, em seguida, clique no botão à direita.
3.  Clique em **Centro de evento**e, em seguida, clique no botão à direita.
4.  Escreva ou selecione os seguintes valores na terceira página:

    * **ALIAS de entrada**: introduza um nome amigável, tal como *CallStream*, para esta tarefa. Tenha em atenção que irá utilizar este nome na consulta mais tarde.

    * **CONCENTRADOR de evento**: se o concentrador de evento que criou for na mesma subscrição do que a tarefa de análise da cadeia, selecione o espaço de nomes se encontra o concentrador de evento.

        Se for o concentrador de evento numa subscrição diferente, selecione **Concentrador de evento de utilizar a partir de outra subscrição**e, em seguida, introduzir manualmente as informações para **o espaço de nomes do serviço BUS**, **Nome do CONCENTRADOR do evento**, **Evento CONCENTRADOR nome da política**, **A chave de política de CONCENTRADOR de evento**e **CONTAGEM de partições CONCENTRADOR de evento**.

    * **Nome do CONCENTRADOR de evento**: selecione o nome do centro do evento.

    * **Nome da política CONCENTRADOR evento**: selecione a política de concentrador de evento que criou anteriormente no tutorial.

    * **Grupo de consumidor do CONCENTRADOR de evento**: escreva o nome do grupo consumidor que criou anteriormente no tutorial.

5.  Clique no botão à direita.
6.  Especifique os seguintes valores:

    * **Formato de SERIALIZADOR evento**: JSON
    * **Codificação**: UTF8
7.  Clique no botão **Verificar** para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para o centro do evento.

### <a name="specify-job-query"></a>Especificar a consulta de tarefa

Análise da cadeia suporta um modelo de consulta simples, declarativa que descreve transformações para processamento em tempo real. Para saber mais sobre o idioma, consulte a [Referência da linguagem de consulta Azure da cadeia Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Neste tutorial irá ajudá-lo do autor e testar várias consultas ao longo do seu fluxo de chamada de dados em tempo real.

#### <a name="optional-sample-input-data"></a>Opcionais: Dados de entrada de exemplo
Para validar a sua consulta de dados real do trabalho, pode utilizar a funcionalidade de **Dados de exemplo** para extrair os eventos da sua cadeia e criar uma. Ficheiro de JSON dos eventos para testar a ligação.  Os seguintes passos mostram como o fazer. Fornecemos também um ficheiro de [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) de exemplo para fins de teste.

1.  Selecione os dados introduzidos concentrador de evento e, em seguida, clique em **Dados de exemplo** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, especifique uma **hora de início** para começar a recolher dados e uma **duração** por quanto dados adicionais para consumir.
3.  Clique no botão **Verificar** para começar a dados de amostragem da entrada.  Pode demorar um minuto ou dois para o ficheiro de dados para ser apresentado.  Quando o processo for concluído, clique em **Detalhes**, transfira o gerado. JSON ficheiro e guarde-o.

    ![Transferir e guardar dados processados num ficheiro JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Consulta pass-through

Se pretende arquivar todos os eventos, pode utilizar uma consulta pass-through para todos os campos a carga útil o evento ou a mensagem de ler. Para iniciar, efetue uma consulta pass-through simples que todos os campos num evento de projetos.

1.  Clique em **consulta** situado na parte superior da página de tarefa a análise da cadeia.
2.  Adicione o seguinte para o editor de código:

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Certifique-se de que o nome da origem de entrada corresponde ao nome da entrada que especificou anterior.

3.  Clique em **Testar** em editor de consultas.
4.  Fornece um ficheiro de teste. Utilize um que tenha criado utilizando os passos anteriores, ou utilize [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Clique no botão **Verificar** e ver os resultados apresentados abaixo a definição de consulta.

    ![Resultados da definição de consulta](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projecções de coluna

Vamos agora irá reduzir os campos devolvidos para um conjunto mais pequeno.

1.  Altere a consulta no editor de código para:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultado no editor de consultas.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de receber chamadas por região: Tumbling janela com agregação

Para comparar o número de chamadas recebidas por região, vamos utilizar um [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de chamadas recebidas agrupados por **SwitchNum** em cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Esta consulta utiliza a palavra-chave **Carimbo por** para especificar um campo de hora a carga útil para ser utilizada no cálculo temporal. Se este campo não foi especificado, teria de ser efetuada a operação de sistema baseado em janelas utilizando a hora em que cada evento chegou no centro do evento. Consulte o artigo [referência da linguagem de consulta "Chegada Vs aplicação hora" na análise da cadeia](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Tenha em atenção que pode aceder a um carimbo de data/hora de fim de cada janela utilizando a propriedade **System.Timestamp** .

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultados da consulta para Timestand por](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Deteção de fraude SIM com uma associação interna

Para identificar a utilização potencialmente fraudulenta, veremos para chamadas que têm origem a partir do mesmo utilizador mas em diferentes localizações no menos de 5 segundos.  Estamos a [associação](https://msdn.microsoft.com/library/azure/dn835026.aspx) a sequência de eventos de chamada com o próprio para verificar a existência nestes casos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultados da consulta de uma associação](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Criar receptor de saída

Agora que depois de definir uma sequência de evento, um concentrador de evento de entrada para ingerir esta última eventos e uma consulta para desempenhar uma transformação a sequência, o último passo é definir um receptor de saída para a tarefa. Armazenamento de Blobs do Azure podemos irá escrita eventos para o comportamento de fraudulento.

Utilize os passos seguintes para criar um contentor de armazenamento de BLOBs se ainda não tiver uma.

1.  Utilizar uma conta de armazenamento existente ou crie uma nova conta de armazenamento ao clicar em **Novo > Serviços de dados > armazenamento > criar rápida**e siga as instruções.
2.  Selecione a conta de armazenamento, clique em **contentores** na parte superior da página e, em seguida, clique em **Adicionar**.
3.  Especifique um **nome** para o contentor e definir o seu **acesso** a **Blob público**.

## <a name="specify-job-output"></a>Especificar o resultado da tarefa

1.  No seu trabalho sequência análise, clique em **saída** na parte superior da página e, em seguida, clique em **Adicionar saída**. Caixa de diálogo que é aberta irá guiá-lo através de vários passos para configurar o seu resultado.
2.  Clique em **Armazenamento de BLOBS**e, em seguida, clique no botão à direita.
3.  Escreva ou selecione os seguintes valores na terceira página:

    * **ALIAS de saída**: introduza um nome amigável para este resultado de tarefa.
    * **Subscrição**: se for o armazenamento de BLOBs que criou na mesma subscrição do que a tarefa de análise da cadeia, clique em **Utilizar a conta de armazenamento da subscrição atual**. Se for o armazenamento de uma subscrição diferente, clique em **Utilizar a conta de armazenamento da subscrição outra**e introduzir manualmente as informações para a **Conta de armazenamento**, **Chave da conta de armazenamento**e **contentor**.
    * **Conta de armazenamento**: selecione o nome da conta de armazenamento.
    * **Contentor**: selecione o nome do contentor.
    * **Nome de ficheiro PREFIXO**: escreva um prefixo de ficheiro para utilizar ao escrever blob saída.

4.  Clique no botão à direita.
5.  Especifique os seguintes valores:

    * **Formato de SERIALIZADOR evento**: JSON
    * **Codificação**: UTF8

6.  Clique no botão **Verificar** para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para a conta de armazenamento.

## <a name="start-job-for-real-time-processing"></a>Iniciar a tarefa para processamento em tempo real

Uma vez que uma entrada de tarefa, consultas e saída todos foram especificados, podemos está prontos para iniciar a tarefa de análise da cadeia de deteção de fraude em tempo real.

1.  A tarefa de **DASHBOARD**, clique em **Iniciar** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, clique em **hora de início de tarefa**e, em seguida, clique no botão **Verificar** na parte inferior da caixa de diálogo. O estado da tarefa irá alterar para **inicial** e brevemente irá alterar para **Executar**.

## <a name="view-fraud-detection-output"></a>Vista fraude deteção saída

Utilize uma ferramenta de como o [Explorador de armazenamento do Azure](http://storageexplorer.com/) ou o [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver eventos fraudulentos à medida que são escritas ao seu resultado em tempo real.  

![Deteção de fraude: eventos fraudulentos visualizados em tempo real](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Tarefas de análise do Azure da cadeia de escala](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
