<properties
    pageTitle="Azure funções accionadores e enlaces | Microsoft Azure"
    description="Compreenda como utilizar accionadores e enlaces no Azure funções."
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
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure funções accionadores e enlaces referência para programadores


Este tópico fornece geral de referência para accionadores e enlaces. Inclui algumas das funcionalidades avançadas de encadernação e sintaxe suportado por todos os tipos de ligação.  

Se estiver à procura para obter informações detalhadas em redor de um tipo específico de accionador ou encadernação de codificação e configurar, poderá pretender clique das accionador ou enlaces listados abaixo em vez disso:

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Estes artigos partem do pressuposto de que já leu a [referência para programadores do Azure funções](functions-reference.md)e os artigos de referência para programadores [c#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md)ou [Node.js](functions-reference-node.md) .



## <a name="overview"></a>Descrição geral

Accionadores são respostas de evento utilizadas para acionar o código personalizado. Permitem-lhe responder a eventos em toda a plataforma Azure ou local. Enlaces representam os dados meta necessários utilizados para ligar o seu código accionador pretendida ou entrada associada ou dados de saída.

Para obter uma ideia geral dos diferentes enlaces que pode integrar com a sua aplicação do Azure função, consulte a tabela seguinte.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

Para compreender melhor accionadores e enlaces em geral, imaginemos que pretende executar alguns código processo largado um novo item para uma fila de armazenamento do Windows Azure. Funções Azure fornece um acionador de Azure fila de espera para suportar esta funcionalidade. Que precisa, as seguintes informações para monitorizar a fila de espera:
 
- A conta de armazenamento onde existe fila de espera.
- O nome de fila de espera.
- Um nome de variável que o seu código utilizaria para consultar o novo item foi eliminado na fila de espera.  
 
Um accionador fila enlace contém estas informações para uma função Azure. O ficheiro de *function.json* para cada função contém todos os enlaces relacionados.  Eis um exemplo *function.json* que contém uma fila acionar encadernação. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

O código pode enviar diferentes tipos de saída, dependendo da forma como o novo item de fila é processado. Por exemplo, poderá pretender escrever um novo registo a uma tabela de armazenamento do Windows Azure.  Para realizar esta tarefa, pode configurar uma ligação de saída a uma tabela de armazenamento do Windows Azure. Eis um exemplo *function.json* que inclui uma ligação de saída de tabela de armazenamento que pode ser utilizada com um acionador de fila de espera. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


A função seguinte c# responde a um novo item a ser eliminado na fila de espera e escreve uma nova entrada de utilizador para uma tabela de armazenamento do Windows Azure.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Para obter mais exemplos de código e informações mais específicas em relação à tipos de armazenamento Azure que são suportadas, consulte [funções de Azure accionadores e enlaces para armazenamento do Windows Azure](functions-bindings-storage.md).


Para utilizar as funcionalidades de encadernação mais avançadas no portal do Azure, clique na opção de **editor avançado** no separador **integrar** da sua função. O editor avançado permite-lhe editar o *function.json* diretamente no portal.

## <a name="dynamic-parameter-binding"></a>Encadernação de parâmetro dinâmicos 

Em vez de uma configuração estática a definição para as propriedades de ligação de saída, pode configurar as definições para dinamicamente estar ligada a dados que faz parte de ligação de entrada do seu acionador. Considere um cenário onde novo encomendas processadas utilizando uma fila de armazenamento do Windows Azure. Cada novo item de fila é uma cadeia JSON que contém, pelo menos, as seguintes propriedades:

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

Poderá pretender enviar uma mensagem de texto SMS com a sua conta Twilio como uma atualização que foi recebida a ordem de cliente.  Pode configurar o `body` e `to` enlace para dinamicamente estar vinculado ao resultado do campo da sua Twilio a `name` e `mobileNumber` que foram parte da entrada da seguinte forma.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
O código de função apenas tem agora iniciar o parâmetro de saída da seguinte forma. Durante a execução as propriedades de saída irão estar vinculadas a dados de entrada pretendidos.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

NODE.js

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>GUID aleatórios

Funções Azure fornece uma sintaxe para gerar GUID aleatórios com as ligações. A seguinte sintaxe de encadernação irá escrever um novo BLOB com um nome exclusivo existentes num contentor de armazenamento do Windows Azure os resultados: 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Devolver um resultado único

Em casos onde o seu código de função devolve um resultado único, pode utilizar uma ligação de saída denominada `$return` para manter uma assinatura de função mais natural no seu código. Só pode ser utilizado com os idiomas que suportam um valor de retorno (c#, Node.js, F #). O enlace seria semelhante a seguinte ligação de saída blob que é utilizada com um acionador de fila de espera.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


O código do c# seguinte devolve o resultado mais naturalmente sem utilizar um `out` parâmetro na assinatura função.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


Esta abordagem mesmo é demonstrada abaixo, com Node.js.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F # exemplo fornecido abaixo.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Isto também pode ser utilizado com vários parâmetros de saída ao designar um único resultado com `$return`.


## <a name="route-support"></a>Suporte de encaminhamento

Por predefinição quando cria uma função para um acionador HTTP ou WebHook, a função é endereçável com uma rota do formulário:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Pode personalizar esta rota utilizando opcional `route` propriedade o accionador HTTP do entrada encadernação. Por exemplo, o ficheiro de *function.json* seguintes define um `route` propriedade de um acionador de HTTP:

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

Utilizar esta configuração, a função está agora endereçável com a seguinte rota em vez da rota original.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Esta opção permite-o código da função suportar dois parâmetros, o endereço de `category` e `id`. Pode utilizar qualquer [Web API encaminhar restrição](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os parâmetros. O seguinte código de função do c# torna a utilização de ambos os parâmetros.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Eis o código da função Node.js para utilizar os mesmos parâmetros encaminhar.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

Por predefinição, todas as rotas função são com o prefixo *api*. Também pode personalizar ou remover o prefixo utilizando o `http.routePrefix` propriedade no seu ficheiro *host.json* . O exemplo seguinte remove o prefixo da rota *api* utilizando uma cadeia vazia para o prefixo no ficheiro *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Para obter informações detalhadas sobre como atualizar o ficheiro de *host.json* para a sua função, consulte o artigo [como atualizar a aplicação de função ficheiros](functions-reference.md#fileupdate). 

Ao adicionar esta configuração, a função está agora endereçável com a rota seguinte:

    http://<yourapp>.azurewebsites.net/products/electronics/357

Para obter informações sobre outras propriedades que pode configurar no seu ficheiro *host.json* , consulte o artigo [referência de host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos:

* [Testar uma função](functions-test-a-function.md)
* [Dimensionar uma função](functions-scale.md)
