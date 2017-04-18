<properties
    pageTitle="Azure AD Xamarin introdução | Microsoft Azure"
    description="Como criar uma aplicação de Xamarin que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integrar o Azure AD numa aplicação para Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin permite-lhe escrever aplicações móveis no c# que pode executar o iOS, Android e Windows (dispositivos móveis e PCs). Se estiver a criar uma aplicação utilizando Xamarin, Azure AD torna simples e de fácil para si autenticar os utilizadores com as respetivas contas do Active Directory. Também lhe permite a aplicação de forma segura consumir qualquer web API protegido por Azure AD, tais como as APIs do Office 365 ou a API do Azure.

Para as aplicações do Xamarin que precisam de aceder recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL. Objetivo de exclusivo do ADAL vida é tornam mais fácil para a sua aplicação obter tokens de acesso. Para demonstrar tal como é fácil, aqui vamos irá criar uma aplicação "Diretório pessoa" que:

-   É executado no iOS, Android, ambiente de trabalho do Windows, Windows Phone e da loja Windows.
- Utiliza uma biblioteca de classes portable única (PCL) para autenticar os utilizadores e obter tokens para a API do Azure AD Graph
-   Procura um diretório para utilizadores com um determinado UPN.

Para criar a aplicação de trabalho concluída, terá:

2. Configurar o seu ambiente de desenvolvimento Xamarin
2. Registe-se a sua aplicação com Azure AD.
3. Instalar e configurar ADAL.
5. Utilize ADAL para obter tokens a partir do Azure AD.

