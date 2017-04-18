<properties
    pageTitle="Azure funções DocumentDB enlaces | Microsoft Azure"
    description="Compreenda como utilizar o Azure DocumentDB enlaces no Azure funções."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funções de Azure, funções, evento processamento, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-documentdb-bindings"></a>Azure funções DocumentDB enlaces

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e código Azure DocumentDB enlaces Azure funções. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Azure DocumentDB encadernação de entrada

Ligações de entrada podem carregar um documento a partir de uma coleção de DocumentDB e transmiti-lo diretamente para a encadernação. O id do documento pode ser determinado com base em acionador de invocar a função. Numa função c#, quaisquer alterações efetuadas para o registo serão automaticamente enviadas para a coleção de quando a função sai com êxito.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON DocumentDB enlaces de entrada

O ficheiro *function.json* fornece as seguintes propriedades:

- `name`: Nome variável utilizado no código de função para o documento.
- `type`: tem de estar definida para "documentdb".
- `databaseName`: A base de dados que contém o documento.
- `collectionName`: A coleções de sites que contém o documento.
- `id`: O Id do documento para obter. Esta propriedade suporta enlaces semelhantes a "{queueTrigger}", que irá utilizar o valor de cadeia da mensagem fila como o documento ID.
- `connection`: Esta cadeia tem de ser um conjunto de definição da aplicação para o ponto final para a sua conta DocumentDB. Se optar por sua conta a partir do separador integrar, será criada por si uma nova definição de aplicação com um nome que assume a forma seguinte, yourAccount_DOCUMENTDB. Se necessitar de criar manualmente a definição de aplicação, a cadeia de ligação real terá o seguinte formulário, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- ' direção: tem de estar definida para *"em"*.

Exemplo *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Exemplo de entrada código DocumentDB Azure para um accionador c# fila de espera
 
Utilizar o function.json de exemplo acima, a ligação de entrada DocumentDB irá obter o documento com o id que corresponde à cadeia da mensagem fila de espera e transmiti-lo para o parâmetro 'documento'. Se não for encontrado esse documento, o parâmetro 'documento' será nulo. O documento, em seguida, será atualizado com o novo valor de texto quando sai a função.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Exemplo de entrada código DocumentDB Azure para um acionador de fila F #

Utilizar o function.json de exemplo acima, a ligação de entrada DocumentDB irá obter o documento com o id que corresponde à cadeia da mensagem fila de espera e transmiti-lo para o parâmetro 'documento'. Se não for encontrado esse documento, o parâmetro 'documento' será nulo. O documento, em seguida, será atualizado com o novo valor de texto quando sai a função.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

É necessário um `project.json` ficheiro que utiliza NuGet para especificar o `FSharp.Interop.Dynamic` e `Dynamitey` pacotes como dependências de pacote, da seguinte forma:

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

Isto irá utilizar NuGet para obter as dependências e irá referenciar no seu script.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Exemplo de entrada código DocumentDB Azure para um accionador de fila Node.js
 
Utilizar o function.json de exemplo acima, a ligação de entrada DocumentDB obterá o documento com o id que corresponde à cadeia da mensagem fila de espera e passar para o `documentIn` encadernação propriedade. Nas funções de Node.js, documentos actualizados não lhe forem enviados para a coleção. No entanto, pode passar a ligação de entrada diretamente para um resultado de DocumentDB encadernação com nome `documentOut` para suportar as atualizações. Este exemplo de código atualiza a propriedade texto do documento de entrada e define-a como o documento de saída.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Azure DocumentDB enlaces de saída

As funções podem escrever JSON ligação de saída de documentos para uma base de dados do Azure DocumentDB utilizando o **Azure DocumentDB documento** . Para mais informações sobre Azure DocumentDB reveja a [Introdução às DocumentDB](../documentdb/documentdb-introduction.md) e o [tutorial de introdução](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>ligação de saída Function.JSON para DocumentDB

O ficheiro function.json fornece as seguintes propriedades:

- `name`: Nome variável utilizado no código de função para o novo documento.
- `type`: tem de estar definida para *"documentdb"*.
- `databaseName`: A base de dados que contém a coleção de onde será criado o novo documento.
- `collectionName`: A colecção onde será criado o novo documento.
- `createIfNotExists`: Este é um valor booleano para indicar se a coleção de será criada se não existir. A predefinição é *Falso*. O motivo para isto é novo coleções de sites são criadas com débito reservado, que possui preços implicações. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Esta cadeia tem de ser que definir uma **Definição de aplicação** para o ponto final para a sua conta DocumentDB. Se optar por sua conta a partir do separador **integrar** , uma nova definição de aplicação será criada por si com um nome que assume a forma seguinte, `yourAccount_DOCUMENTDB`. Se necessitar de criar manualmente a definição de aplicação, a cadeia de ligação real terá o seguinte formulário, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: tem de estar definida para *"saída"*. 
 
Exemplo function.json:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Azure DocumentDB de exemplo de código de saída para um accionador de fila Node.js

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

O documento de saída:

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Azure DocumentDB de exemplo de código de saída para um acionador de fila F #

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Azure DocumentDB de exemplo de código de saída para um accionador c# fila de espera


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Nome de ficheiro de definição de exemplo DocumentDB saída código Azure

Se pretender definir o nome do documento na função, basta definir a `id` valor.  Por exemplo, se JSON conteúdo para um empregado foi a ser interrompida na fila semelhante ao seguinte:

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Pode utilizar o código seguinte c# numa função accionador fila: 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

Ou o código de F # equivalente:

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Resultado do exemplo:

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
