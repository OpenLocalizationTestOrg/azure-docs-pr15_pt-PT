<properties
    pageTitle="Começar a trabalhar com a análise de sequência: deteção de fraude em tempo real | Microsoft Azure"
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

Saiba como criar uma solução de fim para fim para deteção fraude em tempo real com a análise da cadeia de Azure. Trazer eventos a um concentrador de evento Azure, escrever a análise da cadeia de consultas para agregação ou alertar e enviar os resultados para um receptor de saída para obter conhecimentos aprofundados sobre os dados com o processamento em tempo real. Deteção de anomalia em tempo real de telecomunicações está abrangida mas a técnica de exemplo é igualmente adequada para outros tipos de deteção de fraude como o cartão de crédito ou identidade de cenários de roubo.

Análise da cadeia é um serviço totalmente gerido fornecer processamento de baixa latência, altamente disponível, dimensionáveis eventos complexas ao longo de transmissão de dados na nuvem. Para mais informações, consulte o artigo [Introdução ao Azure da cadeia Analytics](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Deteção de fraude de telecomunicações e SIM em tempo real

Numa empresa de telecomunicações tem um grande volume de dados para chamadas a receber. A empresa precisa seguintes a partir dos seus dados:
* Pára estes dados para baixo até um montante fácil e obtenha informações sobre a utilização de cliente ao longo do tempo e regiões geográficas.
* Deteta fraude SIM (chamadas de vários provenientes da mesma identidade à volta ao mesmo tempo, mas em locais diferentes geograficamente) em tempo real para que estes podem responder facilmente por notificando os clientes ou encerrar serviço.

Em cenários de Internet coisas (IoT) canónicos lá está a ser gerado um ton de dados de telemetria ou sensor – e clientes pretende agregá-los ou alerta sobre anomalias em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

- Transferir [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) a partir do Centro de transferências da Microsoft 
- Opcional: O código de origem do gerador evento a partir do [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Criar uma entrada de Azure evento concentradores e grupo do consumidor

A aplicação de exemplo irá gerar eventos e transmiti-los para uma instância do concentrador de evento para processamento em tempo real. Serviço Bus evento concentradores são o método preferencial ingestão de evento para análise da cadeia e pode obter mais informações sobre o evento concentradores na [documentação do Azure Service Bus](/documentation/services/service-bus/).

Para criar um concentrador de evento:

1.  No [portal do Azure](https://manage.windowsazure.com/) , clique em **Novo** > **Serviços de aplicação** > **Serviço Bus** > **Concentrador de evento** > **Criação rápida**. Fornece um nome, a região e o espaço de nomes novo ou existente para criar um novo concentrador de evento.  
2.  Como prática recomendada, deverá ler cada tarefa de análise da cadeia a partir de um único grupo de consumidor do Hub do evento. Vamos irá guiá-lo durante o processo de criação de um grupo do consumidor abaixo e pode [saber mais sobre os grupos do consumidor](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um grupo do consumidor, navegue para o concentrador de evento recentemente criado e clique no separador de **Grupos de consumidores** , em seguida, clique em **Criar** na parte inferior da página e forneça um nome para o grupo do consumidor.
3.  Para conceder acesso ao centro do evento, que precisamos de criar uma política de acesso partilhado.  Clique no separador **Configurar** do seu centro de evento.
4.  Em **Políticas de acesso partilhados**, crie uma nova política com **Gerir** permissões.

    ![Partilhado onde pode criar uma política com permissões de gerir as políticas de acesso.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  Clique em **Guardar** na parte inferior da página.
6.  Navegue para o **Dashboard** e clique em **Informações de ligação** na parte inferior da página, em seguida, copie e guardar as informações de ligação.

## <a name="configure-and-start-event-generator-application"></a>Configurar e iniciar a aplicação criador de evento

Fornecemos uma aplicação de cliente que irá gerar exemplo recebidas metadados de chamada e enviá-lo a concentrador de evento. Siga os passos abaixo para configurar esta aplicação.  

1.  Transferir o [ficheiro TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Em seguida, deszipe para um diretório.

    **Nota**: Windows pode bloquear o ficheiro transferido zip. Clique com o botão direito do rato em ficheiro e selecione propriedades. Se a mensagem "este ficheiro é proveniente de outro computador e poderá estar bloqueado para ajudar a proteger neste computador." em seguida, assinale a caixa de "Desbloquear" e clique em aplicar no ficheiro zip.

2.  Substitua valores de Microsoft.ServiceBus.ConnectionString e EventHubName do **telcodatagen.exe.config** com o seu nome e a cadeia de ligação do Centro de evento.

    **Nota**: A cadeia de ligação copiado os locais portais Azure o nome da ligação no final. Certifique-se remover a "; EntityPath =<value>"da chave de adicionar campo =.

3.  Inicie a aplicação. Utilização de é da seguinte forma:

   telcodatagen.exe [#NumCDRsPerHour] [SIM cartão fraude probabilidade] [#DurationHours]

O exemplo seguinte irá gerar eventos de 1000 com uma probabilidade de 20 por cento de fraude ao longo da 2 horas.

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


## <a name="create-stream-analytics-job"></a>Criar a análise da cadeia de tarefa
Agora que temos uma sequência de eventos de telecomunicações, podemos pode configurar uma tarefa de análise da cadeia para analisar estas eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionar uma tarefa de análise da cadeia

1.  No portal do Azure, clique em **Novo > Serviços de dados > análise da cadeia > criar rápida**.
2.  Especifique os seguintes valores e, em seguida, clique em **Criar tarefa de análise de sequência**:

    * **Nome da tarefa**: introduza um nome de tarefa.

    * **Região**: selecione a região em que pretende executar a tarefa. Considere a colocação a tarefa e centro do evento na mesma região para garantir melhor desempenho e para garantir que que será não paga transferir dados entre regiões.

    * **Conta de armazenamento**: selecione a conta de armazenamento Azure que pretende utilizar para armazenar dados de monitorização para todas as tarefas de análise da cadeia em execução no interior esta região. Tem a opção para escolher uma conta de armazenamento existente ou para criar um novo.

3.  Clique em **Análise de sequência** no painel da esquerda para listar as tarefas de análise da cadeia.

    ![Ícone de serviço de análise da cadeia](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  A nova tarefa será apresentada com o estado **criado**. Repare que o botão **Iniciar** na parte inferior da página está desativado. Tem de configurar a entrada de tarefa, saída e consulta antes de poder iniciar a tarefa.

### <a name="specify-job-input"></a>Especifique a entrada de tarefa
1.  No seu trabalho de análise da cadeia clique em **entradas** situado na parte superior da página e, em seguida, clique em **Adicionar entrada**. Caixa de diálogo que é aberto irá guiá-lo através de um número de passos para configurar o seu teclado.
2.  Selecione **fluxo de dados**e, em seguida, clique no botão à direita.
3.  Selecione **Concentrador de evento**e, em seguida, clique no botão à direita.
4.  Escreva ou selecione os seguintes valores na terceira página:

    * **Alias de entrada**: introduza um nome amigável para esta tarefa, tal como *CallStream*de entrada. Tenha em atenção que irá utilizar este nome na consulta mais tarde.
    * **Concentrador de evento**: se o concentrador de evento que criou for na mesma subscrição do que a tarefa de análise da cadeia, selecione o espaço de nomes se encontra o concentrador de evento.

    Se for o concentrador de evento numa subscrição diferente, selecione **Utilizar concentrador de evento a partir de outra subscrição** e introduzir manualmente as informações para **o espaço de nomes do serviço Bus**, **Evento concentrador nome**, **Nome da política concentrador de evento**, **Chave de política do concentrador de evento**e **Contagem de partições concentrador de evento**.

    * **Nome do concentrador de evento**: selecione o nome do centro do evento.

    * **Nome da política concentrador evento**: selecione a política de evento concentrador criada anteriormente no tutorial.

    * **Grupo de consumidor do concentrador de evento**: escreva o grupo do consumidor criado anteriormente no tutorial.
5.  Clique no botão à direita.
6.  Especifique os seguintes valores:

    * **Formato de serializador evento**: JSON
    * **Codificação**: UTF8
7.  Clique no botão de verificação para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para o centro do evento.

### <a name="specify-job-query"></a>Especificar a consulta de tarefa

Análise da cadeia suporta um modelo de consulta simples, declarativa para descrever transformações para processamento em tempo real. Para saber mais sobre o idioma, consulte a [Referência da linguagem de consulta Azure da cadeia Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Neste tutorial irá ajudá-lo do autor e testar várias consultas ao longo do seu fluxo de chamada de dados em tempo real.

#### <a name="optional-sample-input-data"></a>Opcionais: Dados de entrada de exemplo
Para validar a sua consulta de dados real do trabalho, pode utilizar a funcionalidade de **Dados de exemplo** para extrair eventos a partir da sua sequência e criar uma. Ficheiro de JSON dos eventos para testar a ligação.  Os seguintes passos mostram como o fazer e também fornecemos um ficheiro de [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) de exemplo para fins de teste.

1.  Selecione os dados introduzidos concentrador de evento e clique em **Dados de exemplo** na parte inferior da página.
2.  Na caixa de diálogo que é apresentada, especifique uma **Hora de início** para iniciar a recolha de dados a partir de e uma **duração** por quanto dados adicionais para consumir.
3.  Clique no botão verificação de partida dados amostragem a entrada de dados.  Pode demorar um minuto ou dois para o ficheiro de dados para ser apresentado.  Quando o processo for concluído, clique em **Detalhes** e transferir e guardá-o. Ficheiro JSON que é gerado.

    ![Transferir e guardar dados processados num ficheiro JSON](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>Consulta pass-through

Se pretende arquivar todos os eventos, pode utilizar uma consulta pass-through para todos os campos a carga útil o evento ou a mensagem de ler. Para começar, faça uma consulta pass-through simples que todos os campos num evento de projetos.

1.  Clique em **consulta** situado na parte superior da página de tarefa a análise da cadeia.
2.  Adicione o seguinte para o editor de código:

        SELECT * FROM CallStream

    > Certifique-se de que o nome da origem de entrada corresponde ao nome da entrada que especificou anteriormente.

3.  Clique em **Testar** em editor de consultas.
4.  Fornecer um ficheiro de teste, que tenha criado utilizando os passos anteriores ou utilize [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5.  Clique no botão verificar e ver os resultados fossem apresentados abaixo a definição de consulta.

    ![Resultados da definição de consulta](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projecções de coluna

Vamos agora irá pára para baixo os campos a um conjunto mais pequeno devolvidos.

1.  Altere a consulta no editor de código para:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultado no editor de consultas.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de receber chamadas por região: Tumbling janela com agregação

Para comparar a quantia que chamadas recebidas por região podemos irá tirar partido um [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de chamadas recebidas agrupados por SwitchNum 5 segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Esta consulta utiliza a palavra-chave **Carimbo por** para especificar um campo de hora a carga útil para ser utilizada no cálculo temporal. Se este campo não foi especificado, teria ser efetuada a operação de sistema baseado em janelas utilizando o tempo de que cada evento chegou no concentrador do evento. Consulte o artigo [referência da linguagem de consulta "Chegada Vs aplicação hora" na análise da cadeia](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Tenha em atenção que pode aceder a um carimbo de data/hora de fim de cada janela utilizando a propriedade **System.Timestamp** .

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultados da consulta para Timestand por](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Deteção de fraude SIM com uma associação interna

Para identificar a utilização potencialmente fraudulenta veremos para chamadas com origem a partir do mesmo utilizador mas em diferentes localizações no menos de 5 segundos.  Estamos a [associação](https://msdn.microsoft.com/library/azure/dn835026.aspx) a sequência de eventos de chamada com o próprio para verificar a existência nestes casos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultados da consulta de uma associação](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Criar receptor de saída

Agora que depois de definir uma sequência de evento, um concentrador de evento para ingerir esta última eventos e uma consulta de entrada para efetuar uma transformação sobre a sequência, o último passo é definir um receptor de saída para a tarefa.  Vamos irá escrever eventos para o comportamento de fraudulento ao armazenamento Blob.

Siga os passos abaixo para criar um contentor de armazenamento de BLOBs se ainda não tiver uma.

1.  Utilizar uma conta de armazenamento existente ou crie uma nova conta de armazenamento ao clicar em **Novo > Serviços de dados > armazenamento > criar rápida** e seguir as instruções.
2.  Selecione a conta de armazenamento, clique em **contentores** na parte superior da página e, em seguida, clique em **Adicionar**.
3.  Especifique um **nome** para o contentor e configure o seu **acesso** para Blob público.

## <a name="specify-job-output"></a>Especificar o resultado da tarefa

1.  No seu trabalho de análise da cadeia clique em **saída** situado na parte superior da página e, em seguida, clique em **Adicionar saída**. Caixa de diálogo que é aberta irá guiá-lo-o através de um número de passos para configurar o seu resultado.
2.  Selecione **Armazenamento de BLOBS**e, em seguida, clique no botão à direita.
3.  Escreva ou selecione os seguintes valores na terceira página:

    * **ALIAS de saída**: introduza um nome amigável para este resultado de tarefa.
    * **Subscrição**: se for o armazenamento de BLOBs que criou na mesma subscrição do que a tarefa de análise da cadeia, selecione **Utilizar a conta de armazenamento da subscrição atual**. Se for o armazenamento de uma subscrição diferente, selecione **Utilizar a conta de armazenamento da subscrição outra** e introduzir manualmente as informações de **Conta de armazenamento**, **Chave da conta de armazenamento**, **contentor**.
    * **Conta de armazenamento**: selecione o nome da conta de armazenamento.
    * **Contentor**: selecione o nome do contentor.
    * **Nome de ficheiro PREFIXO**: tipo num prefixo de ficheiro para utilizar quando escrever blob saída.

4.  Clique no botão à direita.
5.  Especifique os seguintes valores:

    * **Formato de SERIALIZADOR evento**: JSON
    * **Codificação**: UTF8

6.  Clique no botão de verificação para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para a conta de armazenamento.

## <a name="start-job-for-real-time-processing"></a>Iniciar a tarefa para processamento em tempo real

Uma vez que uma entrada de tarefa, consultas e saída todos foram especificados, podemos está prontos para iniciar a tarefa de análise da cadeia de deteção de fraude em tempo real.

1.  A tarefa de **DASHBOARD**, clique em **Iniciar** na parte inferior da página.
2.  Na caixa de diálogo que aparece, selecione a **hora de início de tarefa** e, em seguida, clique no botão de verificação na parte inferior da caixa de diálogo. O estado da tarefa irá alterar para **inicial** e brevemente irá mover para **a executar**.

## <a name="view-fraud-detection-output"></a>Vista fraude deteção saída

Utilize uma ferramenta de como o [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou o [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver eventos fraudulentos à medida que são escritas ao seu resultado em tempo real.  

![Deteção de fraude: eventos fraudulentos visualizados em tempo real](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
