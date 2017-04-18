<properties
    pageTitle="Azure Active Directory B2C: Utilizar o API do gráfico | Microsoft Azure"
    description="Como ligar a API do gráfico para um inquilino B2C através de uma identidade de aplicação para automatizar o processo."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C: Utilizar o API do gráfico

Azure Active Directory (Azure AD) B2C inquilinos tendem para ser muito grandes. Isto significa que muitas tarefas de gestão comuns do inquilino têm de ser efetuados através de programação. Um exemplo de principal é a gestão de utilizadores. Poderá ter de migrar um arquivo existente do utilizador para um inquilino B2C. Poderá querer alojar o registo de utilizador na sua página e criar contas de utilizador no Azure AD nos bastidores. Estes tipos de tarefas que requerem a capacidade de criar, ler, atualizar e eliminar contas de utilizador. O que pode fazer estas tarefas ao utilizar a API do Azure AD Graph.

Para B2C inquilinos, existem dois modos principais de comunicar com a API do gráfico.

- Tarefas interativo, executar uma vez, deve actuar como uma conta de administrador no inquilino B2C ao efetuar as tarefas. Este modo requer um administrador iniciar sessão com as credenciais antes desse administrador pode desempenhar todas as chamadas para a API do gráfico.
- Para executar tarefas contínuas automatizadas, deve utilizar algum tipo de conta de serviço que forneça com os privilégios necessários para efetuar tarefas de gestão. No Azure AD, pode fazê-lo por registar uma aplicação e autenticar Azure AD. Isto é feito utilizando um **ID da aplicação** que utiliza a [conceder OAuth 2.0 credenciais do cliente](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Neste caso, a aplicação age como propriamente dito, não como um utilizador, para ligar a API do gráfico.

Neste artigo, vamos abordar como efetuar o caso de utilização automática. Para demonstrar, irá criamos uma 4,5 .NET `B2CGraphClient` que executa o utilizador criar, ler, atualizar e eliminar (CRUD) operações. O cliente vai ter uma interface da linha de comandos do Windows (CLI) que permite-lhe invocar vários métodos. No entanto, o código é escrito se comportam de forma não interactiva, automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um inquilino do Azure AD B2C

Antes de poder criar aplicações ou utilizadores, ou interagir com Azure AD sequer, terá de um inquilino do Azure AD B2C e uma conta de administrador global no inquilino. Se não tiver um inquilino já, [Introdução ao Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Registar uma aplicação de serviço no seu inquilino

Depois de ter um inquilino B2C, tem de criar a sua aplicação de serviço, utilizando os cmdlets do PowerShell do Azure AD.
Em primeiro lugar, transfira e instale o [Assistente Microsoft Online Services iniciar sessão](http://go.microsoft.com/fwlink/?LinkID=286152). Em seguida, transfira e instale o [módulo Azure Active Directory de 64 bits para o Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Para utilizar a API Graph com o seu inquilino B2C, terá de registar uma aplicação do dedicado utilizando o PowerShell. Siga as instruções neste artigo para o fazer. Não é possível reutilizar as aplicações B2C já existente, que registado no portal do Azure.

Depois de instalar o módulo PowerShell, abra o PowerShell e ligar ao seu inquilino B2C. Depois de executar `Get-Credential`, vai ser-lhe para um nome de utilizador e palavra-passe, introduza o nome de utilizador e palavra-passe da sua conta de administrador de inquilino B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de criar a sua aplicação, é necessário gerar um novo **segredo cliente**.  A aplicação irá utilizar o segredo do cliente para autenticar Azure AD e adquirir tokens de acesso. Pode gerar uma palavra-passe válida no PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

O comando final deverá imprimir a sua nova palavra-passe cliente. Copiá-la num local seguro. Terá de-la mais tarde. Agora pode criar a sua aplicação ao fornecer a nova palavra-passe cliente como credenciais para a aplicação:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Se criar a aplicação com êxito, deverá imprimir as propriedades da aplicação como as apresentadas acima. Terá de ambos `ObjectId` e `AppPrincipalId`, por isso, copie esses valores também.

Depois de criar uma aplicação no seu inquilino B2C, tem de atribuir-lhe as permissões que precisa para efectuar operações CRUD do utilizador. Atribuir as três funções da aplicação: leitores de diretório (para ler os utilizadores), escritores de diretório (para criar e actualizar utilizadores) e um administrador de conta de utilizador (para eliminar utilizadores). Estas funções têm famoso identificadores, pelo que pode substituir o `-RoleMemberObjectId` parâmetro com o `ObjectId` dos acima e execute os seguintes comandos. Para ver a lista de todas as funções de diretório, tente executar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Agora tem uma aplicação de que tem permissão para criar, ler, atualizar e eliminar utilizadores a partir do seu inquilino B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Transferir, configurar e criar o código de exemplo

Em primeiro lugar, transfira o código de exemplo e obtê-lo em execução. Em seguida, podemos irá demorar mais detalhada-lo.  Pode [Transferir o código de exemplo como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Também pode clonar para um diretório da sua escolha:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abrir o `B2CGraphClient\B2CGraphClient.sln` solução Visual Studio no Visual Studio. No `B2CGraphClient` do projeto, abra o ficheiro `App.config`. Substitua as definições de três aplicação com os seus próprios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, com o botão direito na `B2CGraphClient` solução e reconstruir da amostra. Se estiver com êxito, deve tem agora um `B2C.exe` ficheiro executável localizado na `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Compilar operações CRUD do utilizador ao utilizar a API do gráfico

Para utilizar o B2CGraphClient, abra uma `cmd` Windows comando pedir-lhe e alterar o seu diretório para o `Debug` diretório. Em seguida, execute o `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Isto vai mostrar uma breve descrição de cada comando. Sempre que é apresentado um destes comandos `B2CGraphClient` faz um pedido para a API do Azure AD Graph.

### <a name="get-an-access-token"></a>Obter um token de acesso

Qualquer pedido à Graph API requer um token de acesso para a autenticação. `B2CGraphClient`utiliza o Active Directory autenticação biblioteca (ADAL) abrir origem para o ajudar a adquirir tokens de acesso. ADAL facilita aquisição token, fornecendo um API simple e tendo o cuidado de alguns detalhes importantes, tal como a colocação em cache tokens de acesso. Não tem de utilizar ADAL para obter tokens, embora. Também pode obter tokens criando intencionada pedidos de HTTP.

> [AZURE.NOTE]
    Este exemplo de código utiliza ADAL v2 para comunicar com a API do gráfico.  Tem de utilizar ADAL v2 ou v3 para poder obter tokens de acesso que podem ser utilizados com a API do Azure AD Graph.

Quando `B2CGraphClient` é executado, que cria uma instância da `B2CGraphClient` escolares. O construtor para esta classe configura um andaimes autenticação ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos utilizar a `B2C Get-User` comando de exemplo. Quando `B2C Get-User` é chamado sem qualquer entradas adicionais, as chamadas de clip de `B2CGraphClient.GetAllUsers(...)` método. Este método chamadas `B2CGraphClient.SendGraphGetRequest(...)`, que submeta um pedido HTTP GET à Graph API. Antes de `B2CGraphClient.SendGraphGetRequest(...)` envia introdução pedir, -la pela primeira vez obtém um acesso token utilizando ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Pode obter um acesso token para a API do gráfico ao contactar o suporte de ADAL `AuthenticationContext.AcquireToken(...)` método. ADAL, em seguida, devolve um `access_token` que representa a identidade da aplicação.

### <a name="read-users"></a>Leia os utilizadores

Quando quiser obter uma lista de utilizadores ou obter um determinado utilizador a partir da API do gráfico, pode enviar uma HTTP `GET` pedir para o `/users` ponto final. Um pedido para todos os utilizadores num inquilino tem o seguinte aspeto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver este pedido, execute:

 ```
 > B2C Get-User
 ```

Existem duas coisas importantes para tenha em atenção:

- O token de acesso adquirido através de ADAL é adicionado à `Authorization` cabeçalho utilizando o `Bearer` esquema.
- Para B2C inquilinos, tem de utilizar o parâmetro de consulta `api-version=1.6`.

Ambos estes detalhes são processados no `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Criar contas de utilizador do consumidor

Quando criar contas de utilizador no inquilino do B2C, pode enviar uma HTTP `POST` pedir para o `/users` ponto final:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A maior parte destas propriedades neste pedido é necessários para criar utilizadores do consumidor. Para obter mais informações, clique [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Tenha em atenção que a `//` comentários que foram incluídos para ilustração. Não inclua-los num pedido de real.

Para ver o pedido, execute um dos seguintes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando leva-o até um ficheiro de .json como um parâmetro de entrada. Este contém uma representação JSON de um objeto de utilizador. Existem dois exemplos de ficheiros de .json no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. Pode modificar estes ficheiros conforme as suas necessidades. Para além dos campos obrigatórios acima, vários campos opcionais que pode utilizar são incluídos nestes ficheiros. Para obter detalhes sobre os campos opcionais podem encontrar na [referência da entidade API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Pode ver como o pedido POST é construídos com base em `B2CGraphClient.SendGraphPostRequest(...)`.

- Anexa um token de acesso para a `Authorization` cabeçalho do pedido.
- Define `api-version=1.6`.
- Inclui o objeto de utilizador JSON no corpo do pedido.

> [AZURE.NOTE]
Se as contas que pretende migrar a partir de um arquivo existente do utilizador tiver inferior rutura de palavra-passe que a [intensidade da palavra-passe segura imposta por Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), pode desativar o requisito de palavra-passe segura utilizando o `DisableStrongPassword` valor na `passwordPolicies` propriedade. Por exemplo, pode modificar o pedido do utilizador criar fornecido acima da seguinte forma: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Atualizar as contas de utilizador do consumidor

Quando atualiza os objetos de utilizador, o processo é semelhante ao que utilizar para criar objetos de utilizador. Mas este processo utiliza o protocolo HTTP `PATCH` método:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Experimente atualizar um utilizador ao atualizar os seus ficheiros JSON por novos dados. Em seguida, pode utilizar `B2CGraphClient` para executar um destes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecionar a `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar este pedido.

### <a name="search-users"></a>Utilizadores de pesquisa

Pode procurar utilizadores no seu inquilino B2C de duas maneiras. Uma, utilizando o utilizador objeto ID ou dois, utilizando início de sessão no identificador o utilizador (por exemplo, o `signInNames` propriedade).

Execute um dos seguintes comandos para procurar um utilizador específico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Aqui estão algumas dos exemplos:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminar utilizadores

O processo para eliminar um utilizador é simples. Utilizar o protocolo HTTP `DELETE` método e de construção o URL com o correta de ID do objeto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, introduza este comando e veja o pedido de eliminar é impresso na consola:

```
> B2C Delete-User <object-id-of-user>
```

Inspecionar a `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar este pedido.

Pode efetuar muitas outras ações com a API do Azure AD Graph para além de gestão de utilizadores. A [referência da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com pedidos de exemplo.

## <a name="use-custom-attributes"></a>Utilizar os atributos personalizados

A maioria das aplicações do consumidor necessário armazenar algum tipo de informações de perfil de utilizador personalizadas. É uma forma, pode fazê-lo definir um atributo personalizado no seu inquilino B2C. Pode, em seguida, são processados esse atributo da mesma forma são processados quaisquer outros direitos de propriedade num objeto do utilizador. Pode atualizar o atributo, elimine o atributo, o atributo de consulta, enviar o atributo como uma afirmação no início de sessão no tokens e muito mais.

Para definir um atributo personalizado no seu inquilino B2C, consulte a [referência de atributo personalizado B2C](active-directory-b2c-reference-custom-attr.md).

Pode ver os atributos personalizados definidos no seu inquilino B2C utilizando `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

O resultado destas funções, são os detalhes de cada atributo personalizado, tal como:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Pode utilizar o nome completo, tal como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade nos objetos de utilizador.  Atualize o seu ficheiro .json com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Ao utilizar `B2CGraphClient`, que tem uma aplicação de serviço que pode gerir os seus utilizadores do B2C inquilino através de programação. `B2CGraphClient`utiliza as suas próprias identidade da aplicação para autenticar à API do Azure AD Graph. Também-adquire tokens utilizando uma palavra-passe cliente. Como incorporar esta funcionalidade a sua aplicação, lembre-se alguns pontos-chave para B2C aplicações:

- Tem de conceder a aplicação as permissões adequadas no inquilino.
- Agora, terá de utilizar ADAL v2 para obter tokens de acesso. (Pode também enviar mensagens de protocolo diretamente, sem utilizar uma biblioteca.)
- Ao ligar para a API do gráfico, utilize `api-version=1.6`.
- Quando cria e atualizar os utilizadores do consumidor, algumas propriedades de necessários, como descrito acima.

Se tiver questões ou pedidos de ações que pretende efetuar ao utilizar a API do gráfico no seu inquilino B2C, deixe um comentário neste artigo ou um problema de ficheiros no repositório de exemplo de código GitHub.
