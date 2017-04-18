<properties
    pageTitle="Projecto e a tarefa de saída persistente no Azure lote | Microsoft Azure"
    description="Saiba como utilizar o armazenamento do Windows Azure como um arquivo resistente para a sua tarefa lote e de projeto de saída e ative a ver esta saída persistente no portal do Azure."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Manter a saída de projecto e a tarefa de lote do Azure

As tarefas que executa no lote normalmente produzem saída que tem de ser armazenada e, em seguida, mais tarde obtida por outras tarefas numa tarefa, a aplicação de cliente que executada a tarefa ou ambas. Este resultado poderá ser ficheiros criados através do processamento de dados de entrada ou associados a execução de tarefas de ficheiros de registo. Este artigo apresenta uma biblioteca de classes .NET que utiliza uma técnica com base em convenções para persistirem saída essa tarefa ao armazenamento de Blobs do Azure, disponibilizar mesmo depois de eliminar conjuntos de dados, tarefas e calcular nós.

Ao utilizar a técnica neste artigo, também pode ver o resultado da tarefa em **ficheiros de saída guardado** e **registos de guardado** no [portal do Azure][portal].

![Ficheiros de saída guardado e guardado registos seletores no portal][1]

>[AZURE.NOTE] [Convenções de ficheiro do Azure lote] [ nuget_package] biblioteca de classes .NET outros fabricantes referida neste artigo atualmente está na pré-visualização. Algumas das funcionalidades aqui descritas podem ser alteradas antes de disponibilidade geral.

## <a name="task-output-considerations"></a>Considerações de saída de tarefa

Quando estrutura a solução lote, tem de considerar diversos fatores relacionadas com os resultados de projecto e a tarefa.

* **Calcular a duração de nó**: calcular nós são geralmente breves, sobretudo com capacidade de autoscale conjuntos de dados. Resultados das tarefas que são executados num nó estão disponíveis apenas enquanto o nó existe e apenas a partir do tempo de retenção do ficheiro que definiu para a tarefa. Para se certificar de que o resultado da tarefa é preservado, as suas tarefas, por isso, tem de carregar os respetivos ficheiros de saída para numa loja resistente, por exemplo, armazenamento do Windows Azure.

* **Armazenamento de saída**: para persistirem dados de saída de tarefas ao armazenamento resistente, pode utilizar o [Azure armazenamento SDK](../storage/storage-dotnet-how-to-use-blobs.md) no seu código de tarefa para carregar o resultado da tarefa para um contentor de armazenamento de Blobs. Se implementar um contentor e Convenção de nomenclatura de ficheiros, a aplicação de cliente ou outras tarefas na tarefa podem, em seguida, localize e transferir este resultado com base em Convenção.

* **Obtenção de saída**: a poderá obter saída da tarefa diretamente a partir de nós de cluster no seu conjunto, ou a partir do armazenamento do Windows Azure se as suas tarefas persistirem os respetivos saída. Para obter a saída de uma tarefa diretamente a partir de um nó cluster, é necessário o nome do ficheiro e a respectiva localização de saída no nó. Se persistirem saída ao armazenamento do Azure, descendentes tarefas ou a sua aplicação cliente tem de ter o caminho completo para o ficheiro de armazenamento do Windows Azure para transferi-lo utilizando o SDK do armazenamento Azure.

* **Visualizar saída**: quando navegar para uma tarefa de lote no portal do Azure e selecione **ficheiros no nó**, são apresentadas com todos os ficheiros associados à tarefa, não apenas os ficheiros de saída que lhe interessa. Novamente, ficheiros em cluster nós estão disponíveis apenas enquanto o nó existe e apenas a partir do tempo de retenção do ficheiro que definiu para a tarefa. Para ver a saída de tarefas tenha persiste ao armazenamento do Azure no portal do ou uma aplicação como o [Explorador de armazenamento do Azure][storage_explorer], tem de saber a sua localização e navegue diretamente para o ficheiro.

## <a name="help-for-persisted-output"></a>Ajuda para o resultado persistente

Para ajudá-lo mais facilmente persistirem projecto e a tarefa de saída, a equipa do lote tem definidos e implementada um conjunto de convenções de nomenclatura, bem como uma biblioteca de classes .NET, [Convenções de ficheiro do Azure lote] [ nuget_package] biblioteca, que pode utilizar nas suas aplicações do lote. Além disso, o portal do Azure estiver em atenção estes convenções de nomenclatura para que possa encontrar facilmente os ficheiros que armazenou utilizando a biblioteca.

