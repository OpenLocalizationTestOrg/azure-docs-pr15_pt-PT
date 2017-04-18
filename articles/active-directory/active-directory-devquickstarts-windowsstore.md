<properties
    pageTitle="Introdução da loja Windows Azure AD | Microsoft Azure"
    description="Como criar uma aplicação da loja Windows que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-with-a-windows-store-app"></a>Integrar o Azure AD com uma aplicação da loja Windows

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se está a desenvolver uma aplicação para o da loja Windows, Azure AD torna simples e de fácil para si autenticar os utilizadores com as respetivas contas do Active Directory.  Também lhe permite a aplicação de forma segura consumir qualquer web API protegido por Azure AD, tais como as APIs do Office 365 ou a API do Azure.

Da loja Windows ambiente de trabalho das aplicações que precisam de aceder recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL.  Objetivo de exclusivo do ADAL vida é tornam mais fácil para a sua aplicação obter tokens de acesso.  Para demonstrar tal como é fácil, aqui vamos irá criar uma aplicação da loja Windows "Diretório pessoa" que:

-   Obtém acesso tokens para chamar a API do Azure AD Graph através do [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Procura um diretório para utilizadores com um determinado UPN.
-   Utilizadores de sinais saída.

Para criar a aplicação de trabalho concluída, terá:

2. Registe-se a sua aplicação com Azure AD.
3. Instalar e configurar ADAL.
5. Utilize ADAL para obter tokens a partir do Azure AD.

Para começar a, [Transferir um projeto de estrutura](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Cada é uma solução Visual Studio 2015.  Também terá de um inquilino do Azure AD na qual pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. registar a aplicação de pessoa do diretório*
Para ativar a sua aplicação obter tokens, primeiro terá registá-lo no seu inquilino do Azure AD e conceder-lhe permissões de acesso a API do Azure AD Graph:

-   Inicie sessão no [Portal de gestão do Azure](https://manage.windowsazure.com)
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione um inquilino nas quais se registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e criar uma nova **Aplicação de cliente nativa**.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **Redirecionar Uri** é uma combinação de esquema e cadeia que irá utilizar Azure AD para devolver tokens respostas.  Introduza um valor de marcador de posição por agora, por exemplo, `http://DirectorySearcher`.  Vamos irá substituir este valor mais tarde.
-   Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de **Configurar** .
- Também no separador **Configurar** , localize a secção "Permissões para outras aplicações".  Para a aplicação "Azure Active Directory", adicione a permissão de **acesso diretório como o utilizador tiver sessão iniciada** em **Permissões do delegado**.  Isto permitirá a sua aplicação consultar a API do gráfico para os utilizadores.

## <a name="2-install--configure-adal"></a>*2. instalar e configurar ADAL*
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escreva o código de identidade.  Para ADAL possam comunicar com o Azure AD, é necessário fornecer-lhe algumas informações sobre o registo de aplicação.
-   Comece por adicionar ADAL ao projeto DirectorySearcher iniciou utilizando a consola do Gestor de pacote.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   No project DirectorySearcher iniciou, abra `MainPage.xaml.cs`.  Substituir valores de `Config Values` região para refletir os valores de entrada no Portal do Azure.  O código irá referenciar estes valores sempre que utiliza ADAL.
    -   O `tenant` é o domínio do seu inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `clientId` é clientId da sua aplicação que copiou a partir do portal.
-   Tem agora descobrir o uri de chamada de retorno para a sua aplicação da loja Windows.  Definir um ponto de interrupção esta linha a `MainPage` método:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Criar a solução, certificando-se de todas as referências de pacote são restauradas.  Se pacotes estiverem em falta, abra o Gestor de pacotes Nuget e restaurar os pacotes.
- Executar a aplicação e, reserve copie o valor de `redirectUri` quando o ponto de interrupção é visitas.  -Deverá ter um aspeto semelhante

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Novamente no separador **Configurar** da sua aplicação no Portal de gestão do Azure, substitua o valor da **RedirectUri** com este valor.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilize ADAL para obter Tokens de AAD*
O princípio básico atrás ADAL é que sempre que a sua aplicação necessita de um token de acesso, basta chama `authContext.AcquireToken(…)`, e ADAL é que os restantes.  

-   O primeiro passo é a sua aplicação de inicialização `AuthenticationContext` -ADAL do classe principal.  Este é o local onde a passar ADAL as coordenadas que necessita para comunicar com o Azure AD e indique-lo como tokens em cache.

```C#
public MainPage()
{
    ...

    authContext = new AuthenticationContext(authority);
}
```

- Agora localizar o `Search(...)` método, que irá chamado quando o utilizador clica no botão "Search" na IU a aplicação.  Este método permite num pedido GET à API do Azure AD Graph a consulta para os utilizadores cuja UPN começa com o termo de pesquisa determinado.  Para consultar a API do gráfico, precisar de incluir um access_token no, mas o `Authorization` cabeçalho do pedido - isto é que ADAL entra em.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
        }
        return;
    }
    ...
}
```
- Quando a aplicação os pedidos de um token ao contactar o suporte `AcquireTokenAsync(...)`, ADAL tentará para devolver um token sem perguntar ao utilizador para introduzir as credenciais.  Se ADAL determinar que o utilizador tem de iniciar sessão obter um token, irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do utilizador e devolver um token relativamente a autenticação com êxito.  Se não conseguir devolver um token por qualquer motivo, ADAL a `AuthenticationResult` estado será um erro.

- Agora está na altura de utilizar o access_token que acabou de adquirir.  Também na `Search(...)` método, anexar o token de ao pedido de Graph API obter no cabeçalho da autorização:

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
- Também pode utilizar o `AuthenticationResult` objeto para apresentar informações sobre o utilizador na sua aplicação, como o id de utilizador:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Por fim, pode utilizar ADAL para assinar o utilizador terminar também a aplicação.  Quando o utilizador clica no botão "Terminar sessão", queremos para se certificar de que a chamada para o próxima `AcquireTokenAsync(...)` irá mostrar um início de sessão na vista.  Com ADAL, este é tão fácil como limpar a cache de tokens:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Parabéns! Agora tem uma aplicação da loja Windows que tem a capacidade de autenticar os utilizadores, contacte segura APIs da Web OAuth 2.0 de utilizar a funcionar e obter informação básica sobre o utilizador.  Se ainda não o fez, é agora o tempo para preencher o inquilino com alguns utilizadores.  Execute a sua aplicação DirectorySearcher iniciou e inicie sessão com uma desses utilizadores.  Pesquisa para outros utilizadores com base na sua UPN.  Feche a aplicação e executá-lo novamente.  Repare como permanece intacta a sessão do utilizador.  Terminar sessão (clicando com o para mostrar a barra da parte inferior) e o volta a iniciar sessão como outro utilizador.

ADAL torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  Tratará de todo o trabalho modificado por si - gestão da cache, suporte do protocolo OAuth, apresentar o utilizador com um início de sessão da IU, atualizar tokens expiradas e muito mais.  Tudo o que realmente precisa de saber for uma chamada de API única, `authContext.AcquireToken*(…)`.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Agora pode mover a cenários de identidade adicionais.  Pretende experimentar:

[Proteger uma .NET da Web API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
