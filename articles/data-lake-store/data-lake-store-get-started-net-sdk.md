<properties
   pageTitle="Utilizar dados Lake loja .NET SDK para desenvolver aplicações | Microsoft Azure"
   description="Utilizar o Azure dados Lake loja .NET SDK para desenvolver aplicações"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introdução ao Azure dados Lake loja utilizando .NET SDK

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)

Saiba como utilizar o [Azure dados Lake loja .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) para efetuar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, etc. Para mais informações sobre Lake de dados, consulte o artigo [Azure dados Lake loja](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* No **visual Studio 2013 ou 2015**. As instruções abaixo utilizam Visual Studio 2015.

* **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Conta azure dados Lake loja**. Para obter instruções sobre como criar uma conta, consulte o artigo [Introdução ao Azure dados Lake arquivo](data-lake-store-get-started-portal.md)

* **Criar uma aplicação do Azure Active Directory**. Utilize a aplicação do Azure AD para autenticar a aplicação de arquivo de Lake de dados com o Azure AD. Existem abordagens diferentes para autenticar com Azure AD, que são **autenticação de utilizador final** ou do **serviço de serviço**. Para obter instruções e mais informações sobre como autenticar, consulte o artigo [autenticar com o arquivo de dados de Lake utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar uma aplicação do .NET

1. Abrir o Visual Studio e crie uma aplicação de consola.

2. No menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.

3. A partir do **Novo projeto**, escreva ou selecione os seguintes valores:

  	| Propriedade | Valor                       |
  	|----------|-----------------------------|
  	| Categoria | Modelos/Visual c# / Windows |
  	| Modelo | Aplicação de consola         |
  	| Nome     | CreateADLApplication        |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes de Nuget ao seu projeto.

    1. O nome do projeto no Explorador de solução com o botão direito e clique em **Gerir pacotes de NuGet**.
    2. Na caixa de diálogo **Gestor de pacotes Nuget** , certifique-se de que a **origem do pacote** está definido para **nuget.org** e essa caixa de verificação **incluir a versão de pré-lançamento** está selecionada.
    3. Procurar e instalar pacotes NuGet seguintes:

        * `Microsoft.Azure.Management.DataLake.Store`-Este tutorial utiliza pré-visualização do v0.12.5.
        * `Microsoft.Azure.Management.DataLake.StoreUploader`-Este tutorial utiliza pré-visualização do v0.10.6.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-Este tutorial utiliza pré-visualização do v2.2.8.

        ![Adicionar uma origem de Nuget] (./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Criar uma nova conta Azure dados Lake")

    4. Feche o **Gestor de pacotes Nuget**.

6. Abrir **Program.cs**, eliminar o código existente e, em seguida, incluir as seguintes instruções para adicionar referências a espaços de nomes.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Declarar as variáveis, conforme apresentado abaixo e forneça os valores para o nome do arquivo de Lake de dados e o nome do grupo de recursos que já existam. Além disso, certifique-se o caminho local e o nome de ficheiro fornecidos aqui tem de existir no computador. Adicione o fragmento de código seguinte após as declarações de espaço de nomes.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Nas secções restantes do artigo, pode ver como utilizar os métodos de .NET disponíveis para efetuar operações, como autenticação, carregamento de ficheiro, etc.

## <a name="authentication"></a>Autenticação

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Se estiver a utilizar autenticação de utilizador final (recomendada para este tutorial)

Utilize esta opção com uma existente Azure AD "Native Client" aplicação; um é fornecido por si abaixo. Para ajudar a concluir este tutorial mais rápido, recomendamos que utilize esta abordagem.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Algumas coisas a saber sobre esta fragmento acima.

* Para ajudar a concluir a iniciação rápida, este fragmento utiliza uma um Azure AD ID de cliente e do domínio que está disponível por predefinição para todas as subscrições do Azure. Sim, pode **utilizar este fragmento como-está na sua aplicação**.
* No entanto, se pretender utilizar o seu próprio ID de cliente Azure AD domínio e de aplicação, tem de criar uma aplicação nativa do Azure AD e, em seguida, utilizar o domínio do Azure AD, ID de cliente e redirecionar URI para a aplicação que criou. Consulte o artigo [criar uma aplicação do Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) para obter instruções.

>[AZURE.NOTE] As instruções nas ligações de acima são para uma aplicação web do Azure AD. No entanto, os passos são exatamente o mesmo, mesmo se tiver optado por criar uma aplicação nativa cliente em vez disso. 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Se estiver a utilizar o serviço de serviço de autenticação com segredo de cliente 

O fragmento de seguinte pode ser utilizado para autenticar a aplicação que não sejam de forma interativa, utilizando o cliente tecla para um capital aplicação / serviço / secreta. Utilize esta opção com uma [aplicação de "aplicação Web" do Azure AD](../resource-group-create-service-principal-portal.md)de existente.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Se estiver a utilizar o serviço de serviço de autenticação com certificado

Tal como uma terceira opção, o fragmento de seguinte pode ser utilizado para autenticar a aplicação que não sejam de forma interativa, utilizando o certificado para um capital aplicação / serviço. Utilize esta opção com uma [aplicação de "aplicação Web" do Azure AD](../resource-group-create-service-principal-portal.md)de existente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Criar o cliente de objetos

O fragmento de seguinte cria o cliente de conta e o sistema de ficheiros de arquivo de Lake dados objetos, que são utilizados para emitir pedidos de serviço.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Listar todas as contas de arquivo de Lake dados dentro de uma subscrição

O fragmento que se segue enumera todas as contas de arquivo de Lake dados dentro de uma subscrição Azure determinada.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## <a name="create-a-directory"></a>Criar um diretório

A seguinte mostra o fragmento um `CreateDirectory` método que pode utilizar para criar um diretório dentro de uma conta do arquivo de Lake de dados.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Carregar um ficheiro

A seguinte mostra o fragmento uma `UploadFile` método que pode utilizar para carregar ficheiros para uma conta do arquivo de Lake de dados.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader`suporta recursiva carregar e transferir entre o caminho do ficheiro local e um caminho de ficheiro de arquivo de Lake de dados.    

## <a name="get-file-or-directory-info"></a>Obter informações do ficheiro ou do diretório

A seguinte mostra o fragmento um `GetItemInfo` método que pode utilizar para obter informações sobre um ficheiro ou directório disponível no arquivo de dados de Lake. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Ficheiro da lista ou directórios

A seguinte mostra o fragmento um `ListItem` método que pode utilizar para o ficheiro e directórios numa conta arquivo de Lake dados da lista.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenar ficheiros

A seguinte mostra o fragmento um `ConcatenateFiles` método que utilizar para concatenar ficheiros. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro

A seguinte mostra o fragmento um `AppendToFile` método que utilizar acrescentar dados a um ficheiro já armazenado numa conta dados Lake loja.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Transferir um ficheiro

A seguinte mostra o fragmento um `DownloadFile` método que utilizar para transferir um ficheiro de uma conta do arquivo de Lake de dados.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referência SDK do arquivo de Lake de dados .NET](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referência de resto do arquivo de Lake de dados](https://msdn.microsoft.com/library/mt693424.aspx)
