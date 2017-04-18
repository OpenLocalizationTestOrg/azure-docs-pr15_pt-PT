<properties
    pageTitle="Experimente a base de dados SQL: Utilizar c# para criar uma base de dados do SQL | Microsoft Azure"
    description="Experimente a base de dados SQL para desenvolver aplicações SQL e c# e criar uma base de dados do SQL Azure com c# utilizando a biblioteca de base de dados SQL para .NET."
    keywords="Experimente sql, c de sql#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="sstein"/>

# <a name="try-sql-database-use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>Experimente a base de dados SQL: Utilizar c# para criar uma base de dados do SQL com a biblioteca de base de dados SQL para .NET


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Saiba como utilizar c# para criar uma base de dados do Azure SQL com a [Biblioteca de gestão do Microsoft Azure SQL para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Este artigo descreve como criar uma base de dados única com SQL e c#. Para criar conjuntos de dados da base de dados flexível, consulte o artigo [criar um agrupamento de base de dados flexível](sql-database-elastic-pool-create-csharp.md).

A biblioteca de gestão de base de dados SQL Azure para .NET fornece um [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)-com base API que é moldado [API REST do base de dados SQL com base no Gestor de recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] Muitas funcionalidades novas da base de dados SQL só são suportadas quando está a utilizar o [modelo de implementação do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md), pelo que deve utilizar sempre as mais recentes **biblioteca de gestão do Azure SQL da base de dados para o .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet pacote](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Mais antigos [implementação clássica modelo baseado bibliotecas](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) são suportadas para retrocompatibilidade apenas, para que recomendamos que utilize as bibliotecas de Gestor de recursos com base mais recentes.

Para concluir os passos neste artigo, precisa do seguinte:

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure simplesmente clique **Conta gratuita** na parte superior desta página e, em seguida, regresse para concluir deste artigo.
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página de [Transferências do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

>[AZURE.NOTE] Este artigo cria uma base de dados SQL do novo em branco. Modificar o método de *CreateOrUpdateDatabase(...)* no exemplo seguinte para copiar as bases de dados, dimensionar bases de dados, criar uma base de dados num conjunto, etc. Para mais informações, consulte o artigo classes [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) e [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx) .



## <a name="create-a-console-app-and-install-the-required-libraries"></a>Criar uma aplicação de consola e instale as bibliotecas necessárias

1. Inicie o Visual Studio.
2. Clique em **ficheiro** > **Novo** > **projeto**.
3. Crie um c# **Aplicação da consola** e o nome: *SqlDbConsoleApp*


Para criar uma base de dados do SQL com c#, carrega as bibliotecas de gestão necessária (utilizando a [consola do Gestor de pacote](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Clique em **Ferramentas** > **Gestor de pacotes NuGet** > **Consola do Gestor de pacote**.
2. Tipo de `Install-Package Microsoft.Azure.Management.Sql –Pre` para instalar a mais recente de [Biblioteca de gestão do Microsoft Azure SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Tipo de `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` para instalar a [Biblioteca do Microsoft Azure Gestor de recursos](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Tipo de `Install-Package Microsoft.Azure.Common.Authentication –Pre` para instalar a [Biblioteca de autenticação do Microsoft Azure comuns](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Os exemplos neste artigo utilizam um formulário de síncrono de cada pedido API e bloquear até conclusão do resto chamar o serviço de subjacente. Existem assíncrona métodos disponíveis.


## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>Criar uma base de dados SQL server, regra de firewall e base de dados do SQL - c# exemplo

O exemplo seguinte cria um grupo de recursos, servidor, regra de firewall e uma base de dados do SQL. Ver, [criar um serviço principal para aceder a recursos](#create-a-service-principal-to-access-resources) para obter o `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variáveis.

Substitua o conteúdo da **Program.cs** com o seguinte e atualizar a `{variables}` com os valores de aplicação (não inclua a `{}`).


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Criar um serviço principal para aceder a recursos

O seguinte script do PowerShell cria a aplicação do Active Directory (AD) e o capital de serviço que precisamos de autenticar a nossa aplicação c#. O script exporta valores que precisamos para c# exemplo anterior. Para obter informações detalhadas, consulte o artigo [Utilizar o PowerShell Azure para criar um serviço principal para aceder a recursos](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>Próximos passos
Agora que já tiver experimentado base de dados SQL e configurar uma base de dados com o c#, está pronto para os seguintes artigos:

- [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Classe de base de dados](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)




<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png
