
<properties 
    pageTitle="Gestão de recursos e entidades relacionadas com os serviços de multimédia .NET SDK" 
    description="Saiba como gerir elementos e entidades relacionadas com o SDK de serviços de multimédia para .NET." 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gestão de recursos e entidades relacionadas com os serviços de multimédia .NET SDK


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [RESTO](media-services-rest-manage-entities.md)


Este tópico mostra-lhe como realizar as seguintes tarefas de gestão de serviços de multimédia:

- Obter uma referência de elementos 
- Obter uma referência de tarefa 
- Todos os recursos da lista 
- Lista tarefas e recursos 
- Lista todas as políticas de acesso 
- Listar todos os Locator
- Enumerar através de grandes colecções de entidades
- Eliminar um ativo 
- Eliminar uma tarefa 
- Eliminar uma política de acesso 

##<a name="prerequisites"></a>Pré-requisitos 

Consulte o artigo [configurar o seu ambiente](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>Obter uma referência de elementos

É uma tarefa frequente obter uma referência a um ativo existente dos serviços de multimédia. O exemplo de código seguinte mostra como fazer com que uma referência de elementos da colecção de activos no servidor objeto de contexto, com base num ativo ID.
Exemplo de código seguinte utiliza uma consulta de Linq para obter uma referência a um objeto IAsset existente.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>Obter uma referência de tarefa

Quando trabalha com o processamento de tarefas no código dos serviços de multimédia, que precisa com frequência para obter uma referência a uma tarefa existente com base num ID. Exemplo de código que se segue mostra como obter uma referência a um objeto in a partir da coleção de tarefas.
WarningWarning poderá ter de obter uma referência de tarefa ao iniciar uma tarefa de codificação de execução longa e precisar de verificar o estado da tarefa num tópico. Em casos assim, quando o método devolve a partir de um tópico, é necessário obter uma referência a uma tarefa atualizada.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>Todos os recursos da lista

À medida que o número de elementos de que tem no armazenamento cresce, é útil listar seus ativos. Exemplo de código que se segue mostra como iteração na coleção de activos no objecto de contexto do servidor. Com cada imobilizado, o exemplo de código também escreve alguns dos seus valores de propriedade da consola. Por exemplo, cada activo pode conter muitos ficheiros de multimédia. O exemplo de código escreve todos os ficheiros associados a cada recurso.



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>Lista tarefas e recursos

Uma tarefa relacionada importante é ativos de lista com a sua tarefa associada dos serviços de multimédia. Exemplo de código que se segue mostra-lhe como listar cada objeto in e, em seguida, para cada tarefa,-apresenta propriedades sobre a tarefa, todas as tarefas relacionadas com o, todos os recursos e todos os elementos de saída de entrada. O código neste exemplo pode ser útil para vários outras tarefas. Por exemplo, se pretender listar os ativos de saída de uma ou mais tarefas de codificação que executou anteriormente, este código mostra como aceder aos ativos de saída. Quando tiver uma referência a um recurso de saída, em seguida, pode entregar o conteúdo para outros utilizadores ou aplicações ao transferi-lo ou fornecer URLs. 

Para mais informações sobre as opções para fornecer elementos, consulte o artigo [Entregar ativos com o SDK de serviços de multimédia para .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>Lista todas as políticas de acesso

Serviços de multimédia, pode definir uma política de acesso num activo ou os respetivos ficheiros. Uma política de acesso define as permissões para um ficheiro ou de um ativo (que tipo de acesso e a duração). No seu código dos serviços de multimédia, pode, normalmente, definir uma política de acesso através da criação de um objeto de IAccessPolicy e, em seguida, associando-a com um activo existente. Em seguida, criar um objecto ILocator, que permite-lhe fornecer acesso direto aos ativos dos serviços de multimédia. O projeto do Visual Studio que acompanha desta série de documentação contém vários exemplos de código que mostram como criar e atribuir políticas de acesso e Locator para recursos.

Exemplo de código seguinte mostra como listar todas as políticas de acesso no servidor e mostra o tipo de permissões associadas com cada uma. Outra forma útil para ver as políticas de acesso é a lista todos os objetos de ILocator no servidor e, em seguida, para cada locator, pode lista sua política de acesso associado ao utilizar a respetiva propriedade AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>Listar todos os Locator

Um localizador de é um URL que fornece um caminho directo para aceder a um ativo, juntamente com as permissões ao activo conforme definido pelo política de acesso associado o localizador. Cada imobilizado pode ter uma coleção de objetos ILocator associado sua propriedade Locator. O contexto de servidor também tem uma coleção de Locator que contém todos os Locator.

Exemplo de código seguinte lista todos os Locator no servidor. Para cada locator,-mostra o Id de para a política de acesso e elementos relacionada. Também apresenta o tipo de permissões, a data de expiração e o caminho completo para o elemento.

Note que um caminho de locator para um recurso é um URL base para o elemento. Para criar um caminho direto para ficheiros individuais que um utilizador ou aplicação poderia procure, o código tem de adicionar o caminho do ficheiro específico para o caminho locator. Para mais informações sobre como fazer isto, consulte o tópico [Entregar ativos com o SDK de serviços de multimédia para .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerar através de grandes colecções de entidades

Quando consultar entidades, existe um limite de 1000 entidades devolvida uma só vez porque público resto v2 limita os resultados da consulta para os resultados de 1000. Tem de utilizar ignorar e tomar quando enumerar através de grandes colecções de entidades. 
    
A função seguinte ciclos através de todas as tarefas a conta de serviços de multimédia fornecida. Dos serviços de multimédia devolve trabalhos de 1000 na coleção de tarefas. A função garante a utilização de ignorar e tomar para se certificar de que todas as tarefas são enumerados (caso tiver mais de 1000 tarefas na sua conta).
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>Eliminar um ativo

O exemplo seguinte elimina um ativo.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>Eliminar uma tarefa

Para eliminar uma tarefa, tem de verificar o estado da tarefa como indicado na propriedade de estado. Tarefas que são concluídas ou canceladas podem ser eliminadas, enquanto tarefas que se encontram em determinados Estados como em fila de espera, agendada ou processamento, tem de ser canceladas pela primeira vez e, em seguida, podem ser eliminados.
Exemplo de código que se segue mostra um método para eliminar uma tarefa verificando Estados das tarefas e, em seguida, eliminar quando o estado é concluído ou cancelado. Este código depende da secção anterior neste tópico para obter uma referência a uma tarefa: obter uma referência de tarefa.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>Eliminar uma política de acesso

O exemplo de código seguinte mostra como obter uma referência a uma política de acesso com base numa política de Id e, em seguida, elimine a política.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
