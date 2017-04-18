<properties
    pageTitle="Consultas de Otimização da lista no Azure lote | Microsoft Azure"
    description="Aumentar o desempenho ao filtrar as suas consultas quando pedir informações sobre recursos lote como conjuntos de dados, tarefas, tarefas e calcular nós."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="query-the-azure-batch-service-efficiently"></a>Consultar o serviço de Azure lote eficazmente

Aqui vai aprender aumentar o desempenho da sua aplicação de Azure lote reduzindo a quantidade de dados que são devolvidos pelo serviço quando tarefas, tarefas, de consulta e calcular nós com o [Lote .NET] [ api_net] biblioteca.

Quase todas as aplicações do lote tem de efetuar algum tipo de operação de monitorização ou outra consulta o serviço de lote, muitas vezes a intervalos regulares. Por exemplo, para determinar se existem quaisquer tarefas em fila restante numa tarefa, tem de obter dados de cada tarefa na tarefa. Para determinar o estado de nós no seu conjunto, terá de obter dados em todos os nós no conjunto. Este artigo explica como executar essas consultas na forma mais eficiente.

## <a name="meet-the-detaillevel"></a>Cumprir a DetailLevel

Uma aplicação do lote de produção, podem numerar entidades como tarefas, tarefas e nós de cluster no milhares. Quando pedir informações sobre estes recursos, uma potencialmente grande quantidade de dados tem "cross o fio" de serviço lote à aplicação em cada consulta. Ao limitar o número de itens e tipo de informação que é devolvido por uma consulta, pode aumentar a velocidade das suas consultas e, por conseguinte, o desempenho da sua aplicação.

Este [Lote .NET] [ api_net] fragmento de código API listas de *cada* tarefa que está associada a uma tarefa, juntamente com *todas as* propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

