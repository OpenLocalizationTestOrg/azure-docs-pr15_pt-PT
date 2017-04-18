<properties
    pageTitle="Maximizar a utilização de nó lote com tarefas paralelas | Microsoft Azure"
    description="Aumentar a eficiência e reduzir os custos utilizando menos nós de cluster e começar a trabalhar em simultâneo tarefas em cada nó num conjunto de dados lote Azure"
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

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Maximizar a utilização de recursos do Azure lote cluster com tarefas de nó em simultâneo

Ao executar mais do que uma tarefa em simultâneo em cada nó cluster no seu conjunto de Azure lote, pode maximizar a utilização de recursos num número de nós no conjunto mais pequeno. Para algumas das cargas de trabalho, isto pode resultar em horas de trabalho mais curtas e o custo inferior.

Enquanto alguns cenários de beneficiem das vantagens dos dedicar todos os recursos de um nó a uma tarefa, várias situações beneficiem das vantagens dos permitindo várias tarefas partilhar essas recursos:

 - **Minimizar a transferência de dados** quando as tarefas são conseguir partilhar dados. Neste cenário, pode reduzir substancialmente custos de transferência de dados ao copiar dados partilhada para um número mais pequeno de nós e executar tarefas em paralelo em cada nó. Especialmente se aplica se os dados sejam copiados para cada nó tem de ser transferidos entre regiões geográficas.

 - **Utilização de memória Maximizing** quando tarefas requerem uma grande quantidade de memória, mas apenas durante curtos períodos de tempo e, em alturas variáveis durante a execução. Pode empregam nós de cluster menos, mas maior, com mais memória para processar eficientemente essas picos. Estes nós teria várias tarefas executados em paralelo em cada nó, mas cada tarefa seria tirar partido de memória fixos de nós em alturas diferentes.

 - **Atenuantes limites números nó** quando é necessária dentro de um conjunto uma comunicação nó dependências entre. Atualmente, conjuntos de dados configurados para comunicações nó dependências entre estão limitados a 50 cluster nós. Se cada nó de um conjunto de conseguir executar tarefas em paralelo, um número maior das tarefas pode ser executado em simultâneo.

 - **Replicação de um local calcular cluster**, tal como quando mover primeiro um ambiente do cluster para Azure. Se a sua solução no local atual executa várias tarefas por cluster nó, pode aumentar o número máximo de tarefas de nó para aproximadamente mais atentamente que a configuração.

## <a name="example-scenario"></a>Cenário de exemplo

Como exemplo para ilustrar os benefícios da execução da tarefa paralela, vamos supor que a aplicação de tarefa tiver requisitos da memória e CPU dessa [padrão\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) nós forem suficientes. Mas, para poder concluir o trabalho no período de tempo necessário, 1.000 de nós são necessários.