## <a name="using-the-file-conventions-library"></a>Utilizar a biblioteca de convenções de ficheiro

[Convenções de ficheiro do Azure lote] [ nuget_package] é uma biblioteca de classes .NET que podem utilizar as aplicações do lote .NET para facilmente armazenar e obter resultados de tarefa de armazenamento do Windows Azure e para. Destina-se para utilização no código de tarefa e cliente – no código de tarefa para ficheiros persistentes e no código do cliente para listar e obtê-las. As tarefas também podem utilizar a biblioteca para obter os resultados das tarefas montante, tal como num cenário de [dependências de tarefas](batch-task-dependencies.md) .

A biblioteca de convenções leva-o até o cuidado de assegurar que contentores de armazenamento e tarefas de saída de ficheiros são com o nome de acordo com a Convenção e são carregados para o local certo quando persistentes ao armazenamento do Azure. Quando obtém resultados, pode localizar facilmente saídas para uma determinada tarefa ou tarefas por listar ou obtenção de resultados por ID e objetivo, em vez de sabe nomes de ficheiros ou quais existe no armazenamento.

Por exemplo, pode utilizar a biblioteca para "lista todos os ficheiros intermédios para Tarefa 7" ou "obter-me a pré-visualização em miniatura para tarefa *mymovie*," sem necessitar de saber os nomes de ficheiro ou localização no interior da sua conta de armazenamento.

### <a name="get-the-library"></a>Obter a biblioteca

Pode obter a biblioteca que contém novas classes e expande a [CloudJob] [ net_cloudjob] e [CloudTask] [ net_cloudtask] classes com novos métodos, a partir do [NuGet][nuget_package]. Pode adicioná-la ao seu projeto do Visual Studio utilizando o [Gestor de pacotes de biblioteca NuGet][nuget_manager].

>[AZURE.TIP] Pode encontrar o [código fonte] [ github_file_conventions] para a biblioteca de convenções de ficheiro do Azure lote no GitHub no Microsoft Azure SDK para repositório .NET.

## <a name="requirement-linked-storage-account"></a>Requisito: conta de armazenamento ligadas

