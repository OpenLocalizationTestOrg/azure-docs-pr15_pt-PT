<properties
    pageTitle="Azure AD 2.0 NodeJS da Web API | Microsoft Azure"
    description="Como construir uma API de Web NodeJS aceita tokens a partir de ambas as Account Microsoft pessoal e contas escolar ou profissional."
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
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="secure-a-web-api-using-nodejs"></a>Proteger uma API Web utilizando node.js

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

Com o Azure Active Directory o ponto final 2.0, pode proteger um API do Web utilizando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , permitindo aos utilizadores com conta Microsoft de pessoal e trabalho ou escola contas para o access segura a API do seu Web.

**Passport** é software intermédio de autenticação para Node.js. Extremamente flexível e modular, Passport pode ser unobtrusively ignorado para qualquer Express com base em ou Resitify aplicação web. Um conjunto abrangente de estratégias suportar uma autenticação utilizando um nome de utilizador e palavra-passe, Facebook, Twitter e muito mais. Desenvolvemos uma estratégia para o Microsoft Azure Active Directory. Iremos instalar esta módulo e, em seguida, adicionar o Microsoft Azure Active Directory `passport-azure-ad` Plug-in.

## <a name="download"></a>Transferir
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Aplicação concluída é fornecida no final deste tutorial também.


