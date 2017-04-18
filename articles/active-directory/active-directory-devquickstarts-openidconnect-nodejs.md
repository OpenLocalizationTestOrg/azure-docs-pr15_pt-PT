<properties
    pageTitle="Introdução ao Azure AD início de sessão e terminar sessão utilizando node.js"
    description="Como construir uma node.js Express MVC do Web App que integra-se com o Azure AD para iniciar sessão."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="brandwe"/>

# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>NodeJS sessão da aplicação da Web e inicie sessão fora com Azure AD


Aqui vamos utilizar Passport para:

- Inicie sessão do utilizador para a aplicação com Azure AD.
- Apresenta algumas informações sobre o utilizador.
- Inicie sessão do utilizador terminar a aplicação.

**Passport** é software intermédio de autenticação para Node.js. Extremamente flexível e modular, Passport pode ser unobtrusively ignorado para qualquer Express com base em ou Resitify aplicação web. Um conjunto abrangente de estratégias suportar uma autenticação utilizando um nome de utilizador e palavra-passe, Facebook, Twitter e muito mais. Desenvolvemos uma estratégia para o Microsoft Azure Active Directory. Iremos instalar esta módulo e, em seguida, adicionar o Microsoft Azure Active Directory `passport-azure-ad` Plug-in.

Para poder fazer isto, terá:

1. Registe-se uma aplicação.
2. Configure a sua aplicação para utilizar a estratégia Passport azure-ad.
3. Utilize Passport para emitir pedidos de início de sessão e terminar sessão para Azure AD.
4. Imprima dados sobre o utilizador.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

A aplicação completa é fornecida no final deste tutorial também.

## <a name="1-register-an-app"></a>1. Registe-se uma aplicação
- Inicie sessão no Portal de gestão Azure.
- No painel de navegação esquerdo, clique no **Active Directory**.
- Selecione o inquilino onde pretender registar a aplicação.
- Clique no separador **aplicações** e clique em Adicionar nível a parte inferior.
- Siga os pedidos e crie uma nova **aplicação Web e/ou WebAPI**.
    - O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **URL de início de sessão** é o URL de base da sua aplicação.  Predefinição a estrutura é ' http://localhost:3000/auth/openid/ENTER '.
    - A **Aplicação ID URI** é um identificador exclusivo para a sua aplicação.  A Convenção é utilizar `https://<tenant-domain>/<app-name>`, por exemplo,`https://contoso.onmicrosoft.com/my-first-aad-app`
- Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes, por isso, copie-a partir do separador de configurar.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. Adicionar pre requisities ao diretório da sua

A partir da linha de comandos, altere directórios para a sua pasta de raiz se ainda não estiver lá e execute os seguintes comandos:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Além disso, terá de nossos `passport-azure-ad` , assim:

- `npm install passport-azure-ad`

Este procedimento irá instalar as bibliotecas que passport azure-ad dependem.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. configurar a aplicação para utilizar a estratégia passport-nó-js
Aqui, podemos irá configurar o software Express intermédio para utilizar o protocolo de autenticação OpenID ligar.  Passport será utilizado para iniciar sessão e terminar sessão pedidos de problema, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

-   Para começar, abra o `config.js` ficheiro na raiz do projeto e introduza os valores de configuração da sua aplicação na `exports.creds` secção.
    -   O `clientID:` é o **Id da aplicação** atribuído para a sua aplicação no portal do registo.
    -   O `returnURL` é **Redirecionar Uri** introduzido no portal.
    - O `clientSecret` é o segredo gerado no portal

