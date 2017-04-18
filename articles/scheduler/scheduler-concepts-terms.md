<properties
 pageTitle="Scheduler conceitos, termos e entidades | Microsoft Azure"
 description="Azure Scheduler conceitos, terminologia e hierarquia entidade, incluindo tarefas e colecções de tarefa.  Mostra um exemplo de uma tarefa agendada abrangente."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Os conceitos de programador, terminologia + hierarquia entidade

## <a name="scheduler-entity-hierarchy"></a>Hierarquia de entidades de programador

A tabela seguinte descreve os recursos principais expostos ou é utilizado pela API do programador:

|Recurso | Descrição |
|---|---|
|**Colecção de tarefa**|Uma coleção de tarefa contém um grupo de tarefas e mantém definições, quotas e limitações que são partilhadas pelas tarefas dentro da coleção. Uma coleção de tarefa é criada por um proprietário de subscrição e grupos em conjunto com base em limites de aplicação ou a utilização de tarefas. É restringido a uma região. Também lhe permite a imposição dos quotas para restringir a utilização de todas as tarefas nessa coleção. As quotas de incluem MaxJobs e MaxRecurrence.|
|**Tarefa**|Uma tarefa define uma única acção recorrente, com simples ou complexas estratégias para execução. Ações podem incluir HTTP, fila de armazenamento, fila de bus serviço ou pedidos de serviço de bus tópico.|
|**Histórico da tarefa**|Um histórico de tarefa representa detalhes de uma execução de uma tarefa. Contém sucesso vs. falha, bem como qualquer detalhes da resposta.|

## <a name="scheduler-entity-management"></a>Gestão da entidade de Programador de tarefas

De alto nível do scheduler e a API de gestão do serviço expõem os recursos as seguintes operações:

|Capacidade de|Descrição e URI endereço|
|---|---|
|**Gestão da coleção de tarefa**|OBTER, colocar e eliminar suporte para criar e modificar colecções de tarefa e as tarefas nela contidas. Uma coleção de tarefa é um contentor para tarefas e mapas às quotas e definições partilhadas. Exemplos de quotas, descritos mais tarde, são o número máximo de tarefas e menor intervalo de periodicidade. <p>COLOCAR e eliminar:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>OBTER:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Gestão de projectos**|OBTER, colocar, publicar, correcção e eliminar suporte para criar e modificar tarefas. Todas as tarefas tem de pertencer a uma coleção de tarefa que já existe, pelo que não existe nenhuma implícita criação. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Gestão de histórico de tarefa**|OBTER suporte para obter a 60 dias do histórico de execução de tarefa, tal como o tempo decorrido tarefa e resultados de execução da tarefa. Adiciona suporte de parâmetro de cadeia de consulta para filtrar com base em e o estado. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Tipos de tarefa

Existem vários tipos de tarefas: serviço bus tópico tarefas, tarefas de fila de bus de serviço, tarefas de fila de armazenamento e tarefas de HTTP (incluindo tarefas HTTPS que suportam SSL). Tarefas de HTTP são ideais se tiver um ponto final de uma carga de trabalho ou serviço existente. Pode utilizar tarefas de fila de armazenamento para publicar mensagens para filas de armazenamento, para que as tarefas são ideais para das cargas de trabalho que utilizam filas de armazenamento. Da mesma forma, as tarefas de bus serviço são ideais para das cargas de trabalho que utilizam filas de bus de serviço e tópicos.

## <a name="the-job-entity-in-detail"></a>A entidade de "tarefa" em detalhe

A um nível básico, uma tarefa agendada tem várias partes:

- A ação a efetuar quando o temporizador da tarefa é acionada  

- (Opcional) O tempo para executar a tarefa  

- (Opcional) Quando e como muitas vezes a repetir a tarefa  

- (Opcional) Uma ação seja acionada se a ação primária falhar  

Internamente, uma tarefa agendada também contém dados fornecidos pelo sistema, como o tempo de execução agendado seguinte.

O código seguinte fornece um exemplo de uma tarefa agendada abrangente. São fornecidos detalhes nas secções subsequentes.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Conforme visto na tarefa agendada de exemplo acima, uma definição de tarefa tem várias partes:

- Hora de início ("Hora de início")  

- Ação ("acção"), que inclui a ação de erro ("errorAction")

- Periodicidade ("periodicidade")  

- Estado ("Estado")  

- Estado ("status")  

- Volte a tentar política ("retryPolicy")  

Vamos examinar cada um destes detalhadamente:

## <a name="starttime"></a>hora de início