Para armazenar saídas ao armazenamento resistente utilizando a biblioteca de convenções de ficheiro e vê-los no portal do Azure, tem de [ligação de uma conta de armazenamento do Windows Azure](batch-application-packages.md#link-a-storage-account) à sua conta do lote. Se ainda não o fez, ligar uma conta de armazenamento à sua conta do lote utilizando o portal do Azure:

**Conta lote** pá > **definições de** > **Conta de armazenamento** > **Conta de armazenamento** (nenhum) > selecionar uma conta de armazenamento na sua subscrição

Para uma mais detalhada guia passo a passo sobre como ligar a uma conta de armazenamento, consulte o artigo [implementação de aplicações com o Azure lote pacotes de aplicações](batch-application-packages.md).

## <a name="persist-output"></a>Manter saída

Existem duas ações principais para desempenhar quando guardar o resultado de projecto e a tarefa com a biblioteca de convenções de ficheiro: criar o contentor de armazenamento e guardar exportação para o contentor.

>[AZURE.WARNING] Uma vez que todos os resultados de projecto e a tarefa são armazenados no mesmo contentor, [limites de armazenamento optimização](../storage/storage-performance-checklist.md#blobs) poderá imposta se um grande número de tarefas tentarem persistirem ficheiros ao mesmo tempo.

### <a name="create-storage-container"></a>Criar o contentor de armazenamento

Antes das suas tarefas começam persistentes saída ao armazenamento, terá de criar um contentor de armazenamento de BLOBs aos quais que irá carregar os respetivos saída. Faça o seguinte ao contactar o suporte [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Este método extensão demora uma [CloudStorageAccount] [ net_cloudstorageaccount] objeto como um parâmetro e cria um contentor com o nome de forma a que os seus conteúdos são detetável portal do Azure e os métodos de obtenção abordados posteriormente neste artigo.

Normalmente, coloque este código na sua aplicação de cliente – a aplicação que cria os conjuntos de dados, tarefas e tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Armazenar os resultados de tarefa

Agora que já preparada um contentor do armazenamento de BLOBs, tarefas podem guardar saída o contentor utilizando o [TaskOutputStorage] [ net_taskoutputstorage] escolares que se encontram na biblioteca de convenções de ficheiro.

No seu código de tarefa, criar uma [TaskOutputStorage] [ net_taskoutputstorage] objeto, em seguida, quando a tarefa foi concluída o seu trabalho, ligar para o [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] método para guardar o seu resultado ao armazenamento do Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

O parâmetro "tipo de saída" categoriza os ficheiros persistentes. Existem quatro predefinidas [TaskOutputKind] [ net_taskoutputkind] tipos de: "TaskOutput", "TaskPreview", "TaskLog" e "TaskIntermediate." Também pode definir tipos personalizados se quiser ser úteis para o fluxo de trabalho.

Estes tipos de saída permitem-lhe especificar qual o tipo de resultados para listar quando consulta mais tarde lote para saídas de persistentes de uma determinada tarefa. Por outras palavras, quando a lista de resultados para uma tarefa, pode filtrar a lista de um dos tipos de saída. Por exemplo, "também me o resultado da *pré-visualização* para tarefa *109*." Mais no listar e obter resultados de aparece em [obter a saída](#retrieve-output) posteriormente neste artigo.

>[AZURE.TIP] O tipo de resultado também atribui onde no portal do Azure um determinado ficheiro é apresentado: *TaskOutput*-ficheiros categorizados apareçam no "Ficheiros de saída da tarefa" e os ficheiros *TaskLog* aparecem na "Inicia a tarefa".

### <a name="store-job-outputs"></a>Armazenar os resultados de tarefa

Para além de armazenamento de resultados de tarefa, pode armazenar saídas associadas com toda a tarefa. Por exemplo, a tarefa de impressão em série de uma tarefa de composição de filme, poderia persistirem o filme totalmente composto como resultado de uma tarefa. Quando a tarefa estiver concluída, a aplicação de cliente pode simplesmente da lista e obter os resultados para a tarefa e não é necessário consultar as tarefas individuais.

Armazenar saída de tarefa ao contactar o [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] método, e especifique o [JobOutputKind] [ net_joboutputkind] e nome de ficheiro:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Com TaskOutputKind para saídas de tarefa, ao utilizar o [JobOutputKind] [ net_joboutputkind] parâmetro para categorizar uma tarefa do persistentes ficheiros. Este parâmetro permite-lhe a consulta posterior para (lista) um tipo específico de saída. O JobOutputKind inclui os tipos de saída e pré-visualizar e suporta a criação de tipos de personalizado.

### <a name="store-task-logs"></a>Armazenar os registos de tarefa

Para além de persistência um ficheiro para o armazenamento resistente quando uma tarefa ou a tarefa estiver concluída, poderá achar necessárias para manter os ficheiros que estão actualizados durante a execução de uma tarefa – ficheiros de registo ou `stdout.txt` e `stderr.txt`, por exemplo. Para o efeito, a biblioteca de convenções de ficheiro do Azure lote fornece o [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] método. Com [SaveTrackedAsync][net_savetrackedasync], pode controlar atualizações para um ficheiro no nó (num intervalo que especificar) e manter esses atualizações do armazenamento do Windows Azure.

No seguinte fragmento de código, utilizamos [SaveTrackedAsync] [ net_savetrackedasync] para atualizar `stdout.txt` no Azure armazenamento cada 15 segundos durante a execução da tarefa:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`é simplesmente um marcador de posição para o código que normalmente faria executar a sua tarefa. Por exemplo, poderá ter o código de que transfere dados a partir de armazenamento do Windows Azure e executa transformação ou cálculo no mesmo. A parte importante deste fragmento é demonstrar como pode moldar essa código num `using` bloquear para atualizar periodicamente um ficheiro com [SaveTrackedAsync][net_savetrackedasync].

O `Task.Delay` é necessária no final deste `using` bloco para se certificar de que o agente de nó tem tempo para esvaziar o conteúdo da fora padrão para o ficheiro stdout.txt no nó (o agente de nó é um programa que é executado em cada nó no conjunto e fornece a interface de comandos e controlo entre o nó e o serviço do lote). Sem este atraso, é possível perde últimos alguns segundos de saída. Este atraso não pode ser necessário para todos os ficheiros.

>[AZURE.NOTE] Quando ativa ficheiro controlar com SaveTrackedAsync, apenas *acrescenta* ao ficheiro de registadas são permanentes ao armazenamento do Azure. Utilize este método apenas para ficheiros de registo que não sejam rodar ou outros ficheiros que são acrescentados à, ou seja, os dados de controlo é adicionado apenas para o fim do ficheiro quando é atualizado.

## <a name="retrieve-output"></a>Obter a saída

Quando obtém a saída persistente utilizando a biblioteca de convenções de ficheiro do Azure lote, pode fazê-lo de uma tarefa e tarefa-centrados em forma. Pode pedir o resultado para um dado tarefa ou tarefa sem necessitar de saber o caminho BLOBs armazenamento ou ainda o nome de ficheiro. Basta pode dizer, "Também me os ficheiros de saída para tarefa *109*."

O fragmento de código seguinte itera através de todas as tarefas de uma tarefa, imprime algumas informações sobre os ficheiros de saída para a tarefa e, em seguida, transferências os respetivos ficheiros a partir do armazenamento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Resultados de tarefa e o portal do Azure

Portal do Azure apresenta resultados de tarefa e os registos que são permanentes para uma conta de armazenamento do Windows Azure ligada utilizando as convenções de nomenclatura que se encontram no [Azure lote ficheiro convenções Leia-me][github_file_conventions_readme]. Pode implementar estas convenções num idioma da sua escolha, ou pode utilizar a biblioteca de convenções de ficheiro nas suas aplicações .NET.

### <a name="enable-portal-display"></a>Ativar a apresentar do portal

Para permitir a visualização de seu saídas no portal do, terá de cumprir os seguintes requisitos:

 1. [Ligação de uma conta de armazenamento do Windows Azure](#requirement-linked-storage-account) à sua conta do lote.
 2. Adira as convenções de nomenclatura predefinidas para contentores de armazenamento e ficheiros quando persistência saídas. Pode encontrar a definição destas convenções na biblioteca de convenções de ficheiro [Leia-me][github_file_conventions_readme]. Se utilizar o [Azure lote ficheiro convenções] [ nuget_package] biblioteca para manter a saída, este requisito é cumprida.

### <a name="view-outputs-in-the-portal"></a>Resultados de vista no portal

Para ver resultados de tarefa e registos no portal do Azure, navegue para a tarefa cuja saída interessados em, em seguida, clique em **ficheiros de saída guardado** ou **registos guardados**. Esta imagem mostra os **ficheiros de saída guardado** para a tarefa com ID "007":

![Tarefa saídas pá no portal do Azure][2]

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs] [ github_persistoutputs] projecto de exemplo é um dos [exemplos de código do Azure lote] [ github_samples] no GitHub. Esta solução do Visual Studio 2015 demonstra como utilizar a biblioteca de convenções de ficheiro do Azure lote para manter a saída de tarefas ao armazenamento resistente. Para executar a amostra, siga estes passos:

1. Abra o projeto no **Visual Studio 2015**.
2. Adicione o lote e armazenamento **as credenciais de conta** para **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Criar** (mas não são executadas) a solução. Restaure quaisquer pacotes NuGet se lhe for solicitado.
4. Utilize o portal do Azure para carregar um [pacote de aplicação](batch-application-packages.md) para **PersistOutputsTask**. Incluir o `PersistOutputsTask.exe` e respetiva assemblagem dependentes no pacote ZIP, defina o ID da aplicação para "PersistOutputsTask" e a versão do pacote de aplicação "1,0".
5. **Iniciar** projeto (executar) a **PersistOutputs** .

## <a name="next-steps"></a>Próximos passos

### <a name="application-deployment"></a>Implementação da aplicação

A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do lote fornece uma forma fácil para ambos implementar e as aplicações que as suas tarefas executar na calcular nós de versão.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste

Consulte o artigo [instalar aplicações e dados de transição no lote calcular nós] [ forum_post] publicar no fórum Azure lote para uma descrição geral dos métodos diferentes para preparar os nós de tarefas em execução. Escrito por um dos membros da equipa Azure lote, esta mensagem é uma boa introdução sobre as diferentes formas de colocar ficheiros (incluindo aplicações e dados de entrada de tarefa) no seu cluster nós e algumas considerações especiais para cada método.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Ficheiros de saída guardado e guardado registos seletores no portal"
[2]: ./media/batch-task-output/task-output-02.png "Tarefa saídas pá no portal do Azure"