- Abra o `app.js` ficheiro na raiz do projecto e adicionar a chamada follwing para invocar a `OIDCStrategy` estratégia que vem com`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Após esta ação, utilizar a estratégia podemos referenciados apenas para processar nossos pedidos de início de sessão

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport utiliza um padrão semelhante para todas as é estratégias (Twitter, Facebook, etc.) que todos os autores de estratégia adiram. Consultar a estratégia Consulte que podemos passar um function() que tem um token e concluído como parâmetros. A estratégia irá lealmente voltar atrás-numa vez que todas as faz-lo do trabalho. Uma vez que faz irá queremos armazenar o utilizador e intermédio o token de para que não precisamos pedi-lo novamente.


> [AZURE.IMPORTANT] 
O código acima leva qualquer utilizador que acontece autenticar a nossa servidor. Isto é conhecido como registo automática. Servidores de produção não seria pretende permitir que qualquer utilizador sem ter primeiro-los aceda através de um processo de registo que decidir. Isto é, normalmente, o padrão de que consulte nas aplicações do consumidor que permitem-lhe para registe-se ao Facebook, mas, em seguida, pedir-lhe para preencher informações adicionais. Se este não era uma aplicação de exemplo, vamos poderia ter apenas extraídas o e-mail do objeto token que é devolvido e, em seguida, perguntado-los para preencher informações adicionais. Uma vez que este é um servidor de teste podemos simplesmente adicioná-los à base de dados na memória.

- Em seguida, vamos adicionar os métodos que permitirão-nos controlar com sessão iniciada no utilizadores conforme necessário pelo Passport. Isto inclui serializar e anular a serialização as informações do utilizador:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- Em seguida, vamos adicionar o código para carregar o motor express. Aqui ver utilizamos /views o predefinido e fornece padrão de /routes Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Por fim, vamos adicionar as rotas que irão mão desativar os pedidos de início de sessão real que o `passport-azure-ad` motor:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. utilizar Passport para emitir pedidos de início de sessão e terminar sessão para Azure AD

A aplicação está agora corretamente configurada para comunicar com o ponto final 2.0 utilizando o protocolo de autenticação OpenID ligar.  `passport-azure-ad`tem o prestado cuidado de todos os detalhes atrativo de intencionada mensagens de autenticação, validar tokens a partir do Azure AD e manter a sessão do utilizador.  Tudo o que é permanece fornecer aos utilizadores uma forma de início de sessão, terminar sessão e reunir informações adicionais sobre o utilizador com sessão iniciada.

- Em primeiro lugar, permite adicionar os métodos de predefinido, início de sessão, conta e termine a nossa `app.js` ficheiro:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Analisemos estes detalhadamente:
    -   O `/` encaminhar irá redirecioná-se para a vista de index.ejs prisma o utilizador no pedido de (se existir)
    - O `/account` encaminhar irá primeiro, ***Certifique-se de que estamos são autenticados*** (Recomendamos implementar que abaixo) e, em seguida, passar o utilizador no pedido de modo a que poderemos obter informações adicionais sobre o utilizador.
    - O `/login` encaminhar ligarem nosso autenticador azuread openidconnect a partir do `passport-azuread` e se que não ter êxito irá redirecionam o utilizador regressar ao /login
    - O `/logout` irá simplesmente o logout.ejs de chamadas (e encaminhar) que limpa cookies e, em seguida, voltar ao utilizador ao index.ejs


- Para a última parte do `app.js`, vamos adicionar o método de EnsureAuthenticated é utilizado na `/account` acima.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- Por fim, vamos realmente criar o próprio servidor no `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. criar vistas e rotas no express para apresentar os nossos utilizador no Web site

Temos nosso `app.js` concluída. Agora precisamos simplesmente adicionar as vistas que lhe vai mostrar a informação é aceder ao utilizador, bem como alça e rotas a `/logout` e `/login` rotas já criámos.

- Criar o `/routes/index.js` encaminhar no diretório de raiz.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Criar o `/routes/user.js` encaminhar no diretório de raiz

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Estas rotas simples apenas irão transmitir ao longo de pedido da nossa vistas, incluindo o utilizador se existir.

- Criar o `/views/index.ejs` vista, sob o diretório de raiz. Esta é uma página simples que serão chamar nossos métodos de início de sessão e terminar sessão e permitir-nos de captar informações da conta. Aviso de que pode utilizamos o condicional `if (!user)` como o utilizador passou no pedido de provas temos uma com sessão iniciada do utilizador.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Criar o `/views/account.ejs` ver em que o diretório de raiz para que recomendamos possam ver informações adicionais que `passport-azuread` tem de colocar no pedido de utilizador.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Por fim, vamos tornar este aspeto muito adicionando um esquema. Criar o ' / Vista, dos views/layout.ejs sob o diretório de raiz

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Por fim, criar e executar a sua aplicação! 

Executar `node app.js` e navegue para`http://localhost:3000`


Inicie sessão com uma Account Microsoft pessoal ou uma conta escolar ou profissional e repare como a identidade do utilizador é reflectida na lista /account.  Tem agora uma aplicação web protegida utilizando protocolos padrão da indústria que podem autenticar os utilizadores com ambas as respetivas contas pessoais e trabalho/escola.

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um. zip aqui](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)ou pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


Agora pode mover para tópicos mais avançados.  Pretende experimentar:

[Proteger um Web API com Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
