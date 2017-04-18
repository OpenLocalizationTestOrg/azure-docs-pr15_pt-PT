<properties
    pageTitle="Referência para programadores do Azure funções | Microsoft Azure"
    description="Compreenda como desenvolver funções Azure utilizando c#."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Azure funções c# de referência para programadores

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [NODE.js](../articles/azure-functions/functions-reference-node.md)
 
A experiência c# do Azure funções é baseada no Azure WebJobs SDK. Dados flui para a sua função c# através do método argumentos. Nomes de argumentos são especificados no `function.json`, e não existem nomes predefinidos para aceder a coisas como as função do registo e cancelamento tokens de.

Este artigo assume que já leu a [referência para programadores do Azure funções](functions-reference.md).

## <a name="how-csx-works"></a>Como funciona a .csx

O `.csx` formato permite escrever menos "automático" e focar-se em apenas uma c# função a escrever. Para funções Azure, basta incluir qualquer referências de assemblagem e espaços de nomes precisa de uma cópia de início, como costuma fazer e, em vez de moldagem tudo num espaço de nomes e escolares, apenas pode definir o `Run` método. Se precisar de incluir qualquer classes, por exemplo para definir o POCO objetos, pode incluir uma classe dentro do mesmo ficheiro.

## <a name="binding-to-arguments"></a>Vincular a argumentos

Os vários enlaces estão vinculados a uma função c# através de `name` propriedade na configuração *function.json* . Cada enlace tem os suas próprias tipos suportados que é documentado por enlace; Por exemplo, um accionador blob pode suportar uma cadeia, um POCO ou vários outros tipos. Pode utilizar o tipo de que melhor se adequa às suas necessidades. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Funcionalidade de registo

Para iniciar sessão saída os registos de transmissão no c#, pode incluir um `TraceWriter` escreveu argumento. Recomendamos que o nome `log`. Recomendamos que evite `Console.Write` nas funções Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Assíncrona

Para tornar uma função assíncrona, utilize o `async` palavra-chave e devolve um `Task` objeto.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Token de cancelamento

Em certos casos, poderá ter operações que são sensíveis a maiúsculas e para a ser encerrar. Enquanto está sempre melhor escrever código que pode processar falhar, casos onde pretende que para lidar com êxito encerramento pedidos, pode definir um [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) escreveu argumento.  A `CancellationToken` serão fornecidos se um encerramento do anfitrião é acionado. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importar espaços de nomes

Se precisar de importar espaços de nomes, que pode fazer por isso, habitualmente, com a `using` cláusula.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Os espaços de nomes seguintes automaticamente são importados e, consequentemente, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciar assemblagem externas

Conjuntos de quadro, adicionar referências utilizando o `#r "AssemblyName"` directiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
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
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Se precisar de fazer referência a uma assemblagem privada, pode carregar o ficheiro de assemblagem para um `bin` pasta em relação a sua função e referência-la, utilizando o ficheiro (por exemplo, nome  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar ficheiros para a sua pasta de função, consulte a seguinte secção sobre a gestão de pacote.

## <a name="package-management"></a>Gestão de pacote

Para utilizar uma função c# NuGet pacotes, carregar um ficheiro de *project.json* para a pasta a função no sistema de ficheiros a aplicação de função. Eis um exemplo de ficheiro de *project.json* que adiciona uma referência a Microsoft.ProjectOxford.Face versão 1.1.0:

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

Apenas a 4.6 do .NET Framework é suportado, por isso, certifique-se de que o seu ficheiro *project.json* especifica `net46` conforme mostrado aqui.

Quando carregar um ficheiro de *project.json* , o runtime obtém os pacotes e adiciona automaticamente as referências aos conjuntos de pacote. Não precisa de adicionar `#r "AssemblyName"` diretivas do. Basta adicionar obrigatório `using` demonstrações ao seu ficheiro *run.csx* para utilizar os tipos de definidos em pacotes de NuGet.


### <a name="how-to-upload-a-projectjson-file"></a>Como carregar um ficheiro de project.json

1. Comece por se certificar de que a aplicação de função está em execução, que pode efetuar ao abrir a sua função no portal do Azure. 

    Isto também dá acesso aos registos de transmissão onde será apresentada saída de instalação do pacote. 

2. Para carregar um ficheiro de project.json, utilize um dos métodos descritos na secção do [tópico de referência para programadores Azure funções](functions-reference.md#fileupdate) **como atualizar os ficheiros de aplicação de função** . 

3. Depois de é carregado o ficheiro *project.json* , consulte saída semelhante ao exemplo seguinte na sua função da transmissão de registo:

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

Para obter uma variável de ambiente ou uma aplicação definir valor, utilize `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo seguinte código:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Reutilizar .csx código

Pode utilizar classes e métodos de definidos noutros ficheiros *.csx* no seu ficheiro *run.csx* . Para fazê-lo, utilize `#load` diretivas no ficheiro *run.csx* , conforme mostrado no seguinte exemplo.

Exemplo *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Pode utilizar um caminho relativo com o `#load` directiva:

* `#load "mylogger.csx"`carrega um ficheiro localizado na pasta de função.

* `#load "loadedfiles\mylogger.csx"`carrega um ficheiro localizado numa pasta na pasta de função.

* `#load "..\shared\mylogger.csx"`carrega um ficheiro localizado numa pasta do mesmo nível, como a pasta de função, ou seja, diretamente em *wwwroot*.
 
O `#load` directiva funciona apenas com os ficheiros *.csx* (c# script), não com ficheiros *. cs* . 

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos:

* [Referência para programadores do Azure funções](functions-reference.md)
* [Azure funções F # de referência para programadores](functions-reference-fsharp.md)
* [Referência para programadores do Azure NodeJS de funções](functions-reference-node.md)
* [Azure accionadores de funções e enlaces](functions-triggers-bindings.md)

