<properties
pageTitle="Aplicação nativa do Azure Active Directory 2.0 .NET | Microsoft Azure"
description="Como criar uma aplicação de nativa .NET assina utilizadores com ambos os Account Microsoft pessoal e contas escolar ou profissional."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Adicionar, iniciar sessão para uma aplicação de ambiente de trabalho Windows

Com o o ponto final 2.0, pode adicionar rapidamente autenticação às suas aplicações de ambiente de trabalho com o suporte para ambas as contas pessoais do Microsoft e contas escolar ou profissional.  Também lhe permite a aplicação em segurança comunicar com uma web back-end, de api, bem como [O Microsoft Graph](https://graph.microsoft.io) e algumas das [APIs de Unified do Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] Nem todos os cenários do Azure Active Directory (AD) e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

Para [aplicações nativas .NET que executar num dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD fornece a biblioteca de autenticação de identidade do Microsoft ou MSAL.  Objetivo de exclusivo do MSAL vida é tornam mais fácil para a sua aplicação obter tokens para ligar a serviços web.  Para demonstrar tal como é fácil, aqui vamos irá criar uma lista de itens pendentes do .NET WPF aplicação que:

- O utilizador a iniciar sessão e obtém acesso tokens utilizando o [protocolo de autenticação OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
- Em segurança chamadas um serviço web de lista de itens pendentes, que também é protegido por OAuth 2.0 de back-end.
- Inicia o utilizador.

## <a name="download-sample-code"></a>Transferir o código de exemplo

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou clonar a estrutura:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

A aplicação completa é fornecida no final deste tutorial também.

## <a name="register-an-app"></a>Registe-se uma aplicação
Criar uma nova aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estes [passos detalhados](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copiar para baixo o **Id da aplicação** atribuído para a sua aplicação, terá de-la mais rapidamente.
- Adicione a plataforma **móvel** para a sua aplicação.

## <a name="install--configure-msal"></a>Instalar e configurar MSAL
Agora que tem uma aplicação registada com a Microsoft, pode instalar MSAL e escreva o código de identidade.  Para MSAL possam comunicar o ponto final 2.0, é necessário fornecer-lhe algumas informações sobre o registo de aplicação.

-   Comece por adicionar MSAL ao projeto TodoListClient utilizando o consola do Gestor de pacote.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   No TodoListClient project, abra `app.config`.  Substitua os valores de elementos no `<appSettings>` secção para refletir os valores de entrada para o portal de registo de aplicação.  O código irá referenciar estes valores sempre que utiliza MSAL.
    -   O `ida:ClientId` é o **Id da aplicação** da sua aplicação que copiou a partir do portal.

- No project fazer serviço, abra `web.config` na raiz do projeto.  
    - Substituir o `ida:Audience` valor com o mesmo **Id da aplicação** a partir do portal.

## <a name="use-msal-to-get-tokens"></a>Utilize MSAL para obter tokens
O princípio básico atrás MSAL é que sempre que a sua aplicação necessita de um token de acesso, basta ligar `app.AcquireToken(...)`, e MSAL é que os restantes.  

-   No `TodoListClient` projeto, abrir `MainWindow.xaml.cs` e localize o `OnInitialized(...)` método.  O primeiro passo é a sua aplicação de inicialização `PublicClientApplication` -classe principal do MSAL que representa aplicações nativas.  Este é onde passar MSAL as coordenadas que necessita para comunicar com o Azure AD e indique-lo como tokens em cache.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Quando inicia a aplicação, podemos pretende verificar e ver se o utilizador já tiver iniciado sessão na aplicação.  No entanto, não queremos invocar uma IU de início de sessão no apenas ainda - que faremos o utilizador, clique em "início de sessão" para fazê-lo.  Também na `OnInitialized(...)` método:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Se o utilizador não iniciou sessão no e clique no botão "Início de sessão em", podemos pretende invocar um início de sessão da IU e solicite ao utilizador que introduza as respetivas credenciais.  Implementa a alça de início de sessão no botão:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
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


}
```

- Se o utilizador com êxito sinais-in, MSAL irá receber e um token em cache para si e pode prosseguir para ligar para o `GetTodoList()` método com confiança.  Tudo o que é da esquerda para obter as tarefas de um utilizador é para implementar o `GetTodoList()` método.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Executar

Parabéns! Tem agora uma aplicação de .NET WPF de trabalhar com a capacidade de autenticar os utilizadores e segura chamar APIs da Web OAuth 2.0 a utilizar.  Execute ambos os seus projetos e inicie sessão com uma conta Microsoft pessoal ou uma conta escolar ou profissional.  Adicione tarefas à lista de itens pendentes desse utilizador.  Terminar sessão e inicie sessão novamente como outro utilizador para ver a sua lista de itens pendentes.  Feche a aplicação e executá-lo novamente.  Repare como a sessão do utilizador permanece intacta - se ao facto da aplicação coloca em cache tokens de um ficheiro local.

MSAL torna mais fácil incorporar funcionalidades de identidade comuns na sua aplicação, utilizando as contas pessoais e de trabalho.  Tratará de todo o trabalho modificado por si - gestão da cache, suporte do protocolo OAuth, apresentar o utilizador com um início de sessão da IU, atualizar tokens expiradas e muito mais.  Tudo o que realmente precisa de saber for uma chamada de API única, `app.AcquireTokenAsync(...)`.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)ou pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Próximos passos

Agora pode mover para tópicos mais avançados.  Pretende experimentar:

- [Proteger a API do Web TodoListService com o ponto final 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Para obter recursos adicionais, consulte:  

- [O Guia do programador 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta de "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
