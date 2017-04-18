<properties
    pageTitle="Azure AD AngularJS introdução | Microsoft Azure"
    description="Como criar uma aplicação de Angular JS única página que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Proteger AngularJS única página aplicações com o Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD torna simples e de fácil para adicionar o início de sessão em Terminar sessão e seguro chamadas à OAuth API as suas aplicações única página.  Permite a sua aplicação autenticar utilizadores com as respetivas contas do Active Directory e consuma qualquer web API protegido por Azure AD, tais como as APIs do Office 365 ou a API do Azure.

Para aplicações de javascript em execução num browser, Azure AD fornece a biblioteca de autenticação do Active Directory ou adal.js.  Objetivo de exclusivo do Adal.js vida é tornam mais fácil para a sua aplicação obter tokens de acesso.  Para demonstrar tal como é fácil, aqui vamos irá criar uma aplicação de AngularJS para fazer a lista que:

- Inicia o utilizador para a aplicação a utilizar o Azure AD como o fornecedor de identidade.
- Apresenta algumas informações sobre o utilizador.
- Em segurança chamadas para fazer lista API a aplicação utilizando tokens de tipo de ligação a partir de AAD.
- Inicia o utilizador terminar a aplicação.

Para criar a aplicação de trabalho concluída, terá:

2. Registe-se a sua aplicação com Azure AD.
3. ADAL de instalar e configurar o SPA.
5. Utilize ADAL para proteger a SPA páginas.

Para começar a, [Transferir a estrutura da aplicação](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [Transferir de exemplo preenchido](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Também terá de um inquilino do Azure AD na qual pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registar a aplicação de DirectorySearcher iniciou*
Para ativar a sua aplicação autenticar os utilizadores e obter tokens, primeiro terá registá-lo no seu inquilino do Azure AD:

-   Inicie sessão no [Portal de gestão do Azure](https://manage.windowsazure.com)
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione um inquilino nas quais se registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e crie uma nova **aplicação Web e/ou WebAPI**.
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais.
    -   O **Redirecionar Uri** é localização à qual AAD irá devolver tokens.  A localização predefinida para que este exemplo é`https://localhost:44326/`
-   Quando terminar de registo, AAD atribuirá sua aplicação de um único **ID de cliente**.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de **Configurar** .
- Adal.js utiliza o fluxo de implícito OAuth para comunicar com o Azure AD.  Tem de ativar o fluxo implícito para a sua aplicação por:
    - Transfira manifesto da aplicação ao clicar em **Gerir manifesto**.
    - Abra o manifesto e localize o `oauth2AllowImplicitFlow` propriedade. Defina o valor para `true`.
    - Guardar e carregar manifesto da aplicação ao clicar em **Gerir manifesto** mais uma vez.

## <a name="2-install-adal--configure-the-spa"></a>*2. ADAL de instalar e configurar o SPA*
Agora que tem uma aplicação no Azure AD, pode instalar adal.js e escreva o código de identidade.

-   Comece por adicionar adal.js ao projeto TodoSPA utilizando o consola do Gestor de pacote:
  - Transferir [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e adicioná-lo para a `App/Scripts/` diretório de projeto.
  - Transferir [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e adicioná-lo para a `App/Scripts/` diretório de projeto.
  - Carregar cada script antes do final da `</body>` no `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   Para back-end o SPA para fazer lista API para aceitar tokens a partir do browser, o back-end necessita de informações de configuração sobre o registo de aplicação. No TodoSPA project, abra `web.config`.  Substitua os valores de elementos no `<appSettings>` secção para refletir os valores de entrada no Portal do Azure.  O código irá referenciar estes valores sempre que utiliza ADAL.
    -   O `ida:Tenant` é o domínio do seu inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `ida:Audience` tem de ser o **ID de cliente** da sua aplicação que copiou a partir do portal.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. Utilize ADAL para proteger as páginas a SPA*
Adal.js foi criado para integrar com fornecedores AngularJS encaminhar e http, que permite-lhe seguro vistas individuais no seu SPA.

- No `App/Scripts/app.js`, trazer no módulo adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Agora pode iniciar o `adalProvider` com os valores de configuração do seu registo de aplicação, também na `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Agora para proteger a `TodoList` ver na aplicação, apenas uma linha de código é necessária - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Tem agora uma aplicação de seguro única página com a capacidade de início de sessão utilizadores e emitir pedidos protegidos tokens de ligação para o respetivo API de back-end.  Quando um utilizador clica a `TodoList` ligação, adal.js irá redirecionar automaticamente para Azure AD para iniciar sessão se for necessário.  Além disso, adal.js automaticamente anexar uma access_token a todos os pedidos ajax que são enviados a back-end da aplicação.  O que precede é ao simples mínimo necessário para construir um SPA com adal.js - mas existem um número de outras funcionalidades que são úteis no estações termais:

- Para explicitamente problema de início de sessão e terminar pedidos sessão pode definir funções no seu controladores que invocar adal.js.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Também poderá pretender apresentar informações de utilizador no IU a aplicação.  O serviço de adal já foi adicionado para o `userDataCtrl` controlador de, para que possa aceder a `userInfo` objeto na vista de associado, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Também existem muitos cenários em que que pretende saber se o utilizador tem sessão iniciada ou não.  Também pode utilizar o `userInfo` objeto para recolher estas informações.  Por exemplo, na `index.html` pode mostrar botão a "Início de sessão" ou "Termine a sessão" com base no estado de autenticação:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Parabéns! O Azure AD integrada única página aplicação está concluída.  Pode autenticar os utilizadores, em segurança chamar respetivo back-end utilizando OAuth 2.0 e obter informação básica sobre o utilizador.  Se ainda não o fez, é agora o tempo para preencher o inquilino com alguns utilizadores.  Execute o seu para fazer lista SPA e inicie sessão com uma desses utilizadores.  Adicione tarefas para os utilizadores da lista, terminar sessão e volta a iniciar sessão.

Adal.js torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  Tratará de todo o trabalho modificado por si - gestão da cache, suporte do protocolo OAuth, apresentar o utilizador com um início de sessão da IU, atualizar tokens expiradas e muito mais.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Agora pode mover para cenários adicionais.  Pretende experimentar:

[Ligar uma CORS da Web API a partir de um SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
