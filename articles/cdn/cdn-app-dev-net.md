<properties
    pageTitle="Introdução ao Azure CDN biblioteca para .NET | Microsoft Azure"
    description="Saiba como escrever aplicações .NET para gerir CDN Azure utilizando o Visual Studio."
    services="cdn"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introdução ao desenvolvimento do Azure CDN

> [AZURE.SELECTOR]
- [NODE.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Pode utilizar a [Biblioteca de CDN Azure para .NET](https://msdn.microsoft.com/library/mt657769.aspx) para automatizar a criação e gestão de perfis CDN e os pontos finais.  Neste tutorial explica a criação de uma aplicação de consola .NET simple que demonstra diversas as operações disponíveis.  Neste tutorial não se destina para descrever todos os aspetos da biblioteca de CDN Azure para .NET detalhadamente.

Precisa de Visual Studio 2015 para concluir este tutorial.  [Visual Studio Comunidade 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) está livre disponível para transferência.

> [AZURE.TIP] A [conclusão do projeto a partir deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) está disponível para transferência no MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Criar o seu projeto e adicionar pacotes Nuget

Agora que recomendamos ter criado um grupo de recursos para os nossos perfis CDN e tendo em conta os nossos permissão de aplicação do Azure AD para gerir perfis de CDN e os pontos finais dentro do grupo, podemos podem começar a criar a nossa aplicação.

No Visual Studio 2015, clique em **ficheiro**, **Novo**, **Project …** para abrir a caixa de diálogo novo projeto.  Expanda **Visual c#**, em seguida, selecione **Windows** no painel à esquerda.  Clique em **Aplicação da consola** no painel central.  Nome do seu projeto, em seguida, clique em **OK**.  

![Novo projeto](./media/cdn-app-dev-net/cdn-new-project.png)

Os nossos projeto vai utilizar algumas bibliotecas Azure contidas Nuget pacotes.  Vamos adicioná-los ao projeto.

1. Clique no menu **Ferramentas** , **Gestor de pacotes Nuget**, em seguida, **Consola do Gestor de pacote**.

    ![Gerir pacotes de Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. Na consola do Gestor de pacotes, execute o seguinte comando para instalar a **Biblioteca de autenticação do Active Directory (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Execute o seguinte procedimento para instalar a **Biblioteca de gestão do Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Diretivas do, constantes, método principal e métodos de ajuda

Vamos começar a estrutura básica do nosso programa escrito.

1. Novamente no separador Program.cs, substitua a `using` diretivas na parte superior com o seguinte:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

2. Precisamos de definir algumas constantes que irão utilizar os nossos métodos.  No `Program` classe, mas antes de `Main` método, adicione o seguinte.  Certifique-se de que substitua os marcadores de posição, incluindo o ** &lt;parênteses em ângulo&gt;**, com os seus próprios valores conforme necessário.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Também ao nível de classe, defina estas duas variáveis.  Vamos utilizar estes mais tarde para determinar o nosso perfil e o ponto final já existirem.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```

4.  Substituir o `Main` método da seguinte forma:

    ```csharp
    static void Main(string[] args)
    {
        //Get a token
        AuthenticationResult authResult = GetAccessToken();

        // Create CDN client
        CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
            { SubscriptionId = subscriptionId };

        ListProfilesAndEndpoints(cdn);

        // Create CDN Profile
        CreateCdnProfile(cdn);

        // Create CDN Endpoint
        CreateCdnEndpoint(cdn);
        
        Console.WriteLine();

        // Purge CDN Endpoint
        PromptPurgeCdnEndpoint(cdn);

        // Delete CDN Endpoint
        PromptDeleteCdnEndpoint(cdn);

        // Delete CDN Profile
        PromptDeleteCdnProfile(cdn);

        Console.WriteLine("Press Enter to end program.");
        Console.ReadLine();
    }
    ```

5. Alguns dos outros métodos de nossos estão a passar para pedir ao utilizador com questões "Sim/não".  Adicione o seguinte método para fazer com que um pouco mais fácil:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Agora que a estrutura básica do nosso programa escrita, criamos os métodos chamados pela `Main` método.

## <a name="authentication"></a>Autenticação

Antes de podemos utilizar a biblioteca de gestão de CDN Azure, precisamos de autenticar nosso principal do serviço e obtenha um token de autenticação.  Este método utiliza ADAL para obter o token.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Se estiver a utilizar autenticação de utilizador individual, a `GetAccessToken` método será o aspeto ligeiramente diferente.

>[AZURE.IMPORTANT] Utilize este exemplo de código apenas se estiver a escolher ter autenticação de utilizador individual em vez de um serviço principal.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Certifique-se de que substituir `<redirect URI>` com o redirecionamento URI que introduziu quando registado a aplicação no Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Perfis CDN de lista e os pontos finais

Agora podemos está prontos efetuar operações de CDN.  A primeira coisa nosso método é que é a lista de todos os perfis e os pontos finais no nosso grupo de recursos e, se encontrar uma correspondência para os nomes de perfil e ponto final especificado no nossas constantes, faz com que uma nota de que mais tarde para que não vamos tentar criar duplicados.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis de CDN e os pontos finais

Em seguida, vamos criar um perfil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Assim que o perfil é criado, vamos criar um ponto final.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

>[AZURE.NOTE] O exemplo acima atribui o ponto final de uma origem de origem com o nome *Contoso* com um nome de anfitrião `www.contoso.com`.  Deverá alterar isto para apontarem para o nome do anfitrião seu próprio origem.

## <a name="purge-an-endpoint"></a>Limpar um ponto final

Partindo do princípio de que foi criado o ponto final, a uma tarefa comuns que poderá queremos para executar no nosso programa é limpar o conteúdo no nosso ponto final.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

>[AZURE.NOTE] No exemplo acima, a cadeia `/*` indica que quiser remover tudo na raiz do caminho de ponto final.  Este é equivalente a verificação **Remover todos** na caixa de diálogo de "Remover" o portal Azure. No `CreateCdnProfile` método, criado o nosso perfil como um perfil de **CDN Azure a partir do Verizon** utilizando o código `Sku = new Sku(SkuName.StandardVerizon)`, para que este será efetuada com êxito.  No entanto, perfis de **CDN Azure a partir do Akamai** não suportam **Limpar todos os**, para que se estava a utilizar um perfil Akamai para este tutorial, é seria necessário incluir caminhos específicos para limpar.

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis CDN e os pontos finais

Os métodos de últimos irão eliminar os nossos ponto final e o perfil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Executar o programa

Agora que possamos compilar e executar o programa ao clicar no botão **começar** no Visual Studio.

![Programa a executar](./media/cdn-app-dev-net/cdn-program-running-1.png)

Quando o programa atinge à linha de comandos acima, deverá conseguir regressar ao seu grupo de recursos no portal do Azure e consulte o artigo que foi criado o perfil.

![Sucesso!](./media/cdn-app-dev-net/cdn-success.png)

Em seguida, podemos pode confirmar os pedidos para executar o resto do programa.

![Concluir o programa](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Próximos passos

Para ver concluído projeto a partir destas instruções, [Transfira a amostra](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Para localizar a documentação adicional sobre a biblioteca de gestão de CDN Azure para .NET, visualize a [referência no MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Gerir os seus recursos CDN com [PowerShell](./cdn-manage-powershell.md).