Para começar a, [Transferir um projeto de estrutura](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada é uma solução do Visual Studio 2013. Também terá de um inquilino do Azure AD na qual pode criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. configurar o seu ambiente de desenvolvimento Xamarin*
Uma vez que este tutorial inclui projetos para iOS, Android e Windows, terá de Visual Studio e Xamarin em conjunto. Para criar o ambiente for necessário, siga as instruções completas sobre a [configuração e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) no MSDN. Estas instruções incluem material que pode rever para saber mais sobre Xamarin enquanto está à espera para os programas de instalação concluir. 

Uma vez que concluiu a configuração for necessária, abra a solução no Visual Studio para começar a utilizar. Irá encontrar seis projetos: cinco específicos da plataforma projetos e uma biblioteca de classes portable que será partilhada em todas as plataformas,`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. registar a aplicação de pessoa do diretório*
Para ativar a sua aplicação obter tokens, primeiro terá registá-lo no seu inquilino do Azure AD e conceder-lhe permissões de acesso a API do Azure AD Graph:

-   Inicie sessão no [Portal de gestão do Azure](https://manage.windowsazure.com)
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione um inquilino nas quais se registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e criar uma nova **Aplicação de cliente nativa**.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **Redirecionar Uri** é uma combinação de esquema e a cadeia que irá utilizar Azure AD para devolver tokens respostas. Introduza um valor, por exemplo, `http://DirectorySearcher`.
-   Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente. Terá este valor nas secções seguintes, por isso, copie-a partir do separador de **Configurar** .
- Também no separador **Configurar** , localize a secção "Permissões para outras aplicações". Para a aplicação "Azure Active Directory", adicione a permissão de **diretório da organização acesso Your** em **Permissões do delegado**. Isto permitirá a sua aplicação consultar a API do gráfico para os utilizadores.

## <a name="2-install--configure-adal"></a>*2. instalar e configurar ADAL*
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escreva o código de identidade. Para ADAL possam comunicar com o Azure AD, é necessário fornecer-lhe algumas informações sobre o registo de aplicação.
-   Comece por adicionar ADAL para cada um dos projetos da solução utilizando a consola do Gestor de pacote.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Deverá Repare que são adicionadas duas referências de biblioteca para cada projeto - parte da PCL da ADAL e uma parte específica de plataforma.

-   No DirectorySearcherLib project, abra `DirectorySearcher.cs`. Altere os valores de membro de classe para refletir os valores de entrada no Portal do Azure. O código fizer referência a estes valores sempre que utiliza ADAL.
    -   O `tenant` é o domínio do seu inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `clientId` é clientId da sua aplicação que copiou a partir do portal.
    - O `returnUri` é redirectUri introduziu no portal do, por exemplo, `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilize ADAL para obter Tokens de AAD*
*Almost* todas lógica de autenticação a aplicação se situar no `DirectorySearcher.SearchByAlias(...)`. Tudo o que é necessário o projetos específicos da plataforma consiste em passar um parâmetro contextual para o `DirectorySearcher` PCL.

- Primeiro, abra `DirectorySearcher.cs` e adicionar um novo parâmetro para a `SearchByAlias(...)` método. `IPlatformParameters`é o parâmetro contextual que contém os objetos específicos da plataforma que necessita de ADAL para desempenhar autenticação.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   Em seguida, iniciar a `AuthenticationContext` -ADAL do classe principal. Este é o local onde a passar ADAL as coordenadas precisa de comunicar com o Azure AD. Em seguida, ligar `AcquireTokenAsync(...)`, que aceita a `IPlatformParameters` objeto e vai invocar o fluxo de autenticação necessário para devolver um token de aplicação.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`em primeiro lugar tentará devolver um token para o recurso pedido (Graph API neste caso) sem perguntar ao utilizador que introduza as suas credenciais (através de colocação em cache ou atualizar tokens antigos). Apenas se for necessário, mostrará o utilizador de início de sessão do Azure AD na página antes de adquirir o token pedido.


- Em seguida, pode anexar o token de acesso ao pedido de Graph API no cabeçalho da autorização:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Há para o `DirectorySearcher` PCL e a sua aplicação do relacionados com a identidade código.  Tudo o que é chamar permanece a `SearchByAlias(...)` método nas vistas de cada plataforma, e onde necessário adicionar código para processar corretamente o ciclo de vida de IU.

####<a name="android"></a>Android:
- No `MainActivity.cs`, adicione uma chamada para `SearchByAlias(...)` no botão clique processador:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Também precisa substituir o `OnActivityResult` método de ciclo de vida para reencaminhar qualquer autenticação redireciona novamente para o método adequado.  ADAL fornece um método de ajuda para esta no Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Ambiente de trabalho do Windows:
- No `MainWindow.xaml.cs`, basta fazer uma chamada para `SearchByAlias(...)` prisma um `WindowInteropHelper` do ambiente de trabalho `PlatformParameters` objeto:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS:
- No `DirSearchClient_iOSViewController.cs`, o iOS `PlatformParameters` objeto simplesmente leva-o até uma referência para o controlador de vista:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Universal do Windows:
- No Windows Universal, abra `MainPage.xaml.cs` e implementar o `Search` método, que utiliza um método de ajuda num projeto partilhada para atualizar IU conforme necessário.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Parabéns! Tem agora uma aplicação de Xamarin que tem a capacidade para autenticar os utilizadores e segura chamar APIs Web usar OAuth 2.0 em diferentes cinco plataformas a funcionar. Se ainda não o fez, é agora o tempo para preencher o inquilino com alguns utilizadores. Execute a sua aplicação DirectorySearcher iniciou e inicie sessão com uma desses utilizadores. Pesquisa para outros utilizadores com base na sua UPN.

ADAL torna mais fácil incorporar funcionalidades de identidade comuns na sua aplicação. Tratará de todo o trabalho modificado por si - gestão da cache, suporte do protocolo OAuth, apresentar o utilizador com um início de sessão da IU, atualizar tokens expiradas e muito mais. Tudo o que realmente precisa de saber for uma chamada de API única, `authContext.AcquireToken*(…)`.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Agora pode mover a cenários de identidade adicionais. Pretende experimentar:

[Proteger uma .NET da Web API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
