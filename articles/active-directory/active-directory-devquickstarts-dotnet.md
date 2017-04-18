<properties
    pageTitle="Azure AD .NET introdução | Microsoft Azure"
    description="Como criar uma aplicação de ambiente de trabalho do .NET Windows que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrar uma aplicação de ambiente de trabalho WPF do Windows Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se está a desenvolver uma aplicação de ambiente de trabalho, Azure AD torna simples e de fácil para si autenticar os utilizadores com as respetivas contas do Active Directory.  Também lhe permite a aplicação de forma segura consumir qualquer web API protegido por Azure AD, tais como as APIs do Office 365 ou a API do Azure.

Para .NET nativos clientes que precisam para aceder aos recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL.  Do ADAL responsável vida objectivo tornam mais fácil para a sua aplicação obter tokens de acesso.  Para demonstrar tal como é fácil, aqui vamos irá criar uma aplicação de lista de itens pendentes do .NET WPF que:

-   Obtém acesso tokens para chamar a API do Azure AD Graph utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Procura um diretório para utilizadores com um determinado alias.
-   Utilizadores de sinais saída.

Para criar a aplicação de trabalho concluída, terá:

2. Registe-se a sua aplicação com Azure AD.
3. Instalar e configurar ADAL.
5. Utilize ADAL para obter tokens a partir do Azure AD.

Para começar a, [Transferir a estrutura da aplicação](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Também terá de um inquilino do Azure AD na qual pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. Registe-se a aplicação de DirectorySearcher iniciou*
Para ativar a sua aplicação obter tokens, primeiro terá registá-lo no seu inquilino do Azure AD e conceder-lhe permissões de acesso a API do Azure AD Graph:

-   Inicie sessão no Portal de gestão do Azure
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione um inquilino nas quais se registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e criar uma nova **Aplicação de cliente nativa**.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **Redirecionar Uri** é uma combinação de esquema e a cadeia que irá utilizar Azure AD para devolver tokens respostas.  Introduza um valor específico à aplicação, por exemplo, `http://DirectorySearcher`.
-   Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de **Configurar** .
- Também no separador **Configurar** , localize a secção "Permissões para outras aplicações".  Para a aplicação "Azure Active Directory", adicione a permissão de **diretório da organização acesso Your** em **Permissões do delegado**.  Isto permitirá a sua aplicação consultar a API do gráfico para os utilizadores.

## <a name="2-install--configure-adal"></a>*2. instalar e configurar ADAL*
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escreva o código de identidade.  Para ADAL possam comunicar com o Azure AD, é necessário fornecer-lhe algumas informações sobre o registo de aplicação.
-   Comece por adicionar ADAL ao projeto DirectorySearcher iniciou utilizando a consola do Gestor de pacote.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   No project DirectorySearcher iniciou, abra `app.config`.  Substitua os valores de elementos no `<appSettings>` secção para refletir os valores de entrada no Portal do Azure.  O código fizer referência a estes valores sempre que utiliza ADAL.
    -   O `ida:Tenant` é o domínio do seu inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `ida:ClientId` é clientId da sua aplicação que copiou a partir do portal.
    -   O `ida:RedirectUri` é o redirecionamento url registado no portal.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilize ADAL para obter Tokens de AAD*
O princípio básico atrás ADAL é que sempre que a sua aplicação necessita de um token de acesso, basta chama `authContext.AcquireTokenAsync(...)`, e ADAL é que os restantes.  

-   No `DirectorySearcher` projeto, abrir `MainWindow.xaml.cs` e localize o `MainWindow()` método.  O primeiro passo é a sua aplicação de inicialização `AuthenticationContext` -ADAL do classe principal.  Este é o local onde a passar ADAL as coordenadas que necessita para comunicar com o Azure AD e indique-lo como tokens em cache.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Agora localizar o `Search(...)` método, que irá chamado quando os cliks de utilizador "Search" do botão na IU a aplicação.  Este método permite num pedido GET à API do Azure AD Graph a consulta para os utilizadores cuja UPN começa com o termo de pesquisa determinado.  Para consultar a API do gráfico, precisar de incluir um access_token no, mas o `Authorization` cabeçalho do pedido - isto é que ADAL entra em.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Quando a aplicação os pedidos de um token ao contactar o suporte `AcquireTokenAsync(...)`, ADAL tentará para devolver um token sem perguntar ao utilizador para introduzir as credenciais.  Se ADAL determinar que o utilizador tem de iniciar sessão obter um token, irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do utilizador e devolver um token relativamente a autenticação com êxito.  Se ADAL não conseguir devolver um token por qualquer motivo, irá gerar um `AdalException`.
- Repare que o `AuthenticationResult` objeto contiver um `UserInfo` objeto que pode ser utilizado para recolher informações poderá ter a sua aplicação.  No DirectorySearcher de iniciou, `UserInfo` é utilizada para personalizar IU a aplicação com o id de utilizador.

- Quando o utilizador clica no botão "Terminar sessão", queremos para se certificar de que a chamada para o próxima `AcquireTokenAsync(...)` irá peça ao utilizador para iniciar sessão.  Com ADAL, este é tão fácil como limpar a cache de tokens:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- No entanto, se o utilizador não clique no botão "Terminar sessão", que irá pretende manter a sessão do utilizador para a próxima vez que são executadas a DirectorySearcher iniciou.  Quando inicia a aplicação, pode verificar a cache de tokens de ADAL para um token existente e atualizar a IU em conformidade.  No `CheckForCachedToken()` método, efetuar outra chamada para `AcquireTokenAsync(...)`, desta vez transmitir a `PromptBehavior.Never` parâmetro.  `PromptBehavior.Never`irá indicar ADAL que o utilizador não deve ser-lhe pedido para iniciar sessão e ADAL em vez disso, deverá gerar uma exceção se for possível devolver um token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Parabéns! Agora tem uma aplicação do .NET WPF que tenha a capacidade de autenticar os utilizadores, contacte segura APIs da Web OAuth 2.0 de utilizar a funcionar e obter informação básica sobre o utilizador.  Se ainda não o fez, é agora o tempo para preencher o inquilino com alguns utilizadores.  Execute a sua aplicação DirectorySearcher iniciou e inicie sessão com uma desses utilizadores.  Pesquisa para outros utilizadores com base na sua UPN.  Feche a aplicação e executá-lo novamente.  Repare como permanece intacta a sessão do utilizador.  Terminar sessão e volta a iniciar sessão como outro utilizador.

ADAL torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  Tratará de todo o trabalho modificado por si - gestão da cache, suporte do protocolo OAuth, apresentar o utilizador com um início de sessão da IU, atualizar tokens expiradas e muito mais.  Tudo o que realmente precisa de saber for uma chamada de API única, `authContext.AcquireTokenAsync(...)`.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Agora pode mover para cenários adicionais.  Pretende experimentar:

[Proteger uma .NET da Web API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
