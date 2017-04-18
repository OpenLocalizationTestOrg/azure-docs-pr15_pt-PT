<properties
    pageTitle="Executar aplicações de MPI no Azure lote com tarefas com várias instâncias | Microsoft Azure"
    description="Saiba como executar as aplicações de mensagem prisma Interface (MPI) com o tipo de tarefa com várias instâncias no lote Azure."
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
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Utilize as instância com várias tarefas para executar as aplicações de mensagem prisma Interface (MPI) no lote Azure

Tarefas com várias instâncias permitem-lhe executar uma tarefa do Azure lote em vários nós de cluster em simultâneo. Estas tarefas ativar cenários como mensagem prisma Interface (MPI) aplicações no lote de computação de alto desempenho. Neste artigo, aprende a executar tarefas com várias instâncias utilizando o [Lote .NET] [ api_net] biblioteca.

>[AZURE.NOTE] Enquanto os exemplos neste artigo concentrar-em lote .NET, MS-MPI e Windows calcular nós, os conceitos de tarefa com várias instâncias abordados aqui são aplicáveis para outras plataformas e tecnologias (Python e Intel MPI em nós Linux, por exemplo).

## <a name="multi-instance-task-overview"></a>Descrição geral de tarefa com várias instâncias

No lote, cada tarefa é normalmente executado num nó cluster única – submeter várias tarefas para uma tarefa e, o serviço do lote agenda cada tarefa para execução num nó. No entanto, através da configuração de uma tarefa **definições da instância com várias**, informe lote em vez disso, criar uma tarefa principal e várias subtarefas, em seguida, são executadas em vários nós.

![Descrição geral de tarefa com várias instâncias][1]

Quando submeter uma tarefa com múltiplas instâncias as definições para uma tarefa, o lote executa vários passos exclusivos para tarefas com várias instâncias:

1. O serviço do lote cria um **principal** e várias **subtarefas** com base nas definições de com várias instâncias. O número total de tarefas (primárias plus todas as subtarefas) corresponde ao número de **instâncias** (cluster nós) especificado nas definições com várias instâncias.
1. Lote atribui um de nós cluster como a **principal**e agenda a tarefa principal para executar no modelo global de. Agenda-as subtarefas executar no resto de nós cluster atribuídos à tarefa com várias instâncias, uma subtarefa por nó.
1. A página principal e todas as subtarefas transfira quaisquer **ficheiros comuns do recurso** especificado nas definições com várias instâncias.
1. Depois do recurso comuns ficheiros foram transferidos, a página principal e subtarefas executar o **comando coordená-lo** especificado nas definições com várias instâncias. O comando coordená-lo é normalmente utilizado para preparar nós para executar a tarefa. Isto pode incluir iniciar os serviços de fundo (tal como [Microsoft MPI][msmpi_msdn]do `smpd.exe`) e que os nós estão prontos para processar as mensagens de nó dependências entre a verificar.
1. A tarefa principal executa o **comando de aplicação** no nó mestra *depois do* que comando coordená-lo ter sido concluído com êxito, a página principal e todas as subtarefas. O comando de aplicação é a linha de comandos da tarefa com várias instâncias propriamente dito e é executado apenas pela tarefa principal. Num [MS MPI][msmpi_msdn]-com base solução, este é sempre que executar a sua aplicação MPI com permissão para utilizar `mpiexec.exe`.

> [AZURE.NOTE] Apesar de ser funcional distinto, a tarefa"com várias instâncias" não é um tipo de tarefa exclusivos, como a [tarefa de início] [ net_starttask] ou [JobPreparationTask][net_jobprep]. A tarefa com várias instâncias é simplesmente uma tarefa de lote padrão ([CloudTask] [ net_task] no lote .NET) cujas definições de instância com várias tenham sido configuradas. Neste artigo, iremos referem-se a esta como a **tarefa com várias instâncias**.

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas com várias instâncias

Tarefas com várias instâncias necessitam de um conjunto de dados com a **comunicação de nó dependências entre ativado**e com **execução tarefa simultâneo desativada**. Se tentar executar uma tarefa com várias instâncias num conjunto de dados com a comunicação vai desativada ou com um valor de *maxTasksPerNode* maior do que 1, a tarefa nunca é agendada – permanece indefinidamente no estado "activo". Este fragmento de código mostra a criação de um conjunto de utilizando a biblioteca de lote .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Para além disso, as tarefas com várias instâncias podem executar *apenas* em nós nos **conjuntos de dados criados após 14 de Dezembro de 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Utilizar uma tarefa de início para instalar MPI