## <a name="1-register-an-app"></a>1. Registe-se uma aplicação
Criar uma nova aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estes [passos detalhados](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copiar para baixo o **Id da aplicação** atribuído para a sua aplicação, terá de-la mais rapidamente.
- Adicione a plataforma **móvel** para a sua aplicação.
- Copiar para baixo o **URI redirecionar** a partir do portal. Tem de utilizar o valor predefinido de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="2-download-nodejs-for-your-platform"></a>2: Transferir node.js para a sua plataforma
Para utilizar com êxito neste exemplo, tem de ter uma instalação de trabalho do Node.js.

Instale Node.js a partir de [http://nodejs.org](http://nodejs.org).

## <a name="3-install-mongodb-on-to-your-platform"></a>3: MongoDB instalar para sua plataforma

Para utilizar com êxito neste exemplo, tem de ter uma instalação de trabalho do MongoDB. Irá utilizamos MongoDB para tornar os nossos persistant REST API em várias instâncias de servidor.

Instale MongoDB a partir de [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este tutorial assume que utiliza os pontos finais da instalação e servidor predefinido para MongoDB, que, no momento deste escrita, é: mongodb://localhost

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4: Instale os módulos Restify a API do seu Web

Vamos utilizar Resitfy para construir o nosso REST API. Restify um quadro de aplicação Node.js mínimo e flexível deriva da Express que tem um conjunto robusto de funcionalidades para criar REST APIs na parte superior de ligar.

### <a name="install-restify"></a>Instalar Restify

A partir da linha de comandos, mude de directórios ao diretório azuread. Se o diretório de **azuread** não existir, crie-a.

`cd azuread`- ou -`mkdir azuread;`

Escreva o seguinte comando:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>Obteve um erro?

Quando utilizar npm em alguns sistemas operativos, poderá receber um erro de erro: EPERM, chmod '/ usr local/Reciclagem /...' e um pedido para experimentar a conta a executar como administrador. Se tal acontecer, utilize o comando sudo para executar npm num nível de privilégio mais acima.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Obteve um erro em relação à DTrace?

Poderá ver algo parecido com ao instalar o Restify:

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


Restify fornece um poderoso mecanismos para chamadas de resto utilizando DTrace de rastreio. No entanto, muitos sistemas operativos não possui DTrace disponível. Em segurança pode ignorar estes erros.


O resultado deste comando deve aparecer semelhante ao seguinte:


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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5: instalar Passport.js para a Web API

[Passport](http://passportjs.org/) é software intermédio de autenticação para Node.js. Extremamente flexível e modular, Passport pode ser unobtrusively ignorado para qualquer Express com base em ou Resitify aplicação web. Um conjunto abrangente de estratégias suportar uma autenticação utilizando um nome de utilizador e palavra-passe, Facebook, Twitter e muito mais. Desenvolvemos uma estratégia para o Azure Active Directory. Iremos instalar esta módulo e, em seguida, adicionar a estratégia Azure Active Directory Plug-in.

A partir da linha de comandos, mude de directórios ao diretório azuread.

Introduza o seguinte comando para instalar passport.js

`npm install passport`

O resultado do comando deve aparecer semelhante ao seguinte:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Adicionar Passport Azure-AD à Web API

Em seguida, vamos adicionar estratégia OAuth, utilizar passport-azuread, um conjunto de estratégias que se ligam Azure Active Directory com Passport. Utilizamos esta estratégia portadores Tokens de neste exemplo Rest API.

> [AZURE.NOTE] Apesar de oauth2 ainda fornece um quadro no qual pode ser emitido qualquer tipo de token conhecido, apenas determinados tipos de token tem adquirida abrangentes utilização. Para proteger os pontos finais, que activou o para ser Tokens de tipo de ligação. Tokens portadores são mais amplamente emitido tipo de token na oauth2 ainda e partem do pressuposto de muitas implementações que tokens portadores são o único tipo de token emitido.

A partir da linha de comandos, mude de directórios ao diretório azuread

Escreva o seguinte comando para instalar Passport.js passport azure-ad módulo:

`npm install passport-azure-ad`

O resultado do comando deve aparecer semelhante ao seguinte:

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: Adicionar MongoDB módulos a API do seu Web

Vamos utilizar MongoDB como nosso arquivo de dados por que motivo, precisamos de instalar ambas as amplamente utilizado Plug-in para gerir modelos e os esquemas mangustos denominados, bem como o controlador de base de dados para MongoDB, também denominado MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: instalar módulos adicionais

Em seguida, podemos irá instalar os restantes módulos necessários.


A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`


Introduza os seguintes comandos para instalar os seguintes módulos no seu diretório node_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## <a name="9-create-a-serverjs-with-your-dependencies"></a>9: criar um server.js com o seu dependências

O ficheiro server.js estará a fornecer a maioria dos nossos funcionalidade para os nossos servidor Web API. Vamos vai adicionar a maior parte das nosso código para este ficheiro. Para fins de produção seria reestruturar a funcionalidade aos ficheiros mais pequenos, tais como rotas separadas e controladores. Para este demonstração irá utilizamos server.js para esta funcionalidade.

A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`

Criar um `server.js` ficheiro no nosso editor favorito e adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Guarde o ficheiro. Vamos regressarão à mesma.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: criar um ficheiro de configuração para guardar as definições do Azure AD

Este ficheiro código transmite os parâmetros de configuração do seu Portal do Azure Active Directory para Passport.js. Criado estes valores de configuração quando adicionou a API Web ao portal de na primeira parte do tutorial. Vamos explicam o que colocar os valores destes parâmetros após copiar o código.


A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`

Criar um `config.js` ficheiro no nosso editor favorito e adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>Valores requeridos

*IdentityMetadata*: Este é onde passport azure-ad terá o aspeto para os seus dados de configuração para o IdP, bem como as teclas validar os tokens JWT. Provavelmente não pretende alterar esta definição se utilizar o Azure Active Directory.

*audiência*: O redirecionamento URI a partir do portal.

> [AZURE.NOTE]
Vamos filmar nossas teclas em intervalos frequentes. Certifique-se de que lhe é sempre puxando a partir do URL de "openid_keys" e que a aplicação pode aceder à internet.


## <a name="11-add-configuration-to-your-serverjs-file"></a>11: adicionar a configuração para o seu ficheiro server.js

Precisamos de ler estes valores a partir do ficheiro de configuração que acabou de criar ao longo da nossa aplicação. Para fazer isto, podemos simplesmente adicionar o ficheiro. config como um recurso necessário na nossa aplicação e, em seguida, defina as variáveis globais no documento config.js

A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`

Abrir o `server.js` ficheiro no nosso editor favorito e adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Em seguida, adicione uma nova secção `server.js` com o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>Passo 12: Adicionar a modelo de MongoDB e as informações de esquema utilizando Moongoose

Agora toda esta preparação vai começar a pagar podemos vento estes três ficheiros em conjunto para um serviço de REST API.

Para este tutorial vamos utilizar MongoDB para armazenar os nossos tarefas, tal como é abordado no ***passo 4***.

Se se lembrar do ficheiro config.js que criámos no passo 11, podemos denominado nosso de base de dados de *lista de tarefas*, à medida que foi podemos colocar no final do nosso URL da ligação de mogoose_auth_local. Não precisa de criar previamente esta base de dados no MongoDB, -criará isto para-na primeira execução da nossa aplicação de servidor (partindo do princípio de que já não existe).

Agora que recomendamos tenha informa o servidor de base de dados que MongoDB Gostaríamos para utilizar, é necessário escrever algum código adicional para criar o modelo e um esquema para tarefas os nossos servidor.

#### <a name="discussion-of-the-model"></a>Debate do modelo

É muito simple nosso modelo de esquema e expandi-lo conforme necessário.

NAME - o nome do quem está atribuído à tarefa. Uma ***cadeia***

TAREFA - a tarefa própria. Uma ***cadeia***

DATA - a data em que a tarefa é para conclusão. Uma ***data/hora***

CONCLUÍDA - se a tarefa é concluída ou não. ***BOOLEANO***

#### <a name="creating-the-schema-in-the-code"></a>Criar o esquema no código


A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`

Abrir o `server.js` ficheiro no nosso editor favorito e adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Isto irá ligar ao servidor MongoDB e à mão novamente um objecto do esquema-nos.

#### <a name="using-the-schema-create-our-model-in-the-code"></a>Utilizando o esquema, crie o nosso modelo no código de

Abaixo o código que escreve acima, adicione o seguinte código:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Como pode descobrir do código, podemos criar nossa esquema e, em seguida, criar um objeto de modelo que utilizamos para armazenar os nossos dados em todo o código quando podemos definir os nossos ***rotas***.

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>Passo 13: Adicionar a nossa rotas para o nosso server tarefa REST API

Agora que temos um modelo de base de dados para trabalhar com, vamos adicionar rotas que utilizamos para o nosso server REST API.

### <a name="about-routes-in-restify"></a>Sobre rotas em Restify

Rotas trabalhar no Restify exata mesma forma que são feitas de utilizar a pilha de Express. Pode definir rotas utilizando o URI que esperava as aplicações de cliente para ligar. Normalmente, pode definir as rotas num ficheiro separado. Para as nossas finalidades, podemos irá colocar os nossos rotas no ficheiro server.js. Recomendamos que factor estes para os seus próprios ficheiro para utilização de produção.

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


Este é o padrão de ao nível do mais básico. Resitfy (e Express) fornecem muito mais aprofundada functionaltiy como definir tipos de aplicação e efetuar encaminhamento complexa através de pontos finais diferentes. Para as nossas finalidades, podemos irão manter estas rotas muito simplesmente.

#### <a name="add-default-routes-to-our-server"></a>Adicionar rotas predefinidas a nossa servidor

Iremos agora adicione as rotas CRUD básicas da atualização de criar, recuperar, e eliminar.

A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`

Abrir o `server.js` ficheiro no nosso editor favorito e adicione as seguintes informações abaixo as entradas de base de dados que efetuou acima:

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
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
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
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
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

### <a name="add-some-error-handling-for-the-routes"></a>Adicionar alguns para as rotas de processamento de erros

Faz sentido para adicionar alguns processamento de erros para que recomendamos possam comunicar volta para o cliente o problema que se tenha deparado ou de uma forma conseguem compreender.

Adicione o seguinte código sob o código que escreveu acima:

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


## <a name="step-14-create-your-server"></a>Passo 14: Crie o seu servidor!

Temos nossa base de dados definido, temos nossas rotas no local e a última coisa a fazer é adicionar nossa instância de servidor que irá gerir as nossas chamadas.

Restify (e Express) tem muitas abrangente personalização que pode fazer para um servidor de REST API, mas novamente vamos utilizar a configuração mais básica para as nossas finalidades.

```Javascript
/**
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
}));
```
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15: adicionar rotas (sem autenticação por agora)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16: antes de adicionar podemos OAuth suporte, vamos executar o servidor.

Testar fora do seu servidor antes de adicionar podemos autenticação

A forma mais fácil para fazer isto é utilizando o laço numa linha de comandos. Antes de podemos fazê-lo, é necessário um utilitário simple que permite-nos analisar saída como JSON. Para o fazer, instale a ferramenta de json à medida que utilizam o os exemplos abaixo.

`$npm install -g jsontool`

Este procedimento instala a ferramenta JSON globalmente. Agora que recomendamos tenha feito que – reproduzir com o servidor:

Em primeiro lugar, certifique-se de que o seu isntance monogoDB está em execução...

`$sudo mongod`

Em seguida, mude para o directório e inicie curling...

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Em seguida, podemos pode adicionar uma tarefa da seguinte forma:

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

A resposta deve ser:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
E podemos listar tarefas para são Brandão da seguinte forma:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tudo isto funciona, podemos está prontos para adicionar OAuth ao servidor de REST API.

**Se tiver um servidor de REST API com MongoDB!**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17: Adicionar autenticação ao nosso servidor de API REST

Agora que temos uma REST API em execução (congrats, btw!) vamos obter para torná-lo útil contra Azure AD.

A partir da linha de comandos, altere directórios para a pasta **azuread** se ainda não estiver lá:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: Utilize o oidcbearerstrategy que está incluído no passport azure-ad

Até ao momento Criámos um servidor de resto TODO típico sem qualquer tipo de autorização. Este é onde foi iniciar colocar em que em conjunto.

Em primeiro lugar, precisamos de indicar queremos utilizar Passport. Coloca esta direita após a configuração do servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Ao escrever APIs deve sempre ligar os dados para algo exclusivo do token que o utilizador não é possível falsificar. Quando este servidor armazena os itens TODO, armazena-os com base no ID da subscrição de utilizador no token de (denominado através de token.sub) que recomendamos colocar no campo "proprietário". Isto garante que apenas esse utilizador pode aceder ao seus tarefas e ninguém pode aceder as tarefas introduzidas. Não existe nenhuma exposição na API do proprietário "da" para que um utilizador externo pode pedir de outras tarefas, mesmo se estes estão autenticados.

Em seguida, vamos utilizar a estratégia de abrir ID ligar portadores que vem com passport azure-ad. Veja apenas o código por agora, irá explica-brevemente. Coloca isto que depois pated acima:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport utiliza um padrão semelhante para todas as é estratégias (Twitter, Facebook, etc.) que todos os autores de estratégia adiram. Consultar a estratégia Consulte que podemos passar um function() que tem um token e concluído como parâmetros. A estratégia irá lealmente voltar atrás-numa vez que todas as faz-lo do trabalho. Uma vez que faz irá queremos armazenar o utilizador e intermédio o token de para que não precisamos pedi-lo novamente.

> [AZURE.IMPORTANT]
O código acima leva qualquer utilizador que acontece autenticar a nossa servidor. Isto é conhecido como registo automática. Servidores de produção não seria pretende permitir que qualquer utilizador sem ter primeiro-los aceda através de um processo de registo que decidir. Isto é, normalmente, o padrão de que consulte nas aplicações do consumidor que permitem-lhe para registe-se ao Facebook, mas, em seguida, pedir-lhe para preencher informações adicionais. Se este não era um programa de linha de comandos, podemos poderia ter apenas extraídas o e-mail do objeto token que é devolvido e, em seguida, pedido-los para preencher informações adicionais. Uma vez que este é um servidor de teste podemos simplesmente adicioná-los à base de dados na memória.

### <a name="2-finally-protect-some-endpoints"></a>2. por fim, proteger alguns pontos finais

Proteger os pontos finais ao especificar a chamada passport.authenticate() com o protocolo que pretende utilizar.

Editar os nossos rota no nosso código do servidor para fazer algo mais interessante:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18: execute novamente a aplicação de servidor e certifique-se de que rejeita

Vamos utilizar `curl` novamente para ver se agora temos oauth2 ainda proteção contra a nossa os pontos finais. Vamos irá fazê-lo antes de runnning qualquer um dos nossos cliente SDK contra este ponto final. Os cabeçalhos devolvidos devem ser suficiente para dê-na que estamos para baixo o caminho à direita.

Em primeiro lugar, certifique-se de que o seu isntance monogoDB está em execução...

    $sudo mongod

Em seguida, mude para o directório e inicie curling...

    $ cd azuread
    $ node server.js

Experimente uma mensagem básica:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Um 401 é a resposta que procura aqui, à medida que indica que a camada Passport está a tentar redirecionar para o ponto final autorizar, que corresponde exatamente o que pretende.


## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Parabéns! Tem um serviço de API REST utilizando oauth2 ainda!

Tenha correu medida em-lo com este servidor sem utilizar um cliente compatível oauth2 ainda. Terá de seguir um tutorial adicional.

Se apenas estava à procura para obter informações sobre como implementar um REST API utilizando Restify e a oauth2 ainda, tem mais do que suficiente código para manter desenvolver o seu serviço e aprendendo a criar neste exemplo.

## <a name="next-steps"></a>Próximos passos

Para sua referência, o exemplo concluída (sem os valores de configuração) [é fornecida como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)ou pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Agora pode mover para tópicos mais avançados.  Pretende experimentar:

[Proteger uma aplicação de web Node.js utilizando o ponto final 2.0 >>](active-directory-v2-devquickstarts-node-web.md)

Para obter recursos adicionais, consulte:
- [O Guia do programador 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