A "Hora de início" é a hora de início e permite o autor da chamada especificar um desvio de fuso horário no fio no [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>ação e errorAction

A ação"" é a ação invocada cada ocorrência e descreve um tipo de invocação de serviço. A ação é o que será executado na agenda fornecida. Scheduler suporta HTTP, fila de armazenamento, tópico bus de serviço e acções do serviço bus fila de espera.

A ação no exemplo acima é uma ação de HTTP. Abaixo está um exemplo de uma ação de fila de armazenamento:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Abaixo está um exemplo de uma ação de tópico bus serviço.

  "ação": {"tipo": "serviceBusTopic", "serviceBusTopicMessage": {"topicPath": "t1",  
      "espaço de nomes": "mySBNamespace", "um transportType": "netMessaging", / / pode ser netMessaging ou AMQP "autenticação": {"sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "mensagem": "Algumas mensagem", "brokeredMessageProperties": {}, "customMessageProperties": {"nome": "FromScheduler"}},}

Abaixo está um exemplo de uma ação de fila de bus de serviço:


  "ação": {"serviceBusQueueMessage": {"NomeFila": "q1",  
      "espaço de nomes": "mySBNamespace", "um transportType": "netMessaging", / / pode ser netMessaging ou AMQP "autenticação": {  
        "sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "mensagem": "Algumas mensagem"  
      "brokeredMessageProperties": {}, "customMessageProperties": {"nome": "FromScheduler"}}, "tipo": "serviceBusQueue"}

"errorAction" é o processador de erro, a ação chamado quando a ação primária falha. Pode utilizar esta variável para ligar para um ponto final de processamento de erros ou enviar uma notificação de utilizador. Isto pode ser utilizado para alcançar um ponto final de secundário no caso de que a página principal não está disponível (por exemplo, no caso de uma falhas no site o ponto final) ou pode ser utilizado para notificar um ponto final de processamento de erros. Tal como a principal ação, a ação de erro pode ser lógica simple ou composta com base em outras ações. Para saber como criar um token de SA, consulte [criar e utilizar uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>Periodicidade

Periodicidade tem várias partes:

- Frequência: Um dos minutos, horas, dia, semana, mês e ano  

- Intervalo: Intervalo determinado frequência de periodicidade  

- Agenda prescrita: Especifique minutos, horas, dias da semana, meses e monthdays de recorrência  

- Contar: Contagem de ocorrências  

- Hora de fim: tarefas não executará após a hora de fim especificada  

Uma tarefa é periódica se tiver um objecto periódico especificado na sua definição de JSON. Se forem especificados contar e hora de fim, é pago a regra de conclusão que ocorre pela primeira vez.

## <a name="state"></a>Estado

O estado da tarefa é uma das quatro valores: ativada, desativada, concluída ou falhou. Pode colocar ou tarefas de PATCHES publicados para atualizá-las para o estado activado ou desactivado. Se uma tarefa foi concluída ou falhas, que é um estado final que não pode ser atualizado (apesar do trabalho ainda pode ser eliminado). Um exemplo da propriedade Estado é da seguinte forma:


        "state": "disabled", // enabled, disabled, completed, or faulted
Tarefas concluídas e com falhas são eliminadas após 60 dias.

## <a name="status"></a>Estado

Depois de ter começado a uma tarefa de programador, serão devolvidas informações sobre o estado atual da tarefa. Este objeto não é pode ser definido pelo utilizador — está definido pelo sistema. No entanto, é incluída no objecto de trabalho (em vez de um recurso ligado em separado) para que um pode obter o estado de uma tarefa de facilmente.

Estado da tarefa inclui a hora a execução do anterior (se existir), a hora da execução agendada seguinte (para em curso trabalhos) e a contagem de execução da tarefa.

## <a name="retrypolicy"></a>retryPolicy

Se uma tarefa de programador falhar, é possível especificar uma política de repetir para determinar se e como a ação é repetida. Isto é determinado pelo objeto **retryType** — está definido para **nenhum** caso não haja nenhuma política de repetir, conforme mostrado acima. Defini-lo **fixo** se existir uma política de repetir.

Para definir uma política de repetir, podem ser especificadas duas definições adicionais: um intervalo de repetição (**retryInterval**) e o número de tentativas (**retryCount**).

O intervalo de repetição especificado com objeto **retryInterval** , é o intervalo entre tentativas. O valor predefinido é 30 segundos, o valor mínimo configurável é 15 segundos e o valor máximo é 18 meses. Tarefas de coleções de tarefa gratuito tem um valor mínimo configurável de 1 hora.  É definido no formato ISO 8601. Do mesmo modo, o valor de número de tentativas for especificado com objeto **retryCount** ; é o número de vezes que uma repetição é tentada. O valor predefinido é 4 e o seu valor máximo é 20\. ambos **retryInterval** e **retryCount** são opcionais. Estes são apresentados os respetivos predefinido valores se **retryType** estiver definido para **fixo** e não são especificadas explicitamente.

## <a name="see-also"></a>Consulte também

 [O que é o Scheduler?](scheduler-intro.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Como construir complexas agendas e periodicidade avançada com o programador do Azure](scheduler-advanced-complexity.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure cmdlets do PowerShell de Programador de referência](scheduler-powershell-reference.md)

 [Azure Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Limites de programador Azure, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)

 [Azure autenticação de saída de programador](scheduler-outbound-authentication.md)
