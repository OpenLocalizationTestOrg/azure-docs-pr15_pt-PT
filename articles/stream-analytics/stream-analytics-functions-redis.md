<properties
    pageTitle="Para uma Cache Redis de Azure, utilizar as funções Azure, a partir do Azure a análise da cadeia de saída | Microsoft Azure"
    description="Saiba como utilizar uma função Azure ligado uma fila de Bus de serviço, para preencher uma Azure Redis Cache de saída de uma tarefa de análise da cadeia."
    keywords="sequência de dados, redis cache, fila de bus de serviço"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Como armazenar dados a partir do Azure da cadeia Analytics numa Azure Redis Cache utilizar funções do Azure

Azure da cadeia Analytics permite-lhe rapidamente desenvolver e implementar soluções de custo de baixo para obter informações em tempo real a partir de dispositivos, sensores, infraestrutura e aplicações ou qualquer sequência de dados. Permite-casos de utilização de vários como gestão em tempo real e monitorização, comando e controlo, deteção de fraude, carros ligados e muito mais. Em muitos essas cenários, poderá querer armazenar dados debitar por Azure da cadeia Analytics para um arquivo de dados distribuídos como uma cache Azure Redis.

Imaginemos que fazem parte de uma empresa de telecomunicações. Está a tentar detetar fraude SIM onde várias chamadas provenientes da mesma identidade, ao mesmo tempo, mas no diferentes geograficamente localizações. Pode é com as tarefas de armazenamento de todos os potenciais fraudulentas chamadas telefónicas numa cache Azure Redis. Neste blogue, fornecemos orientações sobre como pode facilmente concluir a sua tarefa. 

## <a name="prerequisites"></a>Pré-requisitos
Concluir a [Deteção de fraude em tempo real] [ fraud-detection] guia passo a passo para ASA

## <a name="architecture-overview"></a>Descrição geral de arquitectura
![Captura de ecrã de arquitectura](./media/stream-analytics-functions-redis/architecture-overview.png)

Como apresentado na figura anterior, a análise da cadeia permite transmissão de dados de entrada para ser consultados e enviadas para um resultado. Com base no resultado, funções de Azure, em seguida, pode acionar algum tipo de evento. 

Neste blogue, iremos concentrar-na parte do Azure funções deste pipeline ou mais especificamente desencadear de um evento que armazena dados fraudulentos na cache.
Depois de concluir a [Deteção de fraude em tempo real] [ fraud-detection] tutorial, tem uma entrada (um concentrador de evento), uma consulta e uma saída (armazenamento de BLOBs) já está configurado e a executar. Este blogue, vamos alterar a saída para utilizar em vez disso, uma fila de Bus de serviço. Após esta ação, iremos ligar uma função Azure a esta fila. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Criar e ligar uma saída fila de Bus de serviço
Para criar uma fila de Bus de serviço, siga os passos 1 e 2 da secção .NET no [Artigo Introdução ao serviço Bus filas][servicebus-getstarted].
Agora vamos ligar fila de espera para a tarefa de análise da cadeia que foi criada a anterior fraude deteção guia passo a passo.