Em vez de utilizar a norma\_nós D1 que tenham 1 core de CPU, é possível utilizar [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nós que tiverem 16 núcleos e permitir a execução de tarefa paralelo. Por conseguinte, *nós 16 horas menos* podem ser utilizadas – em vez de 1.000 nós, 63 apenas seria necessário. Para além disso, se os ficheiros de grande aplicação ou dados de referência são necessários para cada nó, duração da tarefa e eficiência são novamente melhorado uma vez que os dados são copiados para nós apenas 16.

## <a name="enable-parallel-task-execution"></a>Permitir a execução de paralelo de duas tarefas

Configurar nós de cluster de execução de tarefa paralelas ao nível de agrupamento. Com a biblioteca de lote .NET, defina o [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade quando cria um conjunto de dados. Se estiver a utilizar o lote REST API, defina o [maxTasksPerNode] [ rest_addpool] elemento no corpo do pedido durante a criação do conjunto de dados.

Azure lote permite-lhe definir máximas tarefas por nó até quatro vezes (4 x) o número de núcleos nó. Por exemplo, se o conjunto de está configurado connosco do tamanho "Grande" (quatro núcleos), em seguida, `maxTasksPerNode` poderão ser definidos 16. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, consulte o artigo [tamanhos dos serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md). Para mais informações sobre os limites de serviço, consulte o artigo [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md).

> [AZURE.TIP] Certifique-se de que ter em consideração o `maxTasksPerNode` valor quando constrói uma [fórmula autoscale] [ enable_autoscaling] para o conjunto. Por exemplo, uma fórmula que avalia `$RunningTasks` ser significativamente afectado por um aumento tarefas por nó. Para obter mais informações, consulte a [automaticamente escala calcular nós num conjunto de dados lote Azure](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Distribuição das tarefas

Quando os nós de cluster de um conjunto de dados podem executar tarefas em simultâneo, é importante especificar como pretende que as tarefas que deve ser distribuído ao longo de nós do conjunto.

Ao utilizar o [CloudPool.TaskSchedulingPolicy] [ task_schedule] propriedade, pode especificar que tarefas devem ser atribuídas uniformemente em todos os nós no conjunto de ("propagação"). Ou pode especificar que devem ser atribuídas tarefas maior número possível para cada nó antes de tarefas forem atribuídas para outro nó no conjunto de ("embalagem").

Como um exemplo de como esta funcionalidade é útil, considere o conjunto de [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nós (no exemplo acima) que está configurado com uma [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valor de 16. Se o [CloudPool.TaskSchedulingPolicy] [ task_schedule] está configurado com uma [ComputeNodeFillType] [ fill_type] do *pacote*,-maximizar a utilização da todos os 16 tarolos de cada nó e permitir que um [conjunto de autoscaling](batch-automatic-scaling.md) eliminar não utilizadas nós do conjunto de (nós sem qualquer tarefas atribuídas). Isto minimiza a utilização de recursos e guarda dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET batch

Este [Lote .NET] [ api_net] fragmento de código API mostra um pedido para criar um conjunto de dados que contém quatro nós grandes com um máximo de quatro tarefas por nó. Especifica uma política que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó no conjunto do agendamento de tarefas. Para obter mais informações sobre como adicionar conjuntos de dados ao utilizar a API do .NET lote, consulte o artigo [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemplo de resto batch

Este [Lote resto] [ api_rest] fragmento de API mostra um pedido para criar um conjunto de dados que contém dois nós grandes com um máximo de quatro tarefas por nó. Para mais informações sobre como adicionar conjuntos de dados utilizando a API REST, consulte o artigo [Adicionar um agrupamento de uma conta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] Pode definir a `maxTasksPerNode` elemento e [MaxTasksPerComputeNode] [ maxtasks_net] propriedade apenas a hora de criação de agrupamento. Não pode ser modificadas depois de um conjunto de dados já foi criado.

## <a name="code-sample"></a>Exemplo de código

O [ParallelNodeTasks] [ parallel_tasks_sample] projeto no GitHub ilustra a utilização do [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade.

Esta aplicação de consola do c# utiliza o [Lote .NET] [ api_net] biblioteca para criar um conjunto de dados com um ou mais nós de cluster. Um número configurável de tarefas-executa em nós para simular carga variável. Resultado da aplicação Especifica que nós executada cada tarefa. A aplicação também fornece um resumo dos parâmetros de tarefa e duração. Parte da resumo da saída de duas será executado diferente da aplicação de exemplo é apresentado abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução da aplicação de exemplo mostra que com um único nó no conjunto de e as configurações predefinidas de uma tarefa por cada nó, a duração da tarefa é mais de 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Executar o segundo das apresentações exemplo uma diminuição significativa no duração da tarefa. Isto acontece porque o agrupamento de foi configurado com quatro tarefas por nó, que permite a execução de tarefa paralelas concluir a tarefa de quase um trimestre do tempo.

> [AZURE.NOTE] As durações de tarefa nos resumos acima não inclui a hora de criação de agrupamento. Cada uma das tarefas acima foi submetida para conjuntos de dados criados anteriormente cujos nós de cluster foram no seu estado *inactivo* ao tempo de submissão.

## <a name="next-steps"></a>Próximos passos

### <a name="batch-explorer-heat-map"></a>Mapa térmico do lote Explorer

O [Azure lote Explorer][batch_explorer], uma das [aplicações de exemplo]Azure lote[github_samples], contém uma funcionalidade de *Mapa térmico* que fornece a visualização de execução de tarefa. Quando estiver a executar o [ParallelTasks] [ parallel_tasks_sample] aplicação de exemplo, pode utilizar a funcionalidade de mapa térmico para visualizar facilmente a execução das tarefas paralelas em cada nó.

![Mapa térmico do lote Explorer][1]

*Mapa térmico Explorer de lote que mostra um conjunto de quatro nós, com cada nó atualmente em execução quatro tarefas*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
