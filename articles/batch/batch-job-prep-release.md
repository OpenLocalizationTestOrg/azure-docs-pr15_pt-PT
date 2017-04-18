<properties
    pageTitle="Tarefa de preparação e limpeza no lote | Microsoft Azure"
    description="Utilizar tarefas de nível do trabalho de preparação para minimizar a transferência de dados para nós de cluster Azure lote e, liberte tarefas de limpeza de nó na conclusão da tarefa."
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
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Executar tarefas de preparação e de conclusão da tarefa em nós de cluster lote Azure

 Uma rotina Azure exige frequentemente que alguma forma de configuração antes das suas tarefas são executadas e pós manutenção da tarefa quando as suas tarefas são concluídas. Poderá ter de transferir dados de entrada de tarefas comuns para os nós de cluster ou carregar os dados de saída de tarefa para o armazenamento do Windows Azure depois da tarefa estiver concluída. Pode utilizar tarefas de **preparação de tarefa** e **Libertar tarefa** para efetuar estas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são preparação de tarefa e solte tarefas?

Antes de executar tarefas de uma tarefa, a tarefa de preparação de trabalho é executado em todos os nós de cluster agendados para ser executado pelo menos uma tarefa. Assim que a tarefa está concluída, a tarefa de lançamento do projecto é executado em cada nó no conjunto de que executadas pelo menos uma tarefa. Tal como acontece com tarefas lote normal, pode especificar uma linha de comandos para ser chamado quando uma tarefa de preparação ou lançamento de trabalho é executada.

Tarefas de preparação e lançamento de projecto oferecem funcionalidades de tarefa do lote familiares como a transferência do ficheiro ([ficheiros de recursos][net_job_prep_resourcefiles]), elevados execução, variáveis de ambiente personalizado, duração de execução máximo, contagem de tentativas e tempo de retenção do ficheiro.

Nas secções seguintes, irá aprender a utilizar o [JobPreparationTask] [ net_job_prep] e [JobReleaseTask] [ net_job_release] classes que se encontram no [Lote .NET] [ api_net] biblioteca.

> [AZURE.TIP] Tarefas de preparação e lançamento de projecto são particularmente úteis na ambientes de "partilhado agrupamento", na qual um conjunto de nós de cluster persistir entre execuções do projecto e é utilizado pelo muitas tarefas.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando deve utilizar preparação de tarefa e solte tarefas

Preparação do trabalho e tarefas de lançamento do projecto são uma boa opção nas seguintes situações:

**Transferir dados de tarefas comuns**

Tarefas de lote requerem frequentemente um conjunto comum de dados como entrada para as tarefas do projecto. Por exemplo, em cálculos de análise de risco diária, dados de mercado serão específico do projecto, ainda comuns para todas as tarefas na tarefa. Estes dados de mercado, muitas vezes vários gigabytes de tamanho, deverão transferidos para cada nó cluster apenas uma vez para que qualquer tarefa que é executada no nó pode utilizá-lo. Utilize uma **tarefa de preparação de projecto** para transferir estes dados para cada nó antes da execução da tarefa de estiverem outras tarefas.

**Eliminar projecto e a tarefa de saída**

Num ambiente "partilhado conjunto", onde nós de cluster de um conjunto não estiverem desactivados entre as tarefas, poderá precisar de eliminar dados da tarefa entre execuções. Poderá ter de poupar espaço em disco em nós ou satisfazer as políticas de segurança da sua organização. Utilize uma **tarefa de lançamento do projecto** para eliminar os dados que foram transferidos por uma tarefa de preparação de tarefa ou gerados durante a execução da tarefa.

**Retenção de registo**

Poderá pretender manter uma cópia dos ficheiros de registo que as suas tarefas geram ou talvez falhar ficheiros de informação que podem ser gerados por aplicações falhadas. Utilizar uma **tarefa de lançamento do projecto** nestes casos para comprimir e carregar estes dados para um [Armazenamento do Windows Azure] [ azure_storage] conta.

>[AZURE.TIP] Outra forma de persistirem registos e outros projecto e a tarefa de saída de dados são utilizar a biblioteca de [Convenções de ficheiro do Azure lote](batch-task-output.md) .

## <a name="job-preparation-task"></a>Tarefa de preparação do projecto

Antes da execução das tarefas de uma tarefa, o lote executa a tarefa de preparação da tarefa em cada nó cluster que está agendada para executar uma tarefa. Por predefinição, o serviço de lote aguarda para a tarefa de preparação de projecto ser concluída antes de executar as tarefas agendadas executar no nó. No entanto, pode configurar o serviço não de esperar. Se o nó for reiniciado, a tarefa de preparação de trabalho é executado novamente, mas também pode desativar a este comportamento.

A tarefa de preparação de trabalho é executada apenas em nós são agendadas para executar uma tarefa. Isto impede a execução desnecessária de uma tarefa de preparação caso um nó não é atribuído uma tarefa. Isto pode ocorrer quando o número de tarefas para uma tarefa é menor que o número de nós num conjunto de dados. Quando é activado [execução da tarefa em simultâneo](batch-parallel-node-tasks.md) , que torna alguns nós idle se a contagem de tarefa for inferior às tarefas em simultâneo possíveis total também se aplica. Executando não a tarefa de preparação de projecto em nós idle, que pode gastou menos dinheiro em custos de transferência de dados.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] difere [CloudPool.StartTask] [ pool_starttask] nesse JobPreparationTask executa no início de cada tarefa, Considerando que a tarefa de início executa apenas quando um nó cluster ou pela primeira vez associa um agrupamento de ser reiniciado.

