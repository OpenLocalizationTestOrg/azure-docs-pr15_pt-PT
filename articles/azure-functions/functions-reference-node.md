<properties
    pageTitle="Referência para programadores do Azure funções NodeJS | Microsoft Azure"
    description="Compreenda como desenvolver funções Azure utilizando NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Referência para programadores do Azure NodeJS de funções

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [NODE.js](../articles/azure-functions/functions-reference-node.md)

A experiência de nó/JavaScript para as funções de Azure torna mais fácil exportar uma função que lhe é transmitida um `context` objeto para comunicar com o tempo de execução e para receber e enviar dados através de enlaces.

Este artigo assume que já leu a [referência para programadores do Azure funções](functions-reference.md).

## <a name="exporting-a-function"></a>Exportar uma função

Todas as funções de JavaScript tem de exportar um único `function` através do `module.exports` para runtime localizar a função e executá-la. Esta função tem de incluir sempre um `context` objeto.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Ligações de `direction === "in"` são transmitidos ao longo de como argumentos de função, pode utilizar o que significa que [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) para processar dinamicamente novas entradas (por exemplo, utilizando `arguments.length` para iteramos sobre todas as entradas). Esta funcionalidade é muito conveniente caso só tenha um accionador com nenhuma entrada adicional, tal como seja previsível que pode aceder aos dados de accionador sem referenciar o seu `context` objeto.

Os argumentos são transmitidos sempre ao longo da função pela ordem em que aparecem no *function.json*, mesmo se não especificá-los no seu extrato de exportações. Por exemplo, se tiver `function(context, a, b)` e alterá-la para `function(context, a)`, pode continuar a obter o valor de `b` no código de função ao referir `arguments[3]`.

Todos os enlaces, independentemente de direção, também são transmitidos ao longo `context` objeto (ver abaixo). 

## <a name="context-object"></a>objecto de contexto

O tempo de execução utiliza um `context` objecto para transmitir dados de e para a sua função e permitem-lhe comunicar com o tempo de execução.

O objeto de contexto é sempre o primeiro parâmetro a uma função e sempre devem ser incluído porque tem métodos tais como `context.done` e `context.log` que são necessários para utilizar corretamente o tempo de execução. Pode atribuir um nome no objeto que goste (ou seja, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>Context.Bindings

O `context.bindings` objeto recolhe todos os seus dados de entrada e saídos. Os dados são adicionados para o `context.bindings` objeto através de `name` propriedade da ligação. Por exemplo, dado que a seguinte definição de encadernação *function.json*, pode aceder ao conteúdo de fila através do `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

O `context.done` função indica o tempo de execução que já está em execução. Isto é importante para chamar quando terminar com a função; caso não pretenda, o tempo de execução ainda nunca saberá que a sua função concluída. 

O `context.done` função permite-lhe transmitir novamente um erro definidos pelo utilizador para o tempo de execução, bem como um saco de propriedade das propriedades que irá substituir as propriedades na `context.bindings` objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>Context.log(Message)

O `context.log` método permite-lhe declarações de registo que estão associadas em conjunto para fins de registo de saída. Se utilizar `console.log`, as suas mensagens irão mostrar apenas para o registo ao nível do processo, que não é tão útil.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

O `context.log` método suporta o mesmo formato de parâmetro que suporta o nó [util.format método](https://nodejs.org/api/util.html#util_util_format_format) . Por isso, por exemplo, código da seguinte forma:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

pode ser escrito da seguinte forma:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Accionadores HTTP: context.req e context.res

No caso de HTTP accionadores, porque é um padrão comuns a utilizar `req` e `res` para os objetos de pedidos e respostas HTTP, podemos decidido tornam mais fácil aceder no objeto contexto, em vez de forçar a utilizar o total `context.bindings.name` padrão.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Versão nó e gestão do pacote

A versão de nó atualmente está bloqueada na `5.9.1`. Está a estamos a investigar adicionar suporte para mais versões e tornando configuráveis.

Pode incluir pacotes na sua função ao carregar um ficheiro de *package.json* para a pasta do seu função no sistema de ficheiros a aplicação de função. Para o ficheiro carregar instruções, consulte a secção **como atualizar os ficheiros de aplicação de função** do [tópico de referência para programadores Azure funções](functions-reference.md#fileupdate). 

Também pode utilizar `npm install` na interface de linha de comandos da aplicação de função SMS (Kudu):

1. Navegue para: `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Consola de depuração > CMD**.

3. Navegue para `D:\home\site\wwwroot\<function_name>`.

4. Executar `npm install`.

Assim que os pacotes de precisar estiverem instalados, importá-las para a função das formas habitual (ou seja, através do `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou uma aplicação definir valor, utilize `process.env`, conforme mostrado no exemplo seguinte código:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Suporte de à máquina/CoffeeScript

Não existem ainda qualquer suporte direto para automática-a compilar à máquina/CoffeeScript através do runtime, para que iria todos precisa de ser resolvido fora runtime, no momento da implementação. 

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos:

* [Referência para programadores do Azure funções](functions-reference.md)
* [Azure funções c# de referência para programadores](functions-reference-csharp.md)
* [Azure funções F # de referência para programadores](functions-reference-fsharp.md)
* [Azure accionadores de funções e enlaces](functions-triggers-bindings.md)
