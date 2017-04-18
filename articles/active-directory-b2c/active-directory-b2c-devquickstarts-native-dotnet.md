<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Como criar uma aplicação de ambiente de trabalho do Windows que inclui o início de sessão no, inscrição, e gestão de perfil utilizando o Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Criar uma aplicação de ambiente de trabalho do Windows

Ao utilizar B2C Azure Active Directory (Azure AD), pode adicionar funcionalidades de gestão de identidade self-service poderosas para a sua aplicação de ambiente de trabalho em alguns passos breves. Este artigo mostrar-lhe como criar uma aplicação de "lista de acções a fazer".NET Windows Presentation Foundation (WPF) que inclui o utilizador inscrição, iniciar sessão e gestão de perfil. A aplicação irá incluir o suporte para inscrição e iniciar sessão ao utilizar um nome de utilizador ou o e-mail. Também incluirá suporte para inscrição e iniciar sessão utilizando contas de rede sociais como o Facebook e do Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um directory B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos.  Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e mais. Se ainda não tiver uma, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C. Isto dá informações do Azure AD que precisa de comunicar com a sua aplicação de forma segura. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md).  Não se esqueça de:

- Inclua um **cliente nativo** na aplicação.
- Copiar **Redirecionar URI** `urn:ietf:wg:oauth:2.0:oob`. É o URL predefinido para este exemplo de código.
- Copie o **ID da aplicação** que está atribuída a sua aplicação. Irá necessitá-la mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três identidade experiências: inscrever-se, inicie sessão e editar o perfil. Tem de criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando cria as três políticas, não se esqueça de:

- Selecione o **ID de utilizador inscrição** ou **E-Mail de inscrição** no pá de fornecedores de identidade.
- Selecione o **nome a apresentar** e outros atributos de inscrição na política de inscrição.
- Selecione em afirmações **nome a apresentar** e o **ID do objeto** como afirmações da aplicação para cada política. Pode escolher também em outras afirmações.
- Copie o **nome** de cada política depois de o criar. Deve ter o prefixo `b2c_1_`.  Tem os seguintes nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de ter criado com êxito três políticas, está pronto para criar a sua aplicação.

## <a name="download-the-code"></a>Transferir o código

