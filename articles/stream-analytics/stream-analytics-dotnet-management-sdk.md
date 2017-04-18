<properties
    pageTitle="Gestão .NET SDK para análise da cadeia | Microsoft Azure"
    description="Introdução ao sequência Analytics Management .NET SDK. Saiba como configurar e executar análises tarefas: criar um projeto, entradas, saídas e transformações."
    keywords=".NET SDK, analytics API"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gestão de .NET SDK: Configurar e executar análises tarefas utilizando a API do Azure da cadeia Analytics para .NET

Saiba como configurar um trabalhos de executar a análise utilizando a API de análise da cadeia para o .NET utilizando o .NET SDK gestão. Configurar um projeto, criar origens de entrada e saídas, transformações e iniciar e parar de tarefas. Para as tarefas de análise, é possível transmitir necessidade dados a partir do armazenamento de BLOBs ou de um concentrador de evento.

Consulte a [documentação de referência de gestão para a API de análise da cadeia para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure a análise da cadeia é um serviço totalmente gerido fornecer processamento de baixa latência, altamente disponível, dimensionáveis, complexa eventos através de transmissão de dados na nuvem. Análise da cadeia permite que os clientes configurar a transmissão de tarefas para analisar sequências de dados e permite-lhes unidade perto de análise em tempo real.  


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte procedimento:

- Instale o Visual Studio 2012http ou 2013.
- Transfira e instale o [Azure.NET SDK](https://azure.microsoft.com/downloads/).
- Crie um grupo de recursos do Azure na sua subscrição. Segue-se um exemplo de script Azure PowerShell. Para obter informações do Azure PowerShell, consulte o artigo [instalar e configurar o Azure PowerShell](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   Configure uma origem de entrada e de destino de saída para utilizar. Para mais instruções consulte o artigo [Adicionar entradas](stream-analytics-add-inputs.md) para configurar uma entrada de exemplo e [Adicionar saídas](stream-analytics-add-outputs.md) para configurar uma saída de exemplo.


## <a name="set-up-a-project"></a>Configurar um projecto

Para criar uma tarefa de análise utilize a API de análise da cadeia de .NET, configure primeiro o seu projeto.

1. Crie uma aplicação de consola do Visual Studio c# .NET.
2. Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes de NuGet. A primeira parte é o Azure da cadeia Analytics gestão .NET SDK. O segundo é cliente do Azure Active Directory que será utilizado para autenticação.

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Adicione a seguinte secção **appSettings** para o ficheiro App:

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    Substituir valores para **SubscriptionId** e **ActiveDirectoryTenantId** com a sua subscrição Azure e IDs de inquilinos. Pode obter estes valores, executando o seguinte cmdlet do Azure PowerShell:

        Get-AzureAccount

5. Adicione as seguintes instruções de **utilizar** para o ficheiro de origem (Program.cs) no projeto:

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  Adicione um método de autenticação de helper:

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(
                        ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                        ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AsaClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
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


## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gestão de análise da cadeia

Um objeto de **StreamAnalyticsManagementClient** permite-lhe gerir a tarefa e os componentes de tarefa, tal como entrada, saída e transformação.

Adicione o seguinte código para o início do método **principais** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Valor da variável **resourceGroupName** deve ser o mesmo que o nome do grupo de recursos que criou ou que escolheu nos passos pré-requisitos.

Para automatizar o aspecto de apresentação de credenciais da criação de emprego, consulte [autenticar um principal de serviço com o Gestor de recursos do Azure](../resource-group-authenticate-service-principal.md).

As secções restantes neste artigo presumem que este código está no início do método **principais** .

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa de análise da cadeia

O código seguinte cria uma tarefa de análise da cadeia em grupo de recursos que definiu. Irá adicionar uma entrada, saída e transformação para a tarefa mais tarde.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Criar uma origem de entrada da cadeia Analytics

O código seguinte cria uma origem de entrada de análise da cadeia com o tipo de origem de entrada de BLOBs e serialização CSV. Para criar uma origem de entrada de concentrador de evento, utilize **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, pode personalizar o tipo de serialização da origem de entrada.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Origens de entrada, se a partir do armazenamento de BLOBs ou um concentrador de evento, estão associadas a uma tarefa específica. Para utilizar a mesma origem de entrada para tarefas diferentes, tem de chamar o método novamente e especifique um nome de tarefa diferente.


## <a name="test-a-stream-analytics-input-source"></a>Testar uma origem de entrada da cadeia Analytics

O método de **TestConnection** testa se a tarefa de análise da cadeia é possível ligar a origem de entrada, bem como outros aspetos específicos para o tipo de origem de entrada. Por exemplo, na blob origem de entrada que criou num passo anterior, o método verificará que o nome de conta de armazenamento e a par de chaves pode ser utilizados para ligar à conta de armazenamento, bem como Verifique se existe contentor especificado.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Criar um alvo de saída da análise da cadeia

Criar um alvo de saída é muito semelhante a criação de uma origem de entrada de análise da cadeia. Como origens de entrada, saída destinos estão associados a uma tarefa específica. Para utilizar o mesmo destino de saída para tarefas diferentes, tem de chamar o método novamente e especifique um nome de tarefa diferente.

O código seguinte cria um alvo de saída (base de dados do Azure SQL). Pode personalizar o tipo de dados de destino de saída e/ou o tipo de serialização.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Testar um alvo de saída da análise da cadeia

Um alvo de saída da análise da cadeia também tem o método de **TestConnection** para testar ligações.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Crie uma transformação de análise da cadeia

O código seguinte cria uma transformação de análise da cadeia com a consulta "selecionar * de entrada" e especifica para atribuir uma unidade de transmissão para a tarefa de análise da cadeia. Para mais informações sobre como ajustar a transmissão de unidades, consulte o artigo [tarefas de escala Azure da cadeia Analytics](stream-analytics-scale-jobs.md).


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Como entrada e saída, uma transformações também está associada à tarefa de análise da cadeia específico foi criada em.

## <a name="start-a-stream-analytics-job"></a>Iniciar uma tarefa de análise da cadeia
Depois de criar uma tarefa de análise da cadeia e respetivo input(s), output(s) e transformação, pode começar a tarefa ao contactar o método **Start** .

A seguinte de exemplo começa código uma tarefa de análise da cadeia com uma hora de início de saída personalizado definido para 12 de Dezembro de 2012, 12:12:12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>Parar uma tarefa de análise da cadeia
Pode deixar de uma tarefa de análise da cadeia em execução ao contactar o método **Parar** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Eliminar uma tarefa de análise da cadeia
O método **Delete** irá eliminar a tarefa, bem como recursos subjacentes subpastas, incluindo input(s), output(s) e transformação da tarefa.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximos passos

Tiver os princípios básicos para utilizar um .NET SDK para criar e executar tarefas de análise de formação. Para saber mais, consulte o seguinte:

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
