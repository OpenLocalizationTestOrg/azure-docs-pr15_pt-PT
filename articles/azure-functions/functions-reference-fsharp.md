<properties
    pageTitle="Azure funções F # de referência para programadores | Microsoft Azure"
    description="Compreenda como desenvolver funções Azure utilizando F #."
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="Azure funções, funções, evento processamento, webhooks, cluster dinâmico, sem servidor arquitetura, F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Azure funções referência para programadores F #

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [NODE.js](../articles/azure-functions/functions-reference-node.md)

F # para as funções de Azure é uma solução para executar facilmente pequenas partes de código ou "funções," na nuvem. Dados flui para a sua função F # através de argumentos da função. Nomes de argumentos são especificados no `function.json`, e não existem nomes predefinidos para aceder a coisas como as função do registo e cancelamento tokens de.

Este artigo assume que já leu a [referência para programadores do Azure funções](functions-reference.md).

## <a name="how-fsx-works"></a>Como funciona a .fsx

Um `.fsx` ficheiro é um script F #. Pode ser considerado de como um projeto F # está contido num único ficheiro. O ficheiro contém ambas as o código para o seu programa (neste caso, a função Azure) e diretivas para gerir dependências.

Quando utiliza um `.fsx` para uma função de Azure, geralmente necessários para o assemblagem está incluídas automaticamente para si, permitindo-lhe focar o código de função em vez de "automático".

## <a name="binding-to-arguments"></a>Vincular a argumentos

Cada enlace suporta alguns conjunto de argumentos, tal como descrito nas [funções Azure accionadores e referência para programadores de enlaces](functions-triggers-bindings.md). Por exemplo, uma dos enlaces argumento que um accionador blob suporta é um POCO, que pode ser expressa utilizando um registo F #. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

A função de Azure F # irá demorar um ou mais argumentos. Quando falarmos sobre argumentos de funções Azure, podemos referência argumentos de _entrada_ e _saída_ argumentos. Um argumento de entrada é exatamente o que parecido com: introdução a sua função Azure F #. Um argumento de _saída_ é mutáveis dados ou uma `byref<>` argumento que serve de uma forma para transmitir dados novamente _fora_ da sua função.

No exemplo acima, `blob` é um argumento de entrada, e `output` é um argumento de saída. Repare que utilizámos `byref<>` para `output` (não precisa de adicionar o `[<Out>]` anotação). Utilizar um `byref<>` tipo permite alterar quais registo a ou o objeto que o argumento que se refere a uma função.

Quando um registo F # é utilizado como um tipo de entrada, a definição de registo deve ser marcada com `[<CLIMutable>]` para permitir que o quadro do Azure funções para definir os campos corretamente antes de passar o registo para a função. Em definições avançadas, `[<CLIMutable>]` gera setters para as propriedades do registo. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma classe F # pode também ser utilizada para ambos na e saída de argumentos. Para uma classe, propriedades, normalmente, serão necessário getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Funcionalidade de registo

Para iniciar sessão saída ao seu [transmissão de registos](../app-service-web/web-sites-streaming-logs-and-console.md) no F #, a função deve ter um argumento do tipo `TraceWriter`. Para consistência, recomendamos que este argumento se chamar `log`. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Assíncrona

O `async` fluxo de trabalho pode ser utilizado, mas o resultado tem de devolver um `Task`. Isto pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento

Se necessitar de uma função processar encerramento correctamente, pode atribuir-lhe um [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumento. Isto pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importar espaços de nomes

Espaços de nomes podem ser abertos no como habitualmente:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Os espaços de nomes seguintes são automaticamente abertos:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciar assemblagem externas

Da mesma forma, assemblagem framework referências de ser adicionado com o `#r "AssemblyName"` directiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Os conjuntos seguintes são automaticamente adicionados pelas funções de Azure do ambiente de alojamento:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Além disso, os conjuntos seguintes são especiais caso e podem ser referenciadas pela simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se precisar de fazer referência a uma assemblagem privada, pode carregar o ficheiro de assemblagem para um `bin` pasta em relação a sua função e referência-la, utilizando o ficheiro (por exemplo, nome  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar ficheiros para a sua pasta de função, consulte a seguinte secção sobre a gestão de pacote.

## <a name="editor-prelude"></a>Editor Prelude

Um editor compatível com serviços de compilador F # não serão atento aos espaços de nomes e conjuntos Azure funções inclui automaticamente. Como tal, pode ser útil para incluir uma prelude que ajuda a localizar conjuntos de que está a utilizar o editor de e para abrir explicitamente espaços de nomes. Por exemplo:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Quando o Azure funções executa o seu código, processa origem com `COMPILED` definida, assim, o prelude editor será ignorada.

## <a name="package-management"></a>Gestão de pacote

Para utilizar os pacotes de NuGet numa função F #, adicione uma `project.json` do ficheiro para a pasta a função no sistema de ficheiros a aplicação de função. Eis um exemplo `project.json` ficheiro que adiciona uma referência de pacote NuGet `Microsoft.ProjectOxford.Face` versão 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Apenas a 4.6 do .NET Framework é suportada, por isso, certifique-se de que seu `project.json` ficheiro Especifica `net46` conforme mostrado aqui.

Quando carregar um `project.json` ficheiro, o tempo de execução obtém os pacotes e adiciona automaticamente as referências aos conjuntos de pacote. Não precisa de adicionar `#r "AssemblyName"` diretivas do. Basta adicionar obrigatório `open` declarações para sua `.fsx` ficheiro.

Pode optar por colocar automaticamente assemblagem de referências no seu prelude editor, para melhorar a interação do seu editor com F # compilar os serviços.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um `project.json` ficheiro para a sua função Azure

1. Comece por se certificar de que a aplicação de função está em execução, que pode efetuar ao abrir a sua função no portal do Azure. Isto também dá acesso aos registos de transmissão onde será apresentada saída de instalação do pacote.

2. Para carregar um `project.json` de ficheiros, utilize um dos métodos descritos no [artigo como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate). Se estiver a utilizar [Implementação do Azure funções como contínuo](functions-continuous-deployment.md), pode adicionar um `project.json` ficheiro para o seu ramo de transição para experimentar antes de adicioná-lo ao seu ramo de implementação.

3. Depois do `project.json` ficheiro é adicionado, irá ver o resultado semelhante ao exemplo seguinte na sua função da transmissão de registo:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou uma aplicação definir valor, utilize `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar .fsx código

Pode utilizar código das outras `.fsx` ficheiros utilizando um `#load` directiva. Por exemplo:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Caminhos fornece para o `#load` são directiva em relação a localização da sua `.fsx` ficheiro.

* `#load "logger.fsx"`carrega um ficheiro localizado na pasta de função.

* `#load "package\logger.fsx"`carrega um ficheiro localizado na `package` pasta na pasta de função.

* `#load "..\shared\mylogger.fsx"`carrega um ficheiro localizado na `shared` pasta no mesmo nível, como a pasta de função, ou seja, diretamente em `wwwroot`.

O `#load` directiva apenas funciona com `.fsx` ficheiros (F # script) e não com `.fs` ficheiros.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos:

* [Guia de F #](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Referência para programadores do Azure funções](functions-reference.md)
* [Azure funções c# de referência para programadores](functions-reference-csharp.md)
* [Referência para programadores do Azure NodeJS de funções](functions-reference-node.md)
* [Azure accionadores de funções e enlaces](functions-triggers-bindings.md)
* [Funções Azure testes](functions-test-a-function.md)
* [Azure funções escalar](functions-scale.md)
