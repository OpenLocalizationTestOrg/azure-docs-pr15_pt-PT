<properties
    pageTitle="Azure enlaces HTTP de funções e webhook | Microsoft Azure"
    description="Compreenda como utilizar accionadores HTTP e webhook e enlaces no Azure funções."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Azure enlaces HTTP de funções e webhook

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e o código de HTTP e webhook accionadores e enlaces nas funções Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON para enlaces HTTP e webhook

O ficheiro *function.json* fornece as propriedades que pertencem ao pedido e uma resposta.

Propriedades para o pedido de HTTP:

- `name`: Nome variável utilizado no código de função para o objeto request (ou corpo do pedido, no caso de funções Node.js).
- `type`: Tem de estar definida para *httpTrigger*.
- `direction`: Tem de estar definida *na*. 
- `webHookType`: Para WebHook accionadores, os valores válidos são *github*, *margem*e *genericJson*. Para um acionador de HTTP que não seja um WebHook, defina esta propriedade para uma cadeia vazia. Para mais informações sobre WebHooks, consulte a seguinte secção [WebHook accionadores](#webhook-triggers) .
- `authLevel`: Não aplicável a WebHook accionadores. Defina a "funcionar" para exigir o registo a chave de API, "anónima" requisito chave largar API ou "admin" para exigir o registo a chave de API principal. Consulte o artigo [teclas API](#apikeys) abaixo para obter mais informações.

Propriedades para a resposta de HTTP:

- `name`: Nome variável utilizado no código de função para o objeto de resposta.
- `type`: Tem de estar definida para *http*.
- `direction`: Tem de estar definida para *fora*. 
 
Exemplo *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>WebHook accionadores

Um accionador WebHook é um acionador de HTTP que tem as seguintes funcionalidades concebidas para WebHooks:

* Para fornecedores de WebHook específicos (atualmente GitHub e margem são suportados), o tempo de execução de funções valida assinatura do fornecedor.
* Para as funções de Node.js, o tempo de execução de funções fornece o corpo do pedido em vez do objeto request. Não existe nenhuma tratamento especial para c# funções, uma vez que pode controlar o que é fornecido ao especificar o tipo de parâmetro. Se especificar `HttpRequestMessage` obtém o objeto do pedido. Se especificar um tipo POCO, o tempo de execução de funções tenta analisar um objeto JSON no corpo do pedido para preencher as propriedades do objeto.
* Para acionar uma WebHook função o pedido de HTTP tem de incluir uma tecla de API. Para accionadores não WebHook HTTP, este requisito é opcional.

Para obter informações sobre como configurar um GitHub WebHook, consulte o artigo [GitHub programador - criar WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>URL para acionar a função

Para acionar uma função, enviar um pedido de HTTP a um URL que é uma combinação do URL da aplicação de função e o nome da função:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>Teclas de API

Por predefinição, uma tecla de API tem de ser incluída com um pedido HTTP para acionar uma função HTTP ou WebHook. A tecla pode ser incluída numa variável de cadeia de consulta denominada `code`, ou podem ser incluído num `x-functions-key` cabeçalho HTTP. Para as funções que não sejam WebHook, pode indicar que uma tecla de API não é necessária definindo a `authLevel` propriedade para "anónimo" no ficheiro *function.json* .

Pode encontrar valores chave API na pasta *D:\home\data\Functions\secrets* no sistema de ficheiros da aplicação de função.  A chave principal e uma tecla de função são definidas no ficheiro de *host.json* , tal como é mostrado neste exemplo. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

A tecla de função a partir do *host.json* pode ser utilizada para acionar qualquer função mas não acionam uma função desativada. A chave principal podem ser utilizada para acionar qualquer função e irá acionar uma função, mesmo se estiver desativado. Pode configurar uma função para exigir a chave principal definindo a `authLevel` propriedade a "admin". 

Se a pasta de *segredos* contiver um ficheiro JSON com o mesmo nome de uma função, o `key` propriedade desse ficheiro também pode ser utilizada para acionar a função e esta tecla funcionam apenas com a função que se refere a. Por exemplo, a tecla de API para uma função com o nome `HttpTrigger` especificado no *HttpTrigger.json* na pasta *segredos* . Eis um exemplo:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Quando estiver a configurar um accionador WebHook, não partilhe a chave principal com o fornecedor de WebHook. Utilize uma chave que funciona apenas com a função que processa a WebHook.  A chave principal podem ser utilizada para acionar qualquer função par desativado funções.

## <a name="example-c-code-for-an-http-trigger-function"></a>Código de exemplo c# para uma função de accionador HTTP 

O código de exemplo procura um `name` parâmetro na cadeia de consulta ou corpo do pedido de HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>Código de exemplo F # para uma função de accionador HTTP

O código de exemplo procura um `name` parâmetro na cadeia de consulta ou corpo do pedido de HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Terá de um `project.json` ficheiro que utiliza NuGet para fazer referência a `FSharp.Interop.Dynamic` e `Dynamitey` conjuntos, da seguinte forma:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Isto irá utilizar NuGet para obter as dependências e irá referenciar no seu script.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Código de Node.js de exemplo para uma função de accionador HTTP 

Este código de exemplo procura um `name` parâmetro na cadeia de consulta ou corpo do pedido de HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>Código de exemplo c# para uma função GitHub WebHook 

Este exemplo de código regista GitHub comentários de problema.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>Código de exemplo F # para uma função GitHub WebHook

Este exemplo de código regista GitHub comentários de problema.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Exemplo de código Node.js para uma função GitHub WebHook 

Este exemplo de código regista GitHub comentários de problema.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
