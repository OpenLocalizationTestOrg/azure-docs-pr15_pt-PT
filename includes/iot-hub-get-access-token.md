## <a name="obtain-an-azure-resource-manager-token"></a>Obter um token de Gestor de recursos Azure

Azure do Active Directory tem de autenticar todas as tarefas que efectuar em recursos utilizando o Gestor de recursos Azure. O exemplo apresentado aqui utiliza a autenticação de palavra-passe, para outras abordagens consulte [pedidos de autenticação de Gestor de recursos de Azure][lnk-authenticate-arm].

1. Adicione o seguinte código para o método **Main** Program.cs para obter um token de AD Azure utilizando o id da aplicação e a palavra-passe.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Crie um objecto de **ResourceManagementClient** que utiliza o token, adicionando o seguinte código para o fim do método **principal** :

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Criar ou obter uma referência para o grupo de recursos que está a utilizar:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx