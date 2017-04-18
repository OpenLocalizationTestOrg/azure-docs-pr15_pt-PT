<properties
    pageTitle="Azure funções accionadores e enlaces para armazenamento do Windows Azure | Microsoft Azure"
    description="Compreenda como utilizar accionadores de armazenamento do Windows Azure e enlaces no Azure funções."
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
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure funções accionadores e enlaces para armazenamento do Windows Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e código armazenamento do Windows Azure accionadores e enlaces nas funções Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure acionador de fila de armazenamento

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON para accionador de fila de armazenamento

O ficheiro *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para a fila ou a mensagem de fila de espera. 
- `queueName`: O nome da fila de espera para as de inquérito. Para regras de nomenclatura de fila de espera, consulte o artigo [atribuir nomes a filas e metadados](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: O nome de uma aplicação a definição que contém uma cadeia de ligação de armazenamento. Se as deixar `connection` vazio, o accionador irá funcionar com a cadeia de ligação de armazenamento predefinido para a aplicação, função, que é indicada pela definição de aplicação AzureWebJobsStorage.
- `type`: Tem de estar definida para *queueTrigger*.
- `direction`: Tem de estar definida *na*. 

Exemplo *function.json* para um accionador de fila de armazenamento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Tipos de suportados acionador de fila de espera

A mensagem de fila serialização pode ser anulada para qualquer um dos seguintes tipos de:

* Objeto (a partir do JSON)
* Cadeia
* Matriz de bytes 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Fila de espera accionador metadados

Pode obter metadados fila de espera de uma função ao utilizar estes nomes de variáveis:

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (outra forma de obter o texto da mensagem fila como uma cadeia)

Este exemplo de código c# obtém e registos de fila de metadados:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Tratamento de mensagens da fila corrompida foi

Mensagens cujo conteúdo faz com que uma função a falha chamam *corrompida foi mensagens*. Quando a função falha, a mensagem de fila de espera não é eliminada e eventualmente é recolhida novamente, a causar o ciclo de ser repetida. O SDK automaticamente pode interromper o ciclo de depois de um número limitado de iterações ou pode fazê-lo manualmente.

O SDK ligarem uma função até 5 vezes para processar uma mensagem de fila de espera. Se o experimentar quinto falhar, a mensagem é movida para uma fila corrompida foi.

Fila de espera corrompida foi chama-se *{originalqueuename}*-corrompida foi. Pode escrever é necessária uma função para processar mensagens da fila corrompida foi ao registo-los ou ao enviar uma notificação a esse atenção manual. 

Se pretende lidar com mensagens corrompida foi manualmente, pode obter o número de vezes que uma mensagem ter sido recolhida para cima para processamento verificando `dequeueCount`.

## <a id="storagequeueoutput"></a>Ligação de saída do Azure fila de armazenamento

#### <a name="functionjson-for-storage-queue-output-binding"></a>ligação de saída Function.JSON para fila de armazenamento

O ficheiro *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para a fila ou a mensagem de fila de espera. 
- `queueName`: O nome da fila de espera. Para regras de nomenclatura de fila de espera, consulte o artigo [atribuir nomes a filas e metadados](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: O nome de uma aplicação a definição que contém uma cadeia de ligação de armazenamento. Se as deixar `connection` vazio, o accionador irá funcionar com a cadeia de ligação de armazenamento predefinido para a aplicação, função, que é indicada pela definição de aplicação AzureWebJobsStorage.
- `type`: Tem de estar definida para *fila de espera*.
- `direction`: Tem de estar definida para *fora*. 

Exemplo *function.json* para uma fila de armazenamento de saída encadernação que utiliza um acionador de fila de espera e escreve uma mensagem de fila de espera:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Tipos de ligação de saída suportada fila de espera

O `queue` encadernação pode serializar os seguintes tipos da uma mensagem de fila de espera:

* Objeto (`out T` no c#, cria uma mensagem com um objecto nulo se o parâmetro é nulo quando termina a função)
* Cadeia (`out string` no c#, cria mensagem fila de espera, se o valor do parâmetro é não nulas quando termina a função)
* Matriz de bytes (`out byte[]` na c#, funciona como cadeia) 
* `out CloudQueueMessage`(C#, funciona como cadeia) 

Em c# também pode associar a `ICollector<T>` ou `IAsyncCollector<T>` onde `T` é um dos tipos de suportados.

#### <a name="queue-output-binding-code-examples"></a>Exemplos de código de ligação de saída de fila de espera

Este exemplo de código c# escreve uma mensagem de fila de saída único para cada mensagem de fila de entrada.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Este exemplo de código c# escreve várias mensagens utilizando `ICollector<T>` (utilizar `IAsyncCollector<T>` numa função de assíncrona):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Acionador de Blobs do Azure armazenamento

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON para accionador do armazenamento blob

O ficheiro *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para o blob. 
- `path`: Um caminho que especifica o contentor para monitorizar a e, opcionalmente, um padrão de Blobs do nome.
- `connection`: O nome de uma aplicação a definição que contém uma cadeia de ligação de armazenamento. Se as deixar `connection` vazio, o accionador irá funcionar com a cadeia de ligação de armazenamento predefinido para a aplicação, função, que é indicada pela definição de aplicação AzureWebJobsStorage.
- `type`: Tem de estar definida para *blobTrigger*.
- `direction`: Tem de estar definida *na*.

Exemplo *function.json* para um accionador de Blobs do armazenamento que está a observar para blobs são adicionados ao contentor de amostras workitems:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Acionar blob tipos suportados

O blob serialização pode ser anulado para qualquer um dos seguintes tipos de nas funções nó ou c#:

* Objeto (a partir do JSON)
* Cadeia

Em c# funções também pode associar a qualquer um dos seguintes tipos de:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Outros tipos de serialização anulados pelo [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>Blob accionador c# exemplo de código

Este exemplo de código c# regista os conteúdos de cada blob que é adicionada ao contentor de monitorizada.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Blob accionador nome padrões

Pode especificar um padrão de nome blob na `path` propriedade. Por exemplo:

```json
"path": "input/original-{name}",
```

Este caminho de iria localizar um blob com o nome *original Blob1.txt* no contentor de *entrada* e o valor da `name` variável no código de função seria `Blob1`.

Outro exemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Este caminho de seria também encontrar um blob com o nome *original Blob1.txt*e o valor da `blobname` e `blobextension` variáveis no código de função seria *original Blob1* e *txt*.

Pode restringir os tipos de blobs acionam a função ao especificar um padrão com um valor fixo para a extensão de ficheiro. Se definir o `path` *para/amostras / {nome}. png*, *. png* apenas os blobs no contentor de *amostras* irão acionar a função.

Se precisar de especificar um padrão de nome para nomes de BLOBs que tenham as chavetas no nome de, faça duplo as chavetas. Por exemplo, se pretender localizar blobs no contentor de *imagens* que tenham nomes da seguinte forma:

        {20140101}-soundfile.mp3

Utilize esta opção para o `path` propriedade:

        images/{{20140101}}-{name}

No exemplo, o `name` valor variável seria *soundfile.mp3*. 

#### <a name="blob-receipts"></a>Recibos de BLOBs

O tempo de execução do Azure funções torna-se de que nenhuma função de accionador blob obtém denominada mais de uma vez para o mesmo blob novo ou actualizado. Faz isto, mantendo *blob recibos* para determinar se uma versão de BLOBs determinado foi processada.

Recibos de BLOBs são armazenados num contentor com o nome *anfitriões de webjobs azure* na conta de armazenamento Azure especificada pela cadeia de ligação AzureWebJobsStorage. Um recibo de BLOBs tem as seguintes informações:

* A função que foi denominada para o blob ("*{nome da aplicação função}*. Funções. *{nome da função}*"como, por exemplo:"functionsf74b96f7. Functions.CopyBlob")
* O nome do contentor
* O tipo de BLOBs ("BlockBlob" ou "PageBlob")
* O nome de BLOBs
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Se pretende forçar reprocessamento de um blob, pode eliminar manualmente a recibo blob para esse blob do contentor *anfitriões de webjobs azure* .

#### <a name="handling-poison-blobs"></a>Processamento blobs corrompida foi

Quando uma função de accionador blob falha, o SDK liga-lhe-lo novamente, no caso de falha foi causada por um erro de breves. Se a falha é causada pelo conteúdo do blob, a função falha sempre tentar o blob do processo. Por predefinição, o SDK liga-lhe uma função até 5 vezes para um determinado blob. Se o experimentar quinto falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-poison*.

A mensagem de fila de espera para blobs corrompida foi é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato de *{nome da aplicação função}*. Funções. *{nome da função}*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

#### <a name="blob-polling-for-large-containers"></a>Blob grandes contentores de consulta

Se o contentor de BLOBs que está a acompanhar o accionador contiver mais do que 10.000 blobs, as pesquisas de tempo de execução de funções ficheiros para ver para blobs novas ou alteradas de registo. Este processo não está em tempo real; uma função pode, não, ser acionada até alguns minutos ou já depois do blob é criado. Além disso, base de [armazenamento de registos são criados num "os melhores esforços"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Não há garantias ser capturados de todos os eventos. Em algumas condições, poderão ser perdidos registos. Se as limitações fiabilidade e velocidade de accionadores blob de membros em contentores grandes não são permitidas para a sua aplicação, é o método recomendado criar uma mensagem de fila de espera quando criar o blob e utilize um acionador de fila em vez de um accionador blob para processar o blob.
 
## <a id="storageblobbindings"></a>BLOBs do Azure armazenamento de entrada e saída enlaces

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON para um blob de armazenamento de entrada ou ligação de saída

O ficheiro *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para o blob. 
- `path`: Um caminho que especifica o contentor blob a partir de leitura ou escrita blob para e, opcionalmente, um padrão de Blobs do nome.
- `connection`: O nome de uma aplicação a definição que contém uma cadeia de ligação de armazenamento. Se as deixar `connection` vazio, o enlace irá funcionar com a cadeia de ligação de armazenamento predefinido para a aplicação, função, que é indicada pela definição de aplicação AzureWebJobsStorage.
- `type`: Tem de estar definida para *blob*.
- `direction`: Defina para *ou para *fora** . 

Exemplo *function.json* para um armazenamento blob entrada ou saída enlace, utilizando um acionador de fila de espera para copiar um blob:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>Blob entrados e saídos tipos suportados

O `blob` encadernação pode serializar ou serialização os seguintes tipos de funções Node.js ou c#:

* Objeto (`out T` no c# para blob saída: cria um blob como objeto nulo se o valor do parâmetro é nulo quando termina a função)
* Cadeia (`out string` no c# para blob saída: cria um blob apenas se o parâmetro de cadeia é não nulas quando a função devolve)

Em c# funções, também pode associar aos seguintes tipos de:

* `TextReader`(apenas entrada)
* `TextWriter`(apenas saída)
* `Stream`
* `CloudBlobStream`(apenas saída)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>Blob saída c# exemplo de código

Este exemplo de código c# copia um blob cujo nome é recebido uma mensagem de fila de espera.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Azure tabelas de armazenamento de entrada e saída enlaces

#### <a name="functionjson-for-storage-tables"></a>Function.JSON para tabelas de armazenamento

O *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para a ligação de tabela. 
- `tableName`: O nome da tabela.
- `partitionKey`e `rowKey` : utilizada em conjunto para ler uma única entidade de uma função c# ou nó, ou para escrever uma única entidade numa função nó.
- `take`: O número máximo de linhas a ler para a tabela entrada numa função nó.
- `filter`: Expressão de filtro OData para a tabela entrada numa função nó.
- `connection`: O nome de uma aplicação a definição que contém uma cadeia de ligação de armazenamento. Se as deixar `connection` vazio, o enlace irá funcionar com a cadeia de ligação de armazenamento predefinido para a aplicação, função, que é indicada pela definição de aplicação AzureWebJobsStorage.
- `type`: Tem de estar definida a *tabela*.
- `direction`: Defina para *ou para *fora** . 

O exemplo seguinte *function.json* utiliza um acionador de fila de espera para ler uma linha de tabela única. O JSON fornece um valor de chave codificada partição e especifica que a chave de linha vem a partir da mensagem de fila de espera.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Tabelas de armazenamento de entrada e saída dos tipos de suportados

O `table` encadernação pode serializar ou serialização objetos nas funções Node.js ou c#. Os objetos terá as propriedades RowKey e PartitionKey. 

Em c# funções, também pode associar aos seguintes tipos de:

* `T`onde `T` implementa`ITableEntity`
* `IQueryable<T>`(apenas entrada)
* `ICollector<T>`(apenas saída)
* `IAsyncCollector<T>`(apenas saída)

#### <a name="storage-tables-binding-scenarios"></a>Armazenamento de tabelas cenários de encadernação

A ligação tabela suporta os seguinte cenários:

* Ler uma única linha de uma função c# ou nó.

    Definir `partitionKey` e `rowKey`. O `filter` e `take` propriedades não são utilizadas neste cenário.

* Leia várias linhas numa função c#.

    O tempo de execução de funções fornece uma `IQueryable<T>` objecto ligado à tabela. Tipo de `T` tem deriva `TableEntity` ou implementar `ITableEntity`. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são utilizadas neste cenário; Pode utilizar o `IQueryable` objeto para fazer a filtragem necessário. 

* Leia várias linhas numa função nó.

    Definir o `filter` e `take` propriedades. Não definir `partitionKey` ou `rowKey`.

* Escreva uma ou mais linhas numa função c#.

    O tempo de execução de funções fornece uma `ICollector<T>` ou `IAsyncCollector<T>` vinculado à tabela, onde `T` Especifica o esquema das entidades que pretende adicionar. Normalmente, escreva `T` deriva da `TableEntity` ou implementa `ITableEntity`, mas não tem de. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são utilizadas neste cenário.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Exemplo de tabelas de armazenamento: uma entidade de tabela única no c# ou nó de leitura

Exemplo de código c# seguinte funciona com o ficheiro *function.json* anterior mostrado anteriormente a leitura de uma entidade única tabela. A mensagem de fila tem o valor de chave de linha e a entidade de tabela é de leitura para um tipo de que é definido no ficheiro *run.csx* . O tipo de inclui `PartitionKey` e `RowKey` propriedades e não deriva `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Exemplo de código F # seguinte também funciona com o ficheiro *function.json* anterior para ler uma entidade única tabela.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

O exemplo seguinte de código nó também funciona com o ficheiro *function.json* anterior para ler uma entidade única tabela.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Exemplo de tabelas de armazenamento: Leia várias entidades de tabela no C# 

As seguintes *function.json* e c# código entidades de lê de exemplo para uma chave de partição especificado na mensagem de fila de espera.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O código c# adiciona uma referência para o SDK de armazenamento do Azure para que o tipo de entidade pode deriva `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Exemplo de tabelas de armazenamento: criar entidades de tabela no C# 

O exemplo seguinte de *function.json* e *run.csx* mostra como escrever entidades de tabela no c#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Exemplo de tabelas de armazenamento: criar entidades de tabela no F#

O exemplo seguinte de *function.json* e *run.fsx* mostra como escrever entidades de tabela no F #.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Exemplo de tabelas de armazenamento: criar uma entidade de tabela no nó

O exemplo seguinte de *function.json* e *run.csx* mostra como escrever uma entidade de tabela no nó.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
