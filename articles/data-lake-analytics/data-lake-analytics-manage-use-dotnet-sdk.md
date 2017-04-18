<properties 
   pageTitle="Gerir Azure dados Lake Analytics utilizando Azure .NET SDK | Azure" 
   description="Saiba como gerir tarefas de dados Lake Analytics, origens de dados, os utilizadores. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gerir Azure dados Lake Analytics utilizando Azure .NET SDK

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas do Azure dados Lake Analytics, origens de dados, os utilizadores e tarefas utilizando o Azure .NET SDK. Para ver tópicos de gestão de utilizar outras ferramentas, clique no separador Seleccionar acima.

**Pré-requisitos**

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Ligar a dados Azure Lake Analytics

Irá precisar de pacotes Nuget seguintes:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


O código de exemplo seguinte mostra como ligar ao Azure e as contas de análise de Lake dados existentes com a sua subscrição Azure da lista.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gerir contas

Antes de executar qualquer trabalhos de análise de Lake de dados, tem de ter uma conta de análise de Lake de dados. Ao contrário Azure HDInsight, não pagar para uma conta de análise quando não está em execução uma tarefa.  Pode pagar apenas para o tempo quando estiver em execução uma tarefa.  Para mais informações, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Criar contas

Tem de ter um grupo de gestão de recursos do Azure e uma conta do arquivo de Lake dados antes de poder executar o exemplo seguinte.

O código seguinte mostra como criar um grupo de recursos:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

O código seguinte mostra como criar uma conta do arquivo de Lake dados:

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

O código seguinte mostra como criar uma conta de análise de Lake de dados:

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>Lista de contas

Consulte o artigo [ligar a dados Azure Lake Analytics](#connect_to_azure_data_lake_analytics).

###<a name="find-an-account"></a>Localizar uma conta

Depois de obter um objeto de uma lista de contas de análise de Lake de dados, pode utilizar o seguinte para encontrar uma conta:

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>Eliminar dados Lake Analytics contas

O fragmento de código seguinte elimina uma conta de análise de Lake de dados:

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerir origens de dados de conta

A análise de dados Lake suporta atualmente seguintes origens de dados:

- [Armazenamento de Lake dados Azure](../data-lake-store/data-lake-store-overview.md)
- [Armazenamento Azure](../storage/storage-introduction.md)

Quando cria uma conta de análise, tem de designar uma conta de armazenamento do Windows Azure dados Lake para ser a conta de armazenamento predefinido. A conta de arquivo de dados de Lake predefinida é utilizada para armazenar os registos de auditoria de metadados e de projeto de tarefa. Depois de ter criado uma conta de análise, pode adicionar contas adicionais de armazenamento de Lake de dados e/ou a conta de armazenamento do Windows Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Localizar a conta de dados Lake arquivo predefinida

Consulte o artigo localizar uma conta neste artigo para encontrar a conta de análise de Lake de dados. Em seguida, utilize o seguinte:

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Utilizar grupos de Gestor de recursos do Azure

Aplicações são normalmente constituídas por vários componentes, por exemplo uma aplicação web, base de dados, servidor de base de dados, armazenamento e 3º serviços de terceiros. Gestor de recursos do Azure permite-lhe trabalhar com os recursos na sua aplicação como um grupo, designado de um grupo de recursos do Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos para a sua aplicação numa operação de única e coordenada. Utilizar um modelo para implementação e esse modelo pode ser útil ambientes diferentes, tais como teste, teste e de produção. Pode clarificar faturação da sua organização, veja os custos de agregadas para todo o grupo. Para mais informações, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de dados Lake Analytics pode incluir os seguintes componentes:

- Conta de análise do Azure dados Lake
- Conta de armazenamento do Windows Azure dados Lake predefinidos necessários
- Contas de armazenamento adicional Azure dados Lake
- Contas adicionais de armazenamento do Windows Azure

Pode criar todos estes componentes num grupo de gestão de recursos para torná-las mais fácil de gerir.

![Conta de análise do Azure dados Lake e armazenamento](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta de análise de Lake de dados e as contas de armazenamento dependentes devem ser colocadas no Centro de dados Azure do mesmo.
O grupo de gestão de recursos no entanto pode estar num centro de dados diferentes.  

##<a name="see-also"></a>Consulte também 

- [Descrição geral da análise Lake de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Começar a trabalhar com dados Lake Analytics através do portal Azure](data-lake-analytics-get-started-portal.md)
- [Gerir Azure dados Lake Analytics através do portal Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorizar e resolver problemas de tarefas do Azure dados Lake Analytics através do portal Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