## <a name="job-release-task"></a>Tarefa de lançamento do projecto

Assim que uma tarefa é marcada como concluída, a tarefa de lançamento do projecto é executada em cada nó no conjunto de que executadas pelo menos uma tarefa. Marcar uma tarefa como concluída por emitir um pedido de terminar. Em seguida, o serviço de lote define o estado da tarefa para *Terminar*, termina todas as tarefas ativas ou em execução associadas à tarefa e executa a tarefa de lançamento de tarefa. A tarefa, em seguida, move o cursor para o estado de *conclusão* .

> [AZURE.NOTE] Eliminação de tarefa também executa a tarefa de lançamento do projecto. No entanto, se já foi terminada uma tarefa, libertar tarefa não é executada uma segunda vez se a tarefa é eliminada mais tarde.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tarefa de preparação e solte tarefas com o lote .NET

Para utilizar uma tarefa de preparação do projecto, atribua um [JobPreparationTask] [ net_job_prep] objeto para o [CloudJob.JobPreparationTask seu trabalho] [ net_job_prep_cloudjob] propriedade. Da mesma forma, iniciar uma [JobReleaseTask] [ net_job_release] e atribuí-lo a [CloudJob.JobReleaseTask sua tarefa] [ net_job_prep_cloudjob] propriedade para definir a tarefa lançamento.

Neste fragmento de código, `myBatchClient` é uma instância de [BatchClient][net_batch_client], e `myPool` é um conjunto existente dentro da conta do lote.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Como mencionado anteriormente, libertar tarefa é executada quando uma tarefa é terminada ou eliminada. Terminar uma tarefa com [JobOperations.TerminateJobAsync][net_job_terminate]. Eliminar uma tarefa com [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente, terminar ou eliminar uma tarefa quando as suas tarefas são concluídas ou quando atingiu um limite de tempo que definiu.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub

Para ver preparação de tarefa e solte tarefas em ação, consulte o artigo o [JobPrepRelease] [ job_prep_release_sample] projeto de exemplo no GitHub. Esta aplicação consola faz o seguinte:

1. Cria um conjunto de dados com dois nós "pequenas".
2. Cria uma tarefa com tarefa preparação, versão e tarefas padrão.
3. Executa a tarefa de preparação de tarefa, o que escreve pela primeira vez o ID de nó para um ficheiro de texto no directório de "partilhado" de um nó.
4. Executa uma tarefa em cada nó que escreve o seu ID da tarefa para o mesmo ficheiro de texto.
5. Assim que todas as tarefas sejam concluídas (ou o tempo limite é atingido), imprime o conteúdo do ficheiro de texto de cada nó na consola.
6. Quando a tarefa estiver concluída, executa a tarefa de lançamento de tarefa para eliminar o ficheiro a partir do nó.
6. Imprime os códigos de saída de preparação e lançamento de tarefas do projecto para cada nó no qual executado.
7. Execução pausas para permitir a confirmação de eliminação de tarefa e/ou de agrupamento.

Saída da aplicação de exemplo é semelhante ao seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] Devido a hora de criação e comece a variável de nós num novo conjunto de dados (alguns nós são está preparados para tarefas antes de outras pessoas), poderá ver saída diferente. Especificamente, uma vez que as tarefas concluir rapidamente, um de nós do conjunto pode executar todas as tarefas da tarefa. Se tal acontecer, vai notar que a tarefa de preparação e tarefas de edição não existam para o nó que executados sem tarefas.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecionar preparação de trabalho e tarefas de edição no portal do Azure

Quando executar a aplicação de exemplo, pode utilizar o [Azure portal] [ portal] para ver as propriedades das suas tarefas e a tarefa ou mesmo transferir o ficheiro de texto partilhada modificado pelos tarefas a tarefa.

A captura de ecrã abaixo mostra o **pá de tarefas de preparação** no portal do Azure após a execução da aplicação de exemplo. Navegar para as propriedades de *JobPrepReleaseSampleJob* depois das tarefas concluídas (mas antes de eliminar a tarefa e agrupamento) e clique em **tarefas de preparação** ou **tarefas de edição** para ver as suas propriedades.

![Propriedades de preparação da tarefa no portal do Azure][1]

## <a name="next-steps"></a>Próximos passos

### <a name="application-packages"></a>Pacotes de aplicações

Para além da tarefa de preparação do projecto, também pode utilizar a funcionalidade de [pacotes de aplicações](batch-application-packages.md) do lote preparar cluster nós a execução da tarefa. Esta funcionalidade é especialmente útil para implementar aplicações que não necessitam de executar um programa de instalação, aplicações que contêm muitos ficheiros (100 +) ou aplicações que requerem o controlo de versão estrito.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste

Este fórum MSDN fornece uma descrição geral de vários métodos de preparar os nós de tarefas em execução:

[Instalar aplicações e dados no lote de teste calcular nós][forum_post]

Escrito por um dos membros da equipa Azure lote, aborda várias técnicas que pode utilizar para implementar aplicações e dados para calcular nós.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
