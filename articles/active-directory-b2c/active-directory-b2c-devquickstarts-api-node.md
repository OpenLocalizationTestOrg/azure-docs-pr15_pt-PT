<properties
    pageTitle="Azure AD B2C: Proteger um web API utilizando Node.js | Microsoft Azure"
    description="Como criar um web Node.js API aceita tokens a partir de um inquilino B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: Proteger um web API utilizando Node.js

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Azure Active Directory (Azure AD) B2C, pode proteger um web API utilizando tokens de acesso OAuth 2.0. Estes tokens permitir que as aplicações de cliente que utilizar B2C do Azure AD para autenticar à API. Este artigo mostra-lhe como criar um "lista de acções a fazer" API que permite aos utilizadores adicionar e lista de tarefas. Web API é protegida utilizando Azure AD B2C e só permite autenticados utilizadores para gerir a sua lista de itens pendentes.

> [AZURE.NOTE]  Este exemplo foi escrito de estar ligado utilizando o nosso [iOS B2C aplicação de exemplo](active-directory-b2c-devquickstarts-ios.md). Fazer em primeiro lugar a guia passo a passo atual e, em seguida, siga juntamente com essa amostra.

**Passport** é software intermédio de autenticação para Node.js. Flexível e modular, Passport pode ser instalado unobtrusively em qualquer baseadas em expresso ou Restify aplicação web. Um conjunto abrangente de estratégias suporta a autenticação utilizando um nome de utilizador e palavra-passe, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Azure Active Directory (Azure AD). Instalar esta módulo e, em seguida, adicionar Azure AD `passport-azure-ad` Plug-in.

Para efetuar este exemplo, tem de:

1. Registe uma aplicação do Azure AD.
2. Configurar a sua aplicação para utilizar do Passport `azure-ad-passport` Plug-in.
3. Configure uma aplicação de cliente para ligar a web "lista de acções a fazer" API.


## <a name="get-an-azure-ad-b2c-directory"></a>Obter um directory B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos.  Um diretório é um contentor para todos os utilizadores, aplicações, grupos e muito mais.  Se não tiver um já, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C que lhe dá Azure AD algumas informações que necessita de comunicar com a sua aplicação de forma segura. Neste caso, a aplicação de cliente e o web API são representados por única **ID da aplicação**, uma vez que incluem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). Não se esqueça de:

- Incluir um **app/web da web api** na aplicação
- Introduza `http://localhost/TodoListService` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
- Criar um **secreta de aplicação** para a sua aplicação e copiá-lo. Precisa destes dados mais tarde. Tenha em atenção que este valor tem de estar [escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de a utilizar.
- Copie o **ID da aplicação** que está atribuída a sua aplicação. Precisa destes dados mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém duas experiências identidade: inscrever-se e iniciar sessão. Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Quando cria as políticas de três, não se esqueça de:

- Selecione o **nome a apresentar** e outros atributos de inscrição na política de inscrição.
- Selecione os **nome a apresentar** e o **ID do objeto** afirmações da aplicação em todas as políticas.  Pode escolher também em outras afirmações.
- Copiar para baixo no **nome** da política de cada depois de o criar. Deve ter o prefixo `b2c_1_`.  Tem os nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de ter criado as políticas de três, está pronto para criar a sua aplicação.

Para saber mais sobre como funcionam as políticas de Azure AD B2C, comece com o [.NET web app introdução tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Transferir o código

O código para este tutorial [são mantidas no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Para criar a amostra à medida que avança, pode [Transferir um projeto de estrutura como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

A aplicação completa também está [disponível como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) ou na `complete` ramo do mesmo repositório de.

## <a name="download-nodejs-for-your-platform"></a>Transferir Node.js para a sua plataforma

Para utilizar com êxito este exemplo, necessita de uma instalação de trabalho do Node.js. 

Instale Node.js a partir de [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalar MongoDB para a sua plataforma

Para utilizar com êxito este exemplo, necessita de uma instalação de trabalho do MongoDB. Utilizamos MongoDB para tornar o seu REST API persistente em várias instâncias de servidor.

Instale MongoDB a partir de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este guia passo a passo assume que utiliza os pontos finais da instalação e servidor predefinido para MongoDB, que no momento deste escrita é `mongodb://localhost`.

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalar os módulos Restify na sua web API

Utilizamos Restify para construir a sua REST API. Restify um quadro de aplicação Node.js mínimo e flexível deriva da Express. Tem um conjunto robusto de funcionalidades para criar REST APIs na parte superior de ligar.

### <a name="install-restify"></a>Instalar Restify

A partir da linha de comandos, alterar o seu diretório para `azuread`. Se o `azuread` diretório não existir, criá-lo.

`cd azuread`ou`mkdir azuread;`

Introduza o seguinte comando:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>Obteve um erro?

Em alguns sistemas operativos, ao utilizar `npm`, poderá receber o erro `Error: EPERM, chmod '/usr/local/bin/..'` e um pedido de que a conta executa como administrador. Se este problema ocorre, utilize o `sudo` comando para executar `npm` num nível de privilégio mais acima.

#### <a name="did-you-get-a-dtrace-error"></a>Obteve um erro de DTrace?

Poderá ver algo parecido com este texto quando instala Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify fornece um mecanismo poderoso para traçar o resto chamadas utilizando DTrace. No entanto, muitos sistemas operativos não possui DTrace disponível. Em segurança pode ignorar estes erros.

O resultado do comando deverá ser semelhante a este texto:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalar Passport na sua web API

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá.

Instale Passport utilizando o seguinte comando:

`npm install passport`

O resultado do comando deve ser semelhante a este texto:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Adicionar passport azuread à web API

Em seguida, adicione a estratégia OAuth utilizando `passport-azuread`, um conjunto de estratégias que se ligam Azure AD com Passport. Utilize esta estratégia de tokens de tipo de ligação no exemplo REST API.

> [AZURE.NOTE] Apesar de oauth2 ainda fornece um quadro no qual pode ser emitido qualquer tipo de token conhecido, apenas determinados tipos de token tem adquirida utilização frequente. Os tokens para proteger os pontos finais são tokens de tipo de ligação. Estes tipos de tokens de são mais amplamente emitido no oauth2 ainda. Muitas implementações partem do pressuposto de que tokens portadores são o único tipo de token emitido.

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá.

Instalar o passaporte `passport-azure-ad` módulo utilizando o seguinte comando:

`npm install passport-azure-ad`

O resultado do comando deve ser semelhante a este texto:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Adicionar MongoDB módulos à web API

Este exemplo utiliza MongoDB como arquivo de dados. Para instalar o mangustos, amplamente utilizado Plug-in para gerir modelos e esquemas.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalar módulos adicionais

Em seguida, instale os restantes módulos necessários.

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Instalar os módulos do seu `node_modules` diretório:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>Criar um ficheiro de server.js com o seu dependências

O `server.js` ficheiro fornece a maioria das funcionalidades para o seu servidor Web API. 

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Criar um `server.js` ficheiro num editor de. Adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Guarde o ficheiro. Regressar à mesma mais tarde.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Criar um ficheiro de config.js para guardar as definições do Azure AD

Este ficheiro código transmite os parâmetros de configuração do Azure AD Portal para o `Passport.js` ficheiro. Criado estes valores de configuração quando adicionou web API para o portal de na primeira parte da guia passo a passo. Vamos explicam o que colocar os valores destes parâmetros depois de copiar o código.

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Criar um `config.js` ficheiro num editor de. Adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valores requeridos

`clientID`: O ID de cliente da sua aplicação Web API.

`IdentityMetadata`: Este é onde `passport-azure-ad` procura o fornecedor de identidades para os seus dados de configuração. Procura também as teclas validar os tokens de web JSON. 

`audience`: Uniform resource identifier (URI) a partir do portal que identifica a sua aplicação de chamada. 

`tenantName`: O seu nome de inquilino (por exemplo, a **contoso.onmicrosoft.com**).

`policyName`: A política de que pretende validar os tokens chegar ao seu servidor. Esta política deve ser a mesma política que utilizar sobre a aplicação de cliente para iniciar sessão.

> [AZURE.NOTE] Para a nossa pré-visualização B2C, utilize as mesmas políticas nos cliente e servidor de configuração. Se já tiver concluído uma guia passo a passo e criado estas políticas, não terá fazê-lo novamente. Uma vez que concluir a guia passo a passo, não deva tem de configurar novas políticas para walk-throughs de cliente no site.

## <a name="add-configuration-to-your-serverjs-file"></a>Adicionar a configuração para o seu ficheiro server.js

Para ler os valores a partir de `config.js` ficheiro que criou, adicionar o `.config` ficheiro como um recurso necessário na sua aplicação e, em seguida, defina as variáveis globais à `config.js` documento.

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abrir o `server.js` ficheiro num editor de. Adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Adicionar uma nova secção para `server.js` que inclui o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Em seguida, vamos adicionar alguns marcadores de posição para os utilizadores recebemos a partir de nossos aplicações chamadas.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Vamos e crie demasiado nosso do registo.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Adicione as informações de esquemas e modelo de MongoDB utilizando mangustos

Preparação anterior paga à medida que cumprir estes três ficheiros em conjunto num serviço REST API.

Para este guia passo a passo, utilize MongoDB para armazenar as tarefas, tal como debatido anteriormente.

No `config.js` ficheiro, designado por sua base de dados de **lista de tarefas**. Esse nome também foi o que incluir no final da `mongoose_auth_local` URL da ligação. Não é necessário criar previamente esta base de dados no MongoDB. Cria a base de dados por si na primeira execução da sua aplicação do servidor.

Depois de indicar ao servidor de base de dados que MongoDB para utilizar, tem de escrever algum código para criar o modelo e um esquema para as suas tarefas de servidor adicional.

### <a name="expand-the-model"></a>Expandir o modelo

Este modelo de esquema é simple. Pode expandi-lo conforme necessário.

`owner`: Quem é atribuído à tarefa. Este objeto é uma **cadeia**.  

`Text`: A tarefa própria. Este objeto é uma **cadeia**.

`date`: A data em que a tarefa é para conclusão. Este objeto é uma **data/hora**.

`completed`: Se a tarefa está concluída. Este objeto é **Booleano**.

### <a name="create-the-schema-in-the-code"></a>Criar o esquema no código

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abrir o `server.js` ficheiro num editor de. Adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Tem de criar primeiro o esquema e, em seguida, criar um objeto de modelo que utiliza para armazenar os seus dados em todo o código quando define as **rotas**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Adicionar rotas para o seu servidor de tarefa REST API

Agora que tem um modelo de base de dados para trabalhar com, adicione as rotas que utiliza para o seu servidor de REST API.

### <a name="about-routes-in-restify"></a>Sobre rotas no Restify

Rotas trabalhar no Restify da mesma forma que funcionam quando utilizam a pilha de Express. Definir rotas utilizando o URI que esperava as aplicações de cliente para ligar. 

Um padrão típico para uma rota Restify é:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify e Express pode fornecer muito funcionalidade mais aprofundada, tal como de definir tipos de aplicação e efetuar encaminhamento complexa através de pontos finais diferentes. Para efeitos neste tutorial, vamos manter estas rotas simples.

#### <a name="add-default-routes-to-your-server"></a>Adicione rotas predefinidas ao seu servidor

Agora, adicionar as rotas CRUD básicas de **Criar** e a **lista** para os nossos REST API. Outras rotas podem ser encontradas na `complete` ramo da amostra.

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abrir o `server.js` ficheiro num editor de. Abaixo as entradas de base de dados que efetuou acima adicionar as seguintes informações:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Adicionar para as rotas de processamento de erros

Adicione alguns processamento de erros para que possam comunicar quaisquer problemas que encontrar volta para o cliente de uma forma que possa compreender.

Adicione o seguinte código:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Criar o seu servidor

Agora tiver definido a base de dados e colocar as rotas no local. A última coisa que pode fazer consiste em Adicionar a instância de servidor que gere às chamadas.

Restify e Express fornecem personalização abrangente para um servidor de REST API, mas utilizamos a configuração mais básica aqui. 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Adicione as rotas no servidor (sem autenticação)

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>Adicione autenticação ao seu servidor de REST API

Agora que tem um servidor de REST API em execução, pode fazer útil contra Azure AD.

A partir da linha de comandos, alterar o seu diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Utilizar o OIDCBearerStrategy que está incluído no passport azure-ad


> [AZURE.TIP]
Quando escreve APIs, deve sempre ligar os dados para algo exclusivo do token que o utilizador não é possível falsificar. Quando o servidor armazena ToDo itens, é por isso, com base no **oid** do utilizador no token (denominado através de token.oid), que vai no campo "proprietário". Este valor assegura que apenas esse utilizador acede aos seus próprios itens de ToDo. Não existe nenhuma exposição na API do "proprietário," para que um utilizador externo pode pedir a outras pessoas ToDo itens, mesmo se estes estão autenticados.

Em seguida, utilize a estratégia de tipo de ligação que vem com `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport utiliza o mesmo padrão para todos os seus estratégias. Pode ser efetuada com um `function()` que tenha `token` e `done` como parâmetros. A estratégia vem novamente para si, depois de terminar, todo o seu trabalho. Em seguida, deve armazenar o utilizador e guardar o token de modo a que não precisa de volta a pedi-lo.

> [AZURE.IMPORTANT]
O código acima leva qualquer utilizador do que acontece autenticar ao seu servidor. Este processo é conhecido como autoregistration. Em servidores de produção, não permitem no qualquer acesso de utilizadores a API sem ter primeiro-los aceda através de um processo de registo. Este processo é normalmente o padrão de que consulte nas aplicações do consumidor que permitem-lhe registar utilizando o Facebook, mas, em seguida, pedir-lhe para preencher informações adicionais. Se este programa não era um programa da linha de comandos, podemos poderia ter extraídas o e-mail no objeto token que é devolvido e, em seguida, mais os utilizadores preencherem informações adicionais. Uma vez que este é um exemplo, vamos adicioná-los a uma base de dados na memória.



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Executar a aplicação de servidor verificar que a mesma rejeita

Pode utilizar `curl` para ver se tem agora oauth2 ainda proteção contra a sua os pontos finais. Os cabeçalhos devolvidos deverão ser suficientes para indicar ao que está no caminho à direita.

Certifique-se de que a sua MongoDB instância está em execução:

    $sudo mongodb

Mude para o diretório e executar o servidor:

    $ cd azuread
    $ node server.js

Numa nova janela terminal, executar`curl`

Experimente uma mensagem básica:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Um erro 401 é a resposta que pretende. Indica que a camada Passport está a tentar redirecionar para o ponto final autorizar.


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Tem agora um serviço de REST API que utiliza a oauth2 ainda

Tiver implementado um REST API utilizando Restify e OAuth! Tem agora código suficiente para que possa continuar desenvolver o seu serviço e criar neste exemplo. Foram trocadas medida em-lo com este servidor sem utilizar um cliente compatível com o oauth2 ainda. Para o respetivo passo seguinte utilize uma guia passo a passo adicional, como o nosso tutorial de [ligar a um web API utilizando iOS com B2C](active-directory-b2c-devquickstarts-ios.md) .


## <a name="next-steps"></a>Próximos passos

Agora pode mover para tópicos mais avançados, tais como:

[Ligar para um web API com iOS com B2C](active-directory-b2c-devquickstarts-ios.md)