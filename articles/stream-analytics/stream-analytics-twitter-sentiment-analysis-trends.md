<properties
    pageTitle="Análise de sentimento Twitter em tempo real com a análise da cadeia | Microsoft Azure"
    description="Saiba como utilizar a análise da cadeia para uma análise de sentimento Twitter em tempo real. Orientações passo a passo de geração evento aos dados de um dashboard direto."
    keywords="análise de tendências twitter em tempo real, análise sentimento, análise de redes sociais, exemplo de análise de tendência"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de redes sociais: análise de sentimento Twitter em tempo real no Azure da cadeia Analytics

Saiba como criar uma solução de análise de sentimento para análise de redes sociais por trazer em tempo real Twitter eventos para Azure evento concentradores. Irá escrever uma consulta de análise da cadeia de Azure para analisar os dados. Que irá, em seguida, quer armazenar os resultados para perusal posterior ou utilizar um dashboard e o [Power BI](https://powerbi.com/) para fornecer informações em tempo real.

Ferramentas de análise de redes sociais ajudam as organizações a compreender tendência tópicos, ou seja, assuntos atitudes e que têm um grande volume de mensagens em redes sociais. Análise de sentimento, também denominada *extração de parecer*, utiliza ferramentas de análise de redes sociais para determinar atitudes face um produto, ideia e assim sucessivamente. Análise de tendências Twitter em tempo real é um exemplo excelente porque o modelo de subscrição hashtag permite-lhe ouvir palavras-chave específicas e desenvolver sentimento análise no feed.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento em tempo real

Uma empresa que tem um site de notícias multimédia está interessada em obter uma vantagem sobre os seus concorrentes por que inclui o conteúdo do site que é relevante imediatamente aos seus leitores. A empresa utiliza análise de redes sociais sobre tópicos que são relevantes para os leitores ao efetuar análise de sentimento em tempo real no Twitter dados. Especificamente, para identificar tópicos tendência em tempo real no Twitter, a empresa necessita de análise em tempo real sobre o volume de tweet e sentimento para tópicos-chave. Por isso, na sua essência a necessidade é um motor de análise de análise de sentimento que é baseado neste feed de redes sociais.

## <a name="prerequisites"></a>Pré-requisitos
-   Twitter conta e [token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) a partir do Centro de transferências da Microsoft
-   Opcional: O código de origem para o cliente do twitter do [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Criar um concentrador de evento de entrada e um grupo do consumidor

A aplicação de exemplo irá gerar eventos e transmiti-los para uma instância de evento concentradores (um concentrador evento, para abreviada). Concentradores de evento de serviço Bus são o método preferencial ingestão de evento para análise da cadeia. Consulte a documentação de evento concentradores na [documentação do serviço Bus](/documentation/services/service-bus/).

Utilize os passos seguintes para criar um concentrador de evento.

1.  No portal do Azure, clique em **Novo** > **Serviços de aplicação** > **Serviço BUS** > **CONCENTRADOR de evento** > **Criação rápida**e fornecer um nome, a região e o espaço de nomes novo ou existente.  
2.  Como prática recomendada, deverá ler cada tarefa de análise da cadeia de um grupo de consumidor do evento concentradores único. Vamos irá guiá-lo durante o processo de criação de um grupo de consumidor mais tarde. Pode saber mais sobre os grupos do consumidor na [Descrição geral do Azure evento concentradores](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um grupo do consumidor, aceda ao centro do evento recentemente criado, clique no separador **Consumidor grupos** , clique em **Criar** na parte inferior da página e, em seguida, forneça um nome para o grupo do consumidor.
3.  Para conceder acesso ao centro do evento, precisamos de criar uma política de acesso partilhado. Clique no separador **Configurar** do seu centro de evento.
4.  Em **Políticas de acesso PARTILHADOS**, crie uma nova política com **GERIR** permissões.

    ![Partilhado onde pode criar uma política com permissões de gerir as políticas de acesso.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Clique em **Guardar** na parte inferior da página.
6.  Aceda a **DASHBOARD**, clique em **Informações de ligação** na parte inferior da página e, em seguida, copiar e guardar as informações de ligação. (Utilizar no ícone de cópia que surge sob o ícone de pesquisa).

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar a aplicação de cliente do Twitter

Fornecemos uma aplicação de cliente irá ligar a dados de Twitter através de [Transmissão APIs do Twitter](https://dev.twitter.com/streaming/overview) para recolher eventos Tweet acerca de um conjunto de tópicos parametrizado. A ferramenta de abrir origem [Sentiment140](http://help.sentiment140.com/) é utilizada para atribuir um valor de sentimento a cada tweet.

- 0 = negativo
- 2 = neutro
- 4 = positivo

Em seguida, eventos Tweet são enviados para o concentrador de evento.  

Siga estes passos para configurar a aplicação:

1.  [Transferir a solução TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Abra TwitterClient.exe.config e substitua oauth_consumer_key, oauth_consumer_secret, oauth_token e oauth_token_secret Twitter tokens que têm os valores.  

    [Passos para gerar um token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Tenha em atenção que terá de efetuar uma aplicação vazia para gerar um token.  
3.  Substitua os valores EventHubConnectionString e EventHubName TwitterClient.exe.config a cadeia de ligação e o nome do seu centro de evento. A cadeia de ligação que copiou anteriormente fornece-lhe a cadeia de ligação e o nome do seu centro de evento, por isso, certifique-se para separá-los e colocar cada no campo correto. Por exemplo, considere a seguinte cadeia de ligação:

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    O ficheiro de TwitterClient.exe.config deve conter as definições de tal como no exemplo seguinte:

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    É importante que tenha em atenção que o texto "EntityPath =" é que __não__ são apresentados no valor EventHubName.

4.  *Opcional:* Ajuste as palavras-chave para procurar.  Por predefinição, esta aplicação procura "Azure, Skype, XBox, Microsoft, Seattle".  Pode ajustar os valores para **twitter_keywords** no TwitterClient.exe.config, se pretender.
5.  Execute TwitterClient.exe para iniciar a aplicação. Irá ver eventos Tweet com os valores de **CreatedAt**, **tópico**e **SentimentScore** a ser enviados para o seu centro de evento.

    ![Análise de sentimento: os valores de SentimentScore enviados a um concentrador de evento.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa de análise da cadeia

Agora que eventos Tweet são transmissão em tempo real a partir do Twitter, podemos pode configurar uma tarefa de análise da cadeia para analisar estas eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionar uma tarefa de análise da cadeia

1.  No [portal do Azure](https://manage.windowsazure.com/), clique em **Novo** > **Serviços de dados** > **A análise da cadeia** > **Criação rápida**.
2.  Especifique os seguintes valores e, em seguida, clique em **Criar tarefa de análise de sequência**:

    * **Nome da tarefa**: introduza um nome de tarefa.
    * **Região**: selecione a região em que pretende executar a tarefa. Considere a colocação a tarefa e centro do evento na mesma região para garantir melhor desempenho e para garantir que que será não paga transferir dados entre regiões.
    * **Conta de armazenamento**: selecione a conta de armazenamento Azure que pretende utilizar para armazenar dados de monitorização para todas as tarefas de análise da cadeia que executar esta região. Tem a opção para escolher uma conta de armazenamento existente ou para criar um novo.

3.  Clique em **Análise de sequência** no painel da esquerda para listar as tarefas de análise da cadeia.  
    ![Ícone de serviço de análise da cadeia](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    A nova tarefa será apresentada com o estado **criado**. Repare que o botão **Iniciar** na parte inferior da página está desativado. Tem de configurar a entrada de tarefa, saída e consulta antes de poder iniciar a tarefa.


### <a name="specify-job-input"></a>Especifique a entrada de tarefa
1.  Na sua tarefa de análise da cadeia, clique em **entradas** situado na parte superior da página e, em seguida, clique em **Adicionar entrada**. Caixa de diálogo que é aberto irá guiá-lo através de um número de passos para configurar o seu teclado.
2.  Clique em **fluxo de dados**e, em seguida, clique no botão à direita.
3.  Clique em **Centro de evento**e, em seguida, clique no botão à direita.
4.  Escreva ou selecione os seguintes valores na terceira página:

    * **ALIAS de entrada**: introduza um nome amigável para esta tarefa de entrada, tal como *TwitterStream*. Tenha em atenção que irá utilizar este nome na consulta mais tarde.
    **CONCENTRADOR de evento**: se o concentrador de evento que criou for na mesma subscrição do que a tarefa de análise da cadeia, selecione o espaço de nomes se encontra o concentrador de evento.

        Se o seu centro de evento estiver numa subscrição diferente, clique em **Utilizar concentrador de evento a partir de outra subscrição**e, em seguida, introduzir manualmente as informações para **o espaço de nomes do serviço BUS**, **Nome do CONCENTRADOR do evento**, **Evento CONCENTRADOR nome da política**, **A chave de política de CONCENTRADOR de evento**e **CONTAGEM de partições CONCENTRADOR de evento**.

    * **Nome do CONCENTRADOR de evento**: selecione o nome do centro do evento.

    * **Nome da política CONCENTRADOR evento**: selecione a política de concentrador de evento que criou anteriormente no tutorial.

    * **Grupo de consumidor do CONCENTRADOR de evento**: escreva o nome do grupo consumidor que criou anteriormente no tutorial.
5.  Clique no botão à direita.
6.  Especifique os seguintes valores:

    * **Formato de SERIALIZADOR evento**: JSON
    * **Codificação**: UTF8

7.  Clique no botão **Verificar** para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para o centro do evento.

### <a name="specify-job-query"></a>Especificar a consulta de tarefa

Análise da cadeia suporta um modelo de consulta simples, declarativa que descreve transformações. Para saber mais sobre o idioma, consulte a [Referência da linguagem de consulta Azure da cadeia Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Neste tutorial irá ajudá-lo do autor e testar várias consultas sobre os dados do Twitter.

#### <a name="sample-data-input"></a>Introdução de dados de exemplo

Para validar a sua consulta de dados real do trabalho, pode utilizar a funcionalidade de **Dados de exemplo** para extrair eventos da sua cadeia e cria um ficheiro de .json dos eventos para testar a ligação.

1.  Selecione os dados introduzidos concentrador de evento e, em seguida, clique em **Dados de exemplo** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, especifique uma **hora de início** para começar a recolher dados e uma **duração** por quanto dados adicionais para consumir.
3.  Clique no botão **Detalhes** e, em seguida, clique na ligação **clique aqui** para transferir e guardar o ficheiro .json gerado.

#### <a name="pass-through-query"></a>Consulta pass-through
Para iniciar, vamos fazê uma consulta pass-through simples que todos os campos num evento de projetos.

1.  Clique em **consulta** na parte superior da página de tarefa a análise da cadeia.
2.  No editor de código, substitua o modelo de consulta inicial com o seguinte:

        SELECT * FROM TwitterStream

    Certifique-se de que o nome da origem de entrada corresponde ao nome da entrada que especificou anterior.

3.  Clique em **Testar** em editor de consultas.
4.  Aceda ao seu ficheiro de .json de exemplo.
5.  Clique no botão **Verificar** e ver os resultados abaixo a definição de consulta.

    ![Resultados apresentados abaixo definição de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Contagem de tweets por tópico: Tumbling janela com agregação

Para comparar o número de menções entre tópicos, vamos utilizar um [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico em cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Esta consulta utiliza a palavra-chave **Carimbo por** para especificar um campo de hora a carga útil para ser utilizada no cálculo temporal. Se este campo não foi especificado, teria de ser efetuada a operação de sistema baseado em janelas utilizando a hora em que cada evento chegou no centro do evento.  Saiba mais na secção "Chegada Vs aplicação hora" de [Referência de consulta de análise da cadeia](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Esta consulta acede também um carimbo de data/hora de fim de cada janela utilizando a propriedade **System.Timestamp** .

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

#### <a name="identify-trending-topics-sliding-window"></a>Identificar tópicos tendência: janela deslizante

Para identificar tópicos tendência, veremos para tópicos que cruz um valor limite para @ menções um determinado período de tempo. Para efeitos neste tutorial, vamos irá procurar tópicos que são mencionados mais do que 20 horas nos últimos cinco segundos utilizando um [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Alterar a consulta no editor de código para: SELECIONE System.Timestamp como a hora, tópico, contar (*) como menções a partir do TwitterStream carimbo por CreatedAt grupo por SLIDINGWINDOW(s, 5), tópico tendo contar (*) > 20

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.

    ![Resultado da consulta janela deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Contagem de menções e sentimento: Tumbling janela com agregação
A consulta final que recomendamos irá testar utiliza **TumblingWindow** para obter o número de menções, média, mínimo, máximo e desvio padrão da pontuação sentimento para cada tópico em cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Clique em **voltar a executar** o editor de consultas para ver os resultados da consulta.
3.  Esta é a consulta que utilizamos para os nossos dashboard.  Clique em **Guardar** na parte inferior da página.


## <a name="create-output-sink"></a>Criar receptor de saída

Agora que depois de definir uma sequência de evento, um concentrador de evento de entrada para ingerir esta última eventos e uma consulta para desempenhar uma transformação a sequência, o último passo é definir um receptor de saída para a tarefa.  Pedimos irá escrever os eventos tweet agregado da nossa consulta de tarefa para o armazenamento de Blobs do Azure.  Também poderá emissão os seus resultados Azure base de dados SQL, armazenamento de tabela do Azure, ou concentradores de evento, dependendo da sua aplicação específica necessita.

Utilize os passos seguintes para criar um contentor de armazenamento de BLOBs, se ainda não tiver uma:

1.  Utilizar uma conta de armazenamento existente ou criar uma nova conta de armazenamento, clicando em **Novo** > **Serviços de dados** > **armazenamento** > **Criação rápida**e, em seguida, seguir as instruções no ecrã.
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

## <a name="start-job"></a>Iniciar a tarefa

Uma vez que uma entrada de tarefa, consultas e saída todos foram especificados, podemos está prontos para iniciar a tarefa de análise da cadeia.

1.  A tarefa de **DASHBOARD**, clique em **Iniciar** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, clique em **hora de início de tarefa**e, em seguida, clique no botão **Verificar** na parte inferior da caixa de diálogo. O estado da tarefa irá alterar para **inicial** e brevemente irá alterar para **Executar**.


## <a name="view-output-for-sentiment-analysis"></a>Vista de saída para uma análise sentimento

Após a tarefa está a executar e processamento a sequência de Twitter em tempo real, selecione como pretende ver os resultados para uma análise sentimento. Utilize uma ferramenta de como o [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou o [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver o resultado da tarefa em tempo real. A partir daqui, pode utilizar o [Power BI](https://powerbi.com/) para expandir a sua aplicação para incluir um dashboard personalizado, como o apresentado na captura de ecrã seguinte.

![Análise de redes sociais: resultado da análise da cadeia sentimento análise (extração de parecer) num dashboard do Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
