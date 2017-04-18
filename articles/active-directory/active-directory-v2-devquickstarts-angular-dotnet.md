<properties
    pageTitle="Azure AD 2.0 AngularJS introdução | Microsoft Azure"
    description="Como criar uma aplicação de Angular JS única página sinais em utilizadores com ambas as contas pessoais do Microsoft e contas escolar ou profissional."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---net"></a>Adicionar iniciar sessão a uma aplicação de única página de AngularJS - .NET

Neste artigo adicionaremos inicie sessão com contas do Microsoft ligado a uma aplicação do AngularJS utilizando o ponto final do Azure Active Directory 2.0.  O ponto final 2.0 permite-lhe executar uma única integração na sua aplicação e autenticar os utilizadores com contas pessoais e trabalho/escola.

Este exemplo é uma aplicação de única página de lista de itens pendentes simple que armazena as tarefas no REST API, escritas utilizando o MVC de 4,5 .NET framework e protegidos utilizando tokens de tipo de ligação OAuth a partir do Azure AD back-end.  A aplicação AngularJS irá utilizar os nossos abrir origem JavaScript autenticação biblioteca [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) para processar o início de sessão completo processo e adquirir tokens para chamar a API REST.  O mesmo padrão pode ser aplicado para autenticar para outras API REST, como o [Microsoft Graph](https://graph.microsoft.com).

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="download"></a>Transferir

Para começar, terá de transferir e instalar o Visual Studio.  Em seguida, pode clonar ou [Transferir](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) uma aplicação de estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

A aplicação de estrutura inclui todos os códigos de texto automático para uma aplicação do AngularJS simple, mas não é apresentado todas as peças relacionados com a identidade.  Se não quiser segui-los, em vez disso, pode clonar ou [Transferir](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) de exemplo preenchido.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## <a name="register-an-app"></a>Registe-se uma aplicação

Em primeiro lugar, crie uma aplicação no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estes [passos detalhados](active-directory-v2-app-registration.md).  Certifique-se de que:

- Adicione a plataforma **Web** para a sua aplicação.
- Introduza o correto **Redirecionar URI**. A predefinição para este exemplo é `https://localhost:44326/`.
- Deixe a caixa de verificação **Permitir fluxo implícito** ativada. 

Copiar para baixo o **ID da aplicação** que está atribuída a sua aplicação, terá de-brevemente. 

## <a name="install-adaljs"></a>Instalar adal.js
Para começar, navegar para o project que transferiu e instale adal.js.  Se tiver [bower](http://bower.io/) instalado, apenas pode executar este comando.  Para qualquer incompatibilidade de valores para a versão de dependência, basta escolha a versão superior.
```
bower install adal-angular#experimental
```

Em alternativa, pode transferir manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) e [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Adicionar ambos os ficheiros para o `app/lib/adal-angular-experimental/dist` directório do `TodoSPA` projeto.

Agora abra o projeto no Visual Studio e carregar adal.js no final do corpo da página principal:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurar o REST API

Enquanto que está a configurar coisas, vamos apertado REST API back-end.  Na raiz do projeto, abra `web.config` e substituir o `audience` valor.  REST API irá utilizar este valor para validar os tokens que recebe a partir da aplicação Angular sobre os pedidos de AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

Esse é sempre iremos passam a debater como funciona a API REST.  Esteja à vontade para faça à volta no código, mas se quiser saber mais sobre como proteger web APIs com Azure AD, consulte [Este artigo](active-directory-v2-devquickstarts-dotnet-api.md). 

## <a name="sign-users-in"></a>Utilizadores de início de sessão no
Tempo para escrever algum código de identidade.  Qual poderá já provavelmente, reparou que adal.js contém um fornecedor de AngularJS, pode ser reproduzido corretamente com mecanismos de encaminhamento Angular.  Comece por adicionar o módulo adal para a aplicação:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Agora pode iniciar o `adalProvider` com o seu ID da aplicação:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Excelente, adal.js tem agora todas as informações que necessita para proteger a sua aplicação e início de sessão utilizadores.  Para forçar o início de sessão para uma determinada rota na aplicação, só ocorre o mais de uma linha de código:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Agora quando um utilizador clica a `TodoList` ligação, adal.js irá redirecionar automaticamente para Azure AD para iniciar sessão se for necessário.  Pode também explicitamente enviar pedidos de iniciar sessão e terminar sessão por invocar adal.js no seu controladores:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Apresentar informações de utilizador
Agora que o utilizador tem sessão iniciada, provavelmente terá de aceder a dados de autenticação do tiver sessão iniciada no utilizador na sua aplicação.  Adal.js expõe estas informações para o utilizador na `userInfo` objeto.  Para aceder a este objecto numa vista, adicione adal.js para o âmbito de raiz do controlador de correspondente:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Em seguida, pode também abordar diretamente a `userInfo` objeto na sua vista: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Também pode utilizar o `userInfo` objeto para determinar se o utilizador tem sessão iniciada ou não.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Chamar a API REST
Por fim, é hora para obter alguns tokens e chamar a API REST para criar, ler, atualizar e eliminar tarefas.  Bem acontece?  Não tem de fazer *uma coisa*.  Adal.js serão automaticamente encarregam-se de introdução, colocação em cache e atualizar tokens.  Também tomar cuidado de como anexar essas tokens a enviados pedidos de AJAX que enviar REST API.  

Exatamente como é que isto funciona? É tudo graças à magia da [AngularJS interceptors](https://docs.angularjs.org/api/ng/service/$http), que lhe permite adal.js transformar mensagens de envio e receção de http.  Além disso, adal.js assume que quaisquer pedidos de enviar para o mesmo domínio como a janela deve utilizar tokens destinados o ID da aplicação mesmo como a aplicação AngularJS.  Este é o motivo pelo qual utilizámos o mesmo ID da aplicação na aplicação Angular e, de API REST NodeJS.  Obviamente, pode ignorar este comportamento e saber adal.js para obter tokens para outras API REST se for necessário - mas para este cenário simples as predefinições irão fazer.

Eis um fragmento que mostra como é fácil enviar pedidos de tokens de tipo de ligação a partir do Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Parabéns!  A aplicação integrada única página do Azure AD agora está concluída.  Vá para a frente, tirar um laço.  Pode autenticar os utilizadores, em segurança chamar respetivo back-end REST API utilizando OpenID ligar e obter informação básica sobre o utilizador.  Terminar a caixa de, suporta qualquer utilizador com uma Account Microsoft pessoal ou com uma conta escolar/profissional a partir do Azure AD.  Executar a aplicação de e num browser, navegue até `https://localhost:44326/`.  Inicie sessão com uma conta Microsoft pessoal ou uma conta escolar/profissional.  Adicionar tarefas à lista de itens pendentes do utilizador e terminar sessão.  Tente iniciar sessão com o outro tipo de conta. Se precisar de um inquilino do Azure AD para criar utilizadores de trabalho/escola, [aprender a obter uma aqui](active-directory-howto-tenant.md) (é gratuito).

Para continuar a obter informações sobre o ponto final 2.0, cabeça novamente o nosso [Guia do Programador de 2.0](active-directory-appmodel-v2-overview.md).  Para obter recursos adicionais, consulte:

- [Azure-amostras no GitHub >>](https://github.com/Azure-Samples)
- [Azure AD pilha exceder a capacidade >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Azure documentação AD sobre [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