Para executar as aplicações de MPI com uma tarefa com várias instâncias, tem primeiro de instalar uma implementação MPI (MS MPI ou Intel MPI, por exemplo) em nós cluster no conjunto. Esta é uma boa altura para utilizar uma [tarefa de início][net_starttask], que é executada sempre que um nó associa um conjunto de dados ou for reiniciado. Este fragmento de código cria uma tarefa de início que especifica o pacote de configuração do MS MPI como um [ficheiro de recursos][net_resourcefile]. Linha de comandos da tarefa iniciar é executada após o ficheiro de recursos é transferido para o nó. Neste caso, a linha de comandos executa uma instalação automática da MS MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Acesso à memória direta remoto (RDMA)

Quando escolher um [tamanho RDMA noutros](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) como A9 para os nós de cluster no seu conjunto de lote, a sua aplicação MPI pode tirar partido da rede de acesso (RDMA) de alto desempenho, baixa latência memória direta remoto do Azure.

Procure os tamanhos especificados como "RDMA capaz" nos seguintes artigos:

* **CloudServiceConfiguration** conjuntos de dados

  * [Tamanhos dos serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md) (Apenas para o Windows)

* **VirtualMachineConfiguration** conjuntos de dados

  * [Tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Para tirar partido das RDMA no [Linux calcular nós](batch-linux-nodes.md), tem de utilizar **Intel MPI** em nós. Para mais informações sobre CloudServiceConfiguration e VirtualMachineConfiguration conjuntos de dados, consulte a secção de [agrupamento](batch-api-basics.md#Pool) da descrição geral do lote funcionalidade.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Criar uma tarefa com várias instâncias com lote .NET

Agora que recomendamos tenha abrangidos os requisitos de agrupamento e a instalação do pacote MPI, vamos criar a tarefa com várias instâncias. Neste fragmento, criamos uma padrão [CloudTask][net_task], em seguida, configure o seu [MultiInstanceSettings] [ net_multiinstance_prop] propriedade. Como mencionado anteriormente, a tarefa com várias instâncias não é um tipo de tarefa distintos, mas uma tarefa lote padrão configuradas com instância com várias definições.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Principais tarefas e subtarefas

Quando cria as definições de com várias instâncias de uma tarefa, pode especificar o número de nós de cluster que está a executar a tarefa. Quando submeter a tarefa a uma tarefa, o serviço de lote cria uma tarefa **principal** e suficiente **subtarefas** que em conjunto corresponde ao número de nós que especificou.

Estas tarefas são atribuídas um id de número inteiro no intervalo de 0 a *numberOfInstances* - 1. A tarefa com id 0 é a tarefa principal e todos os outros ids são subtarefas. Por exemplo, se criar as seguintes definições com várias instâncias de uma tarefa, a tarefa principal teria um id de 0 e as subtarefas teria de ids de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó principal

Quando submeter uma tarefa com várias instâncias, o serviço do lote atribui um de nós cluster como o nó do "principal" e agenda a tarefa principal para executar no nó principal. As subtarefas estão agendadas para executar no resto de nós atribuídos à tarefa com várias instâncias.

## <a name="coordination-command"></a>Comando coordená-lo

O **comando coordená-lo** é executado por tanto a página principal e subtarefas.

Está a bloquear a invocação do comando coordená-lo – lote não executar o comando de aplicação até o comando coordená-lo tem devolvido com êxito para todas as subtarefas. O comando coordená-lo deve, por conseguinte, começar a quaisquer serviços de fundo necessários, certifique-se de que estes provêm prontos a utilizar e, em seguida, saia. Por exemplo, este comando coordená-lo para obter uma solução com a versão do MS MPI 7 inicia o serviço SMPD no nó, em seguida, sai:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Tenha em atenção a utilização de `start` neste comando coordená-lo. Isto é necessário porque o `smpd.exe` aplicação não devolver imediatamente após a execução. Sem a utilização de [Iniciar] [ cmd_start] comando, este comando coordená-lo não deve devolver e, consequentemente, iria bloquear o comando de aplicação a execução.

## <a name="application-command"></a>Comando de aplicação

Assim que a tarefa principal e todas as subtarefas tem terminado de executar o comando coordená-lo, linha de comandos da tarefa com várias instâncias é executada pela tarefa principal *apenas*. Vamos telefonar para esta o **comando de aplicação** para distingui-lo a partir do comando coordená-lo.

Para as aplicações do MS MPI, utilize o comando de aplicação para executar a sua aplicação com capacidade de MPI com `mpiexec.exe`. Por exemplo, eis um comando de aplicação para uma solução utilizando MS-MPI versão 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Uma vez que MS-MPI `mpiexec.exe` utiliza o `CCP_NODES` variável por predefinição (consulte [variáveis de ambiente](#environment-variables)) o exemplo acima da linha de comandos aplicação exclui-lo.

## <a name="environment-variables"></a>Variáveis de ambiente

Lote cria várias [variáveis de ambiente] [ msdn_env_var] específicas para tarefas com várias instâncias em nós cluster atribuídos a uma tarefa com várias instâncias. As linhas de comando coordená-lo e a aplicação, pode fazer referência estas variáveis de ambiente, como podem os scripts e programas que executam.

As seguintes variáveis de ambiente são criadas pelo serviço lote para utilização por tarefas com várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter detalhes completos sobre estes e outras lote cluster nó ambiente variáveis, incluindo os seus conteúdos e visibilidade, consulte o artigo [Calcular variáveis de ambiente de nó][msdn_env_var].

>[AZURE.TIP] O exemplo de código do lote Linux MPI contém um exemplo de como podem ser utilizadas várias estas variáveis de ambiente. [Coordená-lo cmd] [ coord_cmd_example] festa script transferências aplicação comuns e os ficheiros do armazenamento do Windows Azure de entrada, permite uma partilha de sistema de ficheiros de rede (NFS) no nó principal e configura os outros nós atribuídos à tarefa com várias instâncias como clientes NFS.

## <a name="resource-files"></a>Ficheiros de recursos

Existem dois conjuntos de ficheiros de recursos ter em consideração para tarefas com várias instâncias: **ficheiros comuns do recurso** que *todas as* tarefas transferir (ambos os principal e subtarefas) e os **ficheiros de recursos** especificada para a instância com várias tarefas propriamente dito, qual a tarefa *apenas a página principal* transferências.

Pode especificar um ou mais **ficheiros de recursos comuns** nas definições para uma tarefa com várias instâncias. Estes ficheiros comuns do recurso são transferidos a partir [Do armazenamento Azure](./../storage/storage-introduction.md) para **directório partilhado tarefa** pela página principal cada nó e todas as subtarefas. Pode aceder ao directório partilhado da tarefa a partir de linhas de comandos da aplicação e coordená-lo utilizando o `AZ_BATCH_TASK_SHARED_DIR` variável de ambiente. O `AZ_BATCH_TASK_SHARED_DIR` caminho é idêntico em todos os nós atribuídos à tarefa com várias instâncias, pelo que pode partilhar um comando coordená-lo único entre a página principal e todas as subtarefas. Lote não "partilhar" diretório no sentido de acesso remoto, mas pode utilizá-la como uma montagem ou ponto como mencionado anteriormente a ponta no variáveis de ambiente de partilhar.

Ficheiros de recursos que especificar para a tarefa com várias instâncias propriamente dito são transferidos para o directório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR`, por predefinição. Tal como mencionado, contrariamente ao ficheiros comuns do recurso, apenas a tarefa principal transfere especificados para a tarefa com várias instâncias propriamente dito os ficheiros de recursos.

> [AZURE.IMPORTANT] Utilizar sempre as variáveis de ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` para fazer referência a estes directórios na sua linha de comandos. Não tente construir os caminhos manualmente.

## <a name="task-lifetime"></a>Duração da tarefa

A duração da tarefa primária controla a duração da tarefa com várias instâncias inteira. Quando sai a página principal, todas as subtarefas são terminadas. O código de saída do computador primário é o código de saída da tarefa e, por isso, é utilizado para determinar o sucesso ou o fracasso da tarefa para fins de repetir.

Se qualquer as subtarefas falhar, sair com um código de retorno diferente de zero, por exemplo, a tarefa com várias instâncias inteira falha. A tarefa com várias instâncias, em seguida, é terminada e repetido, é possível por excesso para o seu limite de repetir.

Quando elimina uma tarefa com várias instâncias, a página principal e todas as subtarefas também são eliminadas pelo serviço lote. Todos os subtarefa diretórios e os respetivos ficheiros são eliminados dos nós de cluster, tal como para uma tarefa padrão.

[TaskConstraints] [ net_taskconstraints] para uma tarefa com várias instâncias, tal como o [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]e [RetentionTime] [ net_taskconstraint_retention] propriedades, são respeitadas como estão para uma tarefa padrão e, aplicam-se para a página principal e todas as subtarefas. No entanto, se mudar a [RetentionTime] [ net_taskconstraint_retention] propriedade depois de adicionar a tarefa com várias instâncias para a tarefa, esta alteração é aplicada apenas para a tarefa principal. Todas as subtarefas continuam a utilizar o original [RetentionTime][net_taskconstraint_retention].

Lista de tarefas recentes um nó cluster reflete o id de uma subtarefa se a tarefa recente foi parte de uma tarefa com várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obter informações sobre subtarefas

Para obter informações sobre subtarefas utilizando a biblioteca de lote .NET, contacte o [CloudTask.ListSubtasks] [ net_task_listsubtasks] método. Este método devolve informações sobre todas as subtarefas e informações sobre o nó cluster que executadas as tarefas. Estas informações, pode determinar diretório de raiz de cada subtarefa, o id do conjunto, o estado atual, código de saída e muito mais. Pode utilizar estas informações em conjunto com o [PoolOperations.GetNodeFile] [ poolops_getnodefile] método para obter ficheiros da subtarefa. Tenha em atenção que este método não devolver informações da tarefa principal (id 0).

> [AZURE.NOTE] Salvo indicação em contrário, lote .NET métodos que funcionam na instância com várias [CloudTask] [ net_task] propriamente dito aplicam-se *apenas* à tarefa principal. Por exemplo, quando ligar para o [CloudTask.ListNodeFiles] [ net_task_listnodefiles] método numa tarefa com várias instâncias, apenas os ficheiros da tarefa primária são devolvidos.

O fragmento de código seguinte mostra como obter as informações de subtarefa, bem como pedir conteúdo do ficheiro a partir de nós no qual executado.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Exemplo de código

O [MultiInstanceTasks] [ github_mpi] exemplo de código no GitHub demonstra como utilizar uma tarefa com várias instâncias para executar uma [MS MPI] [ msmpi_msdn] aplicação no lote calcular nós. Siga os passos de [preparação](#preparation) e [execução](#execution) para executar o exemplo.

### <a name="preparation"></a>Preparação

1. Siga os dois primeiros passos no [artigo como compilar e executar um programa do MS MPI simple][msmpi_howto]. Isto satisfaz prerequesites para o passo seguinte.
1. Criar uma versão de *lançamento* da [MPIHelloWorld] [ helloworld_proj] programa MPI de exemplo. Este é o programa que será executado em nós de cluster pela tarefa com várias instâncias.
1. Criar um ficheiro zip que contém `MPIHelloWorld.exe` (que pode compilado passo 2) e `MSMpiSetup.exe` (que transferiu passo 1). Utilizador irá carregar este ficheiro zip como um pacote de aplicação no próximo passo.
1. Utilizar o [Azure portal] [ portal] para criar uma [aplicação](batch-application-packages.md) de lote denominado "MPIHelloWorld" e especifique o ficheiro zip que criou no passo anterior como versão "1.0" do pacote de aplicação. Para mais informações, consulte [carregar e gerir aplicações](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Criar uma versão de *lançamento* do `MPIHelloWorld.exe` para que não tenha que incluir qualquer dependências adicionais (por exemplo, `msvcp140d.dll` ou `vcruntime140d.dll`) no seu pacote de aplicação.

### <a name="execution"></a>Execução

1. Transferir as [amostras de lote azure] [ github_samples_zip] a partir do GitHub.
1. Abra a MultiInstanceTasks **solução** no Visual Studio 2015. O `MultiInstanceTasks.sln` solução ficheiro está localizado na:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Introduza as suas credenciais de conta e o lote de armazenamento no `AccountSettings.settings` no projeto **Microsoft.Azure.Batch.Samples.Common** .
1. **Criar e executar** a solução MultiInstanceTasks para executar o MPI aplicação de exemplo no calcular nós num conjunto de comandos.
1. *Opcional*: utilizar o [Azure portal] [ portal] ou o [Explorador do lote] [ batch_explorer] examinar o agrupamento de amostra, a tarefa e a tarefa ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de eliminar os recursos.

>[AZURE.TIP] Pode transferir o [Visual Studio Comunidade] [ visual_studio] gratuitamente se não tiver Visual Studio.

A partir de saída `MultiInstanceTasks.exe` é semelhante ao seguinte:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Próximos passos

- O blogue do Microsoft HPC & Azure lote equipa aborda [MPI suporte para Linux no Azure lote][blog_mpi_linux]e inclui informações sobre como utilizar [OpenFOAM] [ openfoam] com lote. Pode encontrar exemplos de código Python para que o [exemplo de OpenFOAM em GitHub][github_mpi].

- Saiba como [criar conjuntos de dados de nós do cluster Linux](batch-linux-nodes.md) para utilização no seu soluções Azure lote MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Descrição geral de com várias instâncias"