1. No portal do Azure, vá para o pá de **resultados** da sua tarefa de e selecione **Adicionar** na parte superior da página.

    ![Adicionar saídas](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Selecione **Fila de Bus serviço** como o **receber** e siga as instruções no ecrã. Certifique-se de que escolher o espaço de nomes de fila de Bus de serviço que criou no [Artigo Introdução ao serviço Bus filas][servicebus-getstarted]. Clique no botão "direito" quando tiver terminado.
3. Especifique os seguintes valores:
    - **Formato de serializador evento**: JSON
    - **Codificação**: UTF8
    - **Formato**: linha separados

4. Clique no botão **Criar** para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para a conta de armazenamento.

5. No separador **Query** , substitua a consulta atual com o seguinte. Substitua o nome de saída que criou no passo 3 *[Nome BUS do seu serviço]* . 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Criar uma Cache Azure Redis
Criar uma cache Azure Redis seguindo a secção .NET [como utilizar Azure Redis Cache] [ use-rediscache] até que a secção chamado ***configurar os clientes de cache***.
Uma vez concluída, tem uma nova Cache Redis. Em **todas as definições**, selecione **as teclas de acesso** e nota para baixo a ***cadeia de ligação principal***.

![Captura de ecrã de arquitectura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Criar uma função Azure
Siga a [criar a primeira função Azure] [ functions-getstarted] tutorial para começar a utilizar com funções Azure. Se já tiver uma função Azure que pretende utilizar, em seguida, avançar para [a escrita Redis Cache](#Writing-to-Redis-Cache)

1. No portal do, selecione os serviços de aplicação a partir do painel de navegação esquerdo, em seguida, clique no nome da aplicação de função Azure para aceder ao Web site de aplicação a função.
    ![Captura de ecrã da lista de funções de serviços de aplicação](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Clique em **nova função > ServiceBusQueueTrigger – c#**. Para os seguintes campos, siga estas instruções:
    - **Nome da fila**: O mesmo nome que o nome que introduziu quando criou fila de espera no [Artigo Introdução ao serviço Bus filas] [ servicebus-getstarted] (e não o nome do bus serviço). Certifique-se de que utilizar na fila que está ligada à saída da análise da cadeia.
    - **Ligação de serviço Bus**: selecione **Adicionar uma cadeia de ligação**. Para localizar a cadeia de ligação, aceda ao portal do clássico, selecione **Bus de serviço**, o bus de serviço que criou e **Informações de ligação** na parte inferior do ecrã. Certifique-se de que está no ecrã principal nesta página. Copie e cole a cadeia de ligação. Esteja à vontade introduzir quaisquer nome da ligação.
    
        ![Captura de ecrã da ligação ao serviço bus](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: selecione **Gerir**


3. Clique em **Criar**

## <a name="writing-to-redis-cache"></a>Ao escrever Redis Cache
Vamos agora ter criado uma função de Azure que lê a partir de uma fila de Bus de serviço. Tudo o que falta para fazer é utilizar o nossa função para escrever estes dados para a Cache do Redis. 

1. Selecione o seu recentemente criado **ServiceBusQueueTrigger**e clique em **definições de aplicação de função** no canto superior direito. Selecione **aceda à aplicação definições do serviço > Definições > definições da aplicação**

2. Na secção de cadeias de ligação, crie um nome na secção **nome** . Cole a cadeia de ligação principal que se encontram no passo **criar uma Cache Redis** para a secção de **valor** . Selecione **personalizada** , onde diz **Base de dados SQL**.

3. Clique em **Guardar** na parte superior.

    ![Captura de ecrã da ligação ao serviço bus](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Agora regresse às definições do serviço de aplicação e selecione **Ferramentas > Editor de serviço de aplicação (pré-visualização) > no > Ir**.

    ![Captura de ecrã da ligação ao serviço bus](./media/stream-analytics-functions-redis/app-service-editor.png)

5. Um editor da sua escolha, crie um ficheiro JSON chamado **project.json** com o seguinte e guarde-o para o disco local.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Carregar este ficheiro para o diretório de raiz da sua função (não WWWROOT). Deverá ver aparecem num ficheiro denominado **project.lock.json** automaticamente, a confirmar que o Nuget pacotes "StackExchange.Redis" e "Newtonsoft.Json" foram importados.

7. No ficheiro de **run.csx** , substitua o código gerado previamente com o código seguinte. Na função lazyConnection, substitua "Nome da ligação" com o nome que criou no passo 2 de **armazenar dados para a cache do Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Iniciar a tarefa de análise da cadeia

1. Inicie a aplicação telcodatagen.exe. Utilização de é da seguinte forma:````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. Pá a tarefa de análise da cadeia no portal do, clique em **Iniciar** na parte superior da página.

    ![Captura de ecrã de início de tarefa](./media/stream-analytics-functions-redis/starting-job.png)

3. Na pá **Iniciar a tarefa** que é apresentada, selecione **agora** e, em seguida, clique no botão **Iniciar** na parte inferior do ecrã. As tarefa as alterações de estado para iniciar e depois algumas alterações de hora para a trabalhar rapidamente.
 
    ![Captura de ecrã da seleção de tempo de trabalho de início](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Executar a solução e verifique os resultados
Regressar à sua página de **ServiceBusQueueTrigger** , deverá ver agora declarações de registo. Estes registos mostrar que recebeu algo do serviço Bus fila de espera, coloque-o para a base de dados e obter a ligação de saída utilizando a hora como chave!

Para verificar se os seus dados estão na cache do Redis, aceda à sua página de cache Redis no novo portal (conforme mostrado no passo anterior [criar uma Cache do Azure Redis](#Create-an-Azure-Redis-Cache) ) e selecione consola.

Agora pode escrever Redis comandos para confirmar que dados são de facto na cache.

![Captura de ecrã da consola de Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Próximos passos
Vamos celebrar sobre os aspetos novos funções do Azure e a análise da cadeia pode fazer em conjunto, podemos consiste e que isto desbloqueia novas possibilidades por si. Se tiver quaisquer comentários sobre o que pretende em seguida, à vontade utilizar o [site do uservoice do Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Se é novo Microsoft Azure, podemos convidar a experimentar ao inscrever-se para uma [conta de avaliação gratuita Azure](https://azure.microsoft.com/pricing/free-trial/). Se estiver familiarizado com a análise da cadeia, em seguida, podemos convidá-lo para [criar a primeira tarefa de análise da cadeia](stream-analytics-create-a-job.md).

Se precisar de qualquer ajuda ou tiver dúvidas, publicar nos fóruns do [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) ou [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) . 

Também pode ver os seguintes recursos:

- [Referência para programadores do Azure funções](../azure-functions/functions-reference.md)
- [Azure funções c# de referência para programadores](../azure-functions/functions-reference-csharp.md)
- [Azure funções F # de referência para programadores](../azure-functions/functions-reference-fsharp.md)
- [Referência para programadores do Azure NodeJS de funções](../azure-functions/functions-reference.md)
- [Azure accionadores de funções e enlaces](../azure-functions/functions-triggers-bindings.md)
- [Como monitorizar o Azure Redis Cache](../redis-cache/cache-how-to-monitor.md)

Para manter-se atualizado relativamente a todos os notícias e funcionalidades mais recentes, siga [@AzureStreaming](https://twitter.com/AzureStreaming) no Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
