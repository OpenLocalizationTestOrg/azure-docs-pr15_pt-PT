<properties
    pageTitle="Tutorial - começar a trabalhar com a biblioteca do Azure lote .NET | Microsoft Azure"
    description="Obter informações sobre os conceitos básicos do lote do Azure e como desenvolver para o serviço de processo com um cenário de exemplo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Começar a trabalhar com a biblioteca do Azure lote para .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Conheça os princípios básicos [Azure lote] [ azure_batch] e o [Lote .NET] [ net_api] biblioteca neste artigo como discutimos numa aplicação passo a passo de exemplo c#. Abordaremos como esta aplicação exemplo tira partido do serviço de lote para processar uma carga de trabalho paralela na nuvem, bem como a forma como interage com o [Armazenamento do Windows Azure](../storage/storage-introduction.md) para transição de ficheiro e obtenção. Irá aprender técnicas de fluxo de trabalho de aplicação lote comuns. Também irá obter uma base compreensão dos componentes principais do lote, tais como tarefas, tarefas, conjuntos de dados e calcular nós.

![Fluxo de trabalho do lote solução (basic)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem um conhecimento de trabalho do c# e Visual Studio. Também partem do princípio que é possível satisfazem os requisitos de criação de conta que estão especificados abaixo para Azure e lote e armazenamento de serviços.

### <a name="accounts"></a>Contas

- **Conta Azure**: Se ainda não tiver uma subscrição do Azure, [criar uma conta Azure gratuita][azure_free_account].
- **Conta lote**: assim que tiver uma subscrição do Azure, [crie uma conta Azure lote](batch-account-create-portal.md).
- **Conta de armazenamento**: consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Lote atualmente suporta *apenas* o tipo de conta de armazenamento **Geral objetivo** , conforme descrito no passo #5 [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>Visual Studio

Tem de ter **2015 do Visual Studio** para construir o projeto de exemplo. Pode encontrar versões de avaliação e gratuitas do Visual Studio um [Descrição geral dos produtos de 2015 do Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Exemplo de código *DotNetTutorial*

O [DotNetTutorial] [ github_dotnettutorial] amostra é um dos exemplos de código muitos encontrados nas [amostras de lote azure] [ github_samples] repositório no GitHub. Pode transferir o exemplo ao clicar no botão **Transferir ZIP** na home page do repositório ou ao clicar no [azure-lote amostras master.zip] [ github_samples_zip] ligação direta de transferência. Assim que tiver extraídos o conteúdo do ficheiro ZIP, irá encontrar a solução na seguinte pasta:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure Explorer lote (opcional)

O [Azure lote Explorer] [ github_batchexplorer] é um utilitário gratuito que vem incluído nas [amostras de lote azure] [ github_samples] repositório no GitHub. Enquanto não necessário para concluir este tutorial, pode ser útil enquanto desenvolver e depuração as soluções do lote.

## <a name="dotnettutorial-sample-project-overview"></a>Descrição geral do projeto de exemplo DotNetTutorial

O exemplo de código *DotNetTutorial* é uma solução de 2015 do Visual Studio que consiste em dois projetos: **DotNetTutorial** e **TaskApplication**.

- **DotNetTutorial** é a aplicação de cliente que interage com os serviços de lote e armazenamento executar uma carga de trabalho paralela na calcular nós (máquinas virtuais). DotNetTutorial é executada no seu estação de trabalho local.

- **TaskApplication** é o programa que é executada em nós de cluster no Azure para executar o trabalho real. No exemplo, `TaskApplication.exe` analisa o texto num ficheiro transferido a partir de armazenamento do Windows Azure (o ficheiro de entrada). Em seguida, gera um ficheiro de texto (o ficheiro de exportação) que contém uma lista das três palavras superiores que aparecem no ficheiro de entrada. Depois de criar o ficheiro de exportação, TaskApplication carrega o ficheiro de armazenamento do Windows Azure. Isto torna disponível para a aplicação de cliente para transferência. TaskApplication é executado em paralelo vários nós de cluster no serviço lote.

O diagrama seguinte ilustra as operações principais que são executadas pela aplicação de cliente, *DotNetTutorial*e a aplicação que é executada pelas tarefas, *TaskApplication*. Este fluxo de trabalho básico é típico de muitas soluções de cluster que são criadas com o lote. Enquanto não demonstra todas as funcionalidades disponíveis no serviço do lote, quase todas as cenário lote inclui processos semelhantes.

![Fluxo de trabalho do lote exemplo][8]<br/>

[**Passo 1.**](#step-1-create-storage-containers) Crie **contentores** no armazenamento de Blobs do Azure.<br/>
[**Passo 2.**](#step-2-upload-task-application-and-data-files) Carregar ficheiros de tarefas da aplicação e ficheiros de entrada para contentores.<br/>
[**Passo 3.**](#step-3-create-batch-pool) Crie um lote de **agrupamento**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3A.** O conjunto de **tarefa de início** transfere os ficheiros binários da tarefa (TaskApplication) para nós à medida que participar o agrupamento.<br/>
[**Passo 4.**](#step-4-create-batch-job) Crie uma **tarefa**.<br/>
[**Passo 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** à tarefa.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5º.** As tarefas são agendadas executar em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5. oB.** Cada tarefa os respetivos dados de entrada as transferências do armazenamento do Windows Azure, em seguida, começa a execução.<br/>
[**Passo 6.**](#step-6-monitor-tasks) Monitorizar as tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Como as tarefas sejam concluídas, carregue os respetivos dados de saída ao armazenamento do Azure.<br/>
[**Passo 7.**](#step-7-download-task-output) Transfira a saída de tarefas a partir do armazenamento.

Tal como mencionado, não todas as soluções do lote executa estes passos exatos e podem incluir muitas mais, mas a aplicação de exemplo *DotNetTutorial* demonstra processos comuns que se encontram numa solução lote.

## <a name="build-the-dotnettutorial-sample-project"></a>Criar o projeto de exemplo *DotNetTutorial*

Antes de poder executar com êxito da amostra, tem de especificar as credenciais de conta do lote e armazenamento do projeto *DotNetTutorial* `Program.cs` ficheiro. Se ainda não o tiver o fez, abra a solução no Visual Studio fazendo duplo clique a `DotNetTutorial.sln` ficheiro solução. Ou abri-lo a partir do Visual Studio utilizando o **ficheiro > Abrir > projeto/solução** menu.

Abrir `Program.cs` dentro do projecto *DotNetTutorial* . Em seguida, adicione as suas credenciais conforme especificado na parte superior do ficheiro:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Tal como mencionado acima, terá de especificar atualmente as credenciais para uma conta de armazenamento **Geral** no armazenamento do Windows Azure. As suas aplicações de lote utiliza armazenamento de BLOBs dentro da conta de armazenamento **Geral objetivo** . Não especificar as credenciais para uma conta de armazenamento que foi criado ao selecionar o tipo de conta de *armazenamento de BLOBs* .

Pode encontrar as credenciais da conta lote e armazenamento dentro pá a conta de cada serviço no [portal do Azure][azure_portal]:

![Batch credenciais no portal do][9]
![credenciais de armazenamento no portal][10]<br/>

Agora que atualizou o projeto com as suas credenciais, com o botão direito a solução no Explorador de soluções e clique em **Criar solução**. Confirme a restauração de quaisquer pacotes NuGet, se lhe for pedido.

> [AZURE.TIP] Se os pacotes de NuGet não são automaticamente restaurados, ou se os erros sobre uma falha para restaurar os pacotes, certifique-se de que tem o [Gestor de pacotes NuGet] [ nuget_packagemgr] instalado. Em seguida, ativar a transferência de pacotes em falta. Consulte o artigo [Ativar pacote restaurar durante construir] [ nuget_restore] para ativar a transferência do pacote.

Nas secções seguintes, vamos divide a aplicação de exemplo para os passos que executa a processar uma carga de trabalho no serviço lote e discutir esses passos detalhadamente. Recomendamos que se refira a solução aberta no Visual Studio enquanto trabalha básicas os restantes neste artigo, uma vez que não cada linha de código no exemplo é abordada.

Navegue até à parte superior da `MainAsync` método do projeto *DotNetTutorial* `Program.cs` ficheiro para iniciar com o passo 1. Cada passo abaixo, em seguida, aproximadamente segue progressão de método de chamadas no `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Passo 1: Criar contentores de armazenamento

![Criar contentores no armazenamento do Windows Azure][1]
<br/>

Lote inclui suporte incorporado para interagir com armazenamento do Windows Azure. Contentores na sua conta de armazenamento irão fornecer os ficheiros necessários pelas tarefas que são executados na sua conta do lote. Os contentores também fornecem um local para armazenar os dados de saída produzem as tarefas. A primeira coisa que é que a aplicação de cliente *DotNetTutorial* é criar três contentores no [Armazenamento de Blobs do Azure](../storage/storage-introduction.md):

- **aplicação**: neste contentor irá guardar a aplicação execute às tarefas, bem como qualquer uma das suas dependências, tal como DLLs.
- **Introdução**: tarefas irão transferir os ficheiros de dados para processar desde o contentor de *entrada* .
- **resultado**: Quando tarefas concluir o processamento de ficheiro de entrada, estes serão carregados os resultados ao contentor de *saída* .

Para interagir com uma conta de armazenamento e a criação de contentores, utilizamos a [Biblioteca de cliente do Azure armazenamento para .NET][net_api_storage]. Vamos criar uma referência para a conta com [CloudStorageAccount][net_cloudstorageaccount]e de que criar um [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Utilizamos os `blobClient` referência em toda a aplicação e transmiti-lo como um parâmetro para vários métodos. Um exemplo de este é o bloco de código imediatamente a seguir acima, onde chamamos de `CreateContainerIfNotExistAsync` para criar realmente os contentores.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Depois de tem sido criados os contentores, a aplicação agora pode carregar os ficheiros que serão utilizados pelas tarefas.

> [AZURE.TIP] [Como utilizar o armazenamento de Blobs do .NET](../storage/storage-dotnet-how-to-use-blobs.md) fornece uma boa visão geral de trabalhar com contentores de armazenamento do Windows Azure e blobs. Deve ser na parte superior da sua lista de leitura como começar a trabalhar com lote.

## <a name="step-2-upload-task-application-and-data-files"></a>Passo 2: Carregar ficheiros de aplicação e dados de tarefa

![Carregar a aplicação de tarefa e ficheiros de entrada (dados) para contentores][2]
<br/>

Na operação de carregamento de ficheiro, *DotNetTutorial* pela primeira vez define coleções de caminhos de ficheiro de **entrada** e de **aplicação** à medida que existam no computador local. Em seguida, serão carregadas estes ficheiros para os contentores que criou no passo anterior.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Existem dois métodos no `Program.cs` que estão, normalmente o processo de carregamento:

- `UploadFilesToContainerAsync`: Este método devolve um conjunto de [ResourceFile] [ net_resourcefile] objetos (abordados abaixo) e internamente chamadas `UploadFileToContainerAsync` para carregar cada ficheiro que lhe é transmitido no parâmetro *filePaths* .
- `UploadFileToContainerAsync`: Este é o método que realmente executa o carregamento de ficheiro e cria a [ResourceFile] [ net_resourcefile] objetos. Após carregar o ficheiro, obtém uma assinatura de acesso partilhado (SA) para o ficheiro e devolve um objecto ResourceFile que a representa. Partilhada acesso assinaturas também são descritas abaixo.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

Um [ResourceFile] [ net_resourcefile] fornece tarefas no lote com o URL para um ficheiro no armazenamento do Windows Azure que é transferido para o nó cluster antes dessa tarefa é executada. O [ResourceFile.BlobSource] [ net_resourcefile_blobsource] propriedade especifica o URL completo do ficheiro, tal como se encontra no armazenamento do Windows Azure. O URL também pode incluir uma assinatura de acesso partilhado (SA) que proporciona acesso seguro para o ficheiro. A maioria dos tipos de tarefas dentro lote .NET incluem uma propriedade de *ResourceFiles* , incluindo:

- [CloudTask][net_task]
- [Tarefa de início][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

A aplicação de exemplo DotNetTutorial não utiliza os tipos de tarefas JobPreparationTask ou JobReleaseTask, mas pode ler mais sobre os mesmos no [Executar preparação e de conclusão de tarefas de projecto no Azure lote calcular nós](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso partilhado (SA)

Acesso partilhado assinaturas são cadeias que — quando incluídos como parte de um URL — fornecer acesso seguro a contentores e blobs no armazenamento do Windows Azure. DotNetTutorial aplicação utiliza blob e contentor partilhado aceder aos URLs de assinatura e demonstra como obter estas cadeias de assinatura acesso partilhado a partir do serviço de armazenamento.

- **Blob partilhado acesso assinaturas**: tarefa de início o agrupamento no DotNetTutorial utiliza assinaturas de acesso de BLOBs partilhado quando transfere o binários da aplicação e ficheiros de dados de entrada a partir do armazenamento (consulte abaixo do passo #3). O `UploadFileToContainerAsync` método do DotNetTutorial `Program.cs` contém o código que obtém a assinatura de acesso partilhado cada blob. Fizer ao contactar o suporte [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Contentor partilhado acesso assinaturas**: como cada tarefa termina o seu trabalho no nó cluster, carrega o ficheiro de exportação para o contentor de *saída* no armazenamento do Windows Azure. Para fazê-lo, TaskApplication utiliza uma assinatura de acesso partilhado contentor que proporciona acesso de escrita para o contentor como parte do caminho quando os carregamentos pendentes para o ficheiro. Obter a assinatura de acesso partilhado contentor é feito de forma semelhante como quando a obter o blob partilhada assinatura de acesso. DotNetTutorial, irá encontrar que o `GetContainerSasUrl` método de ajuda chamadas [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] fazê-lo. Irá ler mais sobre como TaskApplication utiliza o contentor partilhado assinatura de acesso no "passo 6: monitorizar tarefas."

> [AZURE.TIP] Dar saída a série de duas partes sobre assinaturas de acesso partilhado, [parte 1: Noções sobre o modelo de assinatura (SA) acesso partilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) e [parte 2: criar e utilizar uma assinatura de acesso partilhado (SA) com o armazenamento de BLOBs](../storage/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro a dados na sua conta de armazenamento.

## <a name="step-3-create-batch-pool"></a>Passo 3: Criar o conjunto de dados Batch

![Criar um conjunto de comandos][3]
<br/>

Um lote de **agrupamento** é uma coleção de cluster nós (máquinas virtuais) no qual o lote executa tarefas de uma tarefa.

Depois de-os carregamentos pendentes os aplicação e ficheiros de dados para a conta de armazenamento, *DotNetTutorial* inicia respetiva interação com o serviço do lote utilizando a biblioteca de lote .NET. Para fazê-lo, um [BatchClient] [ net_batchclient] criada pela primeira vez:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Em seguida, é criado um conjunto de nós de cluster na conta lote com uma chamada para `CreatePoolAsync`. `CreatePoolAsync`utiliza o [BatchClient.PoolOperations.CreatePool] [ net_pool_create] método para criar realmente um conjunto de dados no serviço lote.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Quando cria um conjunto de dados com [CreatePool][net_pool_create], especificar várias parâmetros, tal como o número de nós de cluster, o [tamanho de nós](../cloud-services/cloud-services-sizes-specs.md)e sistema operativo de nós. *DotNetTutorial*, podemos utilizar [CloudServiceConfiguration] [ net_cloudserviceconfiguration] para especificar o Windows Server 2012 R2 dos [Serviços em nuvem](../cloud-services/cloud-services-guestos-update-matrix.md). No entanto, ao especificar um [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] em vez disso, pode criar conjuntos de dados de nós criado a partir do imagens Marketplace, que inclui imagens do Windows e Linux — consulte o artigo [aprovisionar Linux calcular nós no Azure lote conjuntos de dados](batch-linux-nodes.md) para obter mais informações.

> [AZURE.IMPORTANT] São cobradas para recursos de cluster no lote. Para minimizar os custos, pode diminuir `targetDedicated` para 1 antes de executar a amostra.

Juntamente com estas propriedades nó físico, também pode especificar uma [tarefa de início] [ net_pool_starttask] para o conjunto. A tarefa de início executa em cada nó à medida que o nó associa o agrupamento e, sempre que um nó é reiniciado. A tarefa de início é particularmente útil para instalar aplicações em nós de cluster antes da execução das tarefas. Por exemplo, se as suas tarefas processam dados utilizando Python scripts, pode utilizar uma tarefa de início para instale Python em nós cluster.

Na aplicação neste exemplo, a tarefa de início copia os ficheiros que transfere a partir do armazenamento (que são especificadas utilizando a [tarefa de início][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] propriedade) a partir do diretório de trabalho a tarefa de início ao directório partilhado que podem aceder a *todas as* tarefas em execução no nó. Basicamente, desta forma, copia `TaskApplication.exe` respectivas dependências ao directório partilhado em cada nó tal como o nó adere o conjunto de, para que todas as tarefas que execute no nó possam aceder à mesma.

> [AZURE.TIP] A funcionalidade de **pacotes de aplicações** do Azure lote fornece outra forma de obter a aplicação para os nós de cluster num conjunto de dados. Consulte o artigo de [implementação de aplicações com o Azure lote pacotes de aplicações](batch-application-packages.md) para obter detalhes.

Também notáveis no fragmento de código acima são a utilização de duas variáveis de ambiente a propriedade de *linha de comandos* da tarefa de início: `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nó cluster dentro de um conjunto de lote é configurado automaticamente com várias variáveis de ambiente que sejam específicas lote. Qualquer processo que é executado por uma tarefa tem acesso a estas variáveis de ambiente.

> [AZURE.TIP] Para saber mais sobre o ambiente de variáveis que estão disponíveis em nós de cluster de um conjunto de comandos e informações no directórios de trabalho da tarefa, consulte as secções de [definições de ambiente de tarefas](batch-api-basics.md#environment-settings-for-tasks) e [ficheiros e directórios](batch-api-basics.md#files-and-directories) na [Descrição geral da funcionalidade do lote para programadores](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Passo 4: Criar rotina

![Criar rotina][4]<br/>

Uma **tarefa** é um conjunto de tarefas e está associado um conjunto de nós de cluster. Executar as tarefas numa tarefa em nós de cluster o agrupamento associado.

Pode utilizar uma tarefa não só para organizar e controlar tarefas relacionadas das cargas de trabalho, mas também para impor disciplinas determinadas – tal como o runtime máximo para a tarefa (e por extensão, as suas tarefas) bem como prioridade tarefa em relação a outras tarefas na conta lote. Neste exemplo, no entanto, a tarefa está associada apenas com o conjunto de que foi criado no passo #3. Sem propriedades adicionais são configuradas.

Todas as tarefas de lote estão associadas um conjunto específico. Esta associação indica quais os nós tarefas do projecto serão executado no. Pode especificá-lo ao utilizar o [CloudJob.PoolInformation] [ net_job_poolinfo] propriedade, conforme apresentado no fragmento de código abaixo.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Agora que uma tarefa foi criada, tarefas são adicionadas ao efetuar o trabalho.

## <a name="step-5-add-tasks-to-job"></a>Passo 5: Adicionar tarefas à tarefa

![Adicionar tarefas à tarefa][5]<br/>
*(1) tarefas são adicionadas à tarefa, (2) as tarefas são agendadas para ser executada em nós e (3) as tarefas transferir os ficheiros de dados para processar*

Lote as **tarefas** são as unidades individuais de trabalho que são executados nos nós de cluster. Uma tarefa tem uma linha de comandos e executa os scripts ou executáveis que especificar nessa linha de comandos.

Para executar realmente trabalho, tarefas tem de ser adicionadas a uma tarefa. Cada [CloudTask] [ net_task] está configurado com uma propriedade da linha de comandos e [ResourceFiles] [ net_task_resourcefiles] (como com tarefa de início o agrupamento) que a tarefa de transferências para o nó antes da sua linha de comandos é executada automaticamente. No project de exemplo *DotNetTutorial* , cada tarefa processa apenas um ficheiro. Assim sendo, respectiva colecção de ResourceFiles contém um elemento único.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Quando acederem variáveis de ambiente tais como `%AZ_BATCH_NODE_SHARED_DIR%` ou executar uma aplicação não encontrada no nó `PATH`, linhas do comando tarefa devem ter o prefixo `cmd /c`. Isto explicitamente executar o intérprete de comando e indique-o a terminar depois do seu comando. Este requisito se desnecessários se as suas tarefas executem uma aplicação no nó `PATH` (como *robocopy.exe* ou *powershell.exe*) e são utilizadas sem variáveis de ambiente.

Dentro de `foreach` loop no fragmento de código acima, pode ver que a linha de comandos para a tarefa é construída assim que são transmitidos três argumentos da linha de comandos para *TaskApplication.exe*:

1. O **primeiro argumento** é o caminho do ficheiro para processar. Este é o caminho local para o ficheiro, tal como se encontra no nó. Quando o ResourceFile objecto em `UploadFileToContainerAsync` foi criada pela primeira vez acima, o nome do ficheiro foi utilizado para esta propriedade (como um parâmetro para o construtor de ResourceFile). Isto indica que o ficheiro pode ser encontrado no mesmo directório como *TaskApplication.exe*.

2. O **segundo argumento** Especifica que devem ser escritas as palavras *N* superiores para o ficheiro de exportação. No exemplo, isto é codificado para que as três palavras superiores são escritas para o ficheiro de exportação.

3. O **terceiro argumento** é a assinatura de acesso partilhado (SA) que fornece acesso de escrita para o contentor de **saída** no armazenamento do Windows Azure. *TaskApplication.exe* utiliza este URL de assinatura acesso partilhado quando-carrega o ficheiro de exportação de armazenamento do Windows Azure. Pode localizar o código para o fazer na `UploadFileToContainer` método do projeto TaskApplication `Program.cs` ficheiro:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Passo 6: Monitorizar tarefas

![Monitorizar tarefas][6]<br/>
*A aplicação de cliente (1) monitoriza as tarefas para a conclusão e estado de sucesso e (2) as tarefas carregar dados de resultados para o armazenamento do Windows Azure*

Quando tarefas são adicionadas a uma tarefa, são automaticamente em fila de espera e agendados para execução em nós de cluster dentro do conjunto de associados à tarefa. Com base nas definições que especificar, o lote trata todas as tarefas colocação, agendamento, repetir e outras funções de administração de tarefa para si. Existem muitos abordagens para monitorizar a execução da tarefa. DotNetTutorial mostra um exemplo simples que relatórios apenas falha de conclusão e a tarefa ou sucesso Estados-membros.

Dentro de `MonitorTasks` método do DotNetTutorial `Program.cs`, existem três conceitos lote .NET que justificarem debate. Estão indicadas abaixo na sua ordem de apresentação:

1. **ODATADetailLevel**: especificando [ODATADetailLevel] [ net_odatadetaillevel] na lista de operações (como obter uma lista de tarefas de uma tarefa) é essenciais assegurando desempenho da aplicação lote. Adicione [consultar o serviço de Azure lote eficazmente](batch-efficient-list-queries.md) à sua lista de leitura se planeia efetuar qualquer ordenar de estado de monitorização dentro das aplicações do seu lote.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] fornece aplicações lote .NET com utilitários de helper para monitorizar os Estados de tarefas. No `MonitorTasks`, *DotNetTutorial* aguarda para todas as tarefas alcançar [TaskState.Completed] [ net_taskstate] dentro de um limite de tempo. Em seguida, este termina a tarefa.

3. **TerminateJobAsync**: terminar uma tarefa com [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (ou o bloqueio JobOperations.TerminateJob) marca essa tarefa como concluída. É essencial para fazê-lo se a sua solução lote utiliza um [JobReleaseTask][net_jobreltask]. Este é um tipo especial de tarefa, o que é descrito em [tarefas de preparação e de conclusão da tarefa](batch-job-prep-release.md).

O `MonitorTasks` método *DotNetTutorial*do `Program.cs` é apresentado abaixo:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Passo 7: Transferir saída de tarefas

![Transferir a saída de tarefas a partir do armazenamento][7]<br/>

Agora que a tarefa está concluída, o resultado das tarefas pode ser transferido a partir do armazenamento do Windows Azure. Esta é executada com uma chamada para `DownloadBlobsFromContainerAsync` no *DotNetTutorial*do `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] A chamada para `DownloadBlobsFromContainerAsync` a *DotNetTutorial* aplicação Especifica que os ficheiros devem ser transferidos para o seu `%TEMP%` pasta. Esteja à vontade modificar esta localização de saída.

## <a name="step-8-delete-containers"></a>Passo 8: Contentores de eliminar

Uma vez que são cobrados para os dados que reside armazenamento do Windows Azure, sempre é uma boa ideia para remover quaisquer blobs que já não são necessários para as tarefas de lote. Do DotNetTutorial `Program.cs`, esta é executada com três chamadas para o método de helper `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

O método propriamente dito meramente obtém uma referência para o contentor e, em seguida, liga [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passo 9: Eliminar a tarefa e o agrupamento

No passo final, é pedido ao utilizador para eliminar a tarefa e o conjunto de que foram criados pela aplicação DotNetTutorial. Apesar de não são cobrados para tarefas e próprias tarefas, *são* cobrado por nós de cluster. Assim sendo, recomendamos que alocar nós apenas conforme necessário. Eliminar conjuntos de dados não utilizados pode ser parte do processo de manutenção.

O BatchClient [JobOperations] [ net_joboperations] e [PoolOperations] [ net_pooloperations] têm ambos os métodos de eliminação correspondentes, chamam-se o utilizador confirmar a eliminação:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Tenha em atenção que são cobrados para recursos de cluster — a eliminação de conjuntos de dados não utilizados irão minimizar o custo. Além disso, tenha em atenção de que a eliminação de um conjunto de dados elimina todos os nós de cluster dentro desse conjunto e que quaisquer dados em nós serão irrecuperáveis após o agrupamento é eliminado.

## <a name="run-the-dotnettutorial-sample"></a>Executar o exemplo *DotNetTutorial*

Quando executar a aplicação de exemplo, a saída da consola será semelhante ao seguinte. Durante a execução, irá ocorrer uma pausa na `Awaiting task completion, timeout in 00:30:00...` enquanto nós de cluster no conjunto são iniciadas. Utilizar o [Azure portal] [ azure_portal] para monitorizar o seu conjunto, calcular nós, tarefa e tarefas durante e após a execução. Utilizar o [Azure portal] [ azure_portal] ou o [Explorador de armazenamento do Azure] [ storage_explorers] para ver os recursos de armazenamento (contentores e blobs) que são criados pela aplicação.

Tempo de execução típico é **aproximadamente 5 minutos** quando executar a aplicação na sua configuração predefinida.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Próximos passos

Esteja à vontade efetuar alterações a *DotNetTutorial* e *TaskApplication* para experimentar com os cenários de cluster diferentes. Por exemplo, experimente adicionar um atraso de execução para *TaskApplication*, tal como com [Thread.Sleep][net_thread_sleep], para simular tarefas de execução longa e monitorizá-los no portal. Experimente adicionar tarefas mais ou ajustar o número de nós de cluster. Adicionar lógica de verificar e permitir a utilização de um conjunto existente para o tempo de execução de velocidade (*Sugestão*: consulte o artigo `ArticleHelpers.cs` na [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] projeto no [amostras de lote azure][github_samples]).

Agora que já está familiarizado com o fluxo de trabalho básico de uma solução lote, é hora para aprofundar para as funcionalidades adicionais do serviço do lote.

- Leia a [Descrição geral da funcionalidade do lote para programadores](batch-api-basics.md), que recomendamos para todos os novos utilizadores lote.
- Iniciar os outros artigos de desenvolvimento de lote em **desenvolvimento aprofundado** no [caminho de formação do lote][batch_learning_path].
- Dar saída de uma implementação diferente do processamento a carga de trabalho "palavras início N" ao utilizar o lote na [TopNWords] [ github_topnwords] amostra.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Criar contentores no armazenamento do Windows Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Carregar a aplicação de tarefa e ficheiros de entrada (dados) para contentores"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Criar conjunto de dados Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Criar rotina"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Adicionar tarefas à tarefa"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorizar tarefas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Transferir a saída de tarefas a partir do armazenamento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Fluxo de trabalho do lote solução (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciais do lote no Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciais de armazenamento no Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Fluxo de trabalho do lote solução (diagrama mínimo)"