No entanto, pode executar uma consulta de lista muito mais eficiente, ao aplicar um "nível de detalhe" a sua consulta. Pode fazê-lo, fornecendo um [ODATADetailLevel] [ odata] objeto para o [JobOperations.ListTasks] [ net_list_tasks] método. Este fragmento devolve apenas o ID, linha de comandos e propriedades de informações de nó cluster de tarefas concluídas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Neste cenário de exemplo, se existirem milhares de tarefas no projecto, os resultados da consulta de segundo serão normalmente devolvidos muito mais rápido que o primeiro. Obter mais informações sobre como utilizar ODATADetailLevel quando a lista de itens com a API do .NET lote são incluída [abaixo](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> Recomendamos vivamente que lhe *sempre* forneça um objeto de ODATADetailLevel às suas chamadas de lista .NET API para garantir a eficiência máxima e o desempenho da sua aplicação. Ao especificar um nível de detalhe, pode ajudar a diminuir os tempos de resposta de serviço do lote, melhorar a utilização da rede e minimizar a utilização de memória, aplicações de cliente.

## <a name="filter-select-and-expand"></a>Filtrar, selecione e expandir

O [Lote .NET] [ api_net] e [Coloque o PONTEIRO lote] [ api_rest] APIs fornecem a capacidade de reduzir tanto o número de itens que são devolvidos numa lista, bem como a quantidade de informações que são devolvidas para cada um. Faça-o ao especificar **filtro**, **Selecione**e **Expandir cadeias** quando executar consultas de lista.

### <a name="filter"></a>Filtro
A cadeia de filtro é uma expressão que reduz o número de itens que são devolvidos. Por exemplo, as tarefas em execução para uma tarefa da lista ou da lista apenas os nós de cluster que estão prontos executar as tarefas.

- A cadeia de filtro é composta por uma ou mais expressões, com uma expressão que é constituído por um nome da propriedade, o operador e o valor. As propriedades que podem ser especificadas são específicas para cada tipo de entidade uma consulta, tal como são os operadores que são suportados para cada propriedade.
- Podem ser combinados múltiplas expressões ao utilizar os operadores lógicos `and` e `or`.
- Este exemplo filtrar listas de cadeia só a execução "compor" tarefas: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecione
A cadeia selecione limita os valores de propriedade que são devolvidos para cada item. Especificar uma lista de nomes de propriedade e apenas os valores de propriedade são devolvidos para os itens nos resultados da consulta.

- A cadeia select é composta por uma lista de nomes de propriedades de separados por vírgulas. Pode especificar qualquer uma das propriedades para o tipo de entidade que está a consultar.
- Este exemplo de uma cadeia selecione Especifica que devem ser devolvidos apenas três valores de propriedade para cada tarefa: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expandir
A cadeia de expandir reduz o número de chamadas da API que são necessários para obter algumas informações. Quando utiliza uma cadeia de expansão, podem ser obtidas obter mais informações sobre cada item com uma chamada de API única. Em vez de obter primeiro a lista de entidades, em seguida, pedir informações para cada item na lista, utilize uma cadeia de expansão para obter as mesmas informações numa única chamada API. Menos chamadas à API significa que um melhor desempenho.

- Semelhante a cadeia select, os controlos de cadeia expandir se determinados dados são incluídos nos resultados da consulta de lista.
- A cadeia de expandir só é suportada quando for utilizado na lista de tarefas, agendas de tarefas, tarefas e conjuntos de dados. Atualmente, só suporta informações estatísticas.
- Quando todas as propriedades são necessárias e não selecione especificada uma cadeia é, a cadeia de expandir *deve* ser utilizados para obter informações de estatísticas. Se uma cadeia select é utilizada para obter um subconjunto de propriedades, em seguida, `stats` pode ser especificado na cadeia de selecionar e, a cadeia de expandir não necessitam de ser especificado.
- Este exemplo expandir cadeia Especifica que informações estatísticas devem ser devolvidas para cada item na lista: `stats`.

> [AZURE.NOTE] Quando construir qualquer um dos tipos de cadeia de três consulta (filtrar, selecione e expandir), tem de garantir que os nomes de propriedade e as maiúsculas/minúsculas correspondem dos seus homólogos de elemento REST API. Por exemplo, quando trabalha com a classe de.NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , tem de especificar **Estado** em vez de **Estado**, apesar da propriedade .NET é [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte as tabelas abaixo para mapeamentos de propriedades entre o .NET e os REST APIs.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras para filtrar, selecione e expandir cadeias

- Nomes de propriedades em filtro, selecione e expandir cadeias deverá aparecer, tal como do [Resto do lote] [ api_rest] API – mesmo quando utilizar o [Lote .NET] [ api_net] ou das outras SDK do lote.
- Todos os nomes de propriedade estão entre maiúsculas e minúsculas, mas os valores de propriedade são maiúsculas e minúsculas.
- Data/hora cadeias podem ser um de dois formatos e tem de ser precedidas com `DateTime`.

  - Exemplo de formato W3C DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemplo de formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Booleanas as cadeias estão quer `true` ou `false`.
- Se uma propriedade inválido ou um operador for especificado, um `400 (Bad Request)` erro resultará.

## <a name="efficient-querying-in-batch-net"></a>Consultar no lote .NET eficiente

Dentro do [Lote .NET] [ api_net] API, o [ODATADetailLevel] [ odata] classe é utilizada para fornecer as filtro, selecione e expandir cadeias para operações de lista. A classe de ODataDetailLevel tem três propriedades de cadeia pública que podem ser especificadas no construtor ou definir diretamente no objeto. Em seguida, ser efetuada com o objeto ODataDetailLevel como um parâmetro para várias operações de lista como [ListPools][net_list_pools], [ListJobs][net_list_jobs]e [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limitar o número de itens que são devolvidos.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Especifique os valores de propriedade são devolvidos com cada item.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Obter dados de todos os itens numa única API chamada em vez de chamadas separadas para cada item.

O fragmento de código seguinte utiliza a API do .NET lote para eficazmente consultar o serviço de lote para as estatísticas de um conjunto específico de conjuntos de dados. Neste cenário, o utilizador de lote tem agrupamentos de teste e de produção. O conjunto de teste IDs são o prefixo "teste" e o conjunto de produção IDs são o prefixo "produto". No fragmento *myBatchClient* é uma instância da classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inicializada correctamente.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Uma instância da [ODATADetailLevel] [ odata] que está configurado com selecione e expandir cláusulas também podem transmitir à adequado métodos de obter, tal como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), para limitar a quantidade de dados que são devolvidos.

## <a name="batch-rest-to-net-api-mappings"></a>Lote resto para mapeamentos de .NET API

Nomes de propriedades no filtro, selecione e expandir cadeias *tem* refletir as suas homólogos REST API, tanto em como nome em maiúsculas/minúsculas. As tabelas abaixo fornecem mapeamentos entre os homólogos .NET e REST API.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para as cadeias de filtro

- **Métodos de lista .NET**: cada um dos métodos .NET API nesta coluna aceita uma [ODATADetailLevel] [ odata] objeto, como um parâmetro.
- **Pedidos de lista REST**: página de cada REST API ligada desta coluna contém uma tabela que especifica as propriedades e operações que são permitidas em cadeias de *filtro* . Irá utilizar estes nomes de propriedades e operações quando construir um [ODATADetailLevel.FilterClause] [ odata_filter] cadeia.

| Métodos de lista .NET | Pedidos de lista REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Listar os certificados numa conta][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Listar os ficheiros associados a uma tarefa][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [O estado da tarefa preparação e tarefas de edição de projecto para uma tarefa da lista][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Listar as tarefas numa conta][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Listar os ficheiros num nó][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Listar as tarefas associadas uma tarefa][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Listar as agendas de trabalho numa conta][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Listar as tarefas associadas um agendamento da tarefa][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Listar os nós de cluster de um conjunto de dados][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Listar os conjuntos de uma conta][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Mapeamentos de para selecionados cadeias

- **Tipos de lote .NET**: tipos de lote .NET API.
- **REST API entidades**: cada página nesta coluna contém uma ou mais tabelas que os nomes das propriedades de REST API para o tipo de lista. Estes nomes de propriedade são utilizadas quando construir *Selecione* cadeias. Irá utilizar estes mesmos nomes de propriedade quando construir um [ODATADetailLevel.SelectClause] [ odata_select] cadeia.

| Tipos de .NET batch | Entidades de REST API |
|---|---|
| [Certificado][net_cert] | [Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] | [Obter informações sobre uma tarefa][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obter informações sobre um agendamento da tarefa][rest_get_schedule] |
| [ComputeNode][net_node] | [Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] | [Obter informações sobre um conjunto de dados][rest_get_pool] |
| [CloudTask][net_task] | [Obter informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de filtro

Quando constrói uma cadeia de filtro para [ODATADetailLevel.FilterClause][odata_filter], consulte a tabela acima em "Mapeamentos para as cadeias de filtro" a página de documentação localizar REST API que corresponde à operação de lista que pretende executar. Irá encontrar as propriedades de filtráveis e os respetivos operadores suportados na primeira tabela MúltiplasLinhas nessa página. Se pretender obter todas as tarefas cujo código de saída não estava a zero, por exemplo, esta linha na [lista de tarefas associadas a uma tarefa] [ rest_list_tasks] Especifica a cadeia da propriedade aplicável e operadores permitidos:

| Propriedade | Operações permitidas | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Por conseguinte, a cadeia de filtro para indicar a todas as tarefas com um código de saída diferente de zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma cadeia de selecção

Para construir [ODATADetailLevel.SelectClause][odata_select], consulte a tabela acima em "Mapeamentos para as cadeias selecionados" e navegue para a página de REST API que corresponde ao tipo de entidade que estão a lista. Irá encontrar as propriedades de selecionável e os respetivos operadores suportados na primeira tabela MúltiplasLinhas nessa página. Se pretender obter apenas o ID e a linha de comandos para cada tarefa numa lista, por exemplo, irá encontrar estas linhas na tabela aplicável [obter informações sobre uma tarefa][rest_get_task]:

| Propriedade | Tipo | Notas |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

A cadeia de selecionar para incluir apenas o ID e a linha de comandos com cada tarefa listada, em seguida, seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código

### <a name="efficient-list-queries-code-sample"></a>Exemplo de código de consultas de lista eficiente

Dar saída a [EfficientListQueries] [ efficient_query_sample] projeto de exemplo no GitHub para ver a consulta de lista como eficiente pode afetar o desempenho numa aplicação. Esta aplicação de consola do c# cria e adiciona um grande número de tarefas para uma tarefa. Em seguida, faz várias chamadas para o [JobOperations.ListTasks] [ net_list_tasks] método e fases [ODATADetailLevel] [ odata] objetos que estão configurados com valores de propriedade diferentes para variar a quantidade de dados para ser devolvido. Gera saída semelhante ao seguinte:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Conforme o tempo decorrido, pode reduzir substancialmente tempos de resposta de consulta ao limitar as propriedades e o número de itens que são devolvidos. Pode encontrar este e outros projetos de exemplo as [amostras de lote azure] [ github_samples] repositório no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Exemplo de biblioteca e código BatchMetrics

Para além do exemplo de código EfficientListQueries acima, pode encontrar o [BatchMetrics] [ batch_metrics] projeto nas [amostras de lote azure] [ github_samples] GitHub repositório. O projeto de exemplo BatchMetrics demonstra como eficientemente monitorizar o progresso da tarefa de Azure lote utilizar a API do lote.

O [BatchMetrics] [ batch_metrics] exemplo inclui um projeto de biblioteca de classe .NET que pode incorporar na suas própria projetos e um programa de linha de comandos simple para exercer e demonstram a utilização da biblioteca.

A aplicação de exemplo do projeto demonstra as seguintes operações:

1. Selecionar atributos específicos para poder transferir apenas as propriedades que necessita
2. Filtrar os tempos de transição de estado para poder transferir apenas alterações desde a última consulta

Por exemplo, o método seguinte aparece na biblioteca de BatchMetrics. Devolve uma ODATADetailLevel que especifica que apenas o `id` e `state` devem ser obtidas propriedades para as entidades que são avisadas. Especifica que também apenas entidades cujo estado foi alterado desde que o especificado `DateTime` parâmetro deve ser devolvido.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Próximos passos

### <a name="parallel-node-tasks"></a>Nó paralelo de duas tarefas

[Maximizar o Azure lote calcular utilização de recursos com tarefas em simultâneo nó](batch-parallel-node-tasks.md) é outro artigo relacionado com o desempenho da aplicação lote. Alguns tipos de cargas de trabalho beneficiar paralelo de duas tarefas em execução no maior – mas menos - calcular nós. Consulte o artigo o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre como um cenário.

### <a name="batch-forum"></a>Fórum do lote

[Fórum do Azure lote] [ forum] no MSDN é um ótimo local para discutir lote e perguntas sobre o serviço. Cabeça no sobre para mensagens "autocolantes" úteis e publique as suas perguntas que possam ocorrer enquanto que cria as soluções do lote.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