O código para este tutorial [são mantidas no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para criar a amostra à medida que avança, pode [Transferir um projeto de estrutura como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

A aplicação completa também está [disponível como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou na `complete` ramo do mesmo repositório de.

Depois de transferir o código de exemplo, abra o ficheiro de .sln Visual Studio para começar a utilizar. O `TaskClient` projeto é a aplicação de ambiente de trabalho de WPF que o utilizador interage com. Para efeitos neste tutorial, chama web uma tarefa de back-end API, alojado no Azure, que armazena a lista de itens pendentes de cada utilizador.  Não é necessário criar web API, temos já-lo em execução por si.

Para saber como uma web API autentica segura pedidos utilizando Azure AD B2C, consulte o artigo [da web API introdução artigo](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Executar políticas
A aplicação comunica com Azure AD B2C ao enviar mensagens de autenticação que especifique a política que pretendem executar como parte do pedido de HTTP. Para as aplicações de ambiente de trabalho .NET, pode utilizar a pré-visualização do Microsoft autenticação biblioteca (MSAL) para enviar mensagens de autenticação OAuth 2.0, executar políticas e obter tokens web APIs de chamadas.

### <a name="install-msal"></a>Instalar MSAL
Adicionar MSAL para o `TaskClient` projeto utilizando a consola do Visual Studio pacote gestor.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Introduza os detalhes do seu B2C
Abra o ficheiro `Globals.cs` e substitua cada um dos valores de propriedade pelo seu próprio. Esta classe é utilizada em todo `TaskClient` para valores de referência utilizada frequentemente.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Criar o PublicClientApplication
A classe de MSAL primária é `PublicClientApplication`. Esta classe representa a aplicação no sistema de Azure AD B2C. Quando initalizes aplicação, crie uma instância do `PublicClientApplication` no `MainWindow.xaml.cs`. Isto pode ser utilizado em toda a janela.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Iniciar um fluxo de inscrição
Quando um utilizador opte pela sinais para cima, que pretende iniciar um fluxo de inscrição que utiliza a política de inscrição que criou. Ao utilizar MSAL, apenas a chamada `pca.AcquireTokenAsync(...)`. Os parâmetros passar para `AcquireTokenAsync(...)` determinar qual token de receber, a política utilizada no pedido de autenticação e muito mais.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>Iniciar um fluxo de início de sessão
Pode iniciar um fluxo de início de sessão da mesma forma que iniciar um fluxo de inscrição. Quando um utilizador inicia sessão, fazer a chamada mesmo para MSAL, desta vez, utilizando a política de início de sessão no:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Iniciar um fluxo de Editar perfil
Novamente, pode executar uma política de Editar perfil da mesma forma:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Em todos os nestes casos, MSAL quer devolve um token no `AuthenticationResult` ou inicia uma exceção. Sempre que receber um token do MSAL, pode utilizar o `AuthenticationResult.User` objeto para atualizar os dados de utilizador na aplicação, tal como IU. Também ADAL coloca em cache o token para utilização em outras peças da aplicação.


### <a name="check-for-tokens-on-app-start"></a>Verificar a existência de tokens em Iniciar aplicação
Também pode utilizar MSAL para controlar o estado do utilizador iniciar sessão.  Nesta aplicação, queremos ao utilizador para permanecer com a sessão iniciada no mesmo depois de que fecha a aplicação e abra-o novamente.  Dentro de voltar a `OnInitialized` substituir, utilize do MSAL `AcquireTokenSilent` método para verificar em cache tokens:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>Ligar a tarefa API
Agora que tiver utilizado MSAL para executar as políticas e obter tokens.  Quando quiser utilizar um estes tokens para chamar a tarefa API, pode utilizar novamente do MSAL `AcquireTokenSilent` método para verificar em cache tokens:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

Quando a chamada para `AcquireTokenSilentAsync(...)` é concluída com êxito e um token de que se encontram na cache, pode adicionar o token de para a `Authorization` cabeçalho do pedido HTTP. Web tarefa API irá utilizar este cabeçalho para autenticar o pedido ler a lista de itens pendentes do utilizador:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Terminar sessão do utilizador
Por fim, pode utilizar MSAL para terminar a sessão de um utilizador com a aplicação quando o utilizador seleciona **Terminar sessão**.  Ao utilizar MSAL, isto é feito desmarcando todos os tokens da cache de token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Executar a aplicação de exemplo

Por fim, criar e executar o exemplo.  Inscreva-se para a aplicação utilizando um nome de utilizador ou endereços de e-mail. Terminar sessão e volta a iniciar sessão como o mesmo utilizador. Edite o perfil desse utilizador. Terminar sessão e inscrever-se ao utilizar um utilizador diferente.

## <a name="add-social-idps"></a>Adicionar IDPs sociais

Atualmente, a aplicação suporta apenas utilizador inscrição e início de sessão no que utilizam **contas locais**. Estes são contas armazenadas no seu diretório B2C que utilizam um nome de utilizador e palavra-passe. Ao utilizar o Azure AD B2C, pode adicionar suporte para outros fornecedores de identidade (IDPs) sem alterar qualquer uma das seu código.

Para adicionar IDPs sociais para a sua aplicação, comece por seguir as instruções detalhadas nos seguintes artigos. Para cada IDP que pretende suportar, tem de registar uma aplicação desse sistema e obter um ID de cliente.

- [Configurar o Facebook como uma IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar o LinkedIn, como um IDP](active-directory-b2c-setup-li-app.md)

Depois de adicionar os fornecedores de identidade ao diretório da sua B2C, tem de editar cada uma das suas três políticas para incluir as novas IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de guardar as políticas, execute a aplicação novamente. Deverá ver os novos IDPs adicionados como iniciar sessão e opções de inscrição em cada uma das sua identidade experiências.

Pode experimentar as políticas e observar os efeitos na sua aplicação de exemplo. Adicionar ou remover IDPs, manipular afirmações da aplicação ou alterar os atributos de inscrição. Experimente até que possa ver como políticas, pedidos de autenticação e MSAL associam em conjunto.

Para sua referência, o exemplo concluída [é fornecida como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
