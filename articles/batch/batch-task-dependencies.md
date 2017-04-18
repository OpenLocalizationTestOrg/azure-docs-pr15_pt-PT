<properties
    pageTitle="No Azure lote as dependências de tarefa | Microsoft Azure"
    description="Criar tarefas que dependem da conclusão de outras tarefas para a transformação MapReduce estilo e dados grandes semelhantes das cargas de trabalho no lote Azure."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Dependências de tarefas no lote Azure

A funcionalidade de dependências de tarefas do Azure lote é uma boa opção se pretender do processo:

- Estilo de MapReduce das cargas de trabalho na nuvem.
- Tarefas podem ser expressa cujas tarefas de processamento de dados como um gráfico incluindo direccionado (Comprimentos).
- Qualquer outra tarefa na qual tarefas descendentes dependem de saída de montante tarefas.

Dependências de tarefas do lote permitem-lhe criar tarefas são agendadas para execução em nós de cluster apenas após a conclusão de uma ou mais tarefas com êxito. Por exemplo, pode criar uma tarefa que compõe cada frame de um filme 3D com tarefas separadas, paralelas. No final de tarefas – a "tarefa de impressão em série" – intercala as molduras compostas para o filme concluído apenas depois de todos os pacotes foi processados com êxito.

Pode criar tarefas que dependem das outras tarefas numa relação de uma ou de um-para-muitos. Pode criar dependência de intervalo onde uma tarefa depende da conclusão de um grupo de tarefas num intervalo específico de IDs de tarefa. Pode combinar nestes três cenários básicos para criar relações de muitos-para-muitos.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com o lote .NET

Neste artigo, discutimos como configurar dependências de tarefas utilizando o [Lote .NET] [ net_msdn] biblioteca. Em primeiro lugar mostramos-lhe como [Ativar dependência de tarefas](#enable-task-dependencies) no seu tarefas e, em seguida, demonstram como [Configurar uma tarefa com dependências](#create-dependent-tasks). Por fim, vamos discutir os [cenários de dependência](#dependency-scenarios) que lote de suporte.

## <a name="enable-task-dependencies"></a>Ativar dependências de tarefas

Para utilizar dependências de tarefas na sua aplicação lote, tem primeiro de comunicar ao serviço do lote que a tarefa utiliza dependências de tarefas. No lote .NET, ativá-lo no seu [CloudJob] [ net_cloudjob] definindo a sua [UsesTaskDependencies] [ net_usestaskdependencies] propriedade para `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No fragmento de código anterior, "batchClient" é uma instância da [BatchClient] [ net_batchclient] escolares.

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes

Para criar uma tarefa que é dependente da conclusão de uma ou mais tarefas, informe lote de que a tarefa "depende" as outras tarefas. No lote .NET, configure o [CloudTask][net_cloudtask]. [DependsOn] [net_dependson] propriedade com uma instância da [TaskDependencies] [ net_taskdependencies] classe:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este fragmento de código cria uma tarefa com o ID de "Flores" que vai ser agendado para ser executado num cluster nó apenas depois das tarefas com IDs de "Chuva" e "Sol" tem concluído com êxito.

 > [AZURE.NOTE] Considera-se uma tarefa devem ser preenchidas quando é no estado de **conclusão** e o **código de saída** é `0`. No lote .NET, isto significa que um [CloudTask][net_cloudtask]. [Estado] [net_taskstate] valor da propriedade de `Completed` e o CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [ExitCode] [net_exitcode] valor da propriedade é `0`.

## <a name="dependency-scenarios"></a>Cenários de dependência

Existem três cenários de dependência de tarefas básicas que pode utilizar no Azure lote: uma, um-para-muitos e ID da tarefa, o intervalo dependência. Estes podem ser combinados para fornecer um cenário quarto, muitos-para-muitos.

 Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo | |
 :-------------------: | ------------------- | -------------------
 [Uma](#one-to-one) | *taskB* depende *taskA* <p/> *taskB* não ser agendado para execução até *taskA* foi concluída com êxito | ![Diagrama: dependência de uma tarefa][1]
 [Um-para-muitos](#one-to-many) | *taskC* depende *taskA* e *taskB* <p/> *taskC* não ser agendado para execução até *taskA* e *taskB* tem concluído com êxito | ![Diagrama: dependência de tarefas de um-para-muitos][2]
 [Intervalo de ID da tarefa](#task-id-range) | *taskD* depende de um intervalo de tarefas <p/> *taskD* não ser agendado para execução até que as tarefas com IDs *1* e *10* tem concluído com êxito | ![Diagrama: Dependência de intervalo de id de tarefas][3]

>[AZURE.TIP] Pode criar relações de **muitos-para-muitos** , tal como onde tarefas C, D, E e F cada dependem de tarefas A e B. Isto é útil, por exemplo, em parallelized cenários pré-processamento onde as suas tarefas descendentes dependem o resultado de várias tarefas montante.

### <a name="one-to-one"></a>Uma

Para criar uma tarefa que tem uma dependência de conclusão de uma outra tarefa, tem de fornecer um ID de tarefa para a [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] método estático ao preencher o [DependsOn] [ net_dependson] propriedade de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um-para-muitos

Para criar uma tarefa que tem uma dependência de conclusão de várias tarefas, tem de fornecer uma coleção de tarefa IDs para o [TaskDependencies][net_taskdependencies]. [OnIds] [net_onids] método estático ao preencher o [DependsOn] [ net_dependson] propriedade de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervalo de ID da tarefa

Para criar uma tarefa que tem uma dependência a conclusão com êxito de um grupo de tarefas cuja posição IDs dentro de um intervalo, fornecer a primeira e última IDs de tarefas no intervalo para o [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] método estático ao preencher o [DependsOn] [ net_dependson] propriedade de [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Quando utilizar intervalos de ID da tarefa para a sua dependências, os IDs de tarefa de intervalo, *tem* ser representações de cadeia de valores de número inteiro. Para além disso, todas as tarefas no intervalo tem de concluir com êxito para a tarefa dependente ser agendado para execução.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Exemplo de código

O [TaskDependencies] [ github_taskdependencies] projecto de exemplo é um dos [exemplos de código do Azure lote] [ github_samples] no GitHub. Esta solução do Visual Studio 2015 demonstra como ativar dependência de tarefas de uma tarefa, criar tarefas que dependem das outras tarefas e executar essas tarefas num conjunto de nós de cluster.

## <a name="next-steps"></a>Próximos passos

### <a name="application-deployment"></a>Implementação da aplicação

A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do lote fornece uma forma fácil para ambas implementar e as aplicações que as suas tarefas executar na calcular nós de versão.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste

Consulte o artigo [instalar aplicações e dados de transição no lote calcular nós] [ forum_post] publicar no fórum Azure lote para uma descrição geral dos métodos diferentes para preparar o seu nós para executar as tarefas. Escrito por um dos membros da equipa Azure lote, esta mensagem é uma boa introdução sobre as diferentes formas de colocar ficheiros (incluindo aplicações e dados de entrada de tarefa) para os nós de cluster.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de uma"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um-para-muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de id de tarefas"
