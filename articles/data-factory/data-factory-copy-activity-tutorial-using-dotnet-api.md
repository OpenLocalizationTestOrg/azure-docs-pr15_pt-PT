<properties 
    pageTitle="Tutorial: Criar uma tubagem com cópia atividade utilizando .NET API | Microsoft Azure" 
    description="Neste tutorial, crie um pipeline de fábrica do Azure dados com uma atividade de cópia ao utilizar a .NET API." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Criar uma tubagem com cópia atividade utilizando .NET API
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copiar o Assistente](data-factory-copy-data-wizard-tutorial.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelo do Gestor de recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados Azure utilizando a API do .NET. A tubagem na fábrica dados utiliza uma atividade de copiar para copiar dados de armazenamento de Blobs do Azure para base de dados do SQL Azure.

A atividade de cópia executa o movimento de dados no Azure fábrica de dados. A atividade é recorrendo a um serviço disponível globalmente que pode copiar dados de entre vários arquivos de dados de forma segura, fiável e dimensionáveis. Consulte o artigo [Dados movimento atividades](data-factory-data-movement-activities.md) artigo para obter detalhes sobre a atividade de cópia.   

> [AZURE.NOTE] 
> Este artigo abrange todos os dados fábrica .NET API. Consulte o artigo [Referência da linguagem fábrica .NET API](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre dados fábrica .NET SDK. 

## <a name="prerequisites"></a>Pré-requisitos
- Aceda a [Descrição geral do Tutorial e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma descrição geral da iniciação e concluir os passos de **pré-requisito** . 
- Visual Studio 2012http ou 2013 ou 2015
- Transferir e instalar o [Azure.NET SDK](http://azure.microsoft.com/downloads/)
- Azure PowerShell. Siga as instruções no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar o Azure PowerShell no seu computador. Utilizar o Azure PowerShell para criar uma aplicação do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar uma aplicação no Azure Active Directory
Criar uma aplicação do Azure Active Directory, criar um principal para a aplicação de serviço e atribuí-lo para a função **Contribuinte fábrica de dados** .  

1. Inicie o **PowerShell**. 
1. Execute o seguinte comando e introduza o nome de utilizador e palavra-passe que utiliza para iniciar sessão portal do Azure.
    
        Login-AzureRmAccount   
2. Execute o seguinte comando para ver todas as subscrições para esta conta.

        Get-AzureRmSubscription 
3. Execute o seguinte comando para selecionar a subscrição à qual pretende trabalhar com. Substituir ** &lt;NameOfAzureSubscription** &gt; com o nome da sua subscrição do Azure. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Nota para baixo **SubscriptionId** e **TenantId** a partir dos resultados deste comando. 
4. Crie um grupo de recursos Azure denominado **ADFTutorialResourceGroup** executando o seguinte comando no PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Se já existe um grupo de recursos, especifique se pretende atualizar (Y) ou mantê-la como (N). 

    Se utilizar um grupo de recursos diferentes, terá de utilizar o nome do seu grupo de recursos em vez de ADFTutorialResourceGroup neste tutorial.
5. Crie uma aplicação do Azure Active Directory. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Se receber a seguinte mensagem de erro, especifique um URL diferente e volte a executar o comando. 

        Another object with the same value for property identifierUris already exists.

6. Crie o AD principal de serviço. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Adicione principal do serviço para a função **Contribuinte fábrica de dados** . 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Obter o ID da aplicação.

        $azureAdApplication

    Anote o ID da aplicação (**applicationID** a partir dos resultados).

Deverá ter seguir quatro valores a partir destes passos: 

- ID de inquilino
- ID da subscrição
- ID da aplicação 
- Palavra-passe (especificado no primeiro comando)   

## <a name="walkthrough"></a>Instruções passo a passo
1. Utilizar o Visual Studio 2012/2013/2015, crie uma aplicação de consola c# .NET.
    1. Inicie o **Visual Studio** 2012/2013/2015.
    2. Clique em **ficheiro**, aponte para **Novo**e clique em **projeto**.
    3. Expanda **modelos**e selecione **Visual c#**. Neste tutorial, utiliza c#, mas pode utilizar qualquer idioma .NET.
    4. Selecione a **Aplicação de consola** a partir da lista de tipos de projecto no lado direito.
    5. Introduza **DataFactoryAPITestApp** para o nome.
    6. Selecione **C:\ADFGetStarted** para a localização.
    7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **O Gestor de pacotes Nuget**e clique em **Consola do Gestor de pacote**.
3.  Na **Consola do Gestor de pacote**, efetue os seguintes passos: 
    1.  Execute o seguinte comando para instalar o pacote de fábrica de dados:`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Execute o seguinte comando para instalar o pacote do Azure Active Directory (caso utilize API do Active Directory no código):`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Adicione a seguinte secção **appSetttings** para o ficheiro **App** . Estas definições são utilizadas pelo método helper: **GetAuthorizationHeader**. 

    Substituir valores para ** &lt;ID da aplicação&gt;**, ** &lt;palavra-passe&gt;**, ** &lt;ID da subscrição&gt;**, e ** &lt;ID de inquilinos&gt; ** com os seus próprios valores. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Adicione as seguintes instruções de **utilizar** para o ficheiro de origem (Program.cs) no projeto.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Adicione o seguinte código que cria uma instância de classe **DataPipelineManagementClient** para o método **principal** . Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados de entrada e saídos e uma tubagem. Também utilizar este objeto para monitorizar os setores do gráfico de um conjunto de dados em tempo de execução.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Substitua o valor de **resourceGroupName** com o nome do seu grupo de recursos Azure. 
    > 
    > Atualize o nome da fábrica de dados (**dataFactoryName**) para ser exclusivo. Nome da fábrica dados tem de ser exclusivo global. Consulte o tópico de [Dados Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefactos fábrica de dados. 

7. Adicione o seguinte código que cria uma **fábrica de dados** para o método **principal** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Adicione o seguinte código que cria um **armazenamento do Windows Azure ligadas serviço** para o método **principal** . 

    > [AZURE.IMPORTANT] Substitua **storageaccountname** e **accountkey** nome e a chave da sua conta de armazenamento do Windows Azure. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Adicione o seguinte código que cria uma **Azure SQL ligadas serviço** para o método **principal** .
 
    > [AZURE.IMPORTANT] Substitua **nomedoservidor**, **databasename**, **nome de utilizador**e **palavra-passe** pelos nomes dos seus Azure SQL server, base de dados, o utilizador e palavra-passe.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Adicione o seguinte código que cria **conjuntos de dados de entrada e saídos** para o método **principal** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Adicione o seguinte código que **cria e ativa uma tubagem** para o método **principal** . Este pipeline tem **CopyActivity** que retira **BlobSource** como uma origem e **BlobSink** como um sink.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Adicione o seguinte código ao método **principais** para obter o estado de um setor de dados do conjunto de dados de saída. Existe apenas setor esperado neste exemplo.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Adicione o código seguinte para obter executar detalhes de um setor de dados para o método **principal** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Adicione o seguinte método de helper utilizado pelo método **principal** para a classe de **programa** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


15. No Explorador de solução, expanda o projecto (**DataFactoryAPITestApp**), com o botão direito **referências**e clique em **Adicionar referência**. Selecione a caixa de verificação "**System.Configuration**" assemblagem e clique em **OK**. 
16. Crie a aplicação da consola. Clique em **Criar** no menu e clique em **Criar solução**. 
16. Confirme se existe pelo menos um ficheiro no contentor **adftutorial** no seu armazenamento de Blobs do Azure. Caso contrário, crie **Emp.txt** ficheiro no bloco de notas com o seguinte conteúdo e carregue-o para o contentor adftutorial.

        John, Doe
        Jane, Doe
     
17. Executar o exemplo ao clicar em **Depurar** -> **Depuração de iniciar** no menu. Quando vir a **Introdução executar detalhes de um setor de dados**, aguarde alguns minutos e prima **ENTER**. 
18. Utilize o portal do Azure para verificar que a fábrica de dados **APITutorialFactory** é criado com seguintes erros: 
    - Ligadas serviço: **LinkedService_AzureStorage** 
    - Conjunto de dados: **DatasetBlobSource** e **DatasetBlobDestination**.
    - Em curso: **PipelineBlobSample** 
18. Certifique-se de que os registos dos duas empregados são criados na tabela "**emp**" na base de dados Azure SQL especificada.

## <a name="next-steps"></a>Próximos passos

- Leia o artigo [Atividades de movimento de dados](data-factory-data-movement-activities.md) , que fornece informações detalhadas sobre a atividade de cópia utilizados no tutorial.
- Consulte o artigo [Referência da linguagem fábrica .NET API](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre dados fábrica .NET SDK. Este artigo abrange todos os dados fábrica .NET API. 

 